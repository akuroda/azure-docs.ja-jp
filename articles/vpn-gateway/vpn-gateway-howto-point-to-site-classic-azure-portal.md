---
title: 'P2S を使用してコンピューターを仮想ネットワークに接続する: 証明書認証:Azure portal (クラシック)'
titleSuffix: Azure VPN Gateway
description: Azure portal を使用してクラシックのポイント対サイト VPN Gateway 接続を作成します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/08/2020
ms.author: cherylmc
ms.openlocfilehash: 42b0945de55775f55f20cefdeb547cb5d6492c06
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94657076"
---
# <a name="configure-a-point-to-site-connection-by-using-certificate-authentication-classic"></a>証明書認証 (クラシック) を使用してポイント対サイト接続を構成する

[!INCLUDE [deployment models](../../includes/vpn-gateway-classic-deployment-model-include.md)]

この記事では、ポイント対サイト接続を備えた VNet を作成する方法について説明します。 この VNet は、Azure portal を使用してクラシック デプロイ モデルで作成します。 この構成では、自己署名証明書または CA によって発行された証明書を使用して接続クライアントを認証します。 また、次の記事で説明されているオプションを使用して、別のデプロイ ツールまたはモデルでこの構成を作成することもできます。

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)
> * [Azure Portal (クラシック)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)
>

個々のクライアント コンピューターから仮想ネットワークへのセキュリティで保護された接続を作成するには、ポイント対サイト (P2S) VPN ゲートウェイを使用します。 ポイント対サイト VPN 接続は、リモートの場所から VNet に接続する場合に役立ちます。 VNet に接続する必要のあるクライアントが少数しか存在しない場合は、P2S VPN が、サイト間 VPN の代わりに使用する有効なソリューションになります。 P2S VPN 接続は、クライアント コンピューターから接続を開始することによって確立されます。

> [!IMPORTANT]
> クラシック デプロイ モデルでは、Windows VPN クライアントのみをサポートし、SSL ベースの VPN プロトコルである Secure Socket トンネリング プロトコル (SSTP) を使用します。 Windows 以外の VPN クライアントをサポートするには、Resource Manager デプロイ モデルで VNet を作成する必要があります。 Resource Manager デプロイ モデルでは、SSTP に加えて IKEv2 VPN をサポートしています。 詳細については、[P2S 接続](point-to-site-about.md)に関するページを参照してください。
>
>

![ポイント対サイトのダイアログ](./media/vpn-gateway-howto-point-to-site-classic-azure-portal/point-to-site-connection-diagram.png)

## <a name="settings-and-requirements"></a>設定と要件

### <a name="requirements"></a>要件

ポイント対サイトの証明書認証接続には、以下のアイテムが必要です。 この記事には、それらの作成に役立つ手順が含まれています。

* Dynamic VPN ゲートウェイ。
* Azure にアップロードされた、ルート証明書の公開キー (.cer ファイル)。 このキーは信頼された証明書と見なされ、認証に使用されます。
* ルート証明書から生成され、接続する各クライアント コンピューターにインストールされたクライアント証明書。 この証明書はクライアントの認証に使用されます。
* VPN クライアント構成パッケージが生成され、接続するすべてのクライアント コンピューターにインストールされていること。 このクライアント構成パッケージは、VNet に接続するための必要な情報を持つ、既にオペレーティング システム上に存在するネイティブ VPN クライアントを構成します。

ポイント対サイト接続には、VPN デバイスやオンプレミスの公開された IP アドレスは必要ありません。 VPN 接続は、SSTP (Secure Socket トンネリング プロトコル) 経由で作成されます。 サーバー側でのサポート対象の SSTP バージョンは、1.0、1.1、1.2 です。 使用するバージョンはクライアントによって決まります。 Windows 8.1 以降の場合、SSTP では既定で 1.2 が使用されます。

