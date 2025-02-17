---
title: Azure プライベート DNS とは
description: この記事では、Microsoft Azure のプライベート DNS ホスティング サービスの概要から始めます。
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87985633"
---
# <a name="what-is-azure-private-dns"></a>Azure プライベート DNS とは

ドメイン ネーム システム (DNS) は、サービスの名前をその IP アドレスに変換する (または解決する) 役割を担います。  Azure DNS は、DNS ドメインのホスティング サービスであり、Microsoft Azure インフラストラクチャを使用した名前解決を提供します。 Azure DNS では、インターネットに接続する DNS ドメインのサポートに加えて、プライベート DNS ゾーンもサポートされます。

Azure プライベート DNS は、信頼性が高くセキュリティで保護された DNS サービスを提供し、カスタムの DNS ソリューションの追加を必要とせずに、仮想ネットワークでドメイン名を管理および解決します。 プライベート DNS ゾーンを使用すると、現在利用可能な Azure で提供される名前ではなく、独自のカスタム ドメイン名を使用できます。 カスタム ドメイン名の使用は、組織のニーズに最適な仮想ネットワーク アーキテクチャを調整するのに役立ちます。 仮想ネットワーク内、および仮想ネットワーク間の、仮想マシン (VM) の名前解決を提供します。 さらに、水平分割ビューでゾーン名を構成できるので、プライベート DNS ゾーンとパブリック DNS ゾーンで名前を共有することもできます。

仮想ネットワークからプライベート DNS ゾーンのレコードを解決するには、仮想ネットワークをゾーンにリンクする必要があります。 リンクされた仮想ネットワークはフル アクセス権を持ち、プライベート ゾーンに公開されているすべての DNS レコードを解決できます。 さらに、仮想ネットワーク リンク上で自動登録を有効にすることもできます。 仮想ネットワーク リンク上で自動登録を有効にすると、その仮想ネットワーク上の仮想マシンの DNS レコードがプライベート ゾーンに登録されます。 また、自動登録を有効にすると、仮想マシンの作成または削除、あるいはその IP アドレスの変更を行うたびに、Azure DNS でそのゾーン レコードも更新されます。

![DNS の概要](./media/private-dns-overview/scenario.png)

> [!NOTE]
> ベスト プラクティスとして、 *.local* ドメインはプライベート DNS ゾーンに使用しないでください。 すべてのオペレーティング システムでこれがサポートされているわけではありません。

## <a name="benefits"></a>メリット

Azure プライベート DNS には次のような利点があります。

* **カスタムの DNS ソリューションの必要がなくなります**。 以前は、多くのお客様は、仮想ネットワーク内の DNS ゾーンを管理するためにカスタムの DNS ソリューションを作成していました。 DNS ゾーンは、Azure のネイティブ インフラストラクチャを使用して管理できるようになりました。これにより、カスタムの DNS ソリューションの作成と管理の負担がなくなります。

* **すべての一般的な DNS レコードの種類を使用します**。 Azure DNS では、A、AAAA、CNAME、MX、PTR、SOA、SRV、TXT のレコードをサポートしています。

* **自動のホスト名レコード管理**。 Azure では、カスタムの DNS レコードをホストすると共に、指定された仮想ネットワーク内の VM のホスト名レコードを自動的に維持します。 このシナリオにより、カスタムの DNS ソリューションの作成またはアプリケーションの変更の必要なしで、使用するドメイン名を最適化することができます。

* **仮想ネットワーク間のホスト名解決**。 Azure 提供のホスト名とは異なり、プライベート DNS ゾーンは仮想ネットワーク間で共有できます。 この機能により、仮想ネットワークのピアリングなどのネットワーク間とサービス検索のシナリオが簡略化されます。

* **使い慣れたツールとユーザー エクスペリエンス**。 習得に要する時間を短縮するため、このサービスでは既に確立された Azure DNS ツール (Azure portal、Azure PowerShell、Azure CLI、Azure Resource Manager テンプレート、REST API) が使用されます。

