---
title: チュートリアル:新しい環境に対するブループリント サンプル
description: このチュートリアルでは、ブループリント サンプルを使用して、2 つのリソース グループを設定し、それぞれにロールの割り当てを構成するブループリント定義を作成します。
ms.date: 01/27/2021
ms.topic: tutorial
ms.openlocfilehash: aea3760128221d42e092e4ca45200874d54da54a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915559"
---
# <a name="tutorial-create-an-environment-from-a-blueprint-sample"></a>チュートリアル:ブループリント サンプルから環境を作成する

サンプルのブループリントは、Azure Blueprints を使用して行うことのできる処理の例を示します。 それぞれは特定の意図または目的を持つサンプルですが、単独で完全な環境が作成されるわけではありません。 それぞれが、含まれている成果物、設計、およびパラメーターをさまざまに組み合わせて Azure Blueprints を使用して探索するための出発点と見なされます。

次のチュートリアルでは、**RBAC を使用するリソース グループ** ブループリント サンプルを使用して、Azure Blueprints サービスのさまざまな側面を示します。 ここで示す手順は次のとおりです。

> [!div class="checklist"]
> - サンプルから新しいブループリント定義を作成する
> - サンプルのコピーを **発行済み** としてマークする
> - ブループリントのコピーを既存のサブスクリプションに割り当てる
> - 割り当てにデプロイされたリソースを調べる
> - ブループリントの割り当てを解除してロックを削除する

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="create-blueprint-definition-from-sample"></a>サンプルからブループリント定義を作成する

最初に、ブループリント サンプルを実装します。 インポートを実行すると、サンプルに基づいて新しいブループリントが環境に作成されます。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[はじめに]** ページで、 _[ブループリントの作成]_ の下にある **[作成]** ボタンを選択します。

1. _[その他のサンプル]_ で **[RBAC を使用するリソース グループ]** ブループリント サンプルを探して、それを選択します。

1. ブループリント サンプルの _[基本]_ を入力します。

   - **[ブループリントの名前]** : ブループリント サンプルのコピーの名前を指定します。 このチュートリアルでは、_two-rgs-with-role-assignments_ という名前を使用します。
   - **[定義の場所]** :省略記号を使用して、サンプルのコピーを保存する管理グループまたはサブスクリプションを選択します。

1. ページの上部にある _[アーティファクト]_ タブまたはページの下部にある **[次へ: アーティファクト]** を選択します。

1. ブループリント サンプルを構成するアーティファクトの一覧を確認します。 このサンプルでは、表示名が _ProdRG_ および _PreProdRG_ である 2 つのリソース グループを定義します。 各リソース グループの最終的な名前と場所は、ブループリントの割り当て時に設定されます。 _ProdRG_ リソース グループには _共同作成者_ ロールが割り当てられ、_PreProdRG_ リソース グループには _所有者_ ロールと _閲覧者_ ロールが割り当てられます。 定義で割り当てられたロールは静的ですが、ロールを割り当てるユーザー、アプリ、またはグループは、ブループリントの割り当て時に設定されます。

1. ブループリント サンプルの確認が完了したら、 **[下書きの保存]** を選択します。

この手順では、選択した管理グループまたはサブスクリプションにサンプルのブループリント定義のコピーを作成します。 保存したブループリント定義は、最初から作成したブループリントと同様に管理されます。 管理グループまたはサブスクリプションに必要な回数だけサンプルを保存できます。 ただし、各コピーには一意の名前を指定する必要があります。

"**ブループリント定義の保存に成功しました**" というポータルの通知が表示されたら、次の手順に移ります。

## <a name="publish-the-sample-copy"></a>サンプルのコピーを発行する

環境でのブループリント サンプルのコピーの作成が完了しました。 これは **下書き** モードで作成されており、割り当ておよびデプロイの前に **発行** する必要があります。 ブループリント サンプルのコピーは、環境とニーズに合わせてカスタマイズできます。 このチュートリアルでは、変更を加えません。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 フィルターを使用してブループリント定義 _two-rgs-with-role-assignments_ を検索し、選択します。

1. ページの上部にある **[ブループリントを発行する]** を選択します。 右側の新しいウィンドウで、ブループリント サンプルのコピーの **[バージョン]** を _[1.0]_ に設定します。 このプロパティは、後で変更を加える場合に役立ちます。 **[変更に関するメモ]** (例: "RBAC を使用するリソース グループ ブループリント サンプルから発行する最初のバージョン") を指定します。 次に、ページの下部にある **[発行]** を選択します。

この手順では、ブループリントをサブスクリプションに割り当てることができます。 発行後に変更を行うことも可能です。 追加の変更の際は、同じブループリント定義のバージョン間の違いを追跡するために、 **[バージョン]** の新しい値を使用して発行を行う必要があります。

"**ブループリント定義が正常に発行されました**" というポータルの通知が表示されたら、次の手順に移ります。

