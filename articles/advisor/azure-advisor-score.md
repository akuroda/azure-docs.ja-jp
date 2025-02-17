---
title: Advisor スコアを使用して Azure ワークロードを最適化する
description: Azure Advisor スコアを使用して Azure を最大限に活用します。
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: 11b20bc3b4d604d3a7ff4608cd1c21f129c1cb6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97630124"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Advisor スコアを使用して Azure ワークロードを最適化する

## <a name="introduction-to-advisor-score"></a>Advisor スコアの概要

Azure Advisor によって、お使いのワークロードに対してベスト プラクティスの推奨事項が提供されます。 これらの推奨事項は、次のことを支援するためにパーソナライズされ実行可能になっています。

* ワークロードの構成を改善し、Azure のデプロイを最適化します。
* ベスト プラクティスに従って、重大な問題を事前に防止します。
* [Microsoft Azure Well-Architected フレームワーク](/azure/architecture/framework/)の 5 つの柱に対して Azure ワークロードを評価します。

Advisor スコアは、Advisor の中核機能として、これらの目標の達成を効果的かつ効率的に支援します。

Azure を最大限に活用するには、ワークロードの最適化の過程においてどの地点にいるかを把握することが重要です。 どのサービスまたはリソースがよく消費され、どれがそうでないかを把握する必要があります。 また、推奨事項に基づいてアクションに優先順位を付け、成果を最大化する方法についても把握しておくことをお勧めします。

この最適化の過程での進行状況を追跡し、報告することも重要です。 Advisor スコアを使用すると、新しいゲーミフィケーション エクスペリエンスによってこれらすべてを簡単に実行できます。

パーソナライズされたクラウド コンサルタントとして、Azure Advisor は使用状況のテレメトリとリソースの構成を継続的に評価し、業界のベスト プラクティスを確認します。 その後、Advisor はその結果を 1 つのスコアに集計します。 このスコアを使用すると、信頼性が高く、安全で、コスト効率の高いソリューションを構築するために必要な手順を自分が実行しているかどうかを、一目で判断できます。

Advisor スコアは、5 つのカテゴリ スコアにさらに分類できる 1 つの総合スコアで構成されます。 Advisor の各カテゴリの 1 つのスコアは、Well-Architected フレームワークの 5 つの柱を表します。

