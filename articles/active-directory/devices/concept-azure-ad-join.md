---
title: Azure AD 参加済みデバイスとは
description: Azure AD 参加済みデバイスについて説明し、デバイス ID 管理を活用して、環境内のリソースにアクセスしているデバイスを管理する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b7cc0535549f2dbcdef4ab043ee506527fdbc5f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "90601454"
---
# <a name="azure-ad-joined-devices"></a>Azure AD 参加済みデバイス

Azure AD 参加は、クラウド優先またはクラウド専用であることを望む組織を対象にしています。 すべての組織で、サイズや業界に関係なく、Azure AD 参加済みデバイスをデプロイすることができます。 Azure AD 参加は、ハイブリッド環境でも動作し、クラウドおよびオンプレミス アプリとリソースと両方にアクセスできます。

| Azure AD Join | 説明 |
| --- | --- |
| **定義** | デバイスにサインインするための組織アカウントを必要とする Azure AD にのみ参加します |
| **主な対象** | クラウド専用およびハイブリッド組織の両方に適しています。 |
|   | 組織内のすべてのユーザーに適用できます |
| **デバイスの所有権** | Organization |
| **オペレーティング システム** | Windows 10 Home を除くすべての Windows 10 デバイス |
|   | [Azure で実行する Windows Server 2019 Virtual Machines](howto-vm-sign-in-azure-ad-windows.md) (Server Core はサポートされません) |
| **プロビジョニング** | セルフサービス:Windows OOBE または設定 |
|   | 一括登録 |
|   | Windows Autopilot |
| **デバイスのサインイン オプション** | 以下を使用する組織アカウント: |
|   | Password |
|   | Windows Hello for Business |
|   | FIDO2.0 セキュリティ キー (プレビュー) |
| **デバイス管理** | モバイル デバイス管理 (例:Microsoft Intune) |
|   | Microsoft Intune および Microsoft Endpoint Configuration Manager との共同管理 |
| **主な機能** | クラウドとオンプレミスの両方のリソースへの SSO |
|   | MDM 登録と MDM コンプライアンス評価を使用する条件付きアクセス |
|   | ロック画面でのセルフサービス パスワード リセット および Windows Hello PIN のリセット |
|   | デバイス間での Enterprise State Roaming |

Azure AD 参加済みデバイスには、組織の Azure AD アカウントを使用してサインインします。 組織内のリソースへのアクセスは、その Azure AD アカウントと、デバイス ID に適用される[条件付きアクセス ポリシー](../conditional-access/howto-conditional-access-policy-compliant-device.md)に基づいてさらに制限することができます。

管理者は、Microsoft Intune などの Mobile Device Management (MDM) ツールを使用するか、Microsoft Endpoint Configuration Manager を使用する共同管理シナリオで、Azure AD 登録済みデバイスをセキュリティで保護し、さらに制御することができます。 これらのツールでは、ストレージの暗号化、パスワードの複雑さ、ソフトウェアのインストール、およびソフトウェアの更新を必要とするなど、組織に必要な構成を適用する手段が提供されます。 管理者は、Configuration Manager を利用して Azure AD 参加済みデバイスで組織のアプリケーションを利用できるようにし、[ビジネス向けおよび教育機関向けの Microsoft Store からのアプリを管理する](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business)ことができます。

Azure AD 参加は、Out of Box Experience (OOBE)、一括登録、または [Windows Autopilot](/intune/enrollment-autopilot) などのセルフサービス オプションを使用して実行できます。

Azure AD 参加済みデバイスでは、オンプレミス リソースが組織のネットワーク上にある場合、引き続きそれらへのシングル サインオン アクセスを維持できます。 Azure AD 参加済みであるデバイスは引き続き、ファイル、印刷、およびその他のアプリケーションなどのオンプレミス サーバーに対して認証することができます。

## <a name="scenarios"></a>シナリオ

Azure AD への参加は、主にオンプレミスの Windows Server Active Directory インフラストラクチャを持っていない組織向けですが、次のようなシナリオで確実に使用できます。

- Azure AD および Intune などの MDM を使用してクラウド ベースのインフラストラクチャに移行しようと考えています。
- たとえばタブレットや電話などのモバイル デバイスを管理する必要があるが、オンプレミスのドメインへの参加を使用できない。
- ユーザーが主に必要としているのは、Microsoft 365 や Azure AD に統合されているその他の SaaS アプリにアクセスすることである。
- ユーザーのグループを Active Directory ではなく Azure AD で管理したい。 このシナリオは、季節従業員、請負業者、学生などに適用できます。
- リモートの支店の作業員に制限のあるオンプレミスのインフラに対する参加機能を提供したい。

Windows 10 Home を除き、すべての Windows 10 デバイスで Azure AD 参加済みデバイスを構成できます。

Azure AD 参加済みデバイスの目的は、次の操作を単純化することです。

- 職場所有のデバイスの Windows でのデプロイ
- 任意の Windows デバイスからの組織のアプリとリソースへのアクセス
- 作業が所有するデバイスの、クラウド ベースの管理
- ユーザーが、Azure AD または同期された Active Directory の職場あるいは学校アカウントを使用して、デバイスにサインインする。

![Azure AD 参加済みデバイス](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD Join は、次の方法のいずれかを使用して展開できます：

- [Windows Auto pilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Bulk の展開](/intune/windows-bulk-enroll)
- [セルフ サービス エクスペリエンス](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>次のステップ

- [Azure AD 参加の実装を計画する](azureadjoin-plan.md)
- [Azure AD 参加済みデバイスのローカル管理者グループの管理方法](assign-local-admin.md)
- [Azure portal を使用してデバイス ID を管理する](device-management-azure-portal.md)
- [Azure AD で古いデバイスを管理する](manage-stale-devices.md)
