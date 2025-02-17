---
title: Azure AD Identity Protection でリスクを修復してユーザーをブロック解除する
description: アクティブなリスク検出をクローズすためのオプションについて説明します。
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 01/25/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e322f78468c89a549955a01f73952e8cde7a13c8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872826"
---
# <a name="remediate-risks-and-unblock-users"></a>リスクを修復してユーザーをブロック解除する

[調査](howto-identity-protection-investigate-risk.md)が完了したら、リスクを修復するか、ユーザーをブロック解除するアクションを実行できます。 組織には、[リスク ポリシー](howto-identity-protection-configure-risk-policies.md)を使用して、自動修復を有効にするというオプションもあります。 組織は、組織が適切と考える期間内に、提示されたすべてのリスク検出をクローズすることを試みる必要があります。 Microsoft では、リスクの処理では時間が重要であるため、できるだけ早くイベントをクローズすることをお勧めしています。

## <a name="remediation"></a>Remediation

すべてのアクティブなリスク検出は、ユーザーのリスク レベルと呼ばれる値の計算に影響します。 ユーザーのリスク レベルは、アカウントが侵害されている確率のインジケーター (低、中、高) です。 管理者は、すべてのリスク検出をクローズして、影響を受けるユーザーが危険な状態ではなくなるようにします。

一部のリスク検出は、Identity Protection によってリスクではなくなったと判断されたため、[クローズ (システム)] とマークされる場合があります。

管理者には、修復するための次のオプションがあります。

- リスク ポリシーを使用した自己修復
- パスワードの手動リセット
- ユーザー リスクを無視する
- 個々のリスク検出を手動でクローズする

### <a name="self-remediation-with-risk-policy"></a>リスク ポリシーを使用した自己修復

リスク ポリシーで Azure AD Multi-Factor Authentication (MFA) とセルフサービス パスワード リセット (SSPR) を使用して自己修復を許可した場合、リスクが検出されたときに、ユーザーが自分自身をブロック解除できます。 これらの検出は、クローズされたとみなされます。 リスクが検出されたときに Azure AD MFA と SSPR を使用するには、ユーザーが登録済みである必要があります。

一部の検出では、ユーザーの自己修復を必要とするレベルのリスクが発生しないことありますが、管理者はこれらの検出も評価する必要があります。 管理者は、[特定の場所からのアクセスのブロック](../conditional-access/howto-conditional-access-policy-location.md)やポリシーで許容されるリスクの低減などの追加の対策が必要であると判断できます。

### <a name="manual-password-reset"></a>パスワードの手動リセット

ユーザー リスク ポリシーを使用したパスワード リセットの要求を選択しない場合、管理者は、手動によるパスワードのリセットを使用して、ユーザーのすべてのリスク検出をクローズすることができます。

管理者には、ユーザーのパスワードをリセットするときに 2 つのオプションがあります。

- **一時パスワードを生成する** - 一時パスワードを生成することによって、ID をすぐに安全な状態に戻すことができます。 この方法では、影響を受けるユーザーが一時的なパスワードを知る必要があるために、ユーザーと接触する必要があります。 パスワードは一時的なので、ユーザーは次回サインイン時にパスワードを何か新しいものに変更することを求められます。

- **ユーザーにパスワードをリセットするよう要求する** - ユーザーにパスワードをリセットするよう要求すると、ヘルプ デスクや管理者に連絡せずに自己復旧することができます。 この方法は、Azure AD MFA と SSPR に登録されているユーザーにのみ適用されます。 まだ登録されていないユーザーの場合、このオプションは使用できません。

### <a name="dismiss-user-risk"></a>ユーザー リスクを無視する

ユーザーが削除されているなどの理由でパスワードのリセットを選択できない場合は、ユーザー リスク検出を無視することを選択できます。

**[ユーザー リスクを無視する]** をクリックすると、すべてのイベントがクローズされ、影響を受けるユーザーは危険な状態ではなくなります。 ただし、この方法は既存のパスワードには影響しないため、関連する ID は安全な状態に戻りません。 

