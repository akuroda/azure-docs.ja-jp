---
title: Azure Recovery Services コンテナーとサーバーの管理
description: この記事では、Recovery Services コンテナーの [概要] ダッシュボードを使用して Recovery Services コンテナーを監視および管理する方法を説明します。
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 74351d781287d863db8be0fc7d20517e0479106c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002132"
---
# <a name="monitor-and-manage-recovery-services-vaults"></a>Recovery Services コンテナーの監視と管理

この記事では、Recovery Services コンテナーの **[概要]** ダッシュボードを使用して Recovery Services コンテナーを監視および管理する方法を説明します。 一覧から Recovery Services コンテナーを開くと、選択したコンテナーの **[概要]** ダッシュボードが開きます。 このダッシュボードでは、コンテナーについてのさまざまな詳細が提供されます。 "*タイル*" で、重大アラートと警告アラートの状態、進行中のバックアップ ジョブと失敗したバックアップ ジョブ、使用されているローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) の量が示されます。 Azure VM をコンテナーにバックアップする場合、[ **[バックアップの事前チェックの状態]** タイルには重大項目または警告項目が表示されます](#backup-pre-check-status)。 次の図は、**Contoso-vault** に対する **[概要]** ダッシュボードです。 **[バックアップ項目]** タイルでは、コンテナーに 9 つの項目が登録されていることが示されています。

![Recovery Services コンテナーのダッシュボード](./media/backup-azure-manage-windows-server/rs-vault-blade.png)

この記事の前提条件は、Azure サブスクリプションと Recovery Services コンテナーがあり、少なくとも 1 つのバックアップ項目がコンテナーに対して構成されていることです。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

## <a name="open-a-recovery-services-vault"></a>Recovery Services コンテナーを開く

アラートを監視するには、または Recovery Services コンテナーに関する管理データを表示するには、コンテナーを開きます。

1. Azure サブスクリプションを使用して、[Azure Portal](https://portal.azure.com/) にサインインします。

2. ポータルで **[すべてのサービス]** を選択します。

   ![Open list of Recovery Services vaults step 1](./media/backup-azure-manage-windows-server/open-rs-vault-list.png)

3. **[すべてのサービス]** ダイアログ ボックスで、「**Recovery Services**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Recovery Services コンテナー]** オプションが表示されたら、それを選択してサブスクリプションの Recovery Services コンテナーの一覧を表示します。

    ![Recovery Services コンテナー作成ステップ 1](./media/backup-azure-manage-windows-server/list-of-rs-vaults.png) <br/>

4. コンテナーの一覧でコンテナーを選択して、その **[概要]** ダッシュボードを開きます。

    ![Recovery Services コンテナーのダッシュボード](./media/backup-azure-manage-windows-server/rs-vault-blade.png) <br/>

    [概要] ダッシュボードでは、タイルを使用してアラートとバックアップ ジョブのデータが提供されます。

## <a name="monitor-backup-jobs-and-alerts"></a>バックアップ ジョブとアラートの監視

Recovery Services コンテナーの **[概要]** ダッシュボードでは、監視と使用状況の情報に関するタイルが提供されます。 [監視] セクションのタイルには、重大アラートと警告アラート、および進行中のジョブと失敗したジョブが表示されます。 特定のアラートまたはジョブを選択すると、そのジョブまたはアラートでフィルター処理された [バックアップ アラート] または [バックアップ ジョブ] メニューが開きます。

![バックアップ ダッシュボードのタスク](./media/backup-azure-manage-windows-server/monitor-dashboard-tiles-warning.png)

[監視] セクションには、定義済みの **バックアップ アラート** クエリおよび **バックアップ ジョブ** クエリの結果が表示されます。 監視タイルでは以下に関する最新情報が提供されます。

* バックアップ ジョブに関する重大アラートと警告アラート (過去 24 時間)
* Azure VM の事前チェックの状態。 事前チェックの状態の完全な情報については、「[バックアップの事前チェックの状態](#backup-pre-check-status)」を参照してください。
* 進行中のバックアップ ジョブ、および失敗したジョブ (過去 24 時間)。

使用状況のタイルでは次の情報が提供されます。

* コンテナーに対して構成されているバックアップ項目の数。
* コンテナーによって使用されている Azure Storage (LRS と GRS で別)。

タイルを選択すると ([バックアップ ストレージ] を除く)、関連するメニューが開きます。 上の図では、[バックアップ アラート] タイルに 3 つの重大なアラートが表示されています。 [バックアップ アラート] タイルで重大なアラートの行を選択すると、重大なアラートでフィルター処理されたバックアップ アラートが開きます。

![重大なアラートでフィルター処理されたバックアップ アラート メニュー](./media/backup-azure-manage-windows-server/critical-backup-alerts.png)

上の図の [バックアップ アラート] メニューは、状態がアクティブ、重大度が重大、時刻が過去 24 時間でフィルター処理されています。

### <a name="backup-pre-check-status"></a>バックアップの事前チェックの状態

バックアップの事前チェックでは、VM の構成に、バックアップに悪影響を及ぼす可能性がある問題がないかどうかがチェックされます。 この情報は集計され、Recovery Services コンテナーのダッシュボードから直接表示できます。また、ファイル整合性バックアップやアプリケーション整合性バックアップが正常に行われるようにするための是正措置に関する推奨事項が提供されます。 インフラストラクチャを必要とせず、追加コストもありません。  

バックアップの事前チェックは、Azure VM のスケジュールされたバックアップ操作の一部として実行されます。 これらは、次のいずれかの状態で終了します。

* **合格**: この状態は、この VM の構成であればバックアップが成功すると見なされ、是正措置を実行する必要がないことを示します。
* **警告**:この状態は、この VM の構成にバックアップの失敗につながる *可能性がある* 問題が 1 つ以上存在することを示しています。 バックアップが正常に行われるようにするために *推奨される* 手順が示されます。 たとえば、最新の VM エージェントがインストールされていないと、バックアップが断続的に失敗する可能性があります。 このような状況の場合、警告状態が発生します。
* **[高]** :この状態は、この VM の構成にバックアップの失敗に *つながる* クリティカルな問題が 1 つ以上存在することを示しています。バックアップが正常に行われるようにするための *必要な* 手順が示されます。 たとえば、VM の NSG ルールの更新によって発生したあるネットワークの問題により、VM が Azure Backup サービスと通信できなくなるため、バックアップが失敗します。 このような状況の場合、クリティカルな状態が発生します。

次の手順に従って、Recovery Services コンテナーでの VM バックアップのバックアップ事前チェックによって報告された問題の解決を開始します。

* Recovery Services コンテナーのダッシュボードで、 **[バックアップの事前チェックの状態 (Azure VM)]** タイルを選択します。
* バックアップの事前チェックの状態が **[クリティカル]** または **[警告]** のいずれかである VM を選択します。 この操作により、 **[VM の詳細]** ペインが開きます。
* ペインの上部にあるペインの通知を選択して、構成の問題の説明と修正手順を表示します。

## <a name="manage-backup-alerts"></a>バックアップ アラートの管理

[バックアップ アラート] メニューにアクセスするには、[Recovery Services コンテナー] メニューで **[バックアップ アラート]** を選択します。

![[バックアップ アラート]](./media/backup-azure-manage-windows-server/backup-alerts-menu.png)

バックアップ アラート レポートには、コンテナーのアラートが一覧表示されます。

![バックアップ アラート レポート](./media/backup-azure-manage-windows-server/backup-alerts.png)

### <a name="alerts"></a>警告

バックアップ アラートの一覧には、フィルター処理されたアラートについての選択された情報が表示されます。 [バックアップ アラート] メニューでは、重大アラートまたは警告アラートでフィルター処理できます。

| アラート レベル | アラートを生成するイベント |
| ----------- | ----------- |
| Critical | 重要アラートを受け取るのは、バックアップ ジョブが失敗したとき、回復ジョブが失敗したとき、およびサーバーでの保護を停止したのにデータが保持されているときです。|
| 警告 | 警告アラートを受け取るのは、バックアップ ジョブが警告で完了したとき (たとえば、破損の問題のために 100 個未満のファイルがバックアップされていないとき、または 1,000,000 個より多くのファイルが正常にバックアップされたとき) です。 |
| Informational | 現在、情報アラートは使用されていません。 |

### <a name="viewing-alert-details"></a>アラートの詳細を表示する

バックアップ アラート レポートでは、各アラートに関して 8 項目の詳細が追跡されています。 レポートの詳細を編集するには、 **[列の選択]** ボタンを使用します。

![バックアップ アラートの [列の選択] ボタン](./media/backup-azure-manage-windows-server/backup-alerts.png)

既定では、**最新発生時刻** を除くすべての詳細がレポートに表示されます。

* アラート:
* バックアップ項目
* 保護されるサーバー
* 重大度
* Duration
* 作成時刻
* Status
* 最新発生時刻

### <a name="change-the-details-in-alerts-report"></a>アラート レポートで詳細を変更する

1. レポートの情報を変更するには、 **[バックアップ アラート]** メニューの **[列の選択]** を選択します。

   ![[列の選択] の選択](./media/backup-azure-manage-windows-server/alerts-menu-choose-columns.png)

   **[列の選択]** メニューが開きます。

2. **[列の選択]** メニューで、レポートに表示する詳細を選択します。

    ![[列の選択] メニュー](./media/backup-azure-manage-windows-server/choose-columns-menu.png)

3. **[完了]** を選択して変更を保存し、[列の選択] メニューを閉じます。

   変更したけれども変更を保存したくない場合は、 **[リセット]** を選択して、選択した列を最後に保存した構成に戻します。

### <a name="change-the-filter-in-alerts-report"></a>アラート レポートでフィルターを変更する

アラートの重大度、状態、開始時刻、終了時刻を変更するには、 **[フィルター]** メニューを使用します。

> [!NOTE]
> バックアップ アラートのフィルターを編集しても、コンテナーの [概要] ダッシュボードに表示されている重大アラートまたは警告アラートは変化しません。
>  

1. バックアップ アラート フィルターを変更するには、[バックアップ アラート] メニューで **[フィルター]** を選択します。

   ![[フィルター] メニューを選択する](./media/backup-azure-manage-windows-server/alerts-menu-choose-filter.png)

   [フィルター] メニューが表示されます。

   ![アラートのフィルター メニュー](./media/backup-azure-manage-windows-server/filter-alert-menu.png)

2. 重大度、状態、開始時刻、または終了時刻を編集し、 **[完了]** を選択して変更を保存します。

## <a name="configuring-notifications-for-alerts"></a>アラートの通知を構成する

警告アラートまたは重大アラートが発生したときにメールを生成するように通知を構成します。 メール アラートは、1 時間ごとに、または特定のアラートが発生したときに送信できます。

   ![アラートのフィルター処理](./media/backup-azure-manage-windows-server/configure-notification.png)

既定では、メール通知は **オン** になります。 メール通知を停止するには、 **[オフ]** を選択します。

グループ化したくない場合、またはアラートを生成する項目が多くない場合は、 **[通知]** コントロールで **[アラートごと]** を選択します。 アラートごとに 1 つの通知が生成され (既定の設定)、解決策の電子メールが即時に送信されます。

**[1 時間ごとのダイジェスト]** を選択すると、過去 1 時間に生成されて未解決のアラートを説明するメールが受信者に送信されます。 解決策が示された電子メールは、その時間の終了時に送信されます。

メールの生成に使用されるアラートの重大度 (重大または警告) を選択します。 現在、情報アラートはありません。

## <a name="manage-backup-items"></a>バックアップ項目の管理

Recovery Services コンテナーには、多くの種類のバックアップ データが保持されます。 バックアップできる内容の[詳細についてはこちら](backup-overview.md#what-can-i-back-up)を参照してください。 さまざまなサーバー、コンピューター、データベース、ワークロードを管理するには、 **[バックアップ項目]** タイルを選択して、コンテナーの内容を表示します。

![Backup items tile](./media/backup-azure-manage-windows-server/backup-items.png)

バックアップの管理の種類別に整理されたバックアップ項目の一覧が開きます。

![バックアップ項目の一覧](./media/backup-azure-manage-windows-server/list-backup-items.png)

特定の種類の保護されたインスタンスを調べるには、[バックアップの管理の種類] 列で項目を選択します。 たとえば、上の図では、2 つの Azure 仮想マシンがこのコンテナーで保護されています。 **[Azure 仮想マシン]** を選択すると、このコンテナーで保護されている仮想マシンの一覧が開きます。

![保護されている仮想マシンの一覧](./media/backup-azure-manage-windows-server/list-of-protected-virtual-machines.png)

仮想マシンの一覧には、関連付けられているリソース グループ、前回の[バックアップの事前チェック](#backup-pre-check-status)、前回のバックアップの状態、最新の復元ポイントの日時など、有用なデータが表示されます。 最後の列の省略記号をクリックすると、一般的なタスクをトリガーするメニューが開きます。 列で提供される役立つデータは、バックアップの種類ごとに異なります。

![省略記号のメニューを開いて一般的なタスクを表示する](./media/backup-azure-manage-windows-server/ellipsis-menu.png)

## <a name="manage-backup-jobs"></a>バックアップ ジョブの管理

コンテナー ダッシュボードの **[バックアップ ジョブ]** タイルには、進行中のジョブまたは過去 24 時間以内に失敗したジョブの数が表示されます。 タイルからは [バックアップ ジョブ] メニューを表示できます。

![[バックアップ ジョブ] タイル](./media/backup-azure-manage-windows-server/backup-jobs-tile.png)

ジョブに関する他の詳細を表示するには、 **[進行中]** または **[失敗]** を選択して、その状態でフィルター処理された [バックアップ ジョブ] メニューを開きます。

### <a name="backup-jobs-menu"></a>[バックアップ ジョブ] メニュー

**[バックアップ ジョブ]** メニューには、項目の種類、操作、状態、開始時刻、および継続時間に関する情報が表示されます。  

[バックアップ ジョブ] メニューを開くには、コンテナーのメイン メニューで **[バックアップ ジョブ]** を選択します。

![[バックアップ ジョブ] の選択](./media/backup-azure-manage-windows-server/backup-jobs-menu-item.png)

バックアップ ジョブの一覧が開きます。

![バックアップ ジョブの一覧](./media/backup-azure-manage-windows-server/backup-jobs-list.png)

[バックアップ ジョブ] メニューには、すべてのバックアップの種類について、過去 24 時間のすべての操作の状態が表示されます。 フィルターを変更するには **[フィルター]** を使用します。 フィルターについては次のセクションで説明します。

フィルターを変更するには:

1. コンテナーの [バックアップ ジョブ] メニューで、 **[フィルター]** を選択します。

   ![[バックアップ ジョブ] の [フィルター] の選択](./media/backup-azure-manage-windows-server/vault-backup-job-menu-filter.png)

    [フィルター] メニューが開きます。

   ![バックアップ ジョブの [フィルター] メニューが表示される](./media/backup-azure-manage-windows-server/filter-menu-backup-jobs.png)

2. フィルターの設定を選択して、 **[完了]** を選択します。 フィルター処理された一覧が新しい設定に基づいて更新されます。

#### <a name="item-type"></a>項目の種類

項目の種類は、保護されたインスタンスのバックアップ管理の種類です。 次の一覧で示す 4 種類があります。 すべての項目の種類、または 1 つの項目の種類を表示することができます。 2 つまたは 3 つの項目の種類を選択することはできません。 使用可能な項目の種類は次のとおりです。

* すべての項目の種類
* Azure 仮想マシン
* ファイルとフォルダー
* Azure Storage
* Azure ワークロード

#### <a name="operation"></a>操作

1 つの操作またはすべての操作を表示することができます。 2 つまたは 3 つの操作を選択することはできません。 使用可能な操作は次のとおりです。

* すべての操作
* [登録]
* バックアップの構成
* バックアップ
* 復元
* バックアップを無効にする
* バックアップ データの削除

#### <a name="status"></a>Status

すべての状態または 1 つの状態を表示できます。 2 つまたは 3 つの状態を選択することはできません。 使用可能な状態は次のとおりです。

* すべての状態
* 完了
* 進行中
* 失敗
* Canceled
* 完了 (警告あり)

#### <a name="start-time"></a>開始時刻

クエリが開始した日付と時刻です。 既定では、24 時間以内です。

#### <a name="end-time"></a>終了時刻

クエリが終了した日付と時刻です。

### <a name="export-jobs"></a>ジョブをエクスポートする

すべてのジョブ メニュー情報を含むスプレッドシートを作成するには、 **[ジョブのエクスポート]** を使用します。 スプレッドシートには、すべてのジョブの概要を含む 1 つのシートと、ジョブごとに個別のシートがあります。

ジョブの情報をスプレッドシートにエクスポートするには、 **[ジョブのエクスポート]** を選択します。 サービスにより、コンテナーの名前と日付を使用してスプレッドシートが作成されます。ただし、名前は変更できます。

## <a name="monitor-backup-usage"></a>バックアップの利用状況の監視

ダッシュボードの [ストレージの構成] タイルには、Azure で使用されているストレージが表示されます。 次のストレージの利用状況を確認できます。

* コンテナーに関連付けられているクラウド LRS ストレージの利用状況
* コンテナーに関連付けられているクラウド GRS ストレージの利用状況

## <a name="troubleshooting-monitoring-issues"></a>監視に関する問題のトラブルシューティング

**問題:** Azure Backup エージェントからのジョブやアラートがポータルに表示されない。

**トラブルシューティングの手順:** ```OBRecoveryServicesManagementAgent``` プロセスによって、Azure Backup サービスにジョブとアラートのデータが送信されます。 このプロセスは、頻度は高くないものの停止またはシャットダウンすることがあります。

1. このプロセスが動作しているかどうかを確認するには、**タスク マネージャー** を開いて ```OBRecoveryServicesManagementAgent``` が実行中かどうかを調べます。

2. プロセスが実行されていない場合は、**コントロール パネル** を開いて、サービスの一覧を参照します。 **Microsoft Azure Recovery Services 管理エージェント** を開始または再起動します。

    詳細については、以下の場所にあるログを参照してください。<br/>
   `<AzureBackup_agent_install_folder>\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider*`次に例を示します。<br/>
   `C:\Program Files\Microsoft Azure Recovery Services Agent\Temp\GatewayProvider0.errlog`

## <a name="next-steps"></a>次のステップ

* [Azure からの Windows Server または Windows クライアントの復元](backup-azure-restore-windows-server.md)
* Azure Backup の詳細については、「 [Azure Backup の概要](./backup-overview.md)
