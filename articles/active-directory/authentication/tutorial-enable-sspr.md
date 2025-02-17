---
title: Azure Active Directory のセルフサービス パスワード リセットを有効にする
description: このチュートリアルでは、ユーザーのグループに対して Azure Active Directory のセルフサービス パスワード リセットを有効にし、パスワード リセット プロセスをテストする方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8cd1a68b06814d13c386b873ed715f3b03a7b827
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102198491"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>チュートリアル:Azure Active Directory のセルフサービス パスワード リセットを使用して、ユーザーが自分のアカウントのロック解除またはパスワードのリセットを実行できるようにする

Azure Active Directory (Azure AD) のセルフサービス パスワード リセット (SSPR) により、ユーザーは、管理者やヘルプ デスクが関与することなく、自分のパスワードを変更またはリセットできるようになります。 ユーザーはアカウントがロックされた場合やパスワードを忘れた場合でも、画面の指示に従って自分自身のブロックを解除して、作業に戻ることができます。 この機能により、ユーザーが自分のデバイスやアプリケーションにサインインできなくなった場合のヘルプ デスクの問い合わせが減り、生産性の喪失も軽減されます。 [自分のテナントでセルフサービス パスワード リセットを構成して有効にする方法](https://www.youtube.com/watch?v=rA8TvhNcCvQ)に関するビデオをご覧ください (**推奨**)。 また IT 管理者向けに、[SSPR に関して最も多く発生する 6 つのエンドユーザー エラー メッセージを解決する方法](https://www.youtube.com/watch?v=9RPrNVLzT8I)についてのビデオも用意しています。

> [!IMPORTANT]
> このチュートリアルでは、管理者向けにセルフサービス パスワード リセットを有効にする方法を示します。 既にセルフサービス パスワード リセットの登録が済んでいて、自分のアカウントに戻る必要があるエンド ユーザーは、 https://aka.ms/sspr にアクセスしてください。
>
> ユーザーが自分でパスワードをリセットする機能が IT チームによって有効にされていない場合は、ヘルプデスクに連絡して追加のサポートを依頼してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure AD ユーザーのグループに対してセルフサービス パスワード リセットを有効にする
> * 認証方法と登録オプションを構成する
> * ユーザーとして SSPR プロセスをテストする

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 少なくとも Azure AD Free または試用版ライセンスが有効になっている、動作している Azure AD テナント。 Free レベルでは、SSPR は Azure AD のクラウド ユーザーに対してのみ機能します。
    * このシリーズの後の方のチュートリアルでは、オンプレミスのパスワード ライトバック用の Azure AD Premium P1 または試用版ライセンスが必要です。
    * 必要に応じて、[無料で作成できます](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* "*グローバル管理者*" 特権を持つアカウント。
* パスワードがわかっている管理者以外のユーザー (*testuser* など)。 このチュートリアルでは、このアカウントを使用してエンドユーザーの SSPR エクスペリエンスをテストします。
    * ユーザーを作成する必要がある場合は、「[クイックスタート: Azure Active Directory に新しいユーザーを追加する](../fundamentals/add-users-azure-active-directory.md)」を参照してください。
* 管理者以外のユーザーが所属するグループ (*SSPR-Test-Group* など)。 このチュートリアルでは、このグループに対して SSPR を有効にします。
    * グループを作成する必要がある場合は、[Azure Active Directory でグループを作成し、メンバーを追加する](../fundamentals/active-directory-groups-create-azure-portal.md)方法を参照してください。

## <a name="enable-self-service-password-reset"></a>セルフサービス パスワード リセットを有効にする

Azure AD では、SSPR を有効にするユーザーを *[なし]* 、 *[選択済み]* 、または *[すべて]* から選択できます。 この詳細な機能により、SSPR の登録プロセスとワークフローのテスト対象となるユーザーのサブセットを選択できます。 このプロセスに慣れ、より広範なユーザーに要求を伝えることができるようになったら、ユーザー グループを選択して、SSPR を有効にすることができます。 または、Azure AD テナント内のすべてのユーザーに対して SSPR を有効にすることもできます。

> [!NOTE]
>
> 現在、Azure portal を使用して SSPR に対して有効にできる Azure AD グループは 1 つだけです。 SSPR のより広範な展開の一環として、入れ子になったグループがサポートされています。 選択したグループ内のユーザーに適切なライセンスが割り当てられていることを確認してください。 現在、これらのライセンス要件の検証プロセスは用意されていません。

このチュートリアルでは、テスト グループ内の一連のユーザーに対して SSPR を構成します。 次の例では、*SSPR-Test-Group* グループを使用します。 必要に応じて、独自の Azure AD グループを指定してください。

1. "*グローバル管理者*" のアクセス許可を持つアカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[パスワード リセット]** を選択します。
1. **[プロパティ]** ページの *[セルフ サービスによるパスワードのリセットが有効]* オプションの下で、 **[グループの選択]** を選択します。
1. 使用する Azure AD グループ (*SSPR-Test-Group* など) を参照して選択し、 *[選択]* を選択します。

    [ ![Azure portal で、セルフサービス パスワード リセットを有効にするグループを選択する](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png) ](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. 選択したユーザーに対して SSPR を有効にするには、 **[保存]** を選択します。

## <a name="select-authentication-methods-and-registration-options"></a>認証方法と登録オプションを選択する

ユーザーが自分のアカウントのロックを解除したり、パスワードをリセットしたりする必要がある場合は、追加の確認方法を求められます。 この追加の認証要素によって、承認された SSPR イベントのみが実行されるようになります。 ユーザーが指定する登録情報に基づいて、どの認証方法を許可するかを選択できます。

1. 左側のメニューから **[認証方法]** ページに移動し、 **[リセットのために必要な方法の数]** を *1* に設定します。

    セキュリティを強化するために、SSPR に必要な認証方法の数を増やすことができます。

1. 組織が許可する **[ユーザーが使用できる方法]** を選択します。 このチュートリアルでは、次の方法を有効にするためのチェック ボックスをオンにします。

    * *[モバイル アプリの通知]*
    * *モバイル アプリ コード*
    * *Email*
    * *携帯電話*

    業務要件に合うよう必要に応じて、追加の認証方法 ("*会社電話*" や "*セキュリティの質問*" など) を有効にすることができます。

1. 認証方法を適用するには、 **[保存]** を選択します。

ユーザーが自分のアカウントのロックを解除したり、パスワードをリセットしたりするには、自分の連絡先情報を登録しておく必要があります。 この連絡先情報は、前の手順で構成したさまざまな認証方法に使用されます。

この連絡先情報は、管理者が手動で入力することも、ユーザーが登録ポータルに移動して自分で入力することもできます。 このチュートリアルでは、ユーザーが次回サインインしたときに登録を求められるように構成します。

1. 左側にあるメニューから **[登録]** ページに移動し、 **[サインイン時にユーザーに登録を求めますか]** で *[はい]* を選択します。
1. 連絡先情報が最新の状態に保たれていることが重要です。 SSPR イベントの開始時に連絡先情報が古くなっていると、ユーザーは自分のアカウントのロックを解除したり、パスワードをリセットしたりできない可能性があります。

    **[Number of days before users are asked to reconfirm their authentication information]\(ユーザーが認証情報を再確認するように求められるまでの日数\)** を *180* に設定します。
1. 登録設定を適用するには、 **[保存]** を選択します。

## <a name="configure-notifications-and-customizations"></a>通知とカスタマイズを構成する

アカウントのアクティビティについて常にユーザーに通知されるように、SSPR イベントが発生すると送信されるように電子メール通知を構成できます。 これらの通知は、通常のユーザー アカウントと管理者アカウントの両方を対象とすることができます。 管理者アカウントの場合は、この通知により、SSPR を使用して特権管理者アカウントのパスワードがリセットされたタイミングをより明確に認識できます。 管理者アカウントで SSPR が使用されている場合、すべてのグローバル管理者に通知されます。

1. 左側のメニューから **[通知]** ページに移動し、次のオプションを構成します。

   * **[Notify users on password resets]\(パスワードのリセットについてユーザーに通知する\)** オプションを *[はい]* に設定します。
   * **[Notify all admins when other admins reset their password]\(他の管理者が自分のパスワードをリセットしたときに、すべての管理者に通知する\)** を *[はい]* に設定します。

1. 通知設定を適用するには、 **[保存]** を選択します。

ユーザーが SSPR プロセスについてさらにサポートを必要とする場合は、"管理者に連絡" のリンクをカスタマイズできます。 このリンクは、SSPR 登録プロセスと、ユーザーが自分のアカウントのロックを解除したりパスワードをリセットしたりするときに使用されます。 ユーザーが必要なサポートを受けられるようにするには、カスタム ヘルプデスクのメール アドレスまたは URL を指定することを強くお勧めします。

1. 左側のメニューから **[カスタマイズ]** ページに移動し、 *[ヘルプデスク リンクのカスタマイズ]* を **[はい]** に設定します。
1. **[カスタム ヘルプデスクの電子メールまたは URL]** フィールドに、ユーザーが組織からさらにサポートを得ることのできるメール アドレスまたは Web ページ URL ( *`https://support.contoso.com/`* など) を指定します。
1. カスタム リンクを適用するには、 **[保存]** を選択します。

## <a name="test-self-service-password-reset"></a>セルフサービス パスワード リセット をテストする

SSPR を有効にして構成したら、前のセクションで選択したグループ (*Test-SSPR-Group* など) に属しているユーザーを使用して SSPR プロセスをテストします。 次の例では、*testuser* アカウントを使用します。 このチュートリアルの最初のセクションで SSPR を有効にしたグループに属している独自のユーザー アカウントを指定します。

> [!NOTE]
> セルフサービス パスワード リセットをテストする場合は、管理者以外のアカウントを使用します。 既定では、管理者に対してセルフサービス パスワード リセットが有効になっており、自分のパスワードをリセットするには 2 つの認証方法を使用する必要があります。 詳細については、「[管理者リセット ポリシーの相違点](concept-sspr-policy.md#administrator-reset-policy-differences)」を参照してください。

1. 手動の登録プロセスを確認するには、InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/ssprsetup](https://aka.ms/ssprsetup) を参照します。 ユーザーは次回サインインしたときにこの登録ポータルにリダイレクトされます。
1. 管理者以外のテスト ユーザー (*testuser* など) としてサインインし、認証方法の連絡先情報を登録します。
1. 完了したら、 **[問題ありません]** ボタンを選択し、ブラウザー ウィンドウを閉じます。
1. InPrivate またはシークレット モードで新しいブラウザー ウィンドウを開き、[https://aka.ms/sspr](https://aka.ms/sspr) に移動します。
1. 管理者以外のテスト ユーザーのアカウント情報 (*testuser* など) と CAPTCHA の文字を入力し、 **[次へ]** を選択します。

    ![パスワードをリセットするためにユーザー アカウント情報を入力する](media/tutorial-enable-sspr/password-reset-page.png)

1. 検証の手順に従って、パスワードをリセットします。 完了すると、パスワードがリセットされたことを示す電子メール通知が届きます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このシリーズの次のチュートリアルでは、パスワード ライトバックを構成します。 この機能により、パスワードの変更が Azure AD SSPR からオンプレミス AD 環境に書き戻されます。 このチュートリアル シリーズを続行してパスワード ライトバックを構成する場合は、ここでは SSPR を無効にしないでください。

このチュートリアルの一環として構成した SSPR 機能を使用する必要がなくなった場合は、次の手順を使用して SSPR の状態を **[なし]** に設定します。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **Azure Active Directory** を検索して選択し、左側のメニューから **[パスワード リセット]** を選択します。
1. **[プロパティ]** ページの *[セルフ サービスによるパスワードのリセットが有効]* オプションの下で、 **[なし]** を選択します。
1. SSPR の変更を適用するには、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、選択したユーザー グループに対して Azure AD のセルフサービス パスワード リセットを有効にしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Azure AD ユーザーのグループに対してセルフサービス パスワード リセットを有効にする
> * 認証方法と登録オプションを構成する
> * ユーザーとして SSPR プロセスをテストする

> [!div class="nextstepaction"]
> [Azure AD の Multi-Factor Authentication を有効にする](./tutorial-enable-azure-mfa.md)