* **水平分割 DNS サポート**。 Azure DNS では、同じ名前でゾーンを作成し、それが仮想ネットワーク内からとパブリック インターネットからとで異なる回答に解決されるようにすることができます。 水平分割 DNS の一般的なシナリオでは、仮想ネットワーク内で使用する専用のバージョンのサービスを提供します。

* **すべての Azure リージョンで使用可能**。 Azure DNS Private Zones の機能は Azure パブリック クラウドの全リージョンでご利用いただけます。

## <a name="capabilities"></a>機能

Azure DNS には次の機能があります。

* **自動登録が有効にされたプライベート ゾーンにリンクされている仮想ネットワークからの仮想マシンの自動登録**。 仮想マシンは、各自のプライベート IP アドレスを指す A レコードとしてプライベート ゾーンに登録 (追加) されます。 自動登録が有効にされた仮想ネットワーク リンク内の仮想マシンが削除されると、Azure DNS によって、リンクされたプライベート ゾーンから対応する DNS レコードの削除も自動的に実行されます。

* **正引き DNS 解決は、プライベート ゾーンにリンクされている仮想ネットワーク間でサポートされています**。 仮想ネットワーク間の DNS 解決では、仮想ネットワークが相互にピアリングするという明示的な依存関係はありません。 ただし、他のシナリオ (HTTP トラフィックなど) では、仮想ネットワークをピアリングできます。

* **逆引き DNS 参照は仮想ネットワークのスコープ内でサポートされています**。 プライベート ゾーンに割り当てられた仮想ネットワーク内のプライベート IP の逆引き DNS 参照では、サフィックスとしてホスト/レコード名とゾーン名を含む FQDN が返されます。

## <a name="other-considerations"></a>その他の考慮事項

Azure DNS には次の制限事項があります。

* 特定の仮想ネットワークは、VM DNS レコードの自動登録が有効になっている場合、1 つのプライベート ゾーンにのみリンク可能です。 ただし、複数の仮想ネットワークを 1 つの DNS ゾーンにリンクできます。
* 逆引き DNS は、リンクされた仮想ネットワーク内のプライベート IP 空間に対してのみ機能します。
* リンクされた仮想ネットワークのプライベート IP アドレスの逆引き DNS は、仮想マシンの既定のサフィックスとして *internal.cloudapp.net* を返します。 自動登録が有効にされたプライベート ゾーンにリンクされている仮想ネットワークでは、プライベート IP アドレスの逆引き DNS は 2 つの FQDN を返します。1 つは、既定の *internal.cloudapp.net* というサフィックスが付き、もう 1 つはプライベート ゾーンのサフィックスが付きます。
* 条件付き転送は、現在ネイティブにサポートされていません。 Azure とオンプレミス ネットワーク間の解決を有効にするには、[VM とロール インスタンスの名前解決](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください。
 
## <a name="pricing"></a>価格

料金情報については、「[Azure DNS の価格](https://azure.microsoft.com/pricing/details/dns/)」をご覧ください。

## <a name="next-steps"></a>次のステップ

* [Azure PowerShell](./private-dns-getstarted-powershell.md) または [Azure CLI](./private-dns-getstarted-cli.md) を使用して Azure DNS のプライベート ゾーンを作成する方法を確認します。

* [プライベート ゾーンのシナリオ](./private-dns-scenarios.md)に関しては、Azure DNS のプライベート ゾーンで実現できるいくつかの一般的なシナリオを参照してください。

* Azure DNS のプライベート ゾーンに関する一般的な質問と回答については、[プライベート DNS の FAQ](./dns-faq-private.md) に関する記事をご覧ください。特定の種類の操作で期待できる固有の動作についても説明があります。

* 「[DNS ゾーンとレコードの概要](dns-zones-records.md)」で DNS ゾーンとレコードについて学びます。

* Azure のその他の重要な[ネットワーク機能](../networking/networking-overview.md)について参照してください。
