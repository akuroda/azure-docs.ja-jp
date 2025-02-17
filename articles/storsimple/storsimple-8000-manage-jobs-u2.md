---
title: StorSimple 8000 シリーズ向けのジョブを表示および管理する | Microsoft Docs
description: StorSimple デバイス マネージャー サービスの [ジョブ] ブレードと、最近のバックアップ ジョブ、現在のバックアップ ジョブ、およびスケジュールされたバックアップ ジョブを追跡する方法について説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/29/2017
ms.author: alkohli
ms.openlocfilehash: dc630021cbf93074119d4feedcb3a09fae1453e3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91949430"
---
# <a name="use-the-storsimple-device-manager-service-to-view-and-manage-jobs-update-3-and-later"></a>StorSimple デバイス マネージャー サービスを使用してジョブを表示および管理する (Update 3 以降)

## <a name="overview"></a>概要
**[ジョブ]** ブレードは、StorSimple デバイス マネージャー サービスに接続されているデバイスで開始されたジョブを表示および管理するための一元的なポータルを提供します。 複数のデバイスについて、スケジュールされたジョブ、実行中のジョブ、完了したジョブ、取り消されたジョブ、失敗したジョブを表示できます。 結果は表形式で表示されます。

![ジョブ ブレード](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

以下のフィールドにフィルター処理を行うことで、関心のあるジョブを素早く見つけることができます。

* **状態** – ジョブは、実行中、完了、取り消し済み、失敗、取り消し中、またはエラーありで完了の状態が可能です。
* **時間範囲** - 日付と時刻の範囲に基づいて、ジョブにフィルターを適用できます。 範囲は、過去 1 時間、過去 24 時間、過去 7 日間、過去 30 日間、過去 1 年、またはユーザー設定の日付です。
* **種類** – ジョブの種類は、スケジュールされたバックアップ、手動バックアップ、バックアップの復元、ボリュームの複製、ボリューム コンテナーのフェールオーバー、ローカル固定ボリュームの作成、ボリュームの変更、更新プログラムのインストール、サポート ログの収集、およびクラウド アプリの作成が可能です。
* **デバイス** - ジョブは、サービスに接続されている特定のデバイスで開始されます。
  
フィルター選択されたジョブは、次の属性に基づいて表形式で表示されます。
  
* **名前** – スケジュールされたバックアップ、手動バックアップ、バックアップの復元、ボリュームの複製、ボリューム コンテナーのフェールオーバー、ローカル固定ボリュームの作成、ボリュームの変更、更新プログラムのインストール、サポート ログの収集、またはクラウド アプリの作成。
* **状態** - 実行中、完了、取り消し済み、失敗、取り消し中、またはエラーありで完了。
* **エンティティ** - ジョブは、ボリューム、バックアップ ポリシー、デバイスに関連付けられます。 たとえば、複製ジョブはボリュームに関連付けられますが、スケジュール済みのバックアップ ジョブはバックアップ ポリシーに関連付けられます。 デバイス ジョブは、ディザスター リカバリー (DR) または復元操作の結果として作成されます。
* **デバイス** - ジョブが開始されたデバイスの名前。
* **開始日** - ジョブが開始された日時。
* **期間** – ジョブを完了するために必要な時間。

ジョブの一覧は 30 秒ごとに更新されます。

このページでは、以下のジョブ関連の操作を実行できます。

* ジョブの詳細を表示する
* ジョブを取り消す

## <a name="view-job-details"></a>ジョブの詳細を表示する
任意のジョブの詳細を表示するには、以下の手順を実行します。

#### <a name="to-view-job-details"></a>ジョブの詳細を表示するには
1. StorSimple デバイス マネージャー サービスに移動し、 **[ジョブ]** をクリックします。

2. **[ジョブ]** ブレードで適切なフィルターを使用してクエリを実行し、関心のあるジョブを表示します。 完了済み、実行中、または取り消し済みのジョブを検索できます。

    ![ジョブ ブレード 2](./media/storsimple-8000-manage-jobs-u2/jobs1.png)

2. ジョブを選択してクリックします。

    ![ジョブ ブレード 3](./media/storsimple-8000-manage-jobs-u2/jobs3.png)

3. ジョブの詳細ブレードで、状態、詳細、時間統計、およびデータ統計を確認できます。
   
    ![Job details](./media/storsimple-8000-manage-jobs-u2/jobs4.png)

## <a name="cancel-a-job"></a>ジョブを取り消す
実行中のジョブを取り消すには、以下の手順を実行します。

> [!NOTE]
> ボリュームの種類の変更やボリュームの拡張などの一部のジョブは取り消しできません。


### <a name="to-cancel-a-job"></a>ジョブを取り消すには
1. **[ジョブ]** ページで適切なフィルターを使用してクエリを実行し、取り消しを行う実行中のジョブを表示します。 該当するジョブを選択します。

2. 選択したジョブを右クリックしてコンテキスト メニューを呼び出し、 **[キャンセル]** をクリックします。

    ![ジョブの詳細 2](./media/storsimple-8000-manage-jobs-u2/jobs2.png)

3. 確認を求められたら、 **[はい]** をクリックします。 このジョブが取り消されました。

## <a name="next-steps"></a>次のステップ
* [StorSimple バックアップ ポリシーの管理方法](storsimple-8000-manage-backup-policies-u2.md)。
* [StorSimple デバイス マネージャー サービスを使用して StorSimple デバイスを管理する](storsimple-8000-manager-service-administration.md)方法について説明します。

