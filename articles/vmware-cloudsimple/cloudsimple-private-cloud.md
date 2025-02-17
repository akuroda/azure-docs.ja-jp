---
title: Azure VMware Solution by CloudSimple - プライベート クラウド
description: VMware の運用に関する完全な継続性や、既存のツール、スキル、プロセスとの互換性を含めて、CloudSimple の概念と利点について説明します。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: e2096e8c0ebfb233c7449510bf0bc8e80b56231e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88140719"
---
# <a name="cloudsimple-private-cloud-overview"></a>CloudSimple プライベート クラウドの概要

CloudSimple では、数分で VMware ワークロードがパブリック クラウドに変換、拡張されます。 CloudSimple サービスを使用すると、Azure ベア メタル インフラストラクチャ上に VMware をネイティブにデプロイできます。 デプロイは Azure の場所に存在し、Azure クラウドの残りの部分と完全に統合されます。

CloudSimple ソリューションにより、VMware 運用の完全な継続性が実現します。 このソリューションでは、次のようなパブリック クラウドのメリットを享受できます。

* 弾力性
* 革新
* 効率性

CloudSimple を使用すると、総保有コストを削減するクラウド消費モデルからメリットを得られます。 また、オンデマンド プロビジョニング、従量課金、および容量の最適化も提供されます。

CloudSimple は以下と完全に互換性があります。

* 既存のツール
* スキル
* 処理

この互換性により、チームは、これらの種類のポリシーに違反することなく、Azure クラウド上のワークロードを管理できます。

* ネットワーク
* Security  
* データ保護  
* Audit

CloudSimple では、インフラストラクチャとすべての必要なネットワークおよび管理サービスが管理されます。 CloudSimple サービスにより、チームは次のことに集中できます。

* 事業価値
* アプリケーションのプロビジョニング
* ビジネス継続性
* サポート
* ポリシーの適用

## <a name="private-cloud-environment-overview"></a>プライベート クラウド環境の概要

プライベート クラウドは、以下をサポートする分離 VMware スタックです。

* ESXi ホスト
* vCenter
* vSAN
* NSX

プライベート クラウドは CloudSimple ポータル経由で管理されます。 独自の管理ドメインに独自の vCenter サーバーがあります。

スタックは次の上で実行されます。

* 専用ノード
* 分離されたベア メタル ハードウェア ノード

ユーザーは、以下などのネイティブの VMware ツールを使用してスタックを使用します。

* vCenter
* NSX Manager

Azure の場所で専用のノードをデプロイできます。 次に、Azure と CloudSimple を使ってそれらを管理できます。 プライベート クラウドは 1 つ以上の vSphere クラスターで構成され、各クラスターには 3 から 16 のノードが含まれます。

プライベート クラウドは、購入済みの従量課金制のノードまたは予約済みの専用ノードを使用して作成できます。

次の接続を使用して、プライベート クラウドをオンプレミス環境や Azure ネットワークに接続できます。

* セキュリティで保護
* プライベート VPN
* Azure ExpressRoute

プライベート クラウド環境は、単一障害点がないように設計されます。

* ESXi クラスターは vSphere 高可用性により構成され、回復性のため少なくとも 1 つの予備ノードを持つようにサイズ設定されます。
* vSAN によって冗長プライマリ ストレージが提供されます。 vSan には、単一障害に対する保護を提供するため、ノードが少なくとも 3 つ必要になります。 大規模なクラスターの回復性を上げるように vSAN を構成できます。
* vCenter、PSC、NSX Manager の VM は、ストレージの不具合から守られるよう、RAID-10 ストレージ ポリシーで構成できます。 vSphere HA により、ノードとネットワークの不具合から守られます。

## <a name="scenarios-for-deploying-a-private-cloud"></a>プライベート クラウドのデプロイ シナリオ

ここでは、プライベート クラウドのデプロイのユースケースの例をいくつか紹介します。

### <a name="data-center-retirement-or-migration"></a>データ センターの廃止または移行

* 既存のデータセンターの制限に達するかハードウェアを更新するときに、容量を追加します。
* クラウドに必要な容量を追加し、ハードウェアの更新を管理する手間を省きます。
* 時間のかかる変換や再設計と比較して、クラウド移行のリスクやコストが削減されます。
* 使い慣れた VMware の使ツールとスキルを使用して、クラウド移行を促進します。 クラウドになれば、 Azure サービスを使用して自分のペースでアプリケーションを今の時代に合わせられます。

### <a name="expand-on-demand"></a>必要に応じて拡張

* 新しい開発環境または季節ごとのキャパシティ バーストなどの予期しないニーズに合わせてクラウドを拡張します。
* 新しい容量をオンデマンドで作成し、必要な場合にのみ維持できます。
* 先行投資を削減し、プロビジョニング速度を加速させ、オンプレミスとクラウドの両方でアーキテクチャとポリシーを同じにして複雑さを軽減します。

### <a name="disaster-recovery-and-virtual-desktops-in-the-azure-cloud"></a>Azure クラウドでのディザスター リカバリーおよび仮想デスクトップ

* Azure クラウド内のデータ、アプリ、およびデスクトップへのリモート アクセスを確立します。 高帯域幅接続により、データを高速にアップロード/ダウンロードしてインシデントから復旧します。 ネットワークの待機時間が短いことにより、デスクトップ アプリからの応答時間が短くなることを期待できます。

* CloudSimple portal と使い慣れた VMware ツールを使用してクラウド内のポリシーとネットワークのすべてを複製します。 レプリケーションにより、DR と VDI の実装の作成と管理の作業量とリスクが削減されます。

### <a name="high-performance-applications-and-databases"></a>ハイパフォーマンス アプリケーションとデータベース

* CloudSimple によって提供されるハイパーコンバージド アーキテクチャにより、最も要件の厳しいワークロードを実行します。
* Oracle、Microsoft SQL サーバー、ミドルウェアのシステム、および高パフォーマンスの非 SQL データベースを実行します。
* 25 Gbps の高速ネットワーク接続で、独自のデータ センターとしてクラウドを体験できます。 高速接続を有効にすると、パフォーマンスを低下させることなく、オンプレミス、Azure 上の VMware、Azure プライベート ワークロードにわたってハイブリッド アプリを実行できます。

### <a name="true-hybrid"></a>真のハイブリッド

* VMware と Azure サービス全体で DevOps が統合されます。
* すべてのワークロードに適用できる Azure のサービスとソリューション向け VMware 管理を最適化します。
* データ センターを展開したり、アプリケーションを再設計することなくパブリック クラウド サービスにアクセスします。
* Azure 上での VMware アプリケーションの ID、アクセス制御ポリシー、ログと監視を一元管理します。

## <a name="limits"></a>制限

次の表に、プライベート クラウドのリソースに対するノードの制限を示します。

| リソース | 制限 |
|----------|-------|
| プライベート クラウドを作成するためのノードの最小数 | 3 |
| プライベート クラウド上のクラスター内のノードの最大数 | 16 |
| プライベート クラウド内のノードの最大数 | 64 |
| 新しいクラスター上の最小ノード数 | 3 |

## <a name="next-steps"></a>次のステップ

* [プライベート クラウドを作成する](create-private-cloud.md)方法を確認する
* [プライベート クラウド環境を構成する](quickstart-create-private-cloud.md)方法を確認する
