---
title: チュートリアル:Azure Active Directory と Evidence.com の統合 | Microsoft Docs
description: Azure Active Directory と Evidence.com の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/24/2020
ms.author: jeedes
ms.openlocfilehash: ff77ace78dd3cdd4ee942308432795421f6da997
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92453798"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evidencecom"></a>チュートリアル:Azure Active Directory シングル サインオン (SSO) と Evidence.com の統合

このチュートリアルでは、Evidence.com と Azure Active Directory (Azure AD) を統合する方法について説明します。 Azure AD と Evidence.com を統合すると、次のことができます。

* Evidence.com にアクセスするユーザーを Azure AD で制御できます。
* ユーザーが自分の Azure AD アカウントを使用して Evidence.com に自動的にサインインできるように設定できます。
* 1 つの中央サイト (Azure Portal) で自分のアカウントを管理します。

SaaS アプリと Azure AD の統合の詳細については、「[Azure Active Directory でのアプリケーションへのシングル サインオン](../manage-apps/what-is-single-sign-on.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始するには、次が必要です。

* Azure AD サブスクリプション。 サブスクリプションがない場合は、[無料アカウント](https://azure.microsoft.com/free/)を取得できます。
* Evidence.com でのシングル サインオン (SSO) が有効なサブスクリプション。

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD の SSO を構成してテストします。

* Evidence.com では、**SP** Initiated SSO がサポートされます
* Evidence.com を構成したら、組織の機密データを流出と侵入からリアルタイムで保護するセッション制御を適用することができます。 セッション制御は、条件付きアクセスを拡張したものです。 [Microsoft Cloud App Security でセッション制御を強制する方法](/cloud-app-security/proxy-deployment-any-app)をご覧ください。

## <a name="adding-evidencecom-from-the-gallery"></a>ギャラリーからの Evidence.com の追加

Azure AD への Evidence.com の統合を構成するには、管理対象の SaaS アプリの一覧にギャラリーから Evidence.com を追加する必要があります。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. 左のナビゲーション ウィンドウで **[Azure Active Directory]** サービスを選択します。
1. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** を選択します。
1. 新しいアプリケーションを追加するには、 **[新しいアプリケーション]** を選択します。
1. **[ギャラリーから追加する]** セクションで、検索ボックスに、「**Evidence.com**」と入力します。
1. 結果のパネルから **[Evidence.com]** を選択し、アプリを追加します。 お使いのテナントにアプリが追加されるのを数秒待機します。

## <a name="configure-and-test-azure-ad-single-sign-on-for-evidencecom"></a>Evidence.com の Azure AD シングル サインオンの構成とテスト

**B.Simon** というテスト ユーザーを使用して、Evidence.com に対する Azure AD SSO を構成してテストします。 SSO が機能するためには、Azure AD ユーザーと Evidence.com の関連ユーザーとの間にリンク関係を確立する必要があります。

Evidence.com で Azure AD SSO を構成してテストするには、次の構成要素を完了します。

1. **[Azure AD SSO の構成](#configure-azure-ad-sso)** - ユーザーがこの機能を使用できるようにします。
    1. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - B.Simon で Azure AD のシングル サインオンをテストします。
    1. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - B.Simon が Azure AD シングル サインオンを使用できるようにします。
1. **[Evidence.com の SSO の構成](#configure-evidencecom-sso)** - アプリケーション側でシングル サインオン設定を構成します。
    1. **[Evidence.com のテスト ユーザーの作成](#create-evidencecom-test-user)** - Evidence.com で B.Simon に対応するユーザーを作成し、Azure AD の B.Simon にリンクさせます。
1. **[SSO のテスト](#test-sso)** - 構成が機能するかどうかを確認します。

## <a name="configure-azure-ad-sso"></a>Azure AD SSO の構成

これらの手順に従って、Azure portal で Azure AD SSO を有効にします。

1. [Azure portal](https://portal.azure.com/) の **Evidence.com** アプリケーション統合ページで、 **[管理]** セクションを見つけて、 **[シングル サインオン]** を選択します。
1. **[シングル サインオン方式の選択]** ページで、 **[SAML]** を選択します。
1. **[SAML でシングル サインオンをセットアップします]** ページで、 **[基本的な SAML 構成]** の編集 (ペン) アイコンをクリックして設定を編集します。

   ![基本的な SAML 構成を編集する](common/edit-urls.png)

1. **[SAML でシングル サインオンをセットアップします]** ページで、次の手順を実行します。

    a. **[サインオン URL]** ボックスに、次のパターンを使用して URL を入力します。`https://<yourtenant>.evidence.com`

    b. **[識別子 (エンティティ ID)]** ボックスに、次のパターンを使用して URL を入力します。`https://<yourtenant>.evidence.com`

    c. **[応答 URL]** ボックスに、`https://<your tenant>.evidence.com/?class=UIX&proc=Login` のパターンを使用して URL を入力します。
    
    > [!NOTE]
    > これらは実際の値ではありません。 これらの値を実際のサインオン URL、識別子、および応答 URL で更新してください。 これらの値を取得するには、[Evidence.com クライアント サポート チーム](https://communities.taser.com/support/SupportContactUs?typ=LE)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

5. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

1. **[Evidence.com のセットアップ]** セクションで、要件に基づいて適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成

このセクションでは、Azure portal 内で B.Simon というテスト ユーザーを作成します。

1. Azure portal の左側のウィンドウから、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。
1. 画面の上部にある **[新しいユーザー]** を選択します。
1. **[ユーザー]** プロパティで、以下の手順を実行します。
   1. **[名前]** フィールドに「`B.Simon`」と入力します。  
   1. **[ユーザー名]** フィールドに「username@companydomain.extension」と入力します。 たとえば、「 `B.Simon@contoso.com` 」のように入力します。
   1. **[パスワードを表示]** チェック ボックスをオンにし、 **[パスワード]** ボックスに表示された値を書き留めます。
   1. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、B.Simon に Evidence.com へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** を選択します。
1. アプリケーションの一覧で **[Evidence.com]** を選択します。
1. アプリの概要ページで、 **[管理]** セクションを見つけて、 **[ユーザーとグループ]** を選択します。

   ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[ユーザーの追加] リンク](common/add-assign-user.png)

1. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧から **[B.Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。
1. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリックします。
1. **[割り当ての追加]** ダイアログで、 **[割り当て]** をクリックします。

## <a name="configure-evidencecom-sso"></a>Evidence.com の SSO の構成

1. 別の Web ブラウザー ウィンドウで、Evidence.com テナントに管理者としてサインインし、 **[Admin]\(管理\)** タブに移動します。

2. **[Agency Single Sign On] \(代理店のシングル サインオン)**

3. **[SAML Based Single Sign On] \(SAML ベースのシングル サインオン)**

4. Azure portal に表示される **[Azure AD 識別子]** 、 **[ログイン URL]** 、 **[ログアウト URL]** の値を、Evidence.com の対応するフィールドにコピーします。

5. ダウンロードした証明書 (Base64) ファイルをメモ帳で開き、その内容をクリップボードにコピーし、 **[Security Certificate]\(セキュリティ証明書\)** ボックスに貼り付けます。 

6. Evidence.com の構成を保存します。

### <a name="create-evidencecom-test-user"></a>Evidence.com のテスト ユーザーの作成

Azure AD ユーザーがサインインできるようにするには、ユーザーを Evidence.com アプリケーションにプロビジョニングする必要があります。 このセクションでは、Evidence.com で Azure AD ユーザー アカウントを作成する方法について説明します。

**Evidence.com でユーザー アカウントをプロビジョニングするには**

1. Web ブラウザー ウィンドウで、Evidence.com 企業サイトに管理者としてサインインします。

2. **[Admin] \(管理)** タブをクリックします。

3. **[Add User] \(ユーザーの追加)** をクリックします。

4. **[追加]** をクリックします。

5. 追加したユーザーの **[Email Address] \(電子メール アドレス)** が、アクセス権を付与する Azure AD 内のユーザーのユーザー名と一致する必要があります。 組織内でユーザー名と電子メール アドレスが同じ値でない場合は、Azure Portal の **[Evidence.com] > [属性] > [シングル サインオン]** セクションを使用して、Evidence.com に送信される nameidenitifer を電子メール アドレスに変更できます。

## <a name="test-sso"></a>SSO のテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネル上で [Evidence.com] タイルをクリックすると、SSO を設定した Evidence.com に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory でのアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)

- [Azure AD で Evidence.com を試す](https://aad.portal.azure.com/)

- [Microsoft Cloud App Security におけるセッション制御とは](/cloud-app-security/proxy-intro-aad)

- [高度な可視性と制御によって Evidence.com を保護する方法](/cloud-app-security/proxy-intro-aad)