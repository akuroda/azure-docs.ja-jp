---
title: チュートリアル:Web アプリケーションで認証を行う
titleSuffix: Azure AD B2C
description: ASP.NET Web アプリケーションで Azure Active Directory B2C を使用してユーザー ログインを提供する方法に関するチュートリアル。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: devx-track-csharp, mvc
ms.topic: tutorial
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 9c3c63b6116e02e8a742b69e90c11e182d72ab2e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953034"
---
# <a name="tutorial-enable-authentication-in-a-web-application-using-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C を使用して Web アプリケーションで認証を有効にする

このチュートリアルでは、Azure Active Directory B2C (Azure AD B2C) を使用して ASP.NET Web アプリケーションでユーザーをサインインおよびサインアップする方法を紹介します。 Azure AD B2C を使用すると、アプリケーションはオープンな標準プロトコルを使用してソーシャル アカウント、エンタープライズ アカウント、Azure Active Directory アカウントに対する認証を行うことができます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> このチュートリアルでは、ASP.NET サンプル Web アプリケーションを使用します。 他のサンプル アプリケーション (ASP.NET Core、Node.js、Python など) については、[Azure Active Directory B2C のコード サンプル](code-samples.md)を参照してください。

## <a name="prerequisites"></a>前提条件

* [ユーザー フローを作成](tutorial-create-user-flows.md)してアプリケーションでのユーザー エクスペリエンスを有効にする。
* **[ASP.NET および Web の開発]** ワークロードと共に [Visual Studio 2019](https://www.visualstudio.com/downloads/) をインストールする。

## <a name="update-the-application-registration"></a>アプリケーションの登録を更新する

前提条件の一環として完了したチュートリアルで、Azure AD B2C に Web アプリケーションを登録しました。 このチュートリアルのサンプルとの通信を可能にするには、登録済みアプリケーション用のリダイレクト URI を追加し、クライアント シークレット (キー) を作成する必要があります。

### <a name="add-a-redirect-uri-reply-url"></a>リダイレクト URI (応答 URL) を追加する

Azure AD B2C テナントでアプリケーションを更新するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスかレガシ **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** 、 **[所有しているアプリケーション]** タブ、*webapp1* アプリケーションの順に選択します。
1. **[Web]** で **[URI の追加]** リンクを選択し、「`https://localhost:44316`」と入力して、 **[保存]** を選択します。
1. **[概要]** を選択します。
1. **アプリケーション (クライアント) ID** を記録しておきます。これは、後の手順で Web アプリケーションを構成するときに使用します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、*webapp1* アプリケーションを選択します。
1. **[応答 URL]** に「`https://localhost:44316`」を追加します。
1. **[保存]** を選択します。
1. プロパティ ページで、アプリケーション ID を記録しておきます。これは、後の手順で Web アプリケーションを構成するときに使用します。

* * *

### <a name="create-a-client-secret"></a>クライアント シークレットの作成

次に、登録済み Web アプリケーションに対してクライアント シークレットを作成します。 Web アプリケーションのコード サンプルでは、トークンを要求するときに、これを使って ID を証明します。

[!INCLUDE [active-directory-b2c-client-secret](../../includes/active-directory-b2c-client-secret.md)]

## <a name="configure-the-sample"></a>サンプルの構成

このチュートリアルでは、GitHub からダウンロードできるサンプルを構成します。 このサンプルは ASP.NET を使用して簡単な To Do リストを提供します。 このサンプルでは、[Microsoft OWIN ミドルウェア コンポーネント](/aspnet/aspnet/overview/owin-and-katana/)が使用されています。 [ZIP ファイルをダウンロード](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi/archive/master.zip)するか、GitHub からサンプルを複製します。 サンプル ファイルは必ず、パスの長さが合計 260 文字未満のフォルダーに展開してください。

```
git clone https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi.git
```

サンプル ソリューションには次の 2 つのプロジェクトが含まれています。

* **TaskWebApp** - タスク一覧を作成および編集します。 このサンプルでは、**サインアップまたはサインイン** のユーザー フローを使用してユーザーのサインアップおよびサインインを実行します。
* **TaskService** - タスク リストの作成、読み取り、更新、削除の機能をサポートします。 この API は Azure AD B2C によって保護されており、TaskWebApp によって呼び出されます。

このサンプルは、ご利用のテナントに登録されているアプリケーションを使用するように変更します。これには、以前書き留めておいたアプリケーション ID とキーが含まれます。 また、作成したユーザー フローも構成します。 このサンプルでは、*Web.config* ファイルの設定として構成値を定義します。

次のように Web.config ファイル内の設定を更新してご利用のユーザー フローを操作します。

1. Visual Studio で **B2C-WebAPI-DotNet** ソリューションを開きます。
1. **TaskWebApp** プロジェクトの **Web.config** ファイルを開きます。
    1. `ida:Tenant` および `ida:AadInstance` の値は、作成した Azure AD B2C テナントの名前に置き換えます。 たとえば、`fabrikamb2c` を `contoso` に置き換えます。
    1. `ida:TenantId` の値をディレクトリ ID に置き換えます。これは、Azure B2C テナントのプロパティで確認できます (Azure portal の **[Azure Active Directory]**  >  **[プロパティ]**  >  **[ディレクトリ ID]** )。
    1. `ida:ClientId` の値は、あなたが記録したアプリケーション ID に置き換えます。
    1. `ida:ClientSecret` の値は、実際に記録したキーに置き換えます。 クライアント シークレットに定義済みの XML エンティティ (たとえば、より小さい (`<`)、より大きい (`>`)、アンパサンド (`&`)、二重引用符 (`"`)) が含まれている場合は、Web.config に追加する前に、クライアント シークレットを XML エンコードすることでそれらの文字をエスケープする必要があります。
    1. `ida:SignUpSignInPolicyId` の値は `b2c_1_signupsignin1` に置き換えます。
    1. `ida:EditProfilePolicyId` の値は `b2c_1_profileediting1` に置き換えます。
    1. `ida:ResetPasswordPolicyId` の値は `b2c_1_passwordreset1` に置き換えます。

## <a name="run-the-sample"></a>サンプルを実行する

1. ソリューション エクスプローラーで、**TaskWebApp** プロジェクトを右クリックし、 **[スタートアップ プロジェクトに設定]** をクリックします。
1. **F5** キーを押します。 既定のブラウザーで、ローカルの Web サイトアドレス `https://localhost:44316/` が開かれます。

### <a name="sign-up-using-an-email-address"></a>メール アドレスを使用してサインアップする

1. **[Sign up / Sign in]\(サインアップ/サインイン\)** を選択して、アプリケーションのユーザーとしてサインアップします。 **b2c_1_signupsignin1** ユーザー フローが使用されます。
1. Azure AD B2C によって、サインアップ リンクを含むサインイン ページが表示されます。 まだアカウントを持っていないため、 **[今すぐサインアップ]** を選択します。 サインアップ ワークフローによって、メール アドレスを使用してユーザーの ID を収集および確認するためのページが表示されます。 また、サインアップ ワークフローでは、ユーザー フローで定義されているユーザーのパスワードと要求された属性も収集されます。
1. 有効なメール アドレスを使用し、確認コードを使用して検証します。 パスワードを設定します。 要求された属性の値を入力します。

    ![サインインまたはサインアップ ワークフローの一部として表示されるサインアップ ページ](./media/tutorial-web-app-dotnet/sign-up-workflow.PNG)

1. **[作成]** を選択して、Azure AD B2C テナントにローカル アカウントを作成します。

これで、アプリケーションのユーザーは各自メールアドレスを使用してサインインし、Web アプリケーションを使用できるようになりました。

ただし、**To Do リスト** 機能は、このシリーズの次の [Azure Active Directory B2C を使用した ASP.NET Web API の保護に関するチュートリアル](tutorial-web-api-dotnet.md)を終了するまで正しく動作しません。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD B2C でアプリケーションを更新する
> * アプリケーションを使用するようにサンプルを構成する
> * ユーザー フローを使用してサインアップする

次のチュートリアルに進んで Web アプリケーションの **To Do リスト** 機能を有効にしましょう。 ご利用の Azure AD B2C テナントに Web API アプリケーションを登録し、自分のテナントを API の認証に使用するようコード サンプルに変更を加えます。

> [!div class="nextstepaction"]
> [チュートリアル:Azure Active Directory B2C を使用して ASP.NET Web API を保護する >](tutorial-web-api-dotnet.md)