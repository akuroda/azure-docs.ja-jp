---
title: Azure Active Directory におけるアプリに対する SAML ベースのシングル サインオン (SSO) について理解する
description: Azure Active Directory におけるアプリに対する SAML ベースのシングル サインオン (SSO) について理解する
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/28/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: f6422c3f751a7aaa430b93ee68ca1a3520ac915f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99257694"
---
# <a name="understand-saml-based-single-sign-on"></a>SAML ベースのシングル サインオンについて理解する

アプリケーション管理の[クイックスタート シリーズ](view-applications-portal.md)では、アプリケーションの ID プロバイダー (IdP) として Azure AD を使用する方法について学習しました。 この記事では、シングル サインオンの SAML ベースのオプションについて詳しく説明します。 


## <a name="before-you-begin"></a>開始する前に

Azure AD を ID プロバイダー (IdP) として使用し、シングル サインオン (SSO) を構成することは、使用するアプリケーションに応じて単純な場合もあれば複雑な場合もあります。 一部のアプリケーションは、わずかのアクションで構成できます。 詳細な構成が必要なものもあります。 短時間で理解するには、アプリケーション管理に関する[クイックスタート シリーズ](view-applications-portal.md)を参照してください。 追加しようとしているアプリケーションが単純なものであれば、おそらくこの記事を読む必要はありません。 追加しようとしているアプリケーションで SAML ベースの SSO のカスタム構成が必要な場合は、この記事をお読みください。

[クイックスタート シリーズ](add-application-portal-setup-sso.md)には、シングル サインオンの構成に関する記事があります。 そこでは、アプリの SAML 構成ページにアクセスする方法について説明しています。 SAML 構成ページには 5 つのセクションがあります。 これらのセクションについては、この記事で詳しく説明します。

