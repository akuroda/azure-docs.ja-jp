---
title: VS Code 拡張機能 (プレビュー) によるリソースの作成と管理
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Visual Studio Code 拡張機能を使用して Azure Machine Learning リソースを作成して管理する方法について説明します。
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 11/16/2020
ms.openlocfilehash: f8eb18b190b72381f1a93575eb39b3d19d8d431b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96010675"
---
# <a name="manage-azure-machine-learning-resources-with-the-vs-code-extension-preview"></a>VS Code 拡張機能 (プレビュー) を使用して Azure Machine Learning リソースを管理する

VS Code 拡張機能を使用して Azure Machine Learning リソースを管理する方法について説明します。

![Azure Machine Learning VS Code 拡張機能](media/how-to-manage-resources-vscode/azure-machine-learning-vscode-extension.png)

## <a name="prerequisites"></a>前提条件

- Azure のサブスクリプション。 お持ちでない場合は、[無料版または有料版の Azure Machine Learning](https://aka.ms/AMLFree) にサインアップしてお試しください。
- Visual Studio Code。 インストールしていない場合は、[インストール](https://code.visualstudio.com/docs/setup/setup-overview)します。
- VS Code Azure Machine Learning 拡張機能。 拡張機能をインストールするには、[Azure Machine Learning VS Code 拡張機能のインストール ガイド](tutorial-setup-vscode-extension.md#install-the-extension)に従ってください。

以下のすべてのプロセスは、Visual Studio Code の Azure Machine Learning ビューを開いていることを前提としています。 拡張機能を起動するには、VS Code アクティビティ バーの **[Azure]** アイコンを選択します。

## <a name="workspaces"></a>Workspaces

詳細については、[ワークスペース](concept-workspace.md)に関するページを参照してください。

### <a name="create-a-workspace"></a>ワークスペースの作成

1. Azure Machine Learning ビューで、サブスクリプション ノードを右クリックし、 **[ワークスペースの作成]** を選択します。
1. 次のように入力します。
    1. ワークスペースの名前を指定します
    1. Azure サブスクリプションを選びます
    1. 新しいリソース グループを選択するか作成して、ワークスペースをプロビジョニングします
    1. ワークスペースをプロビジョニングする場所を選択します。

ワークスペースを作成する別の方法として、次のものがあります。

- **[表示] > [コマンド パレット]** からコマンド パレットを開いて、テキスト プロンプトに「**Azure ML:Create Workspace**」と入力します。
- Azure Machine Learning ビューの上部にある `+` アイコンをクリックします。
- 他のリソースをプロビジョニングするときにワークスペースを選択するように求めるメッセージが表示されたら、新しいワークスペースを作成します。

### <a name="remove-a-workspace"></a>ワークスペースを削除する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. 削除するワークスペースを右クリックします。
1. 削除するかどうかを選択します。
    - *[Only the workspace] (ワークスペースのみ)* :このオプションを選択すると、ワークスペース Azure リソース **のみ** が削除されます。 ワークスペースがアタッチされていたリソース グループ、ストレージ アカウント、その他のリソースは Azure に残ります。
    - *[With associated resources] (関連するリソースを含む)* :このオプションを選択すると、ワークスペースが削除され、**さらに** 関連付けられているすべてのリソースが削除されます。

## <a name="datastores"></a>データストア

現在のところ、VS Code 拡張機能では次の種類のデータストアがサポートされています。

- Azure ファイル共有
- Azure Blob Storage

ワークスペースを作成すると、これらの型ごとにデータストアが作成されます。

詳細については、[データストア](concept-data.md#datastores)に関するページを参照してください。

### <a name="create-a-datastore"></a>データストアを作成する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. データストアを作成するワークスペース ノードを展開します。
1. **[データストア]** ノードを右クリックし、 **[Register datastore] (データストアの登録)** を選択します。
1. 次のように入力します。
    1. データストアの名前を指定します。
    1. データストアの種類を選択します。
    1. ストレージ リソースを選択します。 ワークスペースに関連付けられているストレージ リソースを選択するか、Azure サブスクリプション内の任意の有効なストレージ リソースから選択することができます。
    1. 以前に選択したストレージ リソース内のデータが格納されているコンテナーを選択します。
1. 構成ファイルが VS Code に表示されます。 構成ファイルに問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="manage-a-datastore"></a>データストアを管理する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. ワークスペース内の **[データストア]** ノードを展開します。
1. 以下を行うデータストアを選択します。
    - *[既定に設定]* 。 これが、実験を実行するたびに使用されるデータストアになります。
    - *[Inspect read-only settings] (読み取り専用設定を検査)* 。
    - *[変更]* 。 認証の種類と資格情報を変更します。 サポートされている認証の種類には、アカウント キーと SAS トークンがあります。

## <a name="datasets"></a>データセット

現在のところ、拡張機能では次のデータセットの種類がサポートされています。

- *[表形式]* :データフレーム (Pandas または PySpark) にデータを具体化できます。
- *ファイル*:ファイルまたはファイルのコレクション。 コンピューティングにファイルをダウンロードまたはマウントできます。

詳細については、[データセット](concept-data.md#datasets)に関するページを参照してください

### <a name="create-dataset"></a>データセットを作成する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. データストアを作成するワークスペース ノードを展開します。
1. **[データセット]** ノードを右クリックし、 **[データセットの作成]** を選択します。
1. 次のように入力します。
    1. データセットの種類を選択します
    1. データが PC と Web のどちらに配置されているかを定義します
    1. データの場所を指定します。 1 つのファイル、またはデータ ファイルを含むディレクトリを指定できます。
    1. データのアップロード先となるデータストアを選択します。
    1. データストア内のデータセットを識別するためのプレフィックスを指定します。

### <a name="version-datasets"></a>データセットをバージョン管理する

機械学習モデルを構築する場合、データが変更されたときにデータセットのバージョン管理を行いたい場合があります。 これを行うには、VS Code 拡張機能で以下の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. **[データセット]** ノードを展開します。
1. バージョンを管理するデータセットを右クリックし、 **[新しいバージョンを作成する]** を選択します。
1. 次のように入力します。
    1. データセットの種類を選択します
    1. データが PC と Web のどちらに配置されているかを定義します。
    1. データの場所を指定します。 1 つのファイル、またはデータ ファイルを含むディレクトリを指定できます。
    1. データのアップロード先となるデータストアを選択します。
    1. データストア内のデータセットを識別するためのプレフィックスを指定します。

### <a name="view-dataset-properties"></a>データセット プロパティを表示する

このオプションを使用すると、特定のデータセットに関連付けられているメタデータを表示できます。 これを行うには、VS Code 拡張機能で以下の操作を行います。

1. ワークスペース ノードを展開します。
1. **[データセット]** ノードを展開します。
1. 調査するデータセットを右クリックし、 **[View Dataset Properties] (データセット プロパティの表示)** を選択します。 これにより、最新のデータセット バージョンのプロパティを含む構成ファイルが表示されます。

> [!NOTE]
> データセットに複数のバージョンがある場合は、データセット ノードを展開し、対象のバージョンに対してこのセクションで説明した手順と同じ手順を実行することで、特定のバージョンのデータセット プロパティのみを表示できます。

### <a name="unregister-datasets"></a>データセットを登録解除する

データセットとそのすべてのバージョンを削除するには、そのデータセットの登録を解除します。 これを行うには、VS Code 拡張機能で以下の操作を行います。

1. ワークスペース ノードを展開します。
1. **[データセット]** ノードを展開します。
1. 登録を解除するデータセットを右クリックし、 **[データセットの登録解除]** を選択します。

## <a name="environments"></a>環境

詳細については、[環境](concept-environments.md)に関するページを参照してください。

### <a name="create-environment"></a>環境を作成する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. データストアを作成するワークスペース ノードを展開します。
1. **[環境]** ノードを右クリックし、 **[環境の作成]** を選択します。
1. 次のように入力します。
    1. 環境の名前を指定します
    1. 環境構成を定義します。
        - *キュレーションされた環境*:Azure Machine Learning の構成済み環境。 JSON ファイルの `dependencies` プロパティを変更することで、環境をさらにカスタマイズできます。 [キュレーションされた環境](resource-curated-environments.md)の詳細については、こちらを参照してください。
        - *Conda 依存関係ファイル*:Anaconda 環境では、環境定義を含むファイルを指定できます。
        - *pip 要件ファイル*:pip 環境では、環境定義を含むファイルを指定できます。
        - *既存の Conda 環境*:このオプションを選択すると、ローカル PC の conda 環境が検索され、選択した環境から環境を構築しようとします。
        - *Custom*:独自のチャネルと依存関係を定義します
    1. エディターで構成ファイルが開きます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="view-environment-configurations"></a>環境構成を表示する

拡張機能の特定の環境の依存関係と構成を表示するには、以下の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. **[環境]** ノードを展開します。
1. 表示する環境を右クリックし、 **[View Environment] (環境の表示)** を選択します。

### <a name="edit-environment-configurations"></a>環境構成を編集する

拡張機能の特定の環境の依存関係と構成を編集するには、以下の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[環境]** ノードを展開します。
1. 表示する環境を右クリックし、 **[環境の編集]** を選択します。
1. 変更を行った後、構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

## <a name="experiments"></a>実験

詳細については、[実験](concept-azure-machine-learning-architecture.md#experiments)に関するページを参照してください。

### <a name="create-experiment"></a>実験の作成

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. ワークスペースの **[実験]** ノードを右クリックし、 **[実験の作成]** を選択します。
1. プロンプトで、実験の名前を指定します。

### <a name="run-experiment"></a>実験を実行する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[実験]** ノードを展開します。
1. 実行する実験を右クリックします。
1. アクティビティ バーの **[実験の実行]** アイコンを選択します。
1. 実験をローカルで実行するか、リモートで実行するかを選択します。 実験をローカルで実行およびデバッグする詳細については、[デバッグ ガイド](how-to-debug-visual-studio-code.md)に関する項を参照してください。
1. サブスクリプションを選択します。
1. Azure ML ワークスペースを選択して、実験を実行します。
1. 実験を選択します。
1. 実験を実行するコンピューティングを選択または作成します。
1. 実験の実行構成を選択または作成します。

または、拡張機能の上部にある **[実験の実行]** ボタンを選択し、プロンプトで実験の実行を構成することもできます。

### <a name="view-experiment"></a>実験を表示する

Azure Machine Learning Studio で実験を表示するには、以下の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[実験]** ノードを展開します。
1. 表示する実験を右クリックし、 **[View Experiment] (実験の表示)** を選択します。 
1. Azure Machine Learning Studio で実験の URL を開くよう求めるプロンプトが表示されます。 **[Open (開く)]** を選択します。

### <a name="track-run-progress"></a>実行の進行状況を追跡する

実験を実行している間、進行状況を確認することができます。 拡張機能から Azure Machine Learning Studio での実行の進行状況を追跡するには、以下の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[実験]** ノードを展開します。
1. 進行状況を追跡する実験ノードを展開します。
1. 実行を右クリックし、 **[View Run in Azure portal] (Azure portal で実行を表示する)** を選択します。
1. Azure Machine Learning Studio で実行の URL を開くよう求めるプロンプトが表示されます。 **[Open (開く)]** を選択します。

### <a name="download-run-logs--outputs"></a>実行ログと出力をダウンロードする

実行が完了したら、ログや実験の実行の一部として生成されたモデルなどのアセットをダウンロードすることができます。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[実験]** ノードを展開します。
1. 進行状況を追跡する実験ノードを展開します。
1. 実行を右クリックします。
    - 出力をダウンロードするには、 **[出力のダウンロード]** を選択します。
    - ログをダウンロードするには、 **[ログのダウンロード]** を選択します。

### <a name="view-run-metadata"></a>実行メタデータを表示する

拡張機能では、実行に使用される実行構成や実行の詳細などのメタデータを検査できます。

## <a name="compute-instances"></a>コンピューティング インスタンス

詳細については、「[コンピューティング インスタンス](concept-compute-instance.md)」を参照してください。

### <a name="create-compute-instance"></a>コンピューティング インスタンスの作成

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. コンピューティング インスタンスを作成するワークスペース ノードを展開します。
1. **[Compute instances]\(コンピューティング インスタンス\)** ノードを右クリックし、 **[Create compute instance]\(コンピューティング インスタンスの作成\)** を選択します。
1. 次のように入力します。
    1. コンピューティング インスタンスの名前を指定します。
    1. 一覧から VM のサイズを選択します。
    1. SSH アクセスを有効にするかどうかを選択します。
        1. SSH アクセスを有効にする場合は、SSH 公開キーまたはキーを含むファイルも指定する必要があります。 詳細については、[Azure での SSH キーの作成と使用のガイド](../virtual-machines/linux/mac-create-ssh-keys.md)に関するページを参照してください。

### <a name="stop-or-restart-compute-instance"></a>コンピューティング インスタンスの停止または再起動

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[Compute instances]\(コンピューティング インスタンス\)** ノードを展開します。
1. 停止または再起動するコンピューティング インスタンスを右クリックし、 **[Stop Compute instance]\(コンピューティング インスタンスの停止\)** または **[Restart compute instance]\(コンピューティング インスタンスの再起動\)** を選択します。

### <a name="view-compute-instance-configuration"></a>コンピューティング インスタンス構成の表示

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[Compute instances]\(コンピューティング インスタンス\)** ノードを展開します。
1. 検査するコンピューティング インスタンスを右クリックし、 **[View Compute instance Properties]\(コンピューティング インスタンスのプロパティを表示\)** を選択します。

### <a name="delete-compute-instance"></a>コンピューティング インスタンスの削除

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[Compute instances]\(コンピューティング インスタンス\)** ノードを展開します。
1. 削除するコンピューティング インスタンスを右クリックして、 **[Delete compute instance]\(コンピューティング インスタンスの削除\)** を選択します。

## <a name="compute-clusters"></a>コンピューティング クラスター

この拡張機能は、次のコンピューティングの種類に対応しています。

- Azure Machine Learning コンピューティング クラスター
- Azure Kubernetes Service

詳細については、[コンピューティング ターゲット](concept-compute-target.md#train)に関するページを参照してください。

### <a name="create-compute"></a>コンピューティングを作成する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. コンピューティング クラスターを作成するワークスペース ノードを展開します。
1. **[コンピューティング クラスター]** ノードを右クリックし、 **[Create Compute] (コンピューティングの作成)** を選択します。
1. 次のように入力します。
    1. コンピューティングの種類を選択します
    1. VM のサイズを選択します。 [VM サイズ](../virtual-machines/sizes.md)に関する詳細を表示します。
    1. コンピューティングの名前を指定します。

### <a name="view-compute-configuration"></a>コンピューティング構成の表示

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[コンピューティング クラスター]** ノードを展開します。
1. 表示するコンピューティングを右クリックし、 **[View Compute Properties] (コンピューティングのプロパティの表示)** を選択します。

### <a name="edit-compute-scale-settings"></a>コンピューティングのスケール設定を編集する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[コンピューティング クラスター]** ノードを展開します。
1. 編集するコンピューティングを右クリックし、 **[Edit Compute] (コンピューティングの編集)** を選択します。
1. エディターでコンピューティングの構成ファイルが開きます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="delete-compute"></a>コンピューティングを削除する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[コンピューティング クラスター]** ノードを展開します。
1. 削除するコンピューティングを右クリックして、 **[Delete Compute] (コンピューティングの削除)** を選択します。

### <a name="create-run-configuration"></a>実行構成の作成

拡張機能で実行構成を作成するには、次の操作を行います。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[コンピューティング クラスター]** ノードを展開します。
1. 実行構成を作成する対象のコンピューティング ターゲットを右クリックし、 **[実行構成の作成]** を選択します。
1. 次のように入力します。
    1. コンピューティング ターゲットの名前を指定します
    1. 新しい環境を選択または作成します。
    1. 実行するスクリプトの名前を入力するか、**Enter** キーを押してローカル コンピューターのスクリプトをブラウズします。
    1. (省略可能) トレーニング実行用のデータ参照を作成するかどうかを選択します。 この操作を行うと、実行構成でデータセットを定義するように求められます。
        1. 登録されているデータセットの 1 つを選択して実行構成にリンクします。エディターでデータセットの構成ファイルが開きます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。
    1. 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="edit-run-configuration"></a>実行構成を編集する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペースの **[コンピューティング クラスター]** ノードで、コンピューティング クラスター ノードを展開します。
1. 編集する実行構成を右クリックし、 **[Edit Run Configuration] (実行構成の編集)** を選択します。
1. エディターで実行構成の構成ファイルが開きます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="delete-run-configuration"></a>実行構成を削除する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. **[コンピューティング クラスター]** ノード内で、目的のコンピューティング クラスター ノードを展開します。
1. 編集する実行構成を右クリックし、 **[Delete Run Configuration] (実行構成の削除)** を選択します。

## <a name="models"></a>モデル

詳細については、[モデル](concept-azure-machine-learning-architecture.md#models)に関するページを参照してください

### <a name="register-model"></a>モデルの登録

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. **[モデル]** ノードを右クリックし、 **[モデルの登録]** を選択します。
1. 次のように入力します。
    1. モデルの名前を指定します
    1. モデルがファイルとフォルダーのどちらであるかを選択します。
    1. ローカル PC でモデルを検索します。
    1. エディターでモデルの構成ファイルが開きます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

### <a name="view-model-properties"></a>モデルのプロパティを表示する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[モデル]** ノードを展開します。
1. プロパティを表示するモデルを右クリックし、 **[View Model Properties] (モデルのプロパティの表示)** を選択します。 エディターでモデルのプロパティが含まれているファイルが開きます。

### <a name="download-model"></a>モデルをダウンロードする

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[モデル]** ノードを展開します。
1. ダウンロードするモデルを右クリックし、 **[Download Model File] (モデル ファイルのダウンロード)** を選択します。

### <a name="delete-a-model"></a>モデルを削除する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[モデル]** ノードを展開します。
1. 削除するモデルを右クリックして、 **[Remove Model] (モデルの削除)** を選択します。

## <a name="endpoints"></a>エンドポイント

VS Code 拡張機能は、次のデプロイ ターゲットをサポートします。

- Azure Container Instances
- Azure Kubernetes Service

詳細については、[Web サービス エンドポイント](concept-azure-machine-learning-architecture.md#web-service-endpoint)に関するページを参照してください。

### <a name="create-deployments"></a>デプロイを作成する

> [!NOTE]
> 現在、デプロイの作成は Conda 環境でのみ機能します。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース ノードを展開します。
1. **[エンドポイント]** ノードを右クリックし、 **[Deploy Service] (サービスのデプロイ)** を選択します。
1. 次のように入力します。
    1. 既に登録されているモデルまたはローカル モデル ファイルを使用するかどうかを選択します。
    1. モデルを選択します
    1. モデルのデプロイ先となるデプロイ ターゲットを選択します。
    1. モデルの名前を指定します。
    1. モデルのスコア付けの際に実行するスクリプトを指定します。
    1. Conda の依存関係ファイルを指定します。
    1. デプロイの構成ファイルがエディターに表示されます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。

> [!NOTE]
> または、 *[モデル]* ノードで登録済みモデルを右クリックし、 **[Deploy Service From Registered Model] (登録済みモデルからサービスをデプロイする)** を選択します。

### <a name="delete-deployments"></a>デプロイを削除する

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[エンドポイント]** ノードを展開します。
1. 削除するデプロイを右クリックし、 **[サービスの削除]** を選択します。
1. サービスを削除するかどうかを確認するメッセージが表示されます。 **[OK]** を選択します。

### <a name="manage-deployments"></a>デプロイを管理する

デプロイの作成と削除に加えて、デプロイに関連付けられている設定を表示および編集することもできます。

1. ワークスペースが含まれているサブスクリプション ノードを展開します。
1. ワークスペース内の **[エンドポイント]** ノードを展開します。
1. 管理するデプロイを右クリックします。
    - 設定を編集するには、 **[サービスの編集]** を選択します。
        - デプロイの構成ファイルがエディターに表示されます。 構成に問題がなければ、 **[保存して続行]** を選択するか、VS Code コマンドパレット ( **[表示] > [コマンド パレット]** ) を開いて **Azure ML:Save and Continue** と入力します。
    - デプロイの構成設定を表示するには、 **[View service properties] (サービスのプロパティを表示)** を選択します。

## <a name="next-steps"></a>次のステップ

VS Code 拡張機能で[イメージ分類モデルをトレーニングする](tutorial-train-deploy-image-classification-model-vscode.md)