---
title: メモリ リークの検出 - Azure Application Insights のスマート検出
description: Azure Application insights を使用して、メモリ リークの可能性を監視します。
ms.topic: conceptual
ms.date: 12/12/2017
ms.openlocfilehash: 3fe58cd7d61246c5565cd89fa782c8a977f09499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "86539892"
---
# <a name="memory-leak-detection-preview"></a>メモリ リーク検出 (プレビュー)

Application Insight は、アプリケーションの各プロセスのメモリ消費量を自動的に分析し、メモリ リークの可能性やメモリ使用量の増加を警告することができます。

この機能を使用するには、アプリの[パフォーマンス カウンターを構成する](./performance-counters.md)こと以外は、特別な設定は必要ありません。 アプリケーションから十分な量のメモリ パフォーマンス カウンター テレメトリ (プライベート バイトなど) が生成されると、有効になります。

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>この種類のスマート検出通知はどのような場合に取得されますか。
一般的な通知は、アプリケーションの一部である 1 つ以上のプロセスや 1 つ以上のコンピューターで、長時間にわたってメモリ使用量が一貫して増加すると送信されます。 メモリ リークのパターンに一致するメモリ消費の増加を検出するため、機械学習アルゴリズムが使用されます。

## <a name="does-my-app-really-have-a-problem"></a>アプリに問題があるのは確かですか
いいえ。通知は、アプリに確実に問題があることを示すものではありません。 通常、メモリ リーク パターンはアプリケーションの問題を示していますが、特定のプロセスに典型的なパターンか、自然なビジネス上の正当性があり無視できるパターンの可能性があります。

## <a name="how-do-i-fix-it"></a>どのように修正すればよいですか
通知には、診断分析プロセスでサポートできる診断情報が含まれます。
1. **トリアージ**。 この通知には、メモリの増加量 (GB 単位) とメモリが増加した時間の範囲が表示されます。 この情報を基に、問題に優先順位を割り当てることができます。
2. **範囲**。 メモリ リーク パターンを示すコンピューターの数。 メモリ リークの可能性がある間にトリガーされた例外の数。 この情報を通知から取得できます。
3. **診断**。 検出にはメモリ リーク パターンが含まれており、時間の経過によるプロセスのメモリ使用量が示されます。 サポート情報にリンクしている関連項目とレポートを使用して、問題の詳細な診断に役立てることもできます。