> [!IMPORTANT] 
> 一部のシナリオでは、**エンタープライズ アプリケーション** 内のアプリケーションのナビゲーションに **シングル サインオン** オプションが表示されません。 
>
> **アプリの登録** を使用してアプリケーションが登録された場合、既定では、OIDC OAuth を使用するようにシングル サインオン機能が構成されます。 この場合、 **[シングル サインオン]** オプションは、 **[エンタープライズ アプリケーション]** の下のナビゲーションに表示されません。 **アプリの登録** を使用してカスタム アプリを追加する場合は、マニフェスト ファイルでオプションを構成します。 マニフェスト ファイルの詳細については、「[Azure Active Directory のアプリ マニフェスト](../develop/reference-app-manifest.md)」を参照してください。 SSO 標準の詳細については、「[Microsoft ID プラットフォームを使用した認証と承認](../develop/authentication-vs-authorization.md#authentication-and-authorization-using-the-microsoft-identity-platform)」を参照してください。 
>
> ナビゲーションに **[シングル サインオン]** が表示されないその他のシナリオには、アプリケーションが別のテナントでホストされている場合や、アカウントに必要なアクセス許可 (グローバル管理者、クラウド アプリケーション管理者、アプリケーション管理者、またはサービス プリンシパルの所有者) がない場合などがあります。 アクセス許可によっては、 **[シングル サインオン]** を開くことができるが、保存できないシナリオが発生する場合もあります。 Azure AD の管理者ロールの詳細については、(https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) を参照してください。


## <a name="basic-saml-configuration"></a>基本的な SAML 構成

これらの値は、アプリケーション ベンダーから取得する必要があります。 値を手動で入力するか、メタデータ ファイルをアップロードしてフィールドの値を抽出できます。

> [!TIP]
> 多くのアプリは、既に Azure AD で動作するよう事前に構成されています。 これらのアプリは、アプリを Azure AD テナントに追加するときに参照できるアプリ ギャラリーに一覧表示されています。 [クイックスタート シリーズ](add-application-portal-setup-sso.md)では、このプロセスについて説明しています。 ギャラリー内のアプリには、詳細で段階的な手順が用意されています。 この手順にアクセスするには、クイックスタート シリーズで説明されているアプリの SAML 構成ページのリンクをクリックします。または、[SaaS アプリ構成チュートリアル](../saas-apps/tutorial-list.md)で、すべてのアプリ構成チュートリアルの一覧を参照することもできます。

| [基本的な SAML 構成] の設定 | SP-Initiated | idP-Initiated | 説明 |
|:--|:--|:--|:--|
| **識別子 (エンティティ ID)** | 一部のアプリでは必須 | 一部のアプリでは必須 | アプリケーションを一意に識別します。 Azure AD から ID が SAML トークンの Audience パラメーターとしてアプリケーションに送信されます。 アプリケーションではこの ID を検証する必要があります。 また、この値はアプリケーションによって提供される SAML メタデータ内に Entity ID として表示されます。 "'https://<subdomain>.contoso.com' *この値は、アプリケーションから送信された **AuthnRequest** (SAML 要求) の **Issuer** 要素として見つけることができます。"* パターンを使用する URL を入力します。 |
| **応答 URL** | 必須 | 必須 | アプリケーションが SAML トークンを受け取ることになっている場所を指定します。 応答 URL は Assertion Consumer Service (ACS) URL とも呼ばれています。 追加の応答 URL フィールドを使用して、複数の応答 URL を指定できます。 たとえば複数のサブドメインで、追加の応答 URL が必要となります。 またはテスト目的で、複数の応答 URL (ローカル ホストとパブリック URL) を一度に指定できます。 |
| **サインオン URL** | 必須 | 指定しません | この URL をユーザーが開くと、サービス プロバイダーは、ユーザーの認証とサインインを行う Azure AD にそのユーザーをリダイレクトします。 Azure AD はその URL を使用して Microsoft 365 または Azure AD マイ アプリからアプリケーションを起動します。 何も入力されていない場合は、ユーザーが Microsoft 365、Azure AD マイ アプリ、または Azure AD SSO URL からアプリケーションを起動したときに、Azure AD により IdP-Initiated のサインオンが実行されます。|
| **リレー状態** | オプション | オプション | 認証が完了した後にユーザーをリダイレクトする場所をアプリケーションに指示します。 通常、値はアプリケーションで有効な URL です。 ただし、一部のアプリケーションでは、このフィールドを異なる方法で使用します。 詳細については、アプリケーションのベンダーに問い合わせてください。
| **ログアウト URL** | 省略可能 | 省略可能 | SAML ログアウト応答をアプリケーションに返送するために使用します。

## <a name="user-attributes-and-claims"></a>ユーザー属性と要求 

アプリケーションに対するユーザーの認証時に、Azure AD は、ユーザーを一意に識別する情報 (要求) を伴う SAML トークンをアプリケーションに発行します。 既定では、この情報には、ユーザーのユーザー名、電子メール アドレス、名、および姓が含まれます。 たとえば、特定の要求値またはユーザー名以外の **名前** 形式をアプリケーションが必要とする場合に、これらの要求をカスタマイズする必要があります。 

> [!IMPORTANT]
> 多くのアプリは既に事前構成されて、アプリ ギャラリーに含まれているため、ユーザーとグループの要求の設定について心配する必要はありません。 [クイックスタート シリーズ](add-application-portal.md)では、アプリを追加して構成する手順について説明しています。


**[一意のユーザー識別子 (名前 ID)]** の識別子の値は、必須の要求であり、重要です。 既定値は *user.userprincipalname* です。 アプリケーション内の各ユーザーは、ユーザー識別子によって一意に識別されます。 たとえば、メール アドレスがユーザー名と一意識別子を兼ねている場合、この値を *user.mail* に設定します。

SAML 要求のカスタマイズの詳細については、「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](../develop/active-directory-saml-claims-customization.md)」を参照してください。