## <a name="assign-the-sample-copy"></a>サンプルのコピーを割り当てる

正常に **発行** されたブループリント サンプルのコピーは、保存先の管理グループ内のサブスクリプションに割り当てることができます。 この手順では、ブループリント サンプルのコピーの各デプロイを一意にするためのパラメーターを指定します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[ブループリントの定義]** ページを選択します。 フィルターを使用してブループリント定義 _two-rgs-with-role-assignments_ を検索し、選択します。

1. ブループリント定義ページの上部にある **[ブループリントの割り当て]** を選択します。

1. ブループリント割り当て用のパラメーター値を指定します。

   - 基本

     - **サブスクリプション**:ブループリント サンプルのコピーを保存した管理グループ内の 1 つ以上のサブスクリプションを選択します。 複数のサブスクリプションを選択すると、入力したパラメーターを使用して、それぞれに対して割り当てが作成されます。
     - **割り当て名**:名前は、ブループリント定義の名前に基づいてあらかじめ設定されています。
     - **[場所]** :マネージド ID を作成するリージョンを選択します。 Azure Blueprint は、この管理対象 ID を使用して、割り当てられたブループリント内にすべての成果物をデプロイします。 詳細については、[Azure リソースの管理対象 ID の概要](../../../active-directory/managed-identities-azure-resources/overview.md)に関するページをご覧ください。
       このチュートリアルでは、 _[米国東部 2]_ を選択します。
     - **ブループリント定義ラベル**:サンプルのブループリント定義のコピーの **[発行済み]** バージョン _[1.0]_ を選択します。

   - ロックの割り当て

     _[読み取り専用]_ のブループリントのロック モードを選択します。 詳細については、[ブループリント リソースのロック](../concepts/resource-locking.md)に関するページを参照してください。

   - マネージド ID

     既定の _[システム割り当て済み]_ オプションのままにします。 詳細については、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) に関するページを参照してください。

   - アーティファクトのパラメーター

     このセクションで定義するパラメーターは、定義対象のアーティファクトに適用されます。 これらのパラメーターはブループリントの割り当て時に定義されるので、[動的パラメーター](../concepts/parameters.md#dynamic-parameters)です。 各成果物に対して、パラメーター値を **[値]** 列に定義されている内容に設定してください。 `{Your ID}` に対しては、Azure ユーザー アカウントを選択してください。

     |アーティファクト名|アーティファクトの種類|パラメーター名|値|説明|
     |-|-|-|-|-|
     |ProdRG リソース グループ|Resource group|名前|ProductionRG|最初のリソース グループの名前を定義します。|
     |ProdRG リソース グループ|Resource group|場所|米国西部 2|最初のリソース グループの場所を設定します。|
     |Contributor|ロール割り当て|ユーザーまたはグループ|{ユーザー ID}|_共同作成者_ ロールの割り当てを付与する、最初のリソース グループ内のユーザーまたはグループを定義します。|
     |PreProdRG リソース グループ|Resource group|名前|PreProductionRG|2 番目のリソース グループの名前を定義します。|
     |PreProdRG リソース グループ|Resource group|場所|米国西部|2 番目のリソース グループの場所を設定します。|
     |所有者|ロール割り当て|ユーザーまたはグループ|{ユーザー ID}|_所有者_ ロールの割り当てを付与する、2 番目のリソース グループ内のユーザーまたはグループを定義します。|
     |閲覧者|ロール割り当て|ユーザーまたはグループ|{ユーザー ID}|_閲覧者_ ロールの割り当てを付与する、2 番目のリソース グループ内のユーザーまたはグループを定義します。|

1. すべてのパラメーターの入力が完了したら、ページの下部にある **[割り当て]** を選択します。

この手順では、定義されたリソースをデプロイし、選択した **ロックの割り当て** を構成します。 ブループリントのロックの適用には最長 30 分かかる場合があります。

"**ブループリント定義の割り当て完了**" というポータルの通知が表示されたら、次の手順に移ります。

## <a name="inspect-resources-deployed-by-the-assignment"></a>割り当てによってデプロイされたリソースを調べる

ブループリントの割り当てによって、ブループリント定義で定義されている成果物が作成および追跡されます。 ブループリントの割り当てページから、およびリソースを直接調べることで、リソースの状態がわかります。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[割り当てられたブループリント]** ページを選択します。 フィルターを使用してブループリントの割り当て _Assignment-two-rgs-with-role-assignments_ を検索し、選択します。

   このページから、割り当てが成功したこと、および作成されたリソースの一覧とそのブループリントのロック状態がわかります。 割り当てが更新された場合は、各定義のバージョンのデプロイに関する詳細が **[割り当ての操作]** ドロップダウンに表示されます。 表示された作成済みの各リソースを選択すると、そのリソースのプロパティ ページが開きます。

1. **[ProductionRG]** リソース グループを選択します。

   リソース グループの名前は **ProductionRG** であり、成果物の表示名である _ProdRG_ でないことがわかります。 この名前は、ブループリントの割り当て時に設定された値と一致します。

1. 左側の **[アクセス制御 (IAM)]** ページを選択し、 **[ロール割り当て]** タブを選択します。

   ここでは、 _[このリソース]_ のスコープで _共同作成者_ ロールがアカウントに付与されていることがわかります。 ブループリントの割り当て _Assignment-two-rgs-with-role-assignments_ はリソース グループの作成に使用されたため、_所有者_ ロールが割り当てられています。 これらのアクセス許可は、構成済みのブループリントのロックを持つリソースの管理にも使用されます。

1. Azure portal の階層リンクで、 **[Assignment-two-rgs-with-role-assignments]** を選択して 1 ページに戻り、 **[PreProductionRG]** リソース グループを選択します。

1. 左側の **[アクセス制御 (IAM)]** ページを選択し、 **[ロール割り当て]** タブを選択します。

   ここでは、 _[このリソース]_ のスコープで _所有者_ ロールと _閲覧者_ ロールの両方がアカウントに付与されていることがわかります。 最初のリソース グループと同様に、ブループリントの割り当てには _所有者_ ロールも割り当てられています。

1. **[拒否割り当て]** タブを選択します。

   ブループリントの割り当てでは、デプロイされたリソース グループに対して [拒否割り当て](../../../role-based-access-control/deny-assignments.md)が作成され、 _[読み取り専用]_ のブループリントのロック モードが適用されました。 拒否割り当てにより、 _[ロール割り当て]_ タブに表示されている適切な権限を持つユーザーが特定のアクションを実行できなくなります。 拒否割り当ては "_すべてのプリンシパル_" に適用されます。

1. 拒否割り当てを選択し、左側の **[拒否されたアクセス許可]** ページを選択します。

   拒否割り当てでは、 **\* *_ と _* Action** 構成を使用してすべての操作を禁止しますが、**NotActions** を使用して **\*/read** を除外することで読み取りアクセスを許可します。

1. Azure portal の階層リンクで、 **[PreProductionRG - アクセス制御 (IAM)]** を選択します。 次に、左側の **[概要]** ページを選択し、 **[リソース グループの削除]** ボタンをクリックします。 「_PreProductionRG_」という名前を入力して削除を確定し、ウィンドウの下部にある **[削除]** を選択します。

   "**リソース グループ PreProductionRG の削除に失敗しました**" というポータルの通知が表示されます。 このエラーは、アカウントにリソース グループを削除するアクセス許可はあるが、ブループリントの割り当てによってアクセスが拒否されたことを示しています。 ブループリントの割り当て時にブループリントのロック モードとして _[読み取り専用]_ を選択したことを思い出してください。 ブループリントのロックにより、アクセス許可を持つアカウントは (_所有者_ であっても) リソースを削除できなくなります。 詳細については、[ブループリント リソースのロック](../concepts/resource-locking.md)に関するページを参照してください。

上記の手順では、リソースが定義どおりに作成されたこと、およびブループリントのロックによって不必要な削除 (アクセス許可を持つアカウントが行った操作であっても) が禁止されたことを示します。

## <a name="unassign-the-blueprint"></a>ブループリントの割り当てを解除する

最後の手順では、ブループリントの割り当てとデプロイされたリソースを削除します。
割り当てを削除しても、デプロイされた成果物は削除されません。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側の **[割り当てられたブループリント]** ページを選択します。 フィルターを使用してブループリントの割り当て _Assignment-two-rgs-with-role-assignments_ を検索し、選択します。

1. ページの上部にある **[ブループリントの割り当て解除]** ボタンを選択します。 確認ダイアログ内の警告を読んでから、 **[OK]** を選択します。

   ブループリントの割り当てを削除すると、ブループリントのロックも削除されます。 作成されたリソースは、アクセス許可を持つアカウントによってもう一度削除できます。

1. Azure メニューから **[リソース グループ]** を選択し、 **[ProductionRG]** を選択します。

1. 左側の **[アクセス制御 (IAM)]** ページを選択し、 **[ロール割り当て]** タブを選択します。

各リソース グループのセキュリティにはデプロイされたロールの割り当てが依然としてありますが、ブループリントの割り当てには "_所有者_" アクセスがありません。

"**ブループリント割り当ての削除完了**" というポータルの通知が表示されたら、次の手順に移ります。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルを完了したら、次のリソースを削除してください。

- リソース グループ _ProductionRG_
- リソース グループ _PreProductionRG_
- ブループリント定義 _two-rgs-with-role-assignments_

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、サンプル定義から新しいブループリントを作成する方法について学習しました。 Azure Blueprints の詳細については、ブループリントのライフサイクルに関する記事に進んでください。

> [!div class="nextstepaction"]
> [ブループリントのライフサイクルについて学習する](../concepts/lifecycle.md)