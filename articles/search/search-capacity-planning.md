---
title: クエリおよびインデックスのワークロードの容量を見積もる
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のパーティションとレプリカのコンピューティング リソースを調整します。各リソースは課金対象の検索単位で価格設定されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.openlocfilehash: d848c1ed1ab9d4cb24dec9423d93ec62ab45633b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99537223"
---
# <a name="estimate-and-manage-capacity-of-an-azure-cognitive-search-service"></a>Azure Cognitive Search サービスの容量の見積もりと管理を行う

[検索サービスをプロビジョニング](search-create-service-portal.md)し、価格レベルを確定する前に、容量のしくみと、ワークロードの変動に対応するためのレプリカとパーティションの調整方法について理解しておいてください。

容量は[サービス レベル](search-sku-tier.md)の関数であり、サービスまたはパーティションあたりの最大ストレージ容量、作成できるオブジェクトの数の上限を確立します。 Basic レベルは、ストレージ要件が小さい (1 つのパーティションのみ) が、高可用性構成 (3 つのレプリカ) で実行できるアプリ向けに設計されています。 その他のレベルは、マルチテナント機能などの特定のワークロードまたはパターン向けに設計されています。 内部的には、これらのレベルで作成されたサービスは、こうしたシナリオを支援するハードウェアの影響を受けます。

Azure Cognitive Search のスケーラビリティ アーキテクチャは、レプリカとパーティションの柔軟な組み合わせに基づいているため、クエリやインデックス作成を行う能力を強化する必要があるかどうかに応じて容量を変えることができます。 サービスが作成された後も、レプリカまたはパーティションの数は個別に増減できます。 コストは、物理リソースを追加するごとに増加しますが、大規模なワークロードが完了したら、スケールを縮小して請求金額を下げることができます。 調整のレベルとサイズによっては、容量の追加または削減に15分から数時間かかることがあります。

レプリカやパーティションの割り当てを変更する場合は、Azure Portal の使用をお勧めします。 ポータルでは、階層の上限に達しないようにするための許容される組み合わせに強制的に制限されます。 ただし、スクリプト ベースまたはコード ベースのプロビジョニング方法が必要な場合は、代わりに [Azure PowerShell](search-manage-powershell.md) または[管理 REST API](/rest/api/searchmanagement/services) を使用します。

## <a name="concepts-search-units-replicas-partitions-shards"></a>概念: 検索単位、レプリカ、パーティション、シャード

容量は、*パーティション* と *レプリカ* の組み合わせで割り当てられる *検索単位* で表され、基になる *シャーディング* メカニズムを使用して柔軟な構成をサポートします。

| 概念  | 定義|
|----------|-----------|
|*検索単位* | 使用可能な合計容量の単一増分 (36 ユニット)。 Azure Cognitive Search サービスの課金単位でもあります。 サービスを実行するには、少なくとも 1 つの単位が必要です。|
|*[レプリカ]* | 主にクエリ操作の負荷分散に使用される Search サービスのインスタンスです。 各レプリカは、インデックスの 1 つのコピーをホストします。 3 つのレプリカを割り当てると、クエリ要求のサービスに使用できるインデックスのコピーが3つ作成されます。|
|*パーティション* | 読み取り/書き込み操作 (たとえば、インデックスを再構築または更新する場合など) のための物理ストレージと I/O。 各パーティションにインデックス全体のスライスがあります。 3 つのパーティションを割り当てると、インデックスは 3 つに分割されます。 |
|*シャード* | インデックスのチャンク。 Azure Cognitive Search は、各インデックスをシャードに分割し、(シャードを新しい検索単位に移動することによって) パーティションを追加するプロセスを高速化しています。|

次の図は、レプリカ、パーティション、シャード、および検索単位間の関係を示しています。 ここでは、2 つのレプリカと 2 つのパーティションを持つサービスで、どのように 1 つのインデックスが 4 つの検索単位にわたっているかを例で示しています。 4 つの検索単位それぞれには、インデックスのシャードの半分だけが格納されます。 左側の列の検索単位は、シャードの前半を格納して最初のパーティションを構成し、右側の列の検索単位は、シャードの後半を格納して 2 番目のパーティションを構成しています。 レプリカが 2 つあるため、各インデックス シャードのコピーは 2 つあります。 上部の行の検索単位は、1 つのコピーを格納して最初のレプリカを構成し、下部の行の検索単位は、別のコピーを格納して 2 番目のレプリカを構成しています。

