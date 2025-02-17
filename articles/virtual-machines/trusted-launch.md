---
title: 'プレビュー: Azure VM のトラステッド起動'
description: Azure Virtual Machines のトラステッド起動について説明します。
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: 5e500eca601d21f106dbe31236e9b5c2aa76b0d2
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801929"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Azure Virtual Machines のトラステッド起動 (プレビュー)

Azure からは、[第 2 世代](generation-2.md)の VM のセキュリティを向上させるためのシームレスな方法として、トラステッド起動が提供されています。 トラステッド起動により、高度で永続的な攻撃手法から保護されます。 トラステッド起動は、個別に有効にできる、複数の連携するインフラストラクチャ テクノロジで構成されています。 テクノロジごとに、高度な脅威に対する防御の別のレイヤーが提供されます。

> [!IMPORTANT]
> トラステッド起動を使用するには、新しい仮想マシンを作成する必要があります。 最初に作成されたときにトラステッド起動が有効にされていない既存の仮想マシンで、トラステッド起動を有効にすることはできません。
>
> トラステッド起動は、現在パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 
>
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。


## <a name="benefits"></a>メリット 

- 検証済みのブート ローダー、OS カーネル、ドライバーを使用して、仮想マシンが安全にデプロイされます。
- 仮想マシン内のキー、証明書、シークレットが安全に保護されます。
- ブート チェーン全体の整合性に関する分析情報と信頼が得られます。
- ワークロードを信頼できて検証可能であることが保証されます。

## <a name="public-preview-limitations"></a>パブリック プレビューの制限事項

**サイズのサポート**: 次のものを除くすべての [第 2 世代](generation-2.md) VM のサイズ。

- Lsv2 シリーズ 
- M シリーズ 
- Mv2 シリーズ 
- NDv4 シリーズ 
- NVv4 シリーズ

**OS のサポート**:
- Redhat Enterprise Linux 8.3
- SUSE 15 SP2
- Ubuntu 20.04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise
- Windows 10 (Enterprise マルチセッション)

**[地域]**: 
- 米国中南部
- 北ヨーロッパ

**価格**: 既存の VM の価格への追加料金はかかりません。

**次の機能は、このプレビューではサポートされていません**。
- Backup
- Azure Site Recovery
- 共有イメージ ギャラリー
- エフェメラル OS ディスク
- 共有ディスク
- マネージド イメージ
- Azure Dedicated Host 

## <a name="secure-boot"></a>セキュア ブート

トラステッド起動の中核を成すのは、VM のためのセキュア ブートです。 プラットフォームのファームウェアに実装されているこのモードにより、マルウェアベースのルートキットやブート キットがインストールされるのを防ぎます。 セキュア ブートの動作により、署名されたオペレーティング システムとドライバーだけでが起動できることが保証されます。 それにより、VM 上のソフトウェア スタックに対する "信頼のルート" が確立されます。 セキュア ブートが有効になっていると、すべての OS ブート コンポーネント (ブート ローダー、カーネル、カーネル ドライバー) は、信頼された発行元によって署名されている必要があります。 セキュア ブートは、Windows と一部の Linux ディストリビューションの両方でサポートされています。 信頼できる発行元によってイメージが署名されていることをセキュア ブートで認証できなかった場合、VM は起動を許可されません。 詳細については、「[セキュア ブート](/windows-hardware/design/device-experiences/oem-secure-boot)」を参照してください。

## <a name="vtpm"></a>vTPM

トラステッド起動では、Azure VM 用の vTPM も導入されています。 これは、TPM2.0 仕様に準拠した、ハードウェアの[トラステッド プラットフォーム モジュール](/windows/security/information-protection/tpm/trusted-platform-module-overview)の仮想化バージョンです。キーと測定のためのセキュリティで保護された専用のコンテナーとして機能します。 トラステッド起動から VM には、どの VM からも到達できないセキュリティ保護された環境で実行されている、独自の専用 TPM インスタンスが提供されます。 vTPM により、VM のブート チェーン全体 (UEFI、OS、システム、ドライバー) を測定することで、[構成証明](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation)が有効になります。 

トラステッド起動により、vTPM を使用して、クラウドによるリモート構成証明が実行されます。 これは、プラットフォームの正常性チェックと、信頼ベースの決定を行うために使用されます。 正常性チェックとして、トラステッド起動により、VM が正常に起動したことを暗号によって認定できます。 プロセスが失敗した場合、承認されていないコンポーネントが VM で実行されていることが原因の可能性があり、Azure Security Center によって整合性のアラートが発行されます。 そのアラートには、整合性チェックに合格しなかったコンポーネントの詳細が含まれます。

## <a name="virtualization-based-security"></a>仮想化ベースのセキュリティ

