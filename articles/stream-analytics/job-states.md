---
title: Azure Stream Analytics のジョブの状態
description: この記事では、Stream Analytics ジョブの 4 種類の状態 (実行中、停止、デグレード、失敗) について説明します。
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: dd298fae148b9e1e51072e98a4e0e5ece10967f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020453"
---
# <a name="azure-stream-analytics-job-states"></a>Azure Stream Analytics のジョブの状態

Stream Analytics ジョブは常に、実行中、停止、デグレード、失敗の 4 つの状態のいずれかに当てはまります。 ジョブの状態は、Azure portal の Stream Analytics ジョブの概要ページで確認できます。 

| State | 説明 | 推奨アクション |
| --- | --- | --- |
| **実行中** | ジョブは Azure 上で実行されており、定義された入力ソースからのイベントを読み取って、それらを処理し、構成済みの出力シンクに結果を書き込んでいます。 | [主要メトリック](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)を監視して、ジョブのパフォーマンスを追跡することをお勧めします。 |
| **停止** | ジョブは停止し、イベントを処理していません。 | NA | 
| **デグレード** | 入力接続と出力接続に断続的な問題がある可能性があります。 これらのエラーは一時的エラーと呼ばれます。これにより、ジョブが機能低下状態になる場合があります。 Stream Analytics は、このようなエラーからすぐに回復し、(数分内に) 実行中の状態に戻ろうと試行します。 これらのエラーの原因としては、ネットワークの問題、他の Azure リソースの可用性、逆シリアル化エラーなどが考えられます。ジョブがデグレード状態の時には、ジョブのパフォーマンスが低下する可能性があります。| [診断ログやアクティビティ ログ](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs)を見て、これらの一時的エラーの原因に関する詳細を調べることができます。 逆シリアル化エラーなどの場合には、イベントが無効にならないよう、是正措置を実行することをお勧めします。 ジョブのリソース使用率が何度も制限値に達している場合は、SU の数を増やすか、[ジョブを並列化する](./stream-analytics-parallelization.md)ことをお試しください。 ユーザーが是正措置を実行できない場合には、Stream Analytics が *実行中* の状態に回復しようと試みます。 <br> [基準値の遅延](./stream-analytics-set-up-alerts.md#scenarios-to-monitor)メトリックを使用して、これらの一時的エラーがジョブのパフォーマンスに影響するかどうか判別できます。|
| **Failed** | ジョブで重大なエラーが発生し、失敗の状態になりました。 イベントは読み取られず、処理もされていません。 ジョブが失敗の状態になる一般的な原因は、ランタイム エラーです。 | [アラートを構成](./stream-analytics-set-up-alerts.md#set-up-alerts-in-the-azure-portal)すると、ジョブが失敗状態になった際に通知を受けることができます。 <br> <br>[アクティビティ ログやリソース ログ](./stream-analytics-job-diagnostic-logs.md#debugging-using-activity-logs)を使用してデバッグを行うことにより、根本原因を特定し、問題に対処することができます。|

## <a name="next-steps"></a>次のステップ
* [Azure Stream Analytics ジョブのアラートを設定する](stream-analytics-set-up-alerts.md)
* [Stream Analytics で使用できるメトリック](./stream-analytics-monitoring.md#metrics-available-for-stream-analytics)
* [アクティビティ ログとリソース ログを使用したトラブルシューティング](./stream-analytics-job-diagnostic-logs.md)