---
title: Azure ExpressRoute の概要プライベート接続を介して接続する
description: ExpressRoute の技術概要では、ExpressRoute 接続がプライベート接続を介してオンプレミスのネットワークを Azure に拡張するしくみについて説明します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: overview
ms.date: 10/05/2020
ms.author: duau
ms.openlocfilehash: 82562eae748753cd785851c5d91f5f152b4c9960
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92206953"
---
# <a name="what-is-azure-expressroute"></a>Azure ExpressRoute とは
ExpressRoute を利用すると、接続プロバイダーが提供するプライベート接続を介して、オンプレミスのネットワークを Microsoft クラウドに拡張できます。 ExpressRoute では、Microsoft Azure、Microsoft 365 などの Microsoft クラウド サービスへの接続を確立できます。

接続には、任意の環境間 (IP VPN) 接続、ポイントツーポイントのイーサネット接続、共有施設での接続プロバイダーによる仮想交差接続があります。 ExpressRoute 接続はパブリックなインターネットを経由しません。 これにより、ExpressRoute 接続はインターネット経由の一般的な接続に比べて、安全性と信頼性が高く、待機時間も一定しており、高速です。 ExpressRoute を使用してネットワークを Microsoft に接続する方法については、「[ExpressRoute 接続モデル](expressroute-connectivity-models.md)」を参照してください。