詳細については、[ポイント対サイト接続の概要](point-to-site-about.md)と [FAQ](#faq) に関する記事を参照してください。

### <a name="example-settings"></a>設定例

テスト環境を作成するには、次の値を使用します。または、この記事にある例をより適切に理解するには、これらの値を参照してください。

* **[リソース グループ]:** TestRG
* **VNet 名:** VNet1
* **[アドレス空間]:** 192.168.0.0/16 <br>この例では、1 つのアドレス空間のみを使用します。 VNet には、複数のアドレス空間を使用することができます。
* **サブネット名:** FrontEnd
* **サブネットのアドレス範囲:** 192.168.1.0/24
* **GatewaySubnet:** 10.11.255.0/27
* **[リージョン]:** (米国) 米国東部
* **[クライアント アドレス空間]:** 172.16.201.0/24 <br> このポイント対サイト接続を使用して VNet に接続する VPN クライアントは、指定されたプールから IP アドレスを受信します。
* **[接続の種類]** : **[ポイント対サイト]** を選択します。
* **GatewaySubnet のアドレス範囲 (CIDR ブロック):** 192.168.200.0/24

開始する前に、Azure サブスクリプションを持っていることを確認してください。 Azure サブスクリプションをまだお持ちでない場合は、[MSDN サブスクライバーの特典](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)を有効にするか、[無料アカウント](https://azure.microsoft.com/pricing/free-trial)にサインアップしてください。

## <a name="create-a-virtual-network"></a><a name="vnet"></a>仮想ネットワークの作成

既に VNet がある場合は、設定が VPN ゲートウェイの設計に適合していることを確認します。 特に、他のネットワークと重複している可能性のあるサブネットに注意してください。

[!INCLUDE [basic classic vnet](../../includes/vpn-gateway-vnet-classic.md)]

[!INCLUDE [basic classic DNS](../../includes/vpn-gateway-dns-classic.md)]

## <a name="create-a-vpn-gateway"></a><a name="gateway"></a>VPN ゲートウェイの作成

1. 作成した VNet に移動します。
1. [VNet] ページの [設定] で、 **[ゲートウェイ]** を選択します。 **[ゲートウェイ]** ページで、仮想ネットワークのゲートウェイを表示できます。 この仮想ネットワークには、まだゲートウェイがありません。 **[接続とゲートウェイを追加するには、ここをクリックします]** というメモをクリックします。
1. **[VPN 接続とゲートウェイの構成]** ページで、次の設定を選択します。

   * 接続の種類: ポイント対サイト
   * クライアント アドレス空間:VPN クライアントが接続時に受け取る IP アドレスの範囲を追加します。 接続元のオンプレミスの場所や、接続先の VNet と重複しないプライベート IP アドレス範囲を使用します。
1. **[Do not configure a gateway at this time]\(この時点ではゲートウェイを構成しない\)** チェックボックスはオフのままにします。 ゲートウェイを作成します。
1. ページの下部にある **[Next: ゲートウェイ >]** を選択します。
1. **[ゲートウェイ]** タブで、次の値を選択します。

   * **Size:** このサイズは、ご使用の仮想ネットワーク ゲートウェイの SKU です。 Azure Portal では、既定の SKU は **[Default]** です。 ゲートウェイ SKU の詳細については、[VPN ゲートウェイ の設定](vpn-gateway-about-vpn-gateway-settings.md#gwsku)に関するページを参照してください。
   * **ルーティングの種類:** ポイント対サイト構成の場合は **[動的]** を選択する必要があります。 静的ルーティングは機能しません。
   * **ゲートウェイ サブネット:** このフィールドは既に自動入力されています。 この名前を変更することはできません。 PowerShell またはその他の手段で名前を変更すると、ゲートウェイが正常に動作しなくなります。
   * **アドレス範囲 (CIDR ブロック):** /29 と同程度の小規模なゲートウェイ サブネットを作成することはできますが、少なくとも /28 または /27 以上を選択してさらに多くのアドレスが含まれる大規模なサブネットを作成することをお勧めします。 そうすることにより、将来必要になる可能性がある追加の構成に対応できる十分なアドレスが確保されます。 ゲートウェイ サブネットを使用する場合は、ゲートウェイ サブネットにネットワーク セキュリティ グループ (NSG) を関連付けないようにしてください。 このサブネットにネットワーク セキュリティ グループを関連付けると、VPN ゲートウェイが期待どおりに機能しなくなる可能性があります。
1. **[確認および作成]** を選択して設定を検証します。
1. 検証に合格したら、 **[作成]** を選択します。 選択したゲートウェイ SKU によっては、VPN ゲートウェイの完了に最大 45 分かかることがあります。

## <a name="create-certificates"></a><a name="generatecerts"></a>証明書の作成

Azure は、ポイント対サイト VPN の VPN クライアントを認証するために証明書を使用します。 そのため、ルート証明書の公開キー情報を Azure にアップロードします。 それにより、その公開キーは *信頼できる* と見なされます。 信頼されたルート証明書からクライアント証明書を生成し、各クライアント コンピューターの Certificates-Current User\Personal\Certificates 証明書ストアにインストールする必要があります。 この証明書は、VNet に接続しようとするクライアントを認証するために使用されます。 

自己署名証明書を使用する場合は、特定のパラメーターを使用してその証明書を作成する必要があります。 自己署名証明書は、[PowerShell と Windows 10](vpn-gateway-certificates-point-to-site.md)、または [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) の手順を使用して作成できます。 自己署名ルート証明書を使用したり、自己署名ルート証明書からクライアント証明書を生成したりする場合は、これらの説明にある手順に従うことが重要です。 そうしないと、作成する証明書が P2S 接続との互換性がなくなり、接続エラーが表示されます。

### <a name="acquire-the-public-key-cer-for-the-root-certificate"></a>ルート証明書の公開キー (.cer) を取得する

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-rootcert-include.md)]

### <a name="generate-a-client-certificate"></a>クライアント証明書の生成

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-p2s-clientcert-include.md)]