新しい要求を追加できます。詳細については、「[アプリケーション固有の要求の追加](../develop/active-directory-saml-claims-customization.md#adding-application-specific-claims)」を参照してください。グループ要求を追加するには、[グループ要求の構成](../hybrid/how-to-connect-fed-group-claims.md)に関する記事を参照してください。


> [!NOTE]
> Azure AD からお使いのアプリケーションへの SAML トークンをカスタマイズするその他の方法については、次のリソースを参照してください。
>- Azure portal でカスタム ロールを作成するには、[ロール要求の構成](../develop/active-directory-enterprise-app-role-management.md)に関するページを参照してください。
>- PowerShell を使用して要求をカスタマイズするには、[要求のカスタマイズ (PowerShell)](../develop/active-directory-claims-mapping.md) に関するページを参照してください。
>- お使いのアプリケーション向けに省略可能な要求を構成するようアプリケーション マニフェストを変更するには、[省略可能な要求の構成](../develop/active-directory-optional-claims.md)に関するページを参照してください。
>- 更新トークン、アクセス トークン、セッション トークン、ID トークンのトークンの有効期間ポリシーを設定するには、[トークンの有効期間の構成](../develop/active-directory-configurable-token-lifetimes.md)に関するページを参照してください。 また、Azure AD の条件付きアクセスを使用して認証セッションを制限するには、[認証セッションの管理機能](../conditional-access/howto-conditional-access-session-lifetime.md)に関するページを参照してください。

## <a name="saml-signing-certificate"></a>SAML 署名証明書

Azure AD では、アプリケーションに送信する SAML トークンの署名に証明書を使用します。 この証明書を使用して、Azure AD とアプリケーション間の信頼を構成する必要があります。 証明書の形式の詳細については、アプリケーションの SAML ドキュメントを参照してください。 詳細については、[フェデレーション シングル サインオンの証明書の管理](manage-certificates-for-federated-single-sign-on.md)と[SAML トークンの詳細な証明書署名オプション](certificate-signing-options.md)に関するページを参照してください。

> [!IMPORTANT]
> 多くのアプリは既に事前構成されて、アプリ ギャラリーに含まれているため、証明書を確認する必要はありません。 [クイックスタート シリーズ](add-application-portal.md)では、アプリを追加して構成する手順について説明しています。

Azure AD の **[SAML によるシングル サインオンのセットアップ]** メイン ページから直接、Base64 または未加工形式でアクティブな証明書をダウンロードできます。 また、アクティブな証明書は、アプリケーションのメタデータ XML ファイルをダウンロードするか、アプリのフェデレーション メタデータ URL を使用して取得することもできます。 証明書 (アクティブまたは非アクティブ) を表示、作成、またはダウンロードするには、次の手順を実行します。

証明書を検証するための一般的な確認事項としては、次のようなものがあります。 
   - "*正しい有効期限。* " 最大 3 年間の有効期限を構成できます。
   - "*適切な証明書の状態がアクティブになっていること。* " 状態が **非アクティブ** の場合は、**アクティブ** に変更します。 状態を変更するには、目的の証明書の行を右クリックし、 **[証明書をアクティブにする]** を選択します。
   - *適切な署名オプションおよびアルゴリズム。*
   - *適切な通知用メール アドレス。* アクティブな証明書の有効期限が近づくと、Azure AD は、このフィールドに構成されている電子メール アドレスに通知を送信します。

場合によっては、証明書のダウンロードが必要になることがあります。 その場合は、保存する場所に注意してください。 証明書をダウンロードするには、Base64 形式、未加工の形式、フェデレーション メタデータ XML のいずれかのオプションを選択します。 また、Azure AD では、アプリケーション固有のメタデータにアクセスできる **アプリのフェデレーション メタデータ URL** が `https://login.microsoftonline.com/<Directory ID>/federationmetadata/2007-06/federationmetadata.xml?appid=<Application ID>` 形式で提供されます。

> [!NOTE]
> アプリケーションによって、 https://login.microsoftonline.com/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={app-id} を使用するときに表示される XML に存在するバイト オーダー マーカーが処理される必要があります。 バイト オーダー マークは、印字できない ASCII 文字 »¿ として表され、16 進数では XML データを確認するときに EF BB BF として表されます。

証明書の変更を行うには、[編集] ボタンを選択します。 **[SAML 署名証明書]** ページでは、次の操作を実行できます。
   - 新しい証明書を作成する: **[新しい証明書]** を選択し、 **[有効期限]** を選択してから、 **[保存]** を選択します。 証明書をアクティブ化するには、コンテキスト メニュー ( **...** ) を選択し、 **[証明書をアクティブにする]** を選択します。
   - 秘密キーと pfx の資格情報を含む証明書をアップロードする: **[証明書のインポート]** を選択し、その証明書を参照します。 **[PFX パスワード]** を入力し、 **[追加]** を選択します。  
   - 詳細な証明書署名を構成します。 これらのオプションの詳細については、[詳細な証明書署名オプション](certificate-signing-options.md)に関する記事を参照してください。
   - アクティブな証明書の有効期限が近づいたときに他のユーザーに通知する: **[通知の電子メール アドレス]** フィールドにメール アドレスを入力します。

## <a name="set-up-the-application-to-use-azure-ad"></a>Azure AD を使用するようにアプリケーションを設定する

**[\<applicationName> のセットアップ]** セクションには、Azure AD が SAML ID プロバイダーとして使用されるようにアプリケーションで構成する必要のある値の一覧が表示されます。 値は、アプリケーションの Web サイトの [構成] ページで設定します。 たとえば、GitHub を設定する場合は、github.com サイトにアクセスして値を構成します。 アプリケーションが既に事前構成されていて、Azure AD ギャラリーに含まれている場合は、 **[ステップ バイ ステップの手順を表示]** のリンクがあります。 そうでない場合は、構成するアプリケーションのドキュメントを見つける必要があります。 

**[ログイン URL]** と **[ログアウト URL]** の値はどちらも、同じエンドポイント (Azure AD テナントの SAML 要求処理エンドポイント) に解決されます。 

**Azure AD 識別子** は、アプリケーションに発行された SAML トークンでの **[発行者]** の値です。

## <a name="test-single-sign-on"></a>シングル サインオンのテスト

SAML ベースの ID プロバイダーとして Azure AD を使用するようにアプリケーションを構成し終えたら、設定をテストし、ご利用のアカウントでシングル サインオンが機能するかどうかを確認できます。 

**[テスト]** を選択し、現在サインインしているユーザーで、または他のユーザーとしてテストすることを選択します。 

サインオンに成功した場合は、SAML アプリケーションにユーザーとグループを割り当てることができます。 お疲れさまでした。

エラー メッセージが表示されたら、次の手順を完了してください。

1. 詳細をコピーして **[エラーの説明]** ボックスに貼り付けます。

    ![解決ガイダンスを入手する](media/configure-single-sign-on-non-gallery-applications/error-guidance.png)

2. **[解決ガイダンスを入手する]** を選択します。 根本原因と解決ガイダンスが表示されます。  この例では、アプリケーションにユーザーが割り当てられていませんでした。

3. 解決ガイダンスを読み、問題の修正を試みます。

4. 成功するまでテストを再実行します。

詳細については、「[Azure Active Directory のアプリケーションに対する SAML に基づいたシングル サインオンをデバッグする](./debug-saml-sso-issues.md)」を参照してください。


## <a name="next-steps"></a>次のステップ

- [アプリケーション管理のクイックスタート シリーズ](view-applications-portal.md)
- [アプリケーションにユーザーまたはグループを割り当てる](./assign-user-or-group-access-portal.md)
- [自動ユーザー アカウント プロビジョニングを構成する](../app-provisioning/configure-automatic-user-provisioning-portal.md)
- [シングル サインオンの SAML プロトコル](../develop/single-sign-on-saml-protocol.md)