![ExpressRoute 接続の概要](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>主な利点

* 接続プロバイダーによるオンプレミス ネットワークと Microsoft Cloud 間のレイヤー 3 接続。 接続には、任意の環境間 (IP VPN) 接続、ポイント ツー ポイントのイーサネット接続、イーサネット交換による仮想交差接続があります。
* 地政学的領域のすべての地域にわたる Microsoft のクラウド サービスへの接続。
* ExpressRoute Premium アドオンによるすべてのリージョンにわたる Microsoft サービスへのグローバル接続。
* ご利用のネットワークと Microsoft とをつなぐ BGP による動的ルーティング。
* あらゆるピアリングの場所に冗長性を組み込むという高い信頼性。
* 接続アップタイム [SLA](https://azure.microsoft.com/support/legal/sla/)。
* Skype for Business の QoS サポート。

詳しくは、「[ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。

## <a name="features"></a>機能

### <a name="layer-3-connectivity"></a>レイヤー 3 接続
Microsoft は業界標準の動的ルーティング プロトコル (BGP) を利用し、オンプレミス ネットワーク、Azure のインスタンス、および Microsoft パブリック アドレスの間でルートを交換します。 さまざまなトラフィック プロファイルに合わせ、ネットワークとさまざまな BGP セッションを確立します。 詳しくは、 [ExpressRoute の回線とルーティング ドメイン](expressroute-circuit-peerings.md) に関する記事を参照してください。

### <a name="redundancy"></a>冗長性
各 ExpressRoute 回線は、接続プロバイダーまたはネットワーク エッジから、[ExpressRoute の場所](./expressroute-locations.md#expressroute-locations)にある 2 つの Microsoft Enterprise エッジ ルーター (MSEE) への 2 つの接続で構成されます。 Microsoft は接続プロバイダー/ネットワーク エッジから 2 つの BGP 接続を必要とします。MSEE につき 1 つです。 あなたの側で冗長デバイス/イーサネット回線をデプロイしないことを選択できます。 ただし、接続プロバイダーは冗長デバイスを利用することで冗長性が与えられる方法で接続を Microsoft に渡します。 冗長レイヤー 3 接続構成は [SLA](https://azure.microsoft.com/support/legal/sla/) を有効にするための必須条件です。

### <a name="connectivity-to-microsoft-cloud-services"></a>Microsoft クラウド サービスへの接続
ExpressRoute 接続によって、次のようなサービスにアクセスできます。
* Microsoft Azure サービス
* Microsoft 365 サービス

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

ExpressRoute でサポートされているサービスの詳しい一覧については、[ExpressRoute FAQ](expressroute-faqs.md) ページを参照してください。

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>地政学的な地域内のすべてのリージョンへの接続
[ピアリングの場所](expressroute-locations.md)の 1 つで Microsoft に接続し、地政学的な領域内のリージョンにアクセスできます。

たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続しているとします。 北ヨーロッパと西ヨーロッパでホストされているすべての Microsoft クラウド サービスにアクセスできます。 地政学的領域、関連付けられている Microsoft クラウド リージョン、対応する ExpressRoute のピアリングの場所の概要については、「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」という記事をご覧ください。

### <a name="global-connectivity-with-expressroute-premium"></a>ExpressRoute Premium を使用したグローバル接続
[ExpressRoute Premium](expressroute-faqs.md) を有効にすると、地理的境界を越えて接続を拡張できます。 たとえば、ExpressRoute を介してアムステルダムの Microsoft に接続している場合、世界中のすべてのリージョンでホストされているすべての Microsoft クラウド サービスにアクセスできます。 北ヨーロッパおよび西ヨーロッパ リージョンにアクセスする場合と同じ方法で南アメリカやオーストラリアにデプロイされているサービスにもアクセスできます。 国内クラウドは除外されます。

### <a name="local-connectivity-with-expressroute-local"></a>ExpressRoute Local を使用したローカル接続
[Local SKU](expressroute-faqs.md) を有効にすることで、コスト効果の高い方法でデータを転送できます。 Local SKU を使用すると、必要な Azure リージョンの近くにある ExpressRoute の場所にデータを運ぶことができます。 ローカルでは、データ転送は ExpressRoute のポート料金に含まれます。 

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>ExpressRoute Global Reach によるオンプレミスを越えての接続
ExpressRoute Global Reach を有効にし、ExpressRoute 回線を接続することで、オンプレミス サイト間でデータを交換することができます。 たとえば、カリフォルニアのプライベート データ センターがシリコン バレーの ExpressRoute 回線に接続され、テキサスにある別のプライベート データ センターがダラスの ExpressRoute 回線に接続されているとします。 ExpressRoute Global Reach を使用すると、これら 2 つの ExpressRoute 回線経由でプライベート データ センターを接続できます。 データ センター間のトラフィックは、Microsoft のネットワークの中を通って行き来します。

詳細については、[ExpressRoute Global Reach](expressroute-global-reach.md) に関するページを参照してください。
### <a name="rich-connectivity-partner-ecosystem"></a>大勢の接続パートナーが属するエコシステム
ExpressRoute のエコシステムには継続的に新しい接続パートナーとシステム インテグレーター パートナーが加わり、拡大します。 最新の情報については、「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。

### <a name="connectivity-to-national-clouds"></a>国内クラウドへの接続
Microsoft は特殊な地政学的領域と顧客区分のために隔離されたクラウド環境を運営しています。 国内のクラウドとプロバイダーの一覧については、「[ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」のページを参照してください。

### <a name="expressroute-direct"></a>ExpressRoute Direct
ExpressRoute Direct では、世界中に戦略的に分散されたピアリングの場所で Microsoft のグローバル ネットワークに直接接続する機会がお客様に提供されます。 ExpressRoute Direct では、大規模なアクティブ/アクティブ接続をサポートするデュアル 100 Gbps 接続が提供されます。

以下に、ExpressRoute Direct で提供される主な機能の一部を示します。

* Storage や Cosmos DB などのサービスへの大規模なデータ インジェスト
* 規制対象となっていて、専用の分離された接続が必要とされる、銀行、政府機関、小売などの業種に向けた物理的な分離性
* 事業単位に基づいた回線配分の詳細な制御

詳細については、[ExpressRoute Direct](./expressroute-erdirect-about.md) に関するページを参照してください。

### <a name="bandwidth-options"></a>帯域幅のオプション
さまざまな帯域幅に合った ExpressRoute 回線を購入できます。 サポートされる帯域幅は以下のとおりです。 サポートされる帯域幅については接続プロバイダーに必ず確認してください。

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>帯域幅の動的スケーリング
接続を壊さずに (ベスト エフォート基準で) ExpressRoute 回線の帯域幅を増やすことができます。 詳細については、「[ExpressRoute 回線の変更](expressroute-howto-circuit-portal-resource-manager.md#modify)」を参照してください。

### <a name="flexible-billing-models"></a>柔軟な課金モデル
最適な請求モデルを選択できます。 以下に示す課金モデルから選択します。 詳細については、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

* **無制限のデータ**。 課金は、月額料金に基づいて行われます。すべての受信および送信データ転送が無料で含まれます。
* **従量制課金データ**。 課金は、月額料金に基づいて行われます。すべての受信データ転送が無料となります。 送信データ転送は 1 GB のデータ転送単位で課金されます。 データ転送料金は地域によって異なります。
* **ExpressRoute Premium アドオン**。 ExpressRoute Premiumは ExpressRoute 回線のアドオンです。 ExpressRoute Premium アドオンには次の機能があります。 
  * Azure パブリックと Azure プライベートのピアリングのルート上限の増加 (4,000 ルートから 10,000 ルートに)。
  * サービスのグローバル接続。 (国内のクラウドを除く) あらゆるリージョンで作成された ExpressRoute 回線から、世界中の他のすべてのリージョンにあるリソースにアクセスできます。 たとえば、西ヨーロッパで作成された仮想ネットワークにシリコン バレーでプロビジョニングされた ExpressRoute 回線からアクセスできます。
  * ExpressRoute ごとの VNet リンクの上限数が 10 からより大きな数に増加 (回線の帯域幅によって異なる)。

## <a name="faq"></a>よく寄せられる質問
ExpressRoute に関してよく寄せられる質問については、「[ExpressRoute の FAQ](expressroute-faqs.md)」を参照してください。

## <a name="whats-new"></a><a name="new"></a>新機能

RSS フィードを購読し、[Azure 更新情報](https://azure.microsoft.com/updates/?category=networking&query=ExpressRoute)ページで、最新の ExpressRoute 機能の更新を確認します。

## <a name="next-steps"></a>次のステップ
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。
* [ExpressRoute 接続モデル](expressroute-connectivity-models.md)について説明します。
* サービス プロバイダーを検索します。 「 [ExpressRoute パートナーとピアリングの場所](expressroute-locations.md)」を参照してください。