## <a name="upload-the-root-certificate-cer-file"></a>ルート証明書 .cer ファイルのアップロード

ゲートウェイが作成されたら、信頼されたルート証明書の .cer ファイル (公開キー情報を含む) を Azure サーバーにアップロードします。 ルート証明書の秘密キーをアップロードしないでください。 証明書をアップロードした後、Azure はそれを使用して、信頼されたルート証明書から生成されたクライアント証明書がインストールされているクライアントを認証します。 後で、必要に応じて、追加の信頼されたルート証明書ファイルを (最大 20 個) アップロードできます。

1. 作成した仮想ネットワークに移動します。
1. **[設定]** で、 **[ポイント対サイト接続]** を選択します。
1. **[証明書の管理]** を選択します。
1. **[アップロード]** を選択します。
1. **[証明書のアップロード]** ペインで、フォルダー アイコンを選択し、アップロードする証明書に移動します。
1. **[アップロード]** を選択します。
1. 証明書が正常にアップロードされると、[証明書の管理] ページに表示されます。 場合によっては、アップロードしたばかりの証明書を表示するには、 **[更新]** を選択する必要があります。

## <a name="configure-the-client"></a>クライアントの構成

ポイント対サイト VPN を使用して VNet に接続するには、各クライアントにネイティブ Windows VPN クライアントを構成するためのパッケージをインストールする必要があります。 構成パッケージは、仮想ネットワークに接続するために必要な設定を使って、ネイティブ Windows VPN クライアントを構成します。

バージョンがクライアントのアーキテクチャと一致すれば、各クライアント コンピューターで同じ VPN クライアント構成パッケージを使用できます。 サポートされているクライアント オペレーティング システムの一覧については、[ポイント対サイト接続の概要](point-to-site-about.md)と [FAQ](#faq) に関する記事を参照してください。

### <a name="generate-and-install-a-vpn-client-configuration-package"></a>VPN クライアント構成パッケージを生成してインストールする

1. VNet 用の **[ポイント対サイト接続]** の設定に移動します。
1. ページ上部で、インストール先のクライアントのオペレーティング システムに対応するダウンロード パッケージを選択します。

   * 64 ビット クライアントの場合は、 **[VPN クライアント (64 ビット)]** を選択します。
   * 32 ビット クライアントの場合は、 **[VPN クライアント (32 ビット)]** を選択します。

1. Azure によって、クライアントが必要とする特定の設定を含むパッケージが生成されます。 VNet またはゲートウェイを変更するたびに、新しいクライアント構成パッケージをダウンロードしてクライアント コンピューターにインストールする必要があります。
1. パッケージが生成されたら、 **[ダウンロード]** を選択します。
1. クライアント構成パッケージをクライアント コンピューターにインストールします。 インストール時に、お使いの PC がWindows によって保護されていることを通知する SmartScreen ポップアップが表示された場合は、 **[詳細]** を選択し、 **[実行]** を選択します。 パッケージを保存して、他のクライアント コンピューターにインストールすることもできます。

### <a name="install-a-client-certificate"></a>クライアント証明書をインストールする

この演習では、クライアント証明書を生成したときに、コンピューターに自動的にインストールされています。 クライアント証明書を生成するために使用したクライアント コンピューターとは別のコンピューターから P2S 接続を作成するには、生成したクライアント証明書をそのコンピューターにインストールする必要があります。

クライアント証明書をインストールする場合は、そのクライアント証明書がエクスポートされたときに作成されたパスワードが必要です。 通常、その証明書はダブルクリックするだけでインストールできます。 詳細については、「[エクスポートしたクライアント証明書のインストール](vpn-gateway-certificates-point-to-site.md#install)」を参照してください。

## <a name="connect-to-your-vnet"></a>VNet への接続

>[!NOTE]
>接続元のクライアント コンピューターの管理者権限が必要です。
>

1. クライアント コンピューター上で、[VPN 設定] に移動します。
1. 作成した VPN を選択します。 この例の設定を使用した場合、接続には **Group TestRG VNet1** というラベルが付けられます。
1. **[接続]** を選択します。
1. [Windows Azure Virtual Network] ボックスで、 **[接続]** を選択します。 証明書に関するポップアップ メッセージが表示された場合は、管理者特権を使用するために **[続行]** を選択し、 **[はい]** を選択して構成の変更を受け入れます。
1. 接続が成功すると、 **[接続中]** 通知が表示されます。

[!INCLUDE [verify-client-certificates](../../includes/vpn-gateway-certificates-verify-client-cert-include.md)]

## <a name="verify-the-vpn-connection"></a>VPN 接続の確認

1. VPN 接続がアクティブであることを確認します。 クライアント コンピューターで管理者特権でのコマンド プロンプトを開き、**ipconfig/all** を実行します。
1. 結果を表示します。 受信した IP アドレスが、VNet の作成時に指定したポイント対サイト接続アドレス範囲内のアドレスのいずれかであることに注意してください。 結果は、次の例のようになります。

   ```
    PPP adapter VNet1:
        Connection-specific DNS Suffix .:
        Description.....................: VNet1
        Physical Address................:
        DHCP Enabled....................: No
        Autoconfiguration Enabled.......: Yes
        IPv4 Address....................: 192.168.130.2(Preferred)
        Subnet Mask.....................: 255.255.255.255
        Default Gateway.................:
        NetBIOS over Tcpip..............: Enabled
   ```

## <a name="to-connect-to-a-virtual-machine"></a>仮想マシンに接続するには:

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-p2s-classic-include.md)]

