---
title: 'Azure Front Door: DDoS 保護'
description: このページでは、Azure Front Door Standard/Premium が DDoS 攻撃からの保護にどのように役立つかについて説明します。
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 02/18/2021
ms.author: jodowns
ms.openlocfilehash: 9c67944717888439c0d6bd84e1615f51dd91fcac
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101098062"
---
# <a name="ddos-protection-on-azure-front-door-standardpremium-preview"></a>Azure Front Door Standard/Premium (プレビュー) の DDoS 保護

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door には、分散型サービス拒否 (DDoS) 攻撃を防ぐのに役立ついくつかの機能と特性があります。 これらの機能により、攻撃者がアプリケーションに到達したり、アプリケーションの可用性やパフォーマンスに影響を与えたりすることを防ぐことができます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="integration-with-azure-ddos-protection-basic"></a>Azure DDoS Protection Basic との統合

Front Door は、Azure DDoS Protection Basic によって保護されます。 この機能は、追加コストなしに、既定で Front Door プラットフォームに統合されています。 Front Door のグローバルに展開されるネットワークのフル スケールと容量が、常時有効なトラフィック監視とリアルタイムのリスク軽減によって、一般的なネットワーク層攻撃からの保護を提供します。 また、Basic DDoS の保護機能は、特に一般的で頻繁に発生するレイヤー 7 への DNS クエリ フラッド攻撃や、パブリック エンドポイントを標的とするレイヤー 3 および 4 への帯域幅消費型攻撃も防御します。 このサービスには、マイクロソフトのエンタープライズ サービスとコンシューマー サービスを大規模な攻撃から保護してきた実績があります。 詳細については、「[Azure DDoS Protection](../../security/fundamentals/ddos-best-practices.md)」を参照してください。

## <a name="protocol-blocking"></a>プロトコルのブロック

Front Door は、HTTP および HTTPS プロトコルのトラフィックのみを受け入れ、既知の `Host` ヘッダーを持つ有効な要求だけを処理します。 この動作は、さまざまなプロトコルやポート、DNS 増幅攻撃、TCP ポイズニング攻撃などの帯域幅消費型攻撃を含む一般的な DDoS 攻撃の種類を軽減するのに役立ちます。

## <a name="capacity-absorption"></a>容量の吸収

Front Door は、大規模にスケーリングされたグローバル分散サービスです。 マイクロソフトが所有する大規模なクラウド製品を含め、毎秒数十万の要求を受けている多くのお客様がいます。 Front Door は Azure のネットワークのエッジにあり、大量の攻撃を吸収して地理的に隔離します。 これにより、悪意のあるトラフィックが Azure ネットワークのエッジを超えることを防ぐことができます。

## <a name="caching"></a>キャッシュ

[Front Door のキャッシュ機能](concept-caching.md)を使用して、攻撃によって生成される大量のトラフィックからバックエンドを保護することができます。 キャッシュされたリソースは Front Door のエッジ ノードから返されるため、バックエンドに転送されることはありません。 動的な応答でキャッシュの有効期限 (秒または分) が短い場合でも、バックエンド サービスの負荷を大幅に削減できます。 キャッシュの概念とパターンの詳細については、[キャッシュに関する考慮事項](/azure/architecture/best-practices/caching)に関するページや[キャッシュ アサイド パターン](/azure/architecture/patterns/cache-aside)に関するページを参照してください。

## <a name="web-application-firewall-waf"></a>Web アプリケーション ファイアウォール (WAF)

[Front Door の Web アプリケーション ファイアウォール (WAF)](../../web-application-firewall/afds/afds-overview.md) を使用すると、さまざまな種類の攻撃を軽減できます。

* マネージド規則セットを使用すると、多くの一般的な攻撃に対する保護が提供されます。
* 定義された地域外や定義されたリージョン内からのトラフィックは、ブロックしたり、静的な Web ページにリダイレクトしたりできます。 詳細については、[geo フィルタリング](../../web-application-firewall/afds/waf-front-door-geo-filtering.md)に関するページを参照してください。
* 悪意のあるものとして識別された IP アドレスおよび範囲は、ブロックすることができます。
* レート制限を適用して、IP アドレスがサービスを頻繁に呼び出すのを防ぐことができます。
* 既知のシグネチャを持つ HTTP または HTTPS 攻撃を自動的にブロックおよびレート制限するための[カスタム WAF ルール](../../web-application-firewall/afds/waf-front-door-custom-rules.md)を作成できます。

## <a name="for-further-protection"></a>保護の強化

さらに保護を強化する必要がある場合は、バックエンドがデプロイされている VNet で [Azure DDoS Protection Standard](../../security/fundamentals/ddos-best-practices.md#ddos-protection-standard) を有効にすることができます。 DDoS Protection Standard をご利用のお客様は、以下に示す追加の利点を得ることができます。

* コスト保護
* SLA 保証
* 攻撃の際に DDoS Rapid Response Team の専門家に相談してすぐに支援を受ける

## <a name="next-steps"></a>次のステップ

[Front Door Standard/Premium を作成する](create-front-door-portal.md)方法について学習します。