毎日、毎週、および毎月の傾向を含む総合スコアとカテゴリ スコアを表示することで、進行状況を経時的に追跡できます。 また、目標の達成を支援するベンチマークを設定することもできます。

 ![Advisor スコア ページを示すスクリーンショット。](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Advisor スコアを解釈する

Advisor によって、総合的な Advisor スコアと Advisor カテゴリの内訳がパーセント単位で表示されます。 どのカテゴリでも 100% のスコアは、Advisor によって評価されたすべてのリソースが、Advisor に推奨されたベスト プラクティスに沿っていることを意味します。 範囲の対極にある 0% のスコアは、Advisor によって評価されたリソースのいずれも Advisor の推奨に沿っていないことを意味します。 これらのスコア粒度を使用して、次のフローを簡単に実現できます。

* **Advisor スコア** を使用して、Advisor スコアに基づいて、ワークロードまたはサブスクリプションがどのように実行されているかのベースラインを設定できます。 また、どのような傾向にあるかを把握するために、傾向の履歴を確認することもできます。
* 各推奨事項の **カテゴリ別スコア** は、未処理の推奨事項の内、スコアを最も向上させるものを示します。 これらの値には、推奨事項の重みと、予測される実施の容易さの両方が反映されています。 これらの係数を使用すると、かけた時間に対して最大限の価値を確実に得ることができます。 また、これらは優先順位付けにも役立ちます。
* 各推奨事項の **カテゴリ スコアの影響** は、カテゴリごとに修復のアクションに優先度を付けるうえで役立ちます。

各推奨事項によるカテゴリ スコアへの貢献度は、Azure portal の **[Advisor スコア]** ページで明確に示されます。 各カテゴリ スコアは、 **[スコア上昇の可能性]** 列に表示されている割合のポイントで増やすことができます。 この値には、最も容易な可能性があるタスクに対処するために、そのカテゴリ内におけるその推奨事項の重みと、予測される実施の容易さの両方が反映されています。 スコアへの影響力が最も大きい推奨事項に焦点を当てることで、時間単位での効果を最大化することができます。

![Advisor スコアの影響を示すスクリーンショット。](./media/advisor-score-2.png)

Advisor の推奨事項が個々のリソースに関連していない場合は、これらの推奨事項を延期または無視することができます。 これらは、次の更新でスコアの計算から除外されます。 また、Advisor ではこの入力を追加のフィードバックとして使用して、モデルの改善が行われます。

## <a name="how-is-an-advisor-score-calculated"></a>Advisor スコアの計算方法

Advisor には、カテゴリ スコアと総合的な Advisor スコアがパーセント単位で表示されます。 どのカテゴリでも 100% のスコアは、すべてのリソースが "*Advisor によって評価され"* 、Advisor に推奨されたベスト プラクティスに沿っていることを意味します。 範囲の対極にある 0% のスコアは、Advisor によって評価されたリソースのいずれも Advisor の推奨に沿っていないことを意味します。

**5 つの各カテゴリ上で、スコア 100 が可能性として最も高くなります。** 総合的な Advisor スコアは、適用可能な各カテゴリ スコアの合計を、適用可能な全カテゴリから設定できる最高スコア値の合計で除算して計算されます。 つまり、ほとんどのサブスクリプションでは、Advisor によって各カテゴリのスコアが加算され、500 で除算されます。 ただし、"*各カテゴリ スコアは、Advisor によって評価されたリソースを使用する場合にのみ、計算されます*"。

### <a name="advisor-score-calculation-example"></a>Advisor スコアの計算例

* **1 つのサブスクリプションのスコア:** この例は、サブスクリプションのすべての Advisor カテゴリ スコアの単純な平均です。 Advisor カテゴリ スコアが、**コスト** = 73、**信頼性** = 85、**オペレーショナル エクセレンス** = 77、**パフォーマンス** = 100 である場合、Advisor スコアは、(73 + 85 + 77 + 100)/(4x100) = 0.84 つまり 84% になります。
* **複数のサブスクリプションのスコア:** 複数のサブスクリプションが選択されている場合、生成される総合的な Advisor スコアは加重集計されたカテゴリ スコアになります。 ここでは、各 Advisor カテゴリ スコアは、サブスクリプションで消費されるリソースに基づいて集計されます。 Advisor で加重集計されたカテゴリ スコアが得られたら、Advisor により単純平均計算が行われ、サブスクリプションの総合スコアが算出されます。

### <a name="scoring-methodology"></a>スコアリングの手法

Advisor スコアの計算は、次の 4 つのステップにまとめることができます。

1. Advisor によって、"*影響を受けるリソースのリテール コスト*" が計算されます。 これらのリソースは、Advisor に少なくとも 1 つの推奨事項があるサブスクリプション内のものです。
1. Advisor によって、"*評価されたリソースのリテール コスト*" が計算されます。 これらのリソースは、推奨事項があるかどうかにかかわらず、Advisor によって監視されるものです。
1. Advisor によって、推奨事項の種類ごとに "*正常なリソース比率*" が計算されます。 この比率は、影響を受けるリソースのリテール コストを、評価されたリソースのリテール コストで除算した値です。
1. Advisor によって、各カテゴリで正常なリソース比率に 3 つの追加の重み付けが適用されます。

   * より影響が大きい推奨事項は、影響の少ない推奨事項よりも重みが高くなります。
   * 長期にわたる推奨事項が含まれるリソースでは、スコアに対する影響が大きくなります。
   * Advisor 上で延期または無視したリソースは、スコアの計算から完全に除かれます。

Advisor は、このモデルを Advisor カテゴリ レベルで適用して、カテゴリごとに 1 つのアドバイザー スコアを付けます。 **セキュリティ** では、[セキュリティ スコア](../security-center/secure-score-security-controls.md#introduction-to-secure-score) モデルが使用されます。 単純平均により、最終的な Advisor スコアが生成されます。

## <a name="advisor-score-faqs"></a>Advisor スコアに関するよく寄せられる質問

### <a name="how-often-is-my-score-refreshed"></a>スコアはどの程度の頻度で更新されますか。

スコアは、少なくとも 1 日に 1 回は更新されます。

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>一部の推奨事項において、カテゴリ スコアの影響の列に空の値 ("-") があるのはなぜですか。

Advisor では、新しい推薦事項も、スコアリング モデル内の最近変更された推奨事項もすぐには含まれません。 短い評価期間 (通常は数週間) が経過すると、それらはスコアに含まれます。

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>予想される削減額が少ないにもかかわらず、一部の推薦事項のコスト スコアの影響がより大きいのはなぜですか。

**[コスト]** スコアには、使用率が低いリソースで見込まれる削減額と、予測されるそれらの推奨事項の実施の容易さの両方が反映されています。 例えば、予想される削減額が少なくても、影響を受けているリソースが長い間アイドル状態にある場合には、追加の重みが適用されます。

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>1 つ以上のカテゴリまたはサブスクリプションにスコアがないのはなぜですか。

Advisor では、Advisor によって評価されたリソースを含むカテゴリおよびサブスクリプションに対してのみスコアが生成されます。

### <a name="what-if-a-recommendation-isnt-relevant"></a>推奨事項に関連性がない場合は、どうすればよいですか。

Advisor からの推奨事項を無視すると、スコアの計算から除外されます。 また、推奨事項を無視すると、Advisor による推奨の質の向上に役立ちます。

### <a name="why-did-my-score-change"></a>スコアが変更されたのはなぜですか。

影響を受けたリソースを修復すると、Advisor によって推奨されているベスト プラクティスを採用したことで、スコアが変更される場合があります。 自分または自分のサブスクリプションに対するアクセス許可を持つユーザーが、リソースを変更または新規作成した場合に、スコアに変動が見られることもあります。 スコアは、すべてのリソースの総コストに対する、コストの影響を受けるリソースの比率に基づきます。

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Advisor によって、サブスクリプションにおけるリソースのリテール コストは、どのように計算されますか?

Advisor では、「[Azure の価格](https://azure.microsoft.com/pricing/)」で公開されている従量課金制の料金が使用されます。 これらの料金には、適用可能な割引は反映されません。 この料金には、その後、リソースが割り当てられた最後の日に使用量が乗算されます。 リソース コストの計算から割引を省略することで、割引が異なるサブスクリプション、テナント、および登録間で Advisor スコアを比較できるようになります。

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>スコアのポイントを得るためには、Advisor の推奨事項を確認する必要がありますか。

いいえ。 Advisor で推奨事項を確認せずに事前にベスト プラクティスを採用した場合でも、スコアには、Advisor によって推奨されているベスト プラクティスを採用しているかどうかが反映されます。

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>スコアリング手法では、運用環境と開発テストのワークロードの区別は行われますか。

いいえ。現時点では行われません。 ただし、開発とテストでリソースが使用されているが、推奨事項が当てはまらない場合は、それらの個々のリソースで推奨事項を無視できます。

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>100 個のリソースがあるサブスクリプションと 100,000 個のリソースがあるサブスクリプション間でスコアを比較することはできますか。

スコアリング手法は、サブスクリプションとサービスの組み合わせ上でリソースの数を制御するように設計されています。 リソースが少ないサブスクリプションは、より多くのリソースがあるサブスクリプションよりもスコアが高いか、低い可能性があります。

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>スコアの影響の列に "近日対応予定" と表示されているのはどういう意味ですか。

このメッセージは、推奨事項が新しいものであり、Advisor スコア モデルに取り込み作業中であることを意味します。 この新しい推奨事項がスコア計算で考慮されるようになると、推奨事項に対するスコアの影響値が表示されます。

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>スコアは、Azure に対する支出額に依存しますか。

いいえ。 スコアは、必ずしも支出額を反映するものではありません。 不必要な支出があると、**コスト** スコアが低くなります。

## <a name="access-advisor-score"></a>Advisor スコアへのアクセス

Advisor スコアは、Azure portal でパブリック プレビュー段階にあります。 左側のペインの **[Advisor]** セクションの下に **[Advisor スコア]** があります。

![Advisor スコアのエントリ ポイントを示すスクリーンショット。](./media/advisor-score-3.png)

## <a name="next-steps"></a>次のステップ

Advisor の推奨事項について詳しくは、以下を参照してください。

* [Advisor 入門](advisor-overview.md)
* [Advisor の使用を開始する](advisor-get-started.md)
* [Advisor のコストに関する推奨事項](advisor-cost-recommendations.md)
* [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
* [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
* [Advisor のオペレーショナル エクセレンスに関する推奨事項](advisor-operational-excellence-recommendations.md)