[仮想化ベースのセキュリティ](/windows-hardware/design/device-experiences/oem-vbs) (VBS) により、ハイパーバイザーを使用して、セキュリティで保護され、分離されたメモリ領域が作成されます。 Windows により、これらの領域を使用して、脆弱性や悪用に対する保護が強化されたさまざまなセキュリティ ソリューションが実行されます。 トラステッド起動を使用すると、Hypervisor Code Integrity (HVCI) と Windows Defender Credential Guard を有効にすることができます。

HVCI は、悪意のある、または検証されていないコードの注入や実行から Windows カーネル モード プロセスを保護する、システムの強力な軽減策です。 実行前にカーネル モードのドライバーとバイナリがチェックされ、署名されていないファイルがメモリに読み込まれるのを防ぎます。 これにより、そのような実行可能なコードを、読み込みを許可した後で変更することはできなくなります。 VBS と HVCI の詳細については、「[仮想化ベースのセキュリティ (VBS) と Hypervisor Enforced Code Integrity (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571)」を参照してください。

トラステッド起動と VBS を使用すると、Windows Defender Credential Guard を有効にすることができます。 この機能により、シークレットが分離されて保護され、特権のあるシステム ソフトウェアのみがそれらにアクセスできるようになります。 これは、Pass-the-Hash (PtH) 攻撃など、シークレットや資格情報の盗難攻撃に対する不正アクセスを防ぐのに役立ちます。 詳細については、[Credential Guard](/windows/security/identity-protection/credential-guard/credential-guard) に関する記事を参照してください。


## <a name="security-center-integration"></a>Security Center の統合

VM が正しく構成されていることを確認するため、トラステッド起動は Azure Security Center と統合されます。 Azure Security Center により、互換性のある VM が継続的に評価され、関連する推奨事項が発行されます。

- **セキュア ブートの有効化の推奨** - この推奨事項は、トラステッド起動をサポートする VM にのみ適用されます。 セキュア ブートを有効にできるのに無効になっている VM が、Azure Security Center によって特定されます。 それを有効にするように、重大度の低い推奨事項が発行されます。
- **vTPM の有効化の推奨** - VM で vTPM が有効になっている場合、Azure Security Center によるゲストの構成証明の実行と、高度な脅威のパターンの特定に、それを使用できます。 Azure Security Center により、トラステッド起動がサポートされていて vTPM が無効になっている VM が識別された場合、それを有効にするように、重大度の低い推奨事項が発行されます。 
- **構成証明の正常性の評価** - VM で vTPM が有効になっている場合、Azure Security Center の拡張機能により、VM が正常に起動したことをリモートで検証できます。 これはリモート構成証明と呼ばれます。 Azure Security Center により、リモート構成証明の状態を示す評価が発行されます。

## <a name="azure-defender-integration"></a>Azure Defender の統合

VM でトラステッド起動が適切に設定されている場合、Azure Defender により VM の正常性の問題を検出してアラートを発行できます。

- **VM 構成証明の失敗に関するアラート** - Azure Defender により、VM での構成証明が定期的に実行されます。 これは、VM が起動した後にも発生します。 構成証明が失敗した場合、中程度の重大度のアラートがトリガーされます。
    VM の構成証明は、次の理由により失敗する可能性があります。
    - 構成証明の対象の情報 (ブート ログを含む) が、信頼されたベースラインから逸脱している。 これは、信頼されていないモジュールが読み込まれ、OS が侵害される可能性があることを示している場合があります。
    - 構成証明クォートが、構成証明対象の VM の vTPM からのものであることを確認できなかった。 これは、マルウェアが存在し、vTPM へのトラフィックを傍受している可能性があることを示します。
    - VM 上の構成証明拡張機能が応答していない。 これは、マルウェアまたは OS 管理者によるサービス拒否攻撃を示している可能性があります。

    > [!NOTE]
    >  このアラートは、vTPM が有効になっていて構成証明拡張機能がインストールされている VM で使用できます。 構成証明を成功させるには、セキュア ブートを有効にする必要があります。 セキュア ブートが無効になっている場合、構成証明は失敗します。 セキュア ブートを無効にする必要がある場合は、このアラートを抑制して擬陽性を回避することができます。

- **信頼されていない Linux カーネル モジュールのアラート** - セキュア ブートが有効になっているトラステッド起動では、カーネル ドライバーが検証に失敗し、読み込みが禁止されている場合でも、VM を起動できます。 これが発生すると、Azure Defender により重大度の低いアラートが発行されます。 信頼されていないドライバーが読み込まれないため、すぐに脅威が発生することはありませんが、これらのイベントは調査する必要があります。 以下、具体例に沿って説明します。
    - どのカーネル ドライバーが失敗したか。 このドライバーのことをよく知っていて、それが読み込まれることを期待しているか。
    - これは想定されているドライバーの正確なバージョンか。 ドライバー バイナリは完全か。 サードパーティ製のドライバーの場合、ベンダーは OS 準拠テストに合格して署名を取得したか。


## <a name="faq"></a>よく寄せられる質問

トラステッド起動に関してよく寄せられる質問。

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>なぜトラステッド起動を使用する必要があるのですか? トラステッド起動によって何から保護されるのですか?

トラステッド起動により、ブート キット、ルートキット、カーネルレベルのマルウェから保護されます。 これらの高度な種類のマルウェアはカーネル モードで動作し、ユーザーからは見えない状態のままです。 次に例を示します。
- ファームウェア ルートキット: これらのキットによって仮想マシンの BIOS のファームウェアが上書きされ、OS より前にルートキットが起動できるようになります。 
- ブート キット: これらのキットによって OS のブートローダーが置き換えられて、仮想マシンにより OS より前にブート キットが読み込まれます。
- カーネル ルートキット: これらのキットによって OS カーネルの一部が置き換えられ、OS の読み込み時にルートキットが自動的に開始できるようになります。
- ドライバー ルートキット: これらのキットが、OS によって使用される信頼されたドライバーの 1 つとして扱われ、仮想マシンのコンポーネントと通信します。

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>セキュア ブートとメジャー ブートの違いは何ですか?

セキュア ブート チェーンでは、ブート プロセスの各ステップにおいて、後続の手順である暗号化署名が確認されます。 たとえば、BIOS によってローダーの署名が確認され、ローダーによって読み込まれるすべてのカーネル オブジェクトの署名が確認されます。 いずれかのオブジェクトが侵害されている場合、署名は一致せず、VM は起動しません。 詳細については、「[セキュア ブート](/windows-hardware/design/device-experiences/oem-secure-boot)」を参照してください。 メジャー ブートの場合、ブート プロセスは停止されません。チェーン内の次のオブジェクトのハッシュが測定または計算されて、vTPM 上のプラットフォーム構成レジスタ (PCR) にハッシュが格納されます。 ブートの整合性の監視には、メジャー ブート レコードが使用されます。

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>整合性エラーが検出されるとどうなりますか?

Azure Virtual Machines 用のトラステッド起動は、高度な脅威について監視されます。 そのような脅威が検出されると、アラートがトリガーされます。 アラートは、Azure Security Center の [Standard レベル](../security-center/security-center-pricing.md)でのみ利用できます。
Azure Security Center により、定期的に構成証明が実行されます。 構成証明が失敗した場合、中程度の重大度のアラートがトリガーされます。 トラステッド起動の構成証明は、次の理由により失敗する可能性があります。 
- 構成証明の対象の情報 (トラステッド コンピューティング ベース (TCB) のログを含む) が、信頼されたベースライン (セキュア ブートが有効になっている場合など) から逸脱している。 これは、信頼されていないモジュールが読み込まれ、OS が侵害される可能性があることを示している場合があります。
- 構成証明クォートが、構成証明対象の VM の vTPM からのものであることを確認できなかった。 これは、マルウェアが存在し、TPM へのトラフィックを傍受している可能性があることを示します。 
- VM 上の構成証明拡張機能が応答していない。 これは、マルウェアまたは OS 管理者によるサービス拒否攻撃を示している可能性があります。

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>トラステッド起動は、Hyper-V シールドされた VM とどのように違いますか?
Hyper-V シールドされた VM は、現在、Hyper-V でのみ使用できます。 [Hyper-V シールドされた VM](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) は、通常、保護されたファブリックと共にデプロイされます。 保護されたファブリックは、ホスト ガーディアン サービス (HGS)、1 つ以上の保護されたホスト、シールドされた VM のセットで構成されます。 Hyper-V シールドされた VM は、ファブリック管理者と、Hyper-V ホスト上で実行される可能性がある信頼されていないソフトウェアの両方から、仮想マシンのデータと状態を保護する必要があるファブリックで使用されることが意図されています。 一方、トラステッド起動は、HGS のデプロイと管理を追加せずに、スタンドアロン仮想マシンまたは仮想マシン スケール セットとして Azure にデプロイできます。 トラステッド起動のすべての機能は、デプロイ コードの簡単な変更、または Azure portal のチェック ボックスで有効にすることができます。  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>既存の VM をトラステッド起動に変換するにはどうすればよいですか?
第 2 世代 VM の場合、トラステッド起動に変換する移行パスは、一般提供 (GA) の対象になっています。

## <a name="next-steps"></a>次のステップ

[ポータルを使用してトラステッド起動 VM](trusted-launch-portal.md) をデプロイします。
