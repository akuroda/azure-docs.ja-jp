---
title: 時系列予測モデルを自動トレーニングする
titleSuffix: Azure Machine Learning
description: Azure Machine Learning を使用して、自動化された機械学習で時系列予測回帰モデルをトレーニングする方法について説明します。
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, contperf-fy21q1, automl
ms.date: 08/20/2020
ms.openlocfilehash: 14837391f7bf907acbbe1d573f3171acef4db658
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503506"
---
# <a name="auto-train-a-time-series-forecast-model"></a>時系列予測モデルを自動トレーニングする


この記事では、[Azure Machine Learning Python SDK](/python/api/overview/azure/ml/) の自動機械学習 (AutoML) を使用して、時系列予測回帰モデルを構成およびトレーニングする方法について説明します。 

これを行うには、次の手順を実行します。 

> [!div class="checklist"]
> * 時系列モデリング用のデータを準備します。
> * [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) オブジェクトで特定の時系列パラメーターを構成します。
> * 時系列データで予測を実行します。

コードの作成経験が少ない場合は、「[チュートリアル:自動機械学習を使用して自転車シェアリング需要を予測する](tutorial-automated-ml-forecast.md)」で [Azure Machine Learning Studio](https://ml.azure.com/) の自動機械学習を使用して、時系列の予測を行う方法について参照してください。

従来の時系列メソッドとは異なり、自動 ML では過去の時系列値が "ピボット" され、他の予測子と共にリグレッサーの追加のディメンションになります。 このアプローチでは、トレーニング中に複数のコンテキスト変数とそれらの相互関係を組み込みます。 複数の要因が予測に影響を与える可能性があるため、この方法は実際の予測シナリオに適しています。 たとえば、売上の予測では、履歴による傾向とのインタラクション、為替レート、および価格のすべてが、売上の結果に貢献します。 

## <a name="prerequisites"></a>前提条件

この記事では、以下が必要です。 

* Azure Machine Learning ワークスペース。 ワークスペースを作成するには、[Azure Machine Learning ワークスペース](how-to-manage-workspace.md)の作成に関するページを参照してください。

* この記事では、自動化された機械学習実験の設定にある程度精通していることを前提としています。 [チュートリアル](tutorial-auto-train-models.md)または[方法](how-to-configure-auto-train.md)に従って、自動化された機械学習実験の主要な設計パターンについて確認してください。

## <a name="preparing-data"></a>データの準備

AutoML 内での予測回帰タスクの種類と回帰タスクの種類の最も重要な違いは、有効な時系列を表す機能がデータに含まれるという点です。 通常の時系列には、明確に定義され一貫した頻度があり、連続した期間のすべてのサンプル ポイントで値があります。 

ファイル `sample.csv` の次のスナップショットを見てください。
このデータ セットは、A と B の 2 つの異なる店舗を持つ会社の日別の売上データです。 

また、次の機能があります。

 *  `week_of_year`: モデルで週別の季節性を検出できるようにします。
* `day_datetime`: クリーンな時系列を日別の頻度で表します。
* `sales_quantity`: 予測を実行するターゲットの列です。 

```output
day_datetime,store,sales_quantity,week_of_year
9/3/2018,A,2000,36
9/3/2018,B,600,36
9/4/2018,A,2300,36
9/4/2018,B,550,36
9/5/2018,A,2100,36
9/5/2018,B,650,36
9/6/2018,A,2400,36
9/6/2018,B,700,36
9/7/2018,A,2450,36
9/7/2018,B,650,36
```


Pandas データフレームにデータを読み取ってから `to_datetime` 関数を使用して、時系列が `datetime` 型であることを確認します。

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

この場合、データは既に時間フィールド `day_datetime` により昇順で並べ替えられています。 ただし、実験を設定するときに、有効な時系列を構築するために、目的の時間列が昇順で並べ替えられていることを確認してください。 

次のコードによって、以下の処理が実行されます。 
* データには 1,000 個のレコードが含まれていると想定し、データ内を結果が予測できるように分割してトレーニングおよびテスト用データ セットを作成します。 
* ラベル列を `sales_quantity` として識別します。
* ラベル フィールドを `test_data` から分離し、`test_target` セットを作成します。

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!IMPORTANT]
> 将来の値を予測するためにモデルをトレーニングするときは、目的のトレーニングで使用されるすべての機能が、意図した期間の予測を実行するときに使用できることを確認してください。 <br> <br>たとえば、需要の予測を作成するときに、現在の株価の機能を含めれば、トレーニングの精度を大幅に高めることができます。 ただし、長期間にわたり予測する予定の場合、将来の時系列ポイントに応じた将来の株価を正確に予測できない可能性があり、モデルの精度が低下することがあります。

