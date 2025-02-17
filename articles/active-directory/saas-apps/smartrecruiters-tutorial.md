---
title: 'チュートリアル: Azure Active Directory と SmartRecruiters の統合 | Microsoft Docs'
description: Azure Active Directory と SmartRecruiters の間でシングル サインオンを構成する方法について説明します。
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: b7de874cc074faa63203c58638fb04cc5cb2282b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514930"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>チュートリアル: Azure Active Directory と SmartRecruiters の統合

このチュートリアルでは、SmartRecruiters と Azure Active Directory (Azure AD) を統合する方法について説明します。
SmartRecruiters と Azure AD の統合には、次の利点があります。

* SmartRecruiters にアクセスする Azure AD ユーザーを制御できます。
* ユーザーが自分の Azure AD アカウントで SmartRecruiters に自動的にサインイン (シングル サインオン) されるように設定できます。
* 1 つの中央サイト (Azure Portal) でアカウントを管理できます。

SaaS アプリと Azure AD の統合の詳細については、「 [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)」を参照してください。
Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

SmartRecruiters と Azure AD の統合を構成するには、次のものが必要です。

* Azure AD サブスクリプション。 Azure AD の環境がない場合は、[こちら](https://azure.microsoft.com/pricing/free-trial/)から 1 か月の評価版を入手できます
* SmartRecruiters でのシングル サインオンが有効なサブスクリプション

## <a name="scenario-description"></a>シナリオの説明

このチュートリアルでは、テスト環境で Azure AD のシングル サインオンを構成してテストします。

* SmartRecruiters では、**SP と IDP** によって開始される SSO がサポートされます

## <a name="adding-smartrecruiters-from-the-gallery"></a>ギャラリーから SmartRecruiters を追加する

Azure AD への SmartRecruiters の統合を構成するには、ギャラリーから管理対象 SaaS アプリの一覧に SmartRecruiters を追加する必要があります。

**ギャラリーから SmartRecruiters を追加するには、次の手順に従います。**

1. **[Azure Portal](https://portal.azure.com)** の左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** アイコンをクリックします。

    ![Azure Active Directory のボタン](common/select-azuread.png)

2. **[エンタープライズ アプリケーション]** に移動し、 **[すべてのアプリケーション]** オプションを選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

3. 新しいアプリケーションを追加するには、ダイアログの上部にある **[新しいアプリケーション]** をクリックします。

    ![[新しいアプリケーション] ボタン](common/add-new-app.png)

4. 検索ボックスに **「SmartRecruiters」** と入力し、結果ウィンドウで **[SmartRecruiters]** を選び、 **[追加]** をクリックして、アプリケーションを追加します。

     ![結果リストの SmartRecruiters](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成とテスト

このセクションでは、**Britta Simon** というテスト ユーザーに基づいて、SmartRecruiters で Azure AD のシングル サインオンを構成し、テストします。
シングル サインオンを機能させるには、Azure AD ユーザーと SmartRecruiters 内の関連ユーザーとの間にリンク関係が確立されている必要があります。

SmartRecruiters で Azure AD のシングル サインオンを構成してテストするには、次の構成要素を完了する必要があります。

1. **[Azure AD シングル サインオンの構成](#configure-azure-ad-single-sign-on)** - ユーザーがこの機能を使用できるようにします。
2. **[SmartRecruiters のシングル サインオンの構成](#configure-smartrecruiters-single-sign-on)** - アプリケーション側でシングル サインオン設定を構成します。
3. **[Azure AD のテスト ユーザーの作成](#create-an-azure-ad-test-user)** - Britta Simon で Azure AD のシングル サインオンをテストします。
4. **[Azure AD テスト ユーザーの割り当て](#assign-the-azure-ad-test-user)** - Britta Simon が Azure AD シングル サインオンを使用できるようにします。
5. **[SmartRecruiters のテスト ユーザーの作成](#create-smartrecruiters-test-user)** - SmartRecruiters で Britta Simon に対応するユーザーを作成し、Azure AD の Britta Simon にリンクさせます。
6. **[シングル サインオンのテスト](#test-single-sign-on)** - 構成が機能するかどうかを確認します。

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD シングル サインオンの構成

このセクションでは、Azure portal 上で Azure AD のシングル サインオンを有効にします。

SmartRecruiters で Azure AD シングル サインオンを構成するには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com/) の **SmartRecruiters** アプリケーション統合ページで、 **[シングル サインオン]** を選択します。

    ![シングル サインオン構成のリンク](common/select-sso.png)

2. **[シングル サインオン方式の選択]** ダイアログで、 **[SAML/WS-Fed]** モードを選択して、シングル サインオンを有効にします。

    ![シングル サインオン選択モード](common/select-saml-option.png)

3. **[SAML でシングル サインオンをセットアップします]** ページで、 **[編集]** アイコンをクリックして **[基本的な SAML 構成]** ダイアログを開きます。

    ![基本的な SAML 構成を編集する](common/edit-urls.png)

4. **[基本的な SAML 構成]** セクションで、アプリケーションを **IDP** 開始モードで構成する場合は、次の手順を実行します。

    ![このスクリーンショットは、[基本的な SAML 構成] を示しています。ここで、識別子と応答 U R L を入力し、[保存] を選択できます。](common/idp-intiated.png)

    a. **[識別子]** ボックスに、`https://www.smartrecruiters.com/web-sso/saml/<companyname>` の形式で URL を入力します。

    b. **[応答 URL]** ボックスに、`https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback` のパターンを使用して URL を入力します

5. アプリケーションを **SP** 開始モードで構成する場合は、 **[追加の URL を設定します]** をクリックして次の手順を実行します。

    ![このスクリーンショットは、[追加の U R L を設定します] を示しています。ここで、サインオン U R L を入力できます。](common/metadata-upload-additional-signon.png)

    **[サインオン URL]** ボックスに、`https://www.smartrecruiters.com/web-sso/saml/<companyname>/login` という形式で URL を入力します。

    > [!NOTE]
    > これらは実際の値ではありません。 実際の識別子、応答 URL、サインオン URL でこれらの値を更新します。 これらの値を取得するには、[SmartRecruiters クライアント サポート チーム](https://www.smartrecruiters.com/about-us/contact-us/)に問い合わせてください。 Azure portal の **[基本的な SAML 構成]** セクションに示されているパターンを参照することもできます。

6. **[SAML でシングル サインオンをセットアップします]** ページの **[SAML 署名証明書]** セクションで、 **[ダウンロード]** をクリックして要件のとおりに指定したオプションからの **証明書 (Base64)** をダウンロードして、お使いのコンピューターに保存します。

    ![証明書のダウンロードのリンク](common/certificatebase64.png)

7. **[SmartRecruiters のセットアップ]** セクションで、要件に従って適切な URL をコピーします。

    ![構成 URL のコピー](common/copy-configuration-urls.png)

    a. ログイン URL

    b. Azure AD 識別子

    c. ログアウト URL

### <a name="configure-smartrecruiters-single-sign-on"></a>SmartRecruiters のシングル サインオンの構成

1. 別の Web ブラウザー ウィンドウで、SmartRecruiters 企業サイトに管理者としてログインします。

1. **[Settings / Admin]\(設定 / 管理者\)** に移動します。

    ![メニューから [Settings / Admin]\(設定/管理者\) が選択された画面のスクリーンショット。](./media/smartrecruiters-tutorial/configure.png)

1. **[Configuration]\(構成\)** セクションで、 **[Web SSO]** をクリックします。

    ![[Configuration]\(構成\) から [Web S S O] が選択された画面のスクリーンショット。](./media/smartrecruiters-tutorial/configure1.png)

1. **[Enable Web SSO]\(Web SSO を有効にする\)** をトグルします。

    ![[Enable Web S S O]\(Web S S O を有効にする\) コントロールのスクリーンショット。](./media/smartrecruiters-tutorial/configure2.png)

1. **[構成]\(ID プロバイダー構成\)** で、次の手順に従います。

    ![[Identity Provider Configuration]\(ID プロバイダー構成\) のスクリーンショット。ここで、説明されている値を入力できます。](./media/smartrecruiters-tutorial/configure4.png)

    a. **[Identity Provider URL]\(ID プロバイダー URL\)** ボックスに、Azure portal からコピーした **ログイン URL** の値を貼り付けます。

    b. Azure portal からダウンロードした **certificate(Base64)** をメモ帳で開き、その内容をコピーして **[Identity Provider certificate]\(ID プロバイダー証明書\)** ボックスに貼り付けます。

1. **[Save Web SSO configuration]\(Web SSO 構成の保存\)** をクリックします。

### <a name="create-an-azure-ad-test-user"></a>Azure AD のテスト ユーザーの作成 

このセクションの目的は、Azure Portal で Britta Simon というテスト ユーザーを作成することです。

1. Azure portal の左側のウィンドウで、 **[Azure Active Directory]** 、 **[ユーザー]** 、 **[すべてのユーザー]** の順に選択します。

    ![[ユーザーとグループ] と [すべてのユーザー] リンク](common/users.png)

2. 画面の上部にある **[新しいユーザー]** を選択します。

    ![[新しいユーザー] ボタン](common/new-user.png)

3. [ユーザーのプロパティ] で、次の手順を実行します。

    ![[ユーザー] ダイアログ ボックス](common/user-properties.png)

    a. **[名前]** フィールドに「**BrittaSimon**」と入力します。
  
    b. **[ユーザー名]** フィールドに **brittasimon@yourcompanydomain.extension** と入力します。  
    たとえば、BrittaSimon@contoso.com のように指定します。

    c. **[パスワードを表示]** チェック ボックスをオンにし、[パスワード] ボックスに表示された値を書き留めます。

    d. **Create** をクリックしてください。

### <a name="assign-the-azure-ad-test-user"></a>Azure AD テスト ユーザーの割り当て

このセクションでは、Britta Simon に SmartRecruiters へのアクセスを許可することで、このユーザーが Azure シングル サインオンを使用できるようにします。

1. Azure portal で **[エンタープライズ アプリケーション]** を選択し、 **[すべてのアプリケーション]** 、 **[SmartRecruiters]** の順に選択します。

    ![[エンタープライズ アプリケーション] ブレード](common/enterprise-applications.png)

2. アプリケーションの一覧で **[SmartRecruiters]** を選択します。

    ![アプリケーションの一覧の SmartRecruiters のリンク](common/all-applications.png)

3. 左側のメニューで **[ユーザーとグループ]** を選びます。

    ![[ユーザーとグループ] リンク](common/users-groups-blade.png)

4. **[ユーザーの追加]** をクリックし、 **[割り当ての追加]** ダイアログで **[ユーザーとグループ]** を選択します。

    ![[割り当ての追加] ウィンドウ](common/add-assign-user.png)

5. **[ユーザーとグループ]** ダイアログの [ユーザー] の一覧で **[Britta Simon]** を選択し、画面の下部にある **[選択]** ボタンをクリックします。

6. SAML アサーション内に任意のロール値が必要な場合、 **[ロールの選択]** ダイアログでユーザーに適したロールを一覧から選択し、画面の下部にある **[選択]** をクリッします。

7. **[割り当ての追加]** ダイアログで、 **[割り当て]** ボタンをクリックします。

### <a name="create-smartrecruiters-test-user"></a>SmartRecruiters のテスト ユーザーの作成

このセクションでは、SmartRecruiters で Britta Simon というユーザーを作成します。 SmartRecruiters プラットフォームでユーザーを追加するには、[SmartRecruiters サポート チーム](https://www.smartrecruiters.com/about-us/contact-us/)に問い合わせてください。 シングル サインオンを使用する前に、ユーザーを作成し、有効化する必要があります。

### <a name="test-single-sign-on"></a>シングル サインオンのテスト 

このセクションでは、アクセス パネルを使用して Azure AD のシングル サインオン構成をテストします。

アクセス パネルで [SmartRecruiters] タイルをクリックすると、SSO を設定した SmartRecruiters に自動的にサインインします。 アクセス パネルの詳細については、[アクセス パネルの概要](../user-help/my-apps-portal-end-user-access.md)に関する記事を参照してください。

## <a name="additional-resources"></a>その他のリソース

- [SaaS アプリと Azure Active Directory を統合する方法に関するチュートリアルの一覧](./tutorial-list.md)

- [Azure Active Directory のアプリケーション アクセスとシングル サインオンとは](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory の条件付きアクセスとは](../conditional-access/overview.md)