---
title: Azure IoT Hub Device Provisioning Service での Roll X.509 証明書
description: デバイス プロビジョニング サービス (DPS) インスタンスに X.509 証明書を展開する方法
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94951130"
---
# <a name="how-to-roll-x509-device-certificates"></a>デバイスの X.509 証明書を展開する方法

IoT ソリューションのライフサイクルの間に、証明書を展開する必要があります。 証明書を展開する主な理由としては、セキュリティ違反および証明書の期限切れの 2 つが挙げられます。 

セキュリティ違反が発生した際にシステムを保護するためのセキュリティのベスト プラクティスは、証明書を展開することです。 「[Assume Breach Methodology](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf)」(侵害想定の方法) の一環として、マイクロソフトは予防措置に加えて事後対応型のセキュリティ プロセスの必要性を提唱しています。 デバイスの証明書の展開は、これらのセキュリティ プロセスの一部として含まれる必要があります。 証明書を展開する頻度は、ソリューションのセキュリティのニーズに左右されます。 非常に機密性の高いデータを扱うソリューションを持つ顧客は証明書を毎日展開する場合がある一方で、数年ごとに展開する顧客もいます。

デバイスの証明書の展開は、デバイスに格納されている証明書や IoT ハブの更新が関与します。 後ほど、デバイスはデバイス プロビジョニング サービス (DPS) の通常の[プロビジョニング](about-iot-dps.md#provisioning-process)を使用して、IoT ハブと再プロビジョニングします。


## <a name="obtain-new-certificates"></a>新しい証明書を取得する

お使いの IoT デバイス用の新しい証明書を取得する方法は数多くあります。 これらには、デバイス ファクトリから証明書を取得する、独自の証明書を作成する、サード パーティに証明書の作成を管理してもらうなどの方法があります。 

証明書は互いに署名され、ルート CA 証明書から[リーフ証明書](concepts-x509-attestation.md#end-entity-leaf-certificate)への信頼チェーンを形成します。 署名証明書は、信頼チェーンの最後のリーフ証明書の署名に使用される証明書です。 署名証明書には、ルート CA 証明書、または信頼チェーンの中間証明書を指定できます。 詳しくは、「[X.509 証明書](concepts-x509-attestation.md#x509-certificates)」をご覧ください。
 
署名証明書を取得する方法は 2 つあります。 最初の方法は、ルート証明機関 (CA) から署名証明書を購入する方法で、実稼働システムで推奨されます。 この方法は、信頼できる発行元にセキュリティをチェーンでつなぎます。 

2 つ目の方法は、OpenSSL などのツールを使用して、独自の X.509 証明書を作成する方法です。 この方法は X.509 証明書のテストに適していますが、セキュリティはほとんど保証されません。 この手法は、独自の CA プロバイダーとして機能する準備ができていない限り、テストのみに使用することをお勧めします。
 

## <a name="roll-the-certificate-on-the-device"></a>証明書を端末に展開する

デバイス上の証明書は、常に[ハードウェア セキュリティ モジュール (HSM)](concepts-service.md#hardware-security-module) などの安全な場所に格納する必要があります。 デバイスの証明書を展開する方法は、それらが作成された方法と、最初にデバイスにインストールされた方法によって異なります。 

サード パーティから証明書を取得した場合は、その証明書を展開する方法を確認する必要があります。 そのプロセスはサード パーティとの取り決めの中に含まれている場合もあれば、別個のサービスとして提供される場合もあります。 

デバイスの証明書を自身で管理している場合は、証明書を更新するための独自のパイプラインをビルドする必要があります。 新旧のリーフ証明書が同じ共通名 (CN) を持つようにします。 同じ CN を持つことで、デバイスは重複した登録コードを作成することなく、再プロビジョニングできます。 


## <a name="roll-the-certificate-in-the-iot-hub"></a>証明書を IoT ハブに展開する

デバイスの証明書は、手動で IoT ハブに追加できます。 Device Provisioning Service インスタンスを使用して、証明書を自動化することもできます。 この記事では、自動プロビジョニングをサポートするために、Device Provisioning Service インスタンスが使用されているものとします。

デバイスが自動プロビジョニングによって初めてプロビジョニングされると、デバイスは起動し、プロビジョニング サービスに接続されます。 プロビジョニング サービスは、資格情報としてデバイスのリーフ証明書を使用して IoT ハブにデバイスの ID を作成する前に、ID チェックを実行することで応答します。 その後、プロビジョニング サービスはデバイスがどの IoT ハブに割り当てられているかを伝えます。次に、そのデバイスがリーフ証明書を使用して認証し、IoT ハブに接続します。 

新しいリーフ証明書がデバイスに展開されると、接続に新しい証明書が使用されるため、IoT ハブに接続できなくなります。 IoT ハブは、古い証明書を持つデバイスのみを認識します。 デバイスの接続の試行結果は、「未承認」の接続エラーになります。 このエラーを解決するには、デバイスが新しいリーフ証明書に対応するように、デバイスの登録エントリを更新する必要があります。 その後プロビジョニング サービスは、デバイスが再プロビジョニングされるときに、必要に応じて IoT Hub のデバイス レジストリ情報を更新できます。 

この接続エラーに対する例外として、プロビジョニング サービスでデバイスの[登録グループ](concepts-service.md#enrollment-group)を作成している場合のシナリオが考えられます。 このケースで、デバイスの証明書の信頼チェーンにルートまたは中間証明書を展開していない場合、デバイスは新しい証明書が登録グループで定義された信頼チェーンの一部である場合に認識されます。 このシナリオがセキュリティ違反に対する反応として発生する場合は、少なくとも、グループ内の違反と見なされる特定のデバイス証明書を禁止する必要があります。 詳しくは、「[登録グループ内の特定のデバイスを禁止する](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group)」をご覧ください。

展開された証明書の登録エントリの更新は、 **[登録を管理します]** ページで行われます。 そのページにアクセスするには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、デバイスの登録エントリがある IoT Hub Device Provisioning Service インスタンスに移動します。

2. **[登録を管理します]** をクリックします。

    ![登録を管理します](./media/how-to-roll-certificates/manage-enrollments-portal.png)


登録エントリの更新を処理する方法は、個々の登録またはグループ登録のどちらを使用しているかどうかに左右されます。 また、推奨される手順は、セキュリティ違反または証明書の期限切れのどちらが原因で証明書を展開しているかによって異なります。 次のセクションは、これらの更新をどのように処理するかについて説明します。


## <a name="individual-enrollments-and-security-breaches"></a>個々の登録とセキュリティ違反

セキュリティ違反に対する応答として証明書を展開する場合は、次のアプローチを使用して現在の証明書をすぐに削除してください。

1. **[個々の登録]** をクリックし、リスト内の登録 ID エントリをクリックします。 

2. **[Delete current certificate]\(現在の証明書の削除\)** ボタンをクリックし、フォルダー アイコンをクリックしてアップロードする登録エントリ用の新しい証明書を選択します。 完了したら、 **[保存]** をクリックします。

    プライマリとセカンダリの証明書の両方が侵害されている場合、両方に対して次のステップを実行してください。

    ![個々の登録とセキュリティ違反を管理する](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. プロビジョニング サービスからセキュリティが侵害された証明書が削除されても、その証明書に対するデバイスの登録が存在する限り、証明書を使用して IoT ハブにデバイスを接続できます。 これに対処するには 2 つの方法があります。 

    1 番目の方法は、手動で IoT ハブに移動し、直ちに、侵害された証明書に関連付けられているデバイスの登録を削除することです。 その後、更新された証明書でデバイスが再びプロビジョニングされると、新しいデバイス登録が作成されます。     

    ![IoT ハブのデバイスの登録を削除する](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    2 番目の方法は、再プロビジョニングのサポートを使用して、同じ IoT ハブにデバイスを再度プロビジョニングすることです。 この方法を使用すると、IoT ハブ上のデバイス登録に対する証明書を置き換えることができます。 詳しくは、「[How to reprovision devices](how-to-reprovision.md)」(デバイスを再プロビジョニングする方法) をご覧ください。

## <a name="individual-enrollments-and-certificate-expiration"></a>個々の登録と証明書の期限切れ

証明書の期限切れを処理するために証明書を展開する場合、プロビジョニングを試行するデバイスのダウンタイムが短縮されるように、セカンダリ証明書の構成を使用することをお勧めします。

後ほどセカンダリ証明書の期限切れが近づき、展開することが必要になったら、プライマリの構成を使用するよう切り替えることができます。 このようにプライマリとセカンダリの証明書を切り替えることで、プロビジョニングを試行するデバイスのダウンタイムを短縮できます。


1. **[個々の登録]** をクリックし、リスト内の登録 ID エントリをクリックします。 

2. **[セカンダリ証明書]** をクリックし、フォルダー アイコンをクリックしてアップロードする登録エントリ用の新しい証明書を選択します。 **[保存]** をクリックします。

    ![セカンダリ証明書の有効期限を使用して個々の登録を管理する](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. 後ほどプライマリ証明書の期限が切れたら、戻って **[Delete current certificate]\(現在の証明書の削除\)** ボタンをクリックしてそのプライマリ証明書を削除します。

## <a name="enrollment-groups-and-security-breaches"></a>登録グループとセキュリティ違反

セキュリティ違反に対する応答としてグループ登録を更新するには、次のいずれかのアプローチを使用して、現在のルート CA または中間証明書をすぐに削除してください。

#### <a name="update-compromised-root-ca-certificates"></a>侵害されたルート CA 証明書を更新する

1. お使いの Device Provisioning Service インスタンスの **[証明書]** タブをクリックします。

2. リスト内の侵害された証明書をクリックし、 **[削除]** ボタンをクリックします。 証明書の名前を入力して削除を確定し、 **[OK]** をクリックします。 セキュリティが侵害されたすべての証明書に対してこのプロセスを繰り返します。

    ![ルート CA 証明書を削除する](./media/how-to-roll-certificates/delete-root-cert.png)

3. 「[検証済みの CA 証明書の構成](how-to-verify-certificates.md)」に記載の手順に従い、新しいルート CA 証明書を追加して検証します。

4. お使いの Device Provisioning Service インスタンスの **[登録を管理します]** タブをクリックし、 **[登録グループ]** リストをクリックします。 リスト内の登録グループの名前をクリックします。

5. **[CA 証明書]** をクリックし、新しいルート CA 証明書を選択します。 **[保存]** をクリックします。 

    ![侵害された証明書に新しいルート CA 証明書を選択する](./media/how-to-roll-certificates/select-new-root-cert.png)

6. プロビジョニング サービスからセキュリティが侵害された証明書が削除されても、その証明書に対するデバイスの登録が存在する限り、証明書を使用して IoT ハブにデバイスを接続できます。 これに対処するには 2 つの方法があります。 

    1 番目の方法は、手動で IoT ハブに移動し、直ちに、侵害された証明書に関連付けられているデバイスの登録を削除することです。 その後、更新された証明書でデバイスが再びプロビジョニングされると、それぞれに対する新しいデバイス登録が作成されます。     

    ![IoT ハブのデバイスの登録を削除する](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    2 番目の方法は、再プロビジョニングのサポートを使用して、同じ IoT ハブにデバイスを再度プロビジョニングすることです。 この方法を使用すると、IoT ハブ上のデバイス登録に対する証明書を置き換えることができます。 詳しくは、「[How to reprovision devices](how-to-reprovision.md)」(デバイスを再プロビジョニングする方法) をご覧ください。



#### <a name="update-compromised-intermediate-certificates"></a>侵害された中間証明書を更新する

1. **[登録グループ]** をクリックし、リスト内のグループの名前をクリックします。 

2. **[Intermediate Certificate]\(中間証明書\)** をクリックし、 **[Delete current certificate]\(現在の証明書の削除\)** をクリックします。 フォルダー アイコンをクリックして、登録グループ用にアップロードする新しい中間証明書に移動します。 完了したら、 **[保存]** をクリックします。 プライマリとセカンダリの両方が侵害されている場合は、両方の証明書に対して次のステップを実行してください。

    この新しい中間証明書は、プロビジョニング サービスに既に追加されている検証済みのルート CA 証明書で署名する必要があります。 詳しくは、「[X.509 証明書](concepts-x509-attestation.md#x509-certificates)」をご覧ください。

    ![侵害された中間証明書の個別登録を管理する](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. プロビジョニング サービスからセキュリティが侵害された証明書が削除されても、その証明書に対するデバイスの登録が存在する限り、証明書を使用して IoT ハブにデバイスを接続できます。 これに対処するには 2 つの方法があります。 

    1 番目の方法は、手動で IoT ハブに移動し、直ちに、侵害された証明書に関連付けられているデバイスの登録を削除することです。 その後、更新された証明書でデバイスが再びプロビジョニングされると、それぞれに対する新しいデバイス登録が作成されます。     

    ![IoT ハブのデバイスの登録を削除する](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    2 番目の方法は、再プロビジョニングのサポートを使用して、同じ IoT ハブにデバイスを再度プロビジョニングすることです。 この方法を使用すると、IoT ハブ上のデバイス登録に対する証明書を置き換えることができます。 詳しくは、「[How to reprovision devices](how-to-reprovision.md)」(デバイスを再プロビジョニングする方法) をご覧ください。


## <a name="enrollment-groups-and-certificate-expiration"></a>登録グループと証明書の期限切れ

証明書の期限切れを処理するために証明書を展開する場合、プロビジョニングを試行するデバイスのダウンタイムが発生しないように、セカンダリ証明書の構成を使用することをお勧めします。

後ほどセカンダリ証明書の期限切れが近づき、展開することが必要になったら、プライマリの構成を使用するよう切り替えることができます。 このようにプライマリとセカンダリの証明書を切り替えることで、プロビジョニングを試行するデバイスのダウンタイムが発生しないようにすることができます。 

#### <a name="update-expiring-root-ca-certificates"></a>期限が切れるルート CA 証明書を更新する

1. 「[検証済みの CA 証明書の構成](how-to-verify-certificates.md)」に記載の手順に従い、新しいルート CA 証明書を追加して検証します。

2. お使いの Device Provisioning Service インスタンスの **[登録を管理します]** タブをクリックし、 **[登録グループ]** リストをクリックします。 リスト内の登録グループの名前をクリックします。

3. **[CA 証明書]** をクリックし、 **[セカンダリ証明書]** の構成で新しいルート CA 証明書を選択します。 **[保存]** をクリックします。 

    ![有効期限に新しいルート CA 証明書を選択する](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. 後ほどプライマリ証明書の有効期限が切れるときに、お使いの Device Provisioning Service インスタンスの **[証明書]** タブをクリックします。 リスト内の有効期限が切れた証明書をクリックし、 **[削除]** ボタンをクリックします。 証明書の名前を入力して削除を確定し、 **[OK]** をクリックします。

    ![ルート CA 証明書を削除する](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>期限が切れる中間証明書を更新する


1. **[登録グループ]** をクリックし、リスト内のグループの名前をクリックします。 

2. **[セカンダリ証明書]** をクリックし、フォルダー アイコンをクリックしてアップロードする登録エントリ用の新しい証明書を選択します。 **[保存]** をクリックします。

    この新しい中間証明書は、プロビジョニング サービスに既に追加されている検証済みのルート CA 証明書で署名する必要があります。 詳しくは、「[X.509 証明書](concepts-x509-attestation.md#x509-certificates)」をご覧ください。

   ![セカンダリ証明書の期限切れを使用して登録グループを管理する](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. 後ほどプライマリ証明書の期限が切れたら、戻って **[Delete current certificate]\(現在の証明書の削除\)** ボタンをクリックしてそのプライマリ証明書を削除します。


## <a name="reprovision-the-device"></a>デバイスを再プロビジョニングする

証明書がデバイスと Device Provisioning Service の両方に展開されたら、デバイスは Device Provisioning Service に連絡することでデバイス自体を再プロビジョニングできます。 

プロビジョニングするデバイスをプログラミングする簡単な方法の 1 つは、デバイスが IoT ハブに接続を試行しているときに「未承認」のエラーを受け取った場合に、プロビジョニング サービスに連絡してプロビジョニング フローを実行するようデバイスをプログラミングする方法です。

もう 1 つの方法は、新旧の証明書の両方の短期間のオーバーラップを有効にし、IoT ハブを使用してデバイスにコマンドを送信し、IoT Hub の接続情報を更新するためにプロビジョニング サービスを介してそれらデバイスを再登録する方法です。 各デバイスはコマンドを個別に処理する場合があるため、コマンドが呼び出されたときにデバイスが何をするかわかるようにプログラミングする必要があります。 IoT Hub を介してデバイスに指示を出す方法は複数ありますが、[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)または[ジョブ](../iot-hub/iot-hub-devguide-jobs.md)を使用してプロセスを開始することをお勧めします。

再プロビジョニングが完了すると、デバイスは新しい証明書を使用して IoT Hub に接続できるようになります。


## <a name="disallow-certificates"></a>証明書を禁止する

セキュリティ違反への対応として、デバイスの証明書を禁止することが必要になる場合があります。 デバイスの証明書を禁止するには、ターゲット デバイスまたは証明書の登録エントリを無効にします。 詳しくは、[登録解除の管理](how-to-revoke-device-access-portal.md)に関するページのデバイスの禁止に関する記事をご覧ください。

証明書が無効になった登録エントリの一部として追加されると、その証明書が別の登録エントリの一部として有効になっている場合でも、その証明書を使用した IoT ハブへの登録の試行はすべて失敗します。
 



## <a name="next-steps"></a>次のステップ

- デバイス プロビジョニング サービスの X.509 証明書について詳しくは、「[X.509 証明書の構成証明](concepts-x509-attestation.md)」をご覧ください 
- Azure IoT Hub Device Provisioning Service で X.509 CA 証明書の所有証明を行う方法について詳しくは、[証明書を検証する方法](how-to-verify-certificates.md)に関する記事をご覧ください。
- ポータルを使って登録グループを作成する方法について詳しくは、「[Azure Portal でデバイス登録を管理する方法](how-to-manage-enrollments.md)」をご覧ください。