<a name="config"></a>

## <a name="training-and-validation-data"></a>データをトレーニングして検証する

`AutoMLConfig` オブジェクトでは、個別のトレーニングおよび検証セットを直接指定できます。   [AutoMLConfig](#configure-experiment) の詳細について参照してください。

時系列の予測の場合、既定では、**ローリング オリジン クロス検証 (ROCV)** のみが検証に使用されます。 トレーニングおよびと検証データを一緒に渡し、`AutoMLConfig` の `n_cross_validations` パラメーターでクロス検証の分割数を設定します。 ROCV を使用すると、起点の時点を使用して系列をトレーニング データと検証データに分割することができます。 時間内の原点をスライドすると、クロス検証のフォールドが生成されます。 この戦略を使用すると、時系列データの整合性を維持し、データ漏えいのリスクを排除できます。

![ローリング オリジン クロス検証](./media/how-to-auto-train-forecast/ROCV.svg)

また、独自の検証データを取り込むこともできます。詳細については、[AutoML でのデータの分割とクロス検証の構成](how-to-configure-cross-validation-data-splits.md#provide-validation-data)に関する記事を参照してください。


```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```

AutoML でクロス検証を適用して[モデルのオーバーフィットを防止](concept-manage-ml-pitfalls.md#prevent-over-fitting)する方法について、詳細情報をご覧ください。

## <a name="configure-experiment"></a>実験を構成する

[`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) オブジェクトは、自動化された機械学習タスクに必要な設定とデータを定義します。 予測モデルの構成は、標準の回帰モデルの設定に似ていますが、特定のモデル、構成オプション、および特徴量化の手順は時系列データ専用です。 

### <a name="supported-models"></a>サポートされているモデル
自動機械学習では、モデル作成とチューニング プロセスの一環として、さまざまなモデルとアルゴリズムが自動的に試行されます。 ユーザーは、アルゴリズムを指定する必要はありません。 予測実行では、ネイティブな時系列とディープ ラーニングのモデルはどちらも、レコメンデーション システムの一部です。 次の表は、このモデルのサブセットをまとめたものです。 

>[!Tip]
> 従来の回帰モデルも、予測実験のレコメンデーション システムの一部としてテストされます。 モデルの完全な一覧については、[サポートされているモデルの表](how-to-configure-auto-train.md#supported-models)を参照してください。 

モデル| 説明 | メリット
----|----|---
Prophet (プレビュー)|Prophet は、強い季節的影響や複数の季節の履歴データを持つ時系列に最適です。 このモデルを利用するには、`pip install fbprophet` を使用してローカルにインストールします。 | 正確かつ高速で、時系列における外れ値、不足データ、および大幅な変化に対して有効です。
自動 ARIMA (プレビュー)|自己回帰和分移動平均 (ARIMA) は、データが静的な場合に最適です。 これは、平均や分散などの統計的特性がセット全体で一定であることを意味します。 たとえば、コインを投げた場合、今日、明日、来年のいつ投げても、表が出る確率は 50% です。| 過去の値は将来の値を予測するために使用されるため、単変量系列に最適です。
ForecastTCN (プレビュー)| ForecastTCN は、最も要求の厳しい予測タスクに対応するように設計されたニューラル ネットワーク モデルであり、データ内の非線形のローカル傾向とグローバル傾向と、時系列間の関係がキャプチャされます。|データの複雑な傾向を活用し、最大のデータセットに合わせて簡単にスケーリングできます。

### <a name="configuration-settings"></a>構成設定

回帰の問題と同様に、タスクの種類、イテレーションの数、トレーニング データ、クロス検証の数など、標準的なトレーニング パラメーターを定義します。 予測タスクの場合は、実験に影響を与える追加パラメーターを設定する必要があります。 

これらの追加パラメーターの概要を次の表に示します。 構文のデザイン パターンについては、[ForecastingParameter クラスのリファレンス ドキュメント](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters)を参照してください。

| パラメーター名&nbsp; | 説明 | 必須 |
|-------|-------|-------|
|`time_column_name`|時系列の構築とその頻度の推定に使用される入力データで、datetime 列を指定するために使用されます。|✓|
|`forecast_horizon`|予測する今後の期間の数を定義します。 horizon とは、時系列頻度の単位です。 単位は、月ごとや週ごとなどの予測を実行する必要があるトレーニング データの時間間隔に基づきます。|✓|
|`enable_dnn`|[予測 DNN を有効にします]()。||
|`time_series_id_column_names`|タイムスタンプが同じ複数の行を含むデータ内の時系列を一意に識別するために使用される列名。 時系列識別子が定義されていない場合、データ セットは 1 つの時系列であると見なされます。 単一の時系列の詳細については、[energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand) に関するページを参照してください。||
|`freq`| 時系列データセットの頻度。 このパラメーターは、日ごと、週ごと、年ごとなどのイベントが発生することが予想される期間を表します。頻度は、[pandas のオフセットのエイリアス](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)である必要があります。||
|`target_lags`|データの頻度に基づいて対象の値を遅延させる行の数。 このラグは一覧または単一の整数として表されます。 独立変数と依存変数の間のリレーションシップが既定で一致しない場合、または関連付けられていない場合、ラグを使用する必要があります。 ||
|`feature_lags`| `target_lags` が設定され、`feature_lags` が `auto` に設定されている場合、遅延する機能が自動 ML によって自動的に決定されます。 機能のタイム ラグを有効にすると、精度の向上に役立つ場合があります。 既定では、機能のタイム ラグは無効になっています。 ||
|`target_rolling_window_size`|予測値の生成に使用する *n* 履歴期間 (トレーニング セットのサイズ以下)。 省略した場合、*n* はトレーニング セットの全体のサイズになります。 モデルのトレーニング時に特定の量の履歴のみを考慮する場合は、このパラメーターを指定します。 [ターゲットのローリング ウィンドウ集計](#target-rolling-window-aggregation)について、詳細情報をご覧ください。||
|`short_series_handling_config`| 短い時系列処理を有効にして、データの不足によるエラーがトレーニング中に発生しないようにします。 短い時系列処理は、既定で `auto` に設定されています。 [短い系列の処理](#short-series-handling)に関する詳細を確認してください。|


次のコードによって、以下の処理が実行されます。 
* [`ForecastingParameters`](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters) クラスを活用して、実験トレーニング用の予測パラメーターを定義します。
* `time_column_name` をデータ セットの `day_datetime` フィールドに設定します。 
* `time_series_id_column_names` パラメーターを `"store"` に定義します。 これにより、データ用に **2 つの異なる時系列グループ** (ストア A および B 用) が確実に作成されます。
* テスト セット全体を予測するために、`forecast_horizon` を 50 に設定します。 
* `target_rolling_window_size` を使用して、予測ウィンドウを 10 期間に設定します。
* `target_lags` パラメーターで前方に 2 期間のターゲット値に 1 つのタイム ラグを指定します。 
* `target_lags` を推奨される "自動" 設定に設定します。これにより、この値が自動的に検出されます。

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecasting_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                               forecast_horizon=50,
                                               time_series_id_column_names=["store"],
                                               freq='W',
                                               target_lags='auto',
                                               target_rolling_window_size=10)
                                              
```

これらの `forecasting_parameters` は、`forecasting` のタスクの種類、プライマリ メトリック、終了基準、トレーニング データと共に、標準の `AutoMLConfig` オブジェクトに渡されます。 

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **forecasting_parameters)
```

自動 ML で予測モデルを正常にトレーニングするために必要なデータ量は、`AutoMLConfig` の構成時に指定した `forecast_horizon`、`n_cross_validations`、および `target_lags` または `target_rolling_window_size` の値の影響を受けます。 

次の式では、時系列機能を構築するために必要な履歴データ量が計算されます。

必要な最小履歴データ: (2 x `forecast_horizon`) + #`n_cross_validations` + max (max (`target_lags`), `target_rolling_window_size`)

指定された関連する設定に必要な履歴データ量が満たされていないデータセット内ではすべての系列に対してエラー例外が発生します。 

### <a name="featurization-steps"></a>特徴量化の手順

自動機械学習におけるあらゆる実験では、自動スケーリングと正規化の手法が既定でデータに適用されます。 これらの手法は、さまざまなスケールの特徴に反応する "*特定*" のアルゴリズムを支援する **特徴量化** の一種です。 既定の特徴量化の手順の詳細については、[AutoML での特徴量化](how-to-configure-auto-features.md#automatic-featurization)に関するページを参照してください。

ただし、次の手順は `forecasting` のタスクの種類に対してのみ実行されます。

* 時系列のサンプル頻度 (たとえば、1 時間ごと、毎日、毎週) を検出し、存在しない時間ポイントについて新しいレコードを作成して時系列を連続にします。
* ターゲット列 (前方フィルにより) および機能列 (メジアン列値を使用) の欠損値を補完します
* さまざまな系列で一定の効果を可能にする時系列識別子に基づく機能を作成します
* 季節のパターンの学習を支援する時間ベースの機能を作成します
* カテゴリ変数を数量にエンコードします

これらの手順の結果として作成される機能の概要については、「[特徴量化の透過性](how-to-configure-auto-features.md#featurization-transparency)」を参照してください。

> [!NOTE]
> 自動化された機械学習の特徴付け手順 (機能の正規化、欠損データの処理、テキストから数値への変換など) は、基になるモデルの一部になります。 このモデルを予測に使用する場合、トレーニング中に適用されたのと同じ特徴付けの手順がご自分の入力データに自動的に適用されます。

#### <a name="customize-featurization"></a>特徴量化のカスタマイズ

ML モデルのトレーニングに使用されたデータと特徴から確実に適切な予測が得られるよう、特徴量化の設定をカスタマイズすることもできます。 

`forecasting` タスクでサポートされるカスタマイズは次のとおりです。

|カスタマイズ|定義|
|--|--|
|**列の目的の更新**|指定した列の自動検出された特徴の種類をオーバーライドします。|
|**トランスフォーマー パラメーターの更新** |指定したトランスフォーマーのパラメーターを更新します。 現在は、*Imputer* (fill_value および median) がサポートされています。|
|**列の削除** |特徴量化から削除する列を指定します。|

SDK を使用して特徴量化をカスタマイズするには、`AutoMLConfig` オブジェクト内で `"featurization": FeaturizationConfig` を指定します。 [カスタムの特徴量化](how-to-configure-auto-features.md#customize-featurization)について、詳細情報をご覧ください。

>[!NOTE]
> **列の削除** 機能は、SDK バージョン 1.19 以降は非推奨となっています。 自動 ML 実験で使用する前に、データ クレンジングの一環としてデータセットから列を削除します。 

```python
featurization_config = FeaturizationConfig()

# `logQuantity` is a leaky feature, so we remove it.
featurization_config.drop_columns = ['logQuantitity']

# Force the CPWVOL5 feature to be of numeric type.
featurization_config.add_column_purpose('CPWVOL5', 'Numeric')

# Fill missing values in the target column, Quantity, with zeroes.
featurization_config.add_transformer_params('Imputer', ['Quantity'], {"strategy": "constant", "fill_value": 0})

# Fill mising values in the `INCOME` column with median value.
featurization_config.add_transformer_params('Imputer', ['INCOME'], {"strategy": "median"})
```

Azure Machine Learning Studio を実験に使用している場合は、[Studio で特徴量化をカスタマイズする方法](how-to-use-automated-ml-for-ml-models.md#customize-featurization)に関する記事を参照してください。

## <a name="optional-configurations"></a>オプションの構成

ディープ ラーニングの有効化やターゲットのローリング ウィンドウ集計の指定など、予測タスクに対して追加のオプションの構成を使用できます。 

### <a name="enable-deep-learning"></a>ディープ ラーニングを有効にする

> [!NOTE]
> 自動機械学習での予測における DNN サポートは **プレビュー** 段階です。また、ローカルの実行はサポートされていません。

深層ニューラル ネットワーク (DNN) を使用してディープ ラーニングを活用し、モデルのスコアを向上させることもできます。 自動 ML のディープ ラーニングを使用すると、一変量および多変量の時系列データを予測できます。

ディープ ラーニング モデルには、3 つの組み込み機能があります。
1. 入力から出力への任意のマッピングから学習できる
1. 複数の入力と出力をサポートする
1. 長いシーケンスにまたがる入力データのパターンを自動的に抽出できる 

ディープ ラーニングを有効にするには、`AutoMLConfig` オブジェクトの `enable_dnn=True` を設定します。

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **forecasting_parameters)
```
> [!Warning]
> SDK を使用して作成された実験に対して DNN を有効にすると、[最適なモデル説明](how-to-machine-learning-interpretability-automl.md)が無効になります。

Azure Machine Learning Studio で作成された AutoML 実験用の DNN を有効にするには、[Studio 入門にあるタスクの種類の設定に関するページ](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)を参照してください。

DNN を利用した詳細なコード例については、[飲料生産予測 ノートブック](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) をご参照ください。

### <a name="target-rolling-window-aggregation"></a>ターゲットのローリング ウィンドウ集計
多くの場合、予測器が持つことができる最高の情報はターゲットの最近の値です。  ターゲットのローリング ウィンドウ集計を使用すると、データ値のローリング集計を特徴として追加できます。 これらの追加の特徴を追加のコンテキスト データとして生成および使用すると、トレーニング モデルの精度が向上します。

たとえば、エネルギー需要を予測するとします。 3 日間のローリング ウィンドウの特徴を追加して、暖房が入っている空間の熱変化を考慮することもできます。 この例では、`AutoMLConfig` コンストラクターで `target_rolling_window_size= 3` を設定して、このウィンドウを作成します。 

この表は、ウィンドウ集計が適用されたときに発生する特徴エンジニアリングを示しています。 **最小値、最大値**、**合計値** の列は、定義された設定に基づいて、3 のスライディング ウィンドウで生成されます。 各行には新しい計算済みの特徴があります。2017 年 9 月 8 日の午前 4:00 のタイムスタンプの場合、最大値、最小値、合計値は、2017 年 9 月 8 日午前 1:00 から午前 3:00 までの **需要値** を使用して計算されます。 この 3 のウィンドウは、残りの行のデータを設定するためにシフトされます。

![ターゲットのローリング ウィンドウ](./media/how-to-auto-train-forecast/target-roll.svg)

[ターゲットのローリング ウィンドウ集計の特徴](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)を利用した Python コードの例を参照してください。

### <a name="short-series-handling"></a>短い系列の処理

自動 ML では、モデル開発のトレーニングと検証のフェーズを実施するのに十分なデータ ポイントがない場合、時系列は **短い系列** と見なされます。 データ ポイントの数は実験ごとに異なり、max_horizon、クロス検証の分割の数、モデルのルックバックの長さ (時系列の特徴を構成するために必要な最大の履歴) に依存します。 正確な計算については、[short_series_handling_configuration のリファレンス ドキュメント](/python/api/azureml-automl-core/azureml.automl.core.forecasting_parameters.forecastingparameters#short-series-handling-configuration)を参照してください。

自動 ML は、既定では `ForecastingParameters` オブジェクトの `short_series_handling_configuration` パラメーターを使用して、短い系列の処理を提供します。 

短い系列の処理を有効にするには、`freq` パラメーターも定義する必要があります。 時間単位の頻度を定義するには、`freq='H'` を設定します。 頻度の文字列オプションは、[こちら](https://pandas.pydata.org/pandas-docs/stable/user_guide/timeseries.html#dateoffset-objects)をご覧ください。 既定の動作 `short_series_handling_configuration = 'auto'` を変更するには、`ForecastingParameter` オブジェクトの `short_series_handling_configuration` パラメーターを更新します。  

```python
from azureml.automl.core.forecasting_parameters import ForecastingParameters

forecast_parameters = ForecastingParameters(time_column_name='day_datetime', 
                                            forecast_horizon=50,
                                            short_series_handling_configuration='auto',
                                            freq = 'H',
                                            target_lags='auto')
```
次の表は、`short_series_handling_config` のために設定できる項目をまとめたものです。
 
|設定|説明
|---|---
|`auto`| 次に、短い系列の処理の既定の動作を示します。 <li> *すべての系列が短い* 場合は、データを埋め込みます。 <br> <li> *すべてが短い系列ではない* 場合は、短い系列をドロップします。 
|`pad`| `short_series_handling_config = pad` の場合、自動 ML によって、検出されたそれぞれの短い系列に対してランダムの値が追加されます。 次に、列の型と、それらに埋め込まれる内容を示します。 <li>オブジェクト列には NAN <li> 数値列には 0 <li> ブール/論理列には False <li> ターゲット列には、平均が 0、標準偏差が 1 のランダムな値が埋め込まれます。 
|`drop`| `short_series_handling_config = drop` の場合、短い系列は自動 ML によってドロップされ、トレーニングや予測には使用されません。 これらの系列の予測では、NAN が返されます。
|`None`| 埋め込まれる、またはドロップされる系列はありません。

>[!WARNING]
>失敗せずに過去のトレーニングが受けられるように人為的なデータを用いるため、データが埋め込まれると、結果のモデルの精度に影響が及ぶ可能性があります。 <br> <br> 短い系列が多くなる場合、説明可能性の結果に影響が生じる可能性があります。

## <a name="run-the-experiment"></a>実験を実行する 

`AutoMLConfig` オブジェクトの準備が整ったら、実験を送信できます。 モデルの終了後、最適な実行イテレーションを取得します。

```python
ws = Workspace.from_config()
experiment = Experiment(ws, "Tutorial-automl-forecasting")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```
 
## <a name="forecasting-with-best-model"></a>最適モデルによる予測

最適モデルのイテレーションを使用して、テスト データ セットの値を予測します。

`forecast()` 関数を使用すると、予測を開始するタイミングを指定できます。これは、分類と回帰のタスクに通常使用される `predict()` とは異なります。

次の例では、最初に `y_pred` のすべての値を `NaN` に置き換えます。 このケースでは、予測の始まりはトレーニング データの最後になります。 ただし、`y_pred` の後半部分のみを `NaN` に置き換えた場合、この関数では前半の数値を変更しないまま、後半の `NaN` の値を予測します。 この関数は、予測値と調整された特徴の両方を返します。

`forecast()` 関数の `forecast_destination` パラメーターを使用して、指定した日付までの値を予測することもできます。

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

`actual_labels` の実際の値と `predict_labels` の予測値との二乗平均平方根誤差 (RMSE) を計算します。

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

モデル全体の精度は判別しているので、最も現実的な次の手順は、モデルを使用して不明な将来の値を予測することです。 

テスト セット `test_data` と同じ形式ですが将来の日時を使用してデータ セットを提供すると、結果の予測セットは時系列手順ごとに予測された値になります。 データ セット内の最後の時系列レコードは 2018 年 12 月 31 日のものだったとします。 次の日 (または予測する必要のある数の期間、< = `forecast_horizon`) の需要を予測するには、2019 年 1 月 1 日の店舗ごとに 1 つの時系列レコードを作成します。

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

必要な手順を繰り返して、この将来のデータをデータフレームに読み込んで、`best_run.predict(test_data)` を実行して将来の値を予測します。

> [!NOTE]
> `target_lags` や `target_rolling_window_size` が有効になっている場合、サンプル内の予測は、自動 ML を使用した予測ではサポートされません。


## <a name="example-notebooks"></a>サンプルの Notebook
次のような高度な予測の構成の詳細なコード例については、[予測サンプル ノートブック](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning)を参照してください。

* [休日の検出と特性付け](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [ローリング オリジン クロス検証](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [構成可能なラグ](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [ローリング ウィンドウの集計機能](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [DNN](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

## <a name="next-steps"></a>次のステップ

* [モデルをデプロイする方法と場所](how-to-deploy-and-where.md)についてさらに詳しく学習する。
* [解釈可能性: 自動機械学習のモデルの説明 (プレビュー)](how-to-machine-learning-interpretability-automl.md) について学習する。 
* [多数モデル ソリューション アクセラレータ](https://aka.ms/many-models)で AutoML を使用して複数のモデルをトレーニングする方法について学習する。
* 自動機械学習を使用して実験を作成するためのエンド ツー エンドの例については、[チュートリアル](tutorial-auto-train-models.md)に従ってください。