:::image type="content" source="media/search-capacity-planning/shards.png" alt-text="検索インデックスはパーティション間でシャード化されます。":::

上の図は 1 つの例にすぎません。 パーティションとレプリカはさまざまに組み合わせることができ、最大で合計 36 の検索単位が可能です。

Cognitive Search では、シャード管理は実装の詳細であり、構成できませんが、インデックスがシャード化されているとわかっていれば、順位付けやオートコンプリートの動作で不定期に発生する異常を理解する場合に役立ちます。

+ 順位付けの異常:検索スコアは最初にシャード レベルで計算され、続いて単位の結果セットに集計されます。 シャード コンテンツの特性に応じて、あるシャードからの一致が別のシャードの一致よりも高い順位になる場合があります。 検索結果の順位付けが直観に反していることに気付いた場合は、シャーディングの影響が原因である可能性が最も高いです (特にインデックスが小さい場合)。 [インデックス全体でグローバルにスコアを計算する](index-similarity-and-scoring.md#scoring-statistics-and-sticky-sessions)ように選択すれば、これらの順位付けの異常を回避できますが、その場合、パフォーマンスが低下します。

+ オートコンプリートの異常:オートコンプリート クエリでは、部分的に入力された語句の最初のいくつかの文字で照合が行われますが、スペルの少しの間違いを許容するあいまいパラメーターが使用されます。 オートコンプリートの場合、あいまい一致は現在のシャード内の用語に限定されます。 たとえば、シャードに "Microsoft" が含まれており、"micor" という部分的な語句が入力された場合、検索エンジンはそのシャード内の "Microsoft" と一致しますが、インデックスの残りの部分を保持した他のシャードでは一致しません。

## <a name="how-to-evaluate-capacity-requirements"></a>容量の要件を評価する方法

容量とサービスの実行コストは密接に関係しています。 サービス レベルでは、ストレージとコンテンツ (サービスのインデックスの数など) の 2 つのレベルで制限が課されます。 先に上限に達した方が実質的な制限になるため、両方を考慮することが重要です。

インデックスとその他のオブジェクトの数は、通常、ビジネスおよびエンジニアリングの要件によって決まります。 たとえば、アクティブな開発、テスト、および運用において、同じインデックスの複数のバージョンを保持する場合があります。

ストレージのニーズは、構築する予定のインデックスのサイズによって決まります。 見積もりに役立つような、信頼できる経験則や一般原則はありません。 インデックスのサイズを見極める唯一の方法は、[1 つ構築する](search-what-is-an-index.md)ことです。 そのサイズは、インポートされたデータ、テキスト分析、およびインデックス構成 (suggester、フィルタリング、並べ替えを有効にするかどうかなど) に基づきます。

全文検索の場合、主要データ構造は[転置インデックス](https://en.wikipedia.org/wiki/Inverted_index)構造であり、ソース データとは異なる特性があります。 転置インデックスのサイズと複雑性はコンテンツによって決まり、必ずしもそれにフィードするデータ量によって決まるものではありません。 冗長性の高い大規模なデータ ソースは、変動の多いコンテンツを含む小さいデータセットよりも、インデックスのサイズが小さくなることがあります。 そのため、元のデータ セットのサイズに基づいてインデックスのサイズを推測できることはほとんどありません。

> [!NOTE] 
> インデックスとストレージの将来のニーズの見積もりは、当て推量のように感じられるかもしれませんが、行う価値があります。 あるレベルの容量が少なすぎることがわかった場合は、それより上のレベルで新しいサービスをプロビジョニングしたうえで、[インデックスを再読み込み](search-howto-reindex.md)する必要があります。 特定のレベルから別のレベルへのサービスのインプレース アップグレードを実行することはできません。
>

### <a name="estimate-with-the-free-tier"></a>Free レベルでの見積もり

容量を見積もる方法の 1 つは、まず Free レベルを使用することです。 Free サービスでは、最大 3 つのインデックス、50 MB のストレージ、および 2 分間のインデックス作成時間が提供されます。 これらの制約の中で予想インデックス サイズを見積もることは簡単ではない可能性がありますが、その手順は次のとおりです。

+ [Free サービスを作成](search-create-service-portal.md)します。
+ 小さな代表的なデータセットを準備します。
+ インデックスを作成し、データを読み込みます。 インデクサーでサポートされている Azure データ ソース内でデータセットをホストできる場合は、[ポータルにあるデータのインポート ウィザード](search-get-started-portal.md)を使用して、インデックスの作成と読み込みの両方を行うことができます。 それ以外の場合は、REST と [Postman](search-get-started-rest.md) を使用するか、[Visual Studio Code](search-get-started-vs-code.md) を使用してインデックスを作成し、データをプッシュする必要があります。 プッシュ モデルでは、データを JSON ドキュメントの形式にする必要があります。そのドキュメント内のフィールドは、インデックス内のフィールドに対応します。
+ インデックスに関する情報 (サイズなど) を収集します。 機能と属性はストレージに影響を与えます。 たとえば、suggester (Search-as-you-type クエリ) を追加すると、ストレージ要件が増加します。 

  同じデータ セットを使用する場合、各フィールドに異なる属性を設定してインデックスの複数のバージョンを作成し、ストレージ要件がどのように変化するかを確認してみてください。 詳細については、[基本的なインデックスの作成に関するページの「ストレージへの影響」](search-what-is-an-index.md#index-size)を参照してください。

大まかな見積もりが得られたら、2 つのインデックス (開発用と運用用) の量を予測するためにこの値を 2 倍にし、それに応じてレベルを選択します。

### <a name="estimate-with-a-billable-tier"></a>課金対象レベルでの見積もり

専用のリソースでは、より長いサンプリングと処理時間に対応でき、開発段階でのインデックスの量、サイズ、クエリ量についてより現実的な見積もりを求めることができます。 課金対象のレベルから始め、開発プロジェクトが成熟するのに応じて再評価するお客様もいます。

1. [各レベルのサービス制限を確認](./search-limits-quotas-capacity.md#index-limits)して、より低いレベルで、必要なインデックス数に対応できるかどうかを判断してください。 Basic、S1、S2 レベルでのインデックス制限は、それぞれ 15、50、200 です。 Storage Optimized レベルは、少数の非常に大きいインデックスをサポートするように設計されているため、10 インデックスに制限されています。

1. [課金対象レベルでサービスを作成します](search-create-service-portal.md)。

    + 予想される負荷がわからない場合は、Basic または S1 の低いレベルで始めます。
    + テストに大規模なインデックス作成とクエリの負荷が含まれている場合は、S2 または S3 の高レベルから始めます。
    + 社内のビジネス アプリケーションのように、インデックスを付けるデータの量が多く、クエリの負荷が比較的低い場合は、Storage Optimized (L1 または L2) から始めます。

1. [最初のインデックスを構築](search-what-is-an-index.md)して、ソース データがどのようにインデックスに変換されるかを特定します。 これは、インデックスのサイズを推測する唯一の方法です。

1. ポータルで、[ストレージ、サービス制限、クエリ量、および待機時間を監視](search-monitor-usage.md)します。 秒あたりのクエリ数、調整されたクエリ数、および検索の待ち時間がポータルに表示されます。 これらすべての値が、適切なレベルを選択したかどうかを判断するために役立ちます。

1. 高可用性が必要な場合や、クエリ パフォーマンスの低下が発生する場合は、レプリカを追加します。

   クエリの負荷に対応するために必要なレプリカの数に関するガイドラインはありません。 クエリのパフォーマンスは、クエリおよび競合するワークロードの複雑さによって異なります。 レプリカを追加するとパフォーマンスが向上しますが、厳密に線形に向上するわけではありません。3 つのレプリカを追加しても、スループットが 3 倍になるとは限りません。 ソリューションの QPS を推定する方法のガイダンスについては、「[パフォーマンスのためのスケール](search-performance-optimization.md)」と、[クエリの監視](search-monitor-queries.md)に関する記事を参照してください。

> [!NOTE]
> 検索されることのないデータが含まれていると、ストレージの要件が増大することがあります。 ドキュメントには、検索機能に必要なデータのみが含まれていることが理想的な状態です。 バイナリ データは検索できないため、別途保存する必要があります (おそらく Azure テーブルまたは BLOB ストレージに)。 その後、外部データへの URL 参照を保持するためのフィールドをインデックスに追加する必要があります。 個々の検索ドキュメントの最大サイズは 16 MB です (1 回の要求で複数のドキュメントを一括アップロードする場合は、それより小さくなります)。 詳細については、[Azure Cognitive Search サービスの制限](search-limits-quotas-capacity.md)に関する記事を参照してください。
>

**クエリ数に関する考慮事項**

クエリ/秒 (QPS) は、パフォーマンスのチューニング中の重要なメトリックですが、一般に、最初からクエリ数が多いことが予想される場合は、レベルに関する考慮事項に過ぎません。

Standard レベルでは、レプリカとパーティションのバランスを取ることができます。 負荷分散用のレプリカを追加したり、並列処理用のパーティションを追加したりすることで、クエリのターンアラウンドを向上させることができます。 その後、サービスがプロビジョニングされた後に、パフォーマンスをチューニングできます。

最初から大量のクエリ数が継続することが予想される場合は、より強力なハードウェアを使用する、より高い Standard レベルを検討してください。 その後、これらのクエリ数が発生しなかった場合は、パーティションとレプリカをオフラインにするか、より低いレベルのサービスに切り替えることができます。 クエリ スループットの計算方法の詳細については、[Azure Cognitive Search のパフォーマンスと最適化](search-performance-optimization.md)に関する記事を参照してください。

Storage Optimized レベルは、大規模なデータ ワークロードに適しており、クエリの待ち時間要件がそれほど重要でない場合に、より全体的に利用可能なインデックス ストレージをサポートします。 それでも、負荷分散のためにはレプリカを追加し、並列処理のためにはパーティションを追加する必要があります。 その後、サービスがプロビジョニングされた後に、パフォーマンスをチューニングできます。

**サービスレベル アグリーメント**

Free レベルおよびプレビュー機能には、[サービスレベル アグリーメント (SLA)](https://azure.microsoft.com/support/legal/sla/search/v1_0/) がありません。 課金対象のすべてのレベルで、SLA が有効になるのは、サービスにとって十分な冗長性がプロビジョニングされるときです。 クエリ (読み取り) の SLA には複数のレプリカが必要です。 クエリとインデックス作成 (読み取り/書き込み) の SLA には 3 つ以上のレプリカが必要です。 パーティションの数は、SLA に影響しません。

## <a name="tips-for-capacity-planning"></a>容量計画に関するヒント

+ クエリに関するメトリックを構築し、利用パターン (業務時間中のクエリ数、オフピーク時のインデックス作成) に関するデータを収集します。 このデータを使用して、サービス プロビジョニングに関する決定に役立つ情報を提供します。 時間ごと、または毎日の周期では実用的でありませんが、パーティションやリソースを動的に調節して、クエリ数の計画された変化に対応できます。 計画外ではあっても持続した変化に対応することもできます。ただし、対処する間、そのレベルが保持される場合です。

+ 過小なプロビジョニングの唯一の欠点として、実際の要件が予測より大きかった場合にサービスを中断する必要があるということに注意してください。 サービスの中断を回避するには、より高いレベルで新しいサービスを作成し、すべてのアプリと要求が新しいエンドポイントを指すようになるまで並行して実行します。

## <a name="when-to-add-partitions-and-replicas"></a>パーティションとレプリカを追加する場合

最初に、1 つのパーティションと 1 つのレプリカで構成される最小限のリソースがサービスに割り当てられます。

1 つのサービスに、すべてのワークロード (インデックスおよびクエリ) を処理するための十分なリソースが必要です。 どちらのワークロードもバックグラウンドで実行されません。 クエリ要求の頻度が低い時間帯にインデックスを作成するようにスケジュールできますが、それ以外の時間帯では、サービスはあるタスクを別のタスクより優先させません。 さらに、ある程度の冗長性により、サービスまたはノードが内部的に更新されるときのクエリのパフォーマンスの問題を解決します。

一般的な規則として、検索アプリケーションでは、パーティションよりもレプリカの方が多く必要となる傾向があります。特に、サービス操作でクエリ ワークロードの比重が高い場合は、その傾向が強まります。 その理由については、[高可用性](#HA)に関するセクションで説明します。

[選択するレベル](search-sku-tier.md)によってパーティションのサイズと速度が決まり、各レベルはさまざまなシナリオに適した一連の特性に対して最適化されます。 ハイエンド レベルを選択した場合は、S1 を使用する場合よりもパーティション数を減らす必要がある場合があります。 自己指示型のテストを通じて回答する必要がある質問の 1 つは、より大きなパーティションの方が、低いレベルでプロビジョニングされたサービス上の 2 つの安価なパーティションよりもパフォーマンスが優れているかどうかということです。

ほぼリアルタイムのデータ更新を要求する検索アプリケーションでは、レプリカよりも多くのパーティションが比例して必要になります。 パーティションを追加すると、読み取り/書き込み操作がより多くのコンピューティング リソースに分散されます。 また、追加のインデックスとドキュメントを格納するためのディスク領域も増加します。

インデックスが大きくなると、クエリの実行に時間がかかります。 そのため、パーティションで段階的な増加が発生するたびに、レプリカでは小規模であるが比例的な増加が必要となる場合があります。 クエリの複雑さとボリュームは、クエリの実行が完了するまでの速度の要因となります。

> [!NOTE]
> レプリカやパーティションを追加すると、サービスの実行コストが増加し、結果の並べ替え方法が少し変化する可能性があります。 ノードを追加した場合の課金の影響を理解するには、[料金計算ツール](https://azure.microsoft.com/pricing/calculator/) を必ず確認してください。 [下のグラフ](#chart)は、特定の構成に必要な検索単位の数を相互参照するのに役立ちます。 レプリカの追加によるクエリ処理への影響について詳しくは、「[結果の並べ替え](search-pagination-page-layout.md#ordering-results)」をご覧ください。

## <a name="how-to-allocate-replicas-and-partitions"></a>レプリカとパーティションを割り当てる方法

1. [Azure Portal](https://portal.azure.com/) にサインインし、Search サービスを選択します。

1. **[設定]** で、 **[スケール]** ページを開いてレプリカとパーティションの数を変更します。 

   次のスクリーンショットは、1 つのレプリカとパーティションでプロビジョニングされる Basic Standard を示しています。 下部の式は、使用される検索ユニットの数 (1) を示しています。 ユニットの価格が 100 ドル (実際の価格ではありません) の場合、このサービスを実行するための毎月のコストは平均 100 ドルになります。

   :::image type="content" source="media/search-capacity-planning/1-initial-values.png" alt-text="現在の値が表示されている [スケール] ページ" border="true":::

1. スライダーを使ってパーティションの数を増減します。 下部の式は、使用される検索ユニットの数を示しています。 **[保存]** を選択します。

   この例では、2 つ目のレプリカおよびパーティションを追加します。 請求の計算式では、レプリカ数とパーティション数が乗算されるため (2 x 2)、検索ユニット数が 4 になっていることに注意してください。 容量を 2 倍にすると、サービスを実行するためのコストが 2 倍以上になります。 検索ユニットのコストが 100 ドルの場合、新しい毎月の請求額は 400 ドルになります。

   各レベルの現在のユニットあたりのコストについては、[価格のページ](https://azure.microsoft.com/pricing/details/search/)をご覧ください。

   :::image type="content" source="media/search-capacity-planning/2-add-2-each.png" alt-text="レプリカとパーティションの追加" border="true":::

1. 保存した後、通知を確認して、操作が成功したことを確認できます。

   :::image type="content" source="media/search-capacity-planning/3-save-confirm.png" alt-text="変更を保存" border="true":::

   容量の変更が完了するまでに数時間かかることがあります。 プロセスが開始されたらキャンセルすることはできません。また、レプリカとパーティションの調整のリアルタイムの監視はありません。 ただし、変更が行われている間、次のメッセージが常に表示されています。

   :::image type="content" source="media/search-capacity-planning/4-updating.png" alt-text="ポータルのステータス メッセージ" border="true":::

> [!NOTE]
> サービスをプロビジョニングした後は、上位のレベルにアップグレードすることはできません。 新しいレベルで Search Service を作成し、インデックスを再度読み込む必要があります。 サービス プロビジョニングについては、[ポータルで Azure Cognitive Search サービスの作成](search-create-service-portal.md)に関する記事を参照してください。
>
> さらに、パーティションとレプリカは、サービスによって排他的に管理されます。 プロセッサの関係や、ワークロードを特定のノードに割り当てる概念はありません。
>

<a id="chart"></a>

## <a name="partition-and-replica-combinations"></a>パーティションとレプリカの組み合わせ

Basic サービスは、3 SU の上限に対して厳密に 1 個のパーティションと最大 3 個のレプリカを持つことができます。 調整可能なリソースはレプリカだけです。 クエリの高可用性のためには、少なくとも 2 個のレプリカが必要です。

すべての Standard および Storage Optimized 検索サービスでは、これらのレベルで許可されている 36 SU の制限のもとで、レプリカとパーティションの次の組み合わせを想定できます。 

|   | **1 個のパーティション** | **2 個のパーティション** | **3 個のパーティション** | **4 個のパーティション** | **6 個のパーティション** | **12 個のパーティション** |
| --- | --- | --- | --- | --- | --- | --- |
| **1 つのレプリカ** |1 SU |2 SU |3 SU |4 SU |6 SU |12 SU |
| **2 つのレプリカ** |2 SU |4 SU |6 SU |8 SU |12 SU |24 SU |
| **3 つのレプリカ** |3 SU |6 SU |9 SU |12 SU |18 SU |36 SU |
| **4 つのレプリカ** |4 SU |8 SU |12 SU |16 SU |24 SU |該当なし |
| **5 つのレプリカ** |5 SU |10 SU |15 SU |20 SU |30 SU |該当なし |
| **6 つのレプリカ** |6 SU |12 SU |18 SU |24 SU |36 SU |該当なし |
| **12 レプリカ** |12 SU |24 SU |36 SU |該当なし |該当なし |該当なし |

SU、価格、および容量の詳細については、Azure Web サイトをご覧ください。 詳細については、「[料金の詳細](https://azure.microsoft.com/pricing/details/search/)」をご覧ください。

> [!NOTE]
> レプリカとパーティションの数は、均等に 12 分割されます (具体的には、1、2、3、4、6、12)。 これは、Azure Cognitive Search では各インデックスがすべてのパーティションに均等に分散されるように 12 のシャードに事前に分割されるためです。 たとえば、サービスに 3 つのパーティションがあり、インデックスを作成する場合、各パーティションにはインデックスの 4 つのシャードを含めます。 Azure Cognitive Search でインデックスがどのようにシャードされるかは実装の問題であり、今後のリリースで変更される場合があります。 今日 12 個であっても、今後も必ず 12 個になるとは限りません。
>

<a id="HA"></a>

## <a name="high-availability"></a>高可用性

通常、簡単かつ比較的速くスケールアップできるため、1 つのパーティションと 1 ～ 2 つのレプリカで開始し、クエリ数の増加に合わせてスケールアップすることをお勧めします。 クエリのワークロードは主にレプリカ上で実行されます。 より高いスループットまたは高可用性のためには、レプリカの追加が必要となります。

高可用性のための一般的な推奨事項は次のとおりです。

+ 読み取り専用ワークロード (クエリ) の高可用性を実現するには 2 つのレプリカ

+ 読み取り/書き込みワークロード (クエリに加え、個々のドキュメントを追加、更新、または削除するときのインデックス作成) の高可用性を実現するには 3 つ以上のレプリカ

Azure Cognitive Search のサービス レベル アグリーメント (SLA) は、クエリ操作と、ドキュメントの追加、更新、削除から成るインデックスの更新とを対象としています。

Basic レベルでは、1 つのパーティションと 3 つのレプリカが上限です。 インデックス作成とクエリのスループットに対する需要の変動にすばやく反応する柔軟性が必要な場合は、Standard レベルのいずれかを検討してください。  ストレージ要件がクエリ スループットよりもはるかに速く増大している場合は、Storage Optimized レベルの 1 つを検討します。

## <a name="about-queries-per-second-qps"></a>1 秒あたりのクエリ数 (QPS) について

クエリのパフォーマンスには多くの要因があるため、Microsoft では予想される QPS 数を公開していません。 QPS の見積もりは、お使いのアプリケーションで有効なサービス レベル、構成、インデックス、クエリ構成を使用して、お客様ごとに個別に作成する必要があります。 インデックスのサイズと複雑さ、クエリのサイズと複雑さ、およびトラフィックの量が QPS を決定する主な要因です。 このような要因が不明な場合は、意義のある見積もりを用意する方法はありません。

見積もりは、専用リソース (Basic および Standard レベル) 上で実行されるサービスに基づいて計算される場合、予測可能性がより高くなります。 より多くのパラメーターを制御できるため、さらに厳密に QPS を見積もることができます。 見積もりを行う方法のガイダンスについては、[Azure Cognitive Search のパフォーマンスと最適化](search-performance-optimization.md)に関する記事を参照してください。

ストレージ最適化レベル (L1 および L2) では、Standard レベルよりもクエリ スループットが低く、待機時間が長くなります。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [コストの見積もりと管理を行う方法](search-sku-manage-costs.md)