## <a name="to-add-or-remove-trusted-root-certificates"></a>信頼されたルート証明書を追加または削除するには

信頼されたルート証明書を Azure に追加したり、Azure から削除したりできます。 ルート証明書を削除すると、そのルートから生成された証明書を含むクライアントは認証を受けて接続することができなくなります。 それらのクライアントが再び認証を受けて接続するには、Azure に信頼されているルート証明書から生成された新しいクライアント証明書をインストールする必要があります。

### <a name="add-a-trusted-root-certificate"></a>信頼されたルート証明書を追加する

最初の信頼されたルート証明書を追加するために使用したのと同じプロセスを使用して、最大 20 の信頼されたルート証明書 .cer ファイルを Azure に追加できます。

### <a name="remove-a-trusted-root-certificate"></a>信頼されたルート証明書を削除する

1. お使いの VNet 用のページの **[ポイント対サイト接続]** セクションで、 **[証明書の管理]** を選択します。
1. 削除する証明書の横にある省略記号を選択し、 **[削除]** を選択します。

## <a name="to-revoke-a-client-certificate"></a>クライアント証明書を失効させるには

必要に応じて、クライアント証明書を失効させることができます。 証明書失効リストを使用すると、個々のクライアント証明書に基づくポイント対サイト接続を選択して拒否することができます。 この方法は、信頼されたルート証明書の削除とは異なります。 信頼されたルート証明書 .cer を Azure から削除すると、失効したルート証明書によって生成または署名されたすべてのクライアント証明書のアクセス権が取り消されます。 ルート証明書ではなくクライアント証明書を失効させることで、ルート証明書から生成されたその他の証明書は、その後もポイント対サイト接続の認証に使用できます。

一般的な方法としては、ルート証明書を使用してチームまたは組織レベルでアクセスを管理し、失効したクライアント証明書を使用して、個々のユーザーの細かいアクセス制御を構成します。

失効リストに拇印を追加することで、クライアント証明書を失効させることができます。

1. クライアント証明書の拇印を取得します。 詳細については、「[方法: 証明書のサムプリントを取得する](/dotnet/framework/wcf/feature-details/how-to-retrieve-the-thumbprint-of-a-certificate)」を参照してください。
1. この情報をテキスト エディターにコピーし、連続した文字列になるようにスペースを削除します。
1. **[ポイント対サイト VPN 接続]** を選択し、 **[証明書の管理]** を選択します。
1. **[失効リスト]** を選択して **[失効リスト]** ページを開きます。
1. **[Thumbprint] (サムプリント)** で、証明書のサムプリントをスペースのない連続した 1 行のテキストとして貼り付けます。
1. **[+ 一覧に追加]** を選択して、証明書失効リスト (CRL) にサムプリントを追加します。

更新が完了した後は、証明書を接続に使用することができなくなります。 この証明書を使用して接続しようとするクライアントは、その証明書が有効でなくなったことを示すメッセージを受信します。

## <a name="faq"></a><a name="faq"></a>FAQ

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-point-to-site-classic-include.md)]

## <a name="next-steps"></a>次のステップ

* 接続が完了したら、仮想ネットワークに仮想マシンを追加できます。 詳細については、[Virtual Machines](../index.yml) に関するページを参照してください。

* ネットワークと Linux 仮想マシンに関する詳細を理解するには、[Azure と Linux の VM ネットワークの概要](../virtual-machines/network-overview.md)に関するページを参照してください。

* P2S のトラブルシューティング情報については、[Azure ポイント対サイト接続のトラブルシューティング](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)に関するページを参照してください。