### <a name="close-individual-risk-detections-manually"></a>個々のリスク検出を手動でクローズする

個々のリスク検出を手動でクローズすることができます。 リスク検出を手動でクローズすることで、ユーザーのリスク レベルを低くすることができます。 通常、リスク検出は、関連する調査に応じて手動でクローズされます。 たとえば、ユーザーに伝えることで、アクティブなリスク検出が不要となったことがわかったときです。 
 
リスク検出を手動でクローズする場合は、リスク検出の状態を変更する次のいずれかの操作を実行できます。

- ユーザーの侵害を確認
- ユーザー リスクを無視する
- サインインを安全と確認
- サインインを侵害ありと確認

## <a name="unblocking-users"></a>ユーザーのブロック解除

管理者は、リスク ポリシーまたは調査に基づいて、サインインをブロックすることを選択できます。 ブロックは、サインイン リスクまたはユーザー リスクに基づいて発生させることができます。

### <a name="unblocking-based-on-user-risk"></a>ユーザー リスクに基づくブロック解除

ユーザー リスクによってブロックされたアカウントをブロック解除する場合、管理者には、次のオプションがあります。

1. **パスワードをリセットする** - ユーザーのパスワードをリセットすることができます。
1. **ユーザー リスクを無視する** - アクセスをブロックするために構成されたユーザー リスク レベルに到達した場合、ユーザー リスク ポリシーによってユーザーがブロックされます。 ユーザー リスクを無視するか、報告されたリスク検出を手動でクローズすることで、ユーザーのリスク レベルを下げることができます。
1. **ポリシーからユーザーを除外する** - サインイン ポリシーの現在の構成が原因で特定のユーザーに問題が発生していると考えられる場合は、そのポリシーからユーザーを除外できます。 詳細については、記事「[方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md#exclusions)」を参照してください。
1. **ポリシーを無効にする** - ポリシーの構成が原因ですべてのユーザーに問題が発生していると考えられる場合は、ポリシーを無効にすることができます。 詳細については、記事「[方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md)」を参照してください。

### <a name="unblocking-based-on-sign-in-risk"></a>サインイン リスクに基づくブロック解除

サインイン リスクに基づいてアカウントをブロック解除する場合、管理者には、次のオプションがあります。

1. **よく使用する場所やデバイスからサインインする** - 不審なサインインがブロックされる一般的な理由は、使用頻度の低い場所やデバイスからのサインイン試行にあります。 ユーザーは、よく使用する場所またはデバイスからのサインインを試みることで、これがブロックの理由であるかどうかをすぐに確認できます。
1. **ポリシーからユーザーを除外する** - サインイン ポリシーの現在の構成が原因で特定のユーザーに問題が発生していると考えられる場合は、そのポリシーからユーザーを除外できます。 詳細については、記事「[方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md#exclusions)」を参照してください。
1. **ポリシーを無効にする** - ポリシーの構成が原因ですべてのユーザーに問題が発生していると考えられる場合は、ポリシーを無効にすることができます。 詳細については、記事「[方法: リスク ポリシーを構成して有効にする](howto-identity-protection-configure-risk-policies.md)」を参照してください。

## <a name="powershell-preview"></a>PowerShell プレビュー

Microsoft Graph PowerShell SDK プレビュー モジュールを使用することにより、組織は PowerShell を使ってリスクを管理できます。 プレビュー モジュールとサンプル コードは、[Azure AD GitHub リポジトリ](https://github.com/AzureAD/IdentityProtectionTools)にあります。 

リポジトリに含まれる `Invoke-AzureADIPDismissRiskyUser.ps1` スクリプトを使用すると、組織のディレクトリ内の危険なユーザーをすべて無視できます。

## <a name="next-steps"></a>次の手順

Azure AD Identity Protection の概要を入手するには、[Azure AD Identity Protection の概要](overview-identity-protection.md)に関するページを参照してください。
