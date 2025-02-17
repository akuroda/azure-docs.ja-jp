---
title: StorSimple 8000 シリーズのシステム制限 | Microsoft Docs
description: StorSimple 8000 シリーズ コンポーネントおよび接続のシステム制限および推奨サイズについて説明します。
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "68963384"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>StorSimple 8000 シリーズのシステム制限とは

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>概要

StorSimple は、データセンターのスケーラブルかつフレキシブルなストレージを提供します。 ただし、StorSimple ソリューションを計画、デプロイ、および実施する際に留意する必要がある制限がいくつかあります。 StorSimple ソリューションを最大限に活用できるように、これらの制限と推奨事項のいくつかを以下の表に示します。

| 制限の種類 | 制限 | 説明 |
| --- | --- | --- |
| ストレージ アカウントの資格情報の最大数 |64 | |
| ボリューム コンテナーの最大数 |64 | |
| ボリュームの最大数 |255 | |
| ローカル固定ボリュームの最大数 |32 | |
| 帯域幅テンプレートごとのスケジュールの最大数 |168 |毎日の 1 時間ごとのスケジュール (24\*7)。 |
| 物理デバイス上の階層化ボリュームの最大サイズ |8100 と 8600 では 64 TB |8100 と 8600 は物理デバイスです。 |
| Azure 内の仮想デバイス上の階層化ボリュームの最大サイズ |8010 では 30 TB <br></br> 8020 では 64 TB |8010 と 8020 は、それぞれ Standard Storage と Premium Storage を使用する Azure の仮想デバイスです。 |
| 物理デバイス上のローカル固定ボリュームの最大サイズ |8100 では 8.5 TB <br></br> 8600 では 22.5 TB |8100 と 8600 は物理デバイスです。 |
| iSCSI 接続の最大数 |512 | |
| イニシエーターからの iSCSI 接続の最大数 |512 | |
| デバイスごとのアクセス制御レコードの最大数 |64 | |
| バックアップ ポリシーごとのボリュームの最大数 |20 | |
| スケジュールごとに保持されるバックアップの最大数 (バックアップ ポリシー) |64 | |
| バックアップ ポリシーごとのスケジュールの最大数 |10 | |
| 種類に関係なく保持されるスナップショットのボリュームあたりの最大数 |256 |これにはローカル スナップショットとクラウド スナップショットが含まれます。 |
| 1 つのデバイス内に存在できるスナップショットの最大数 |10,000 | |
| バックアップ、復元、またはクローニングのために並列に処理できるボリュームの最大数 |16 |<ul><li>ボリュームの数が 16 を超える場合は、処理スロットが使用可能になるのに合わせて順番に処理されます。</li><li>複製または復元された階層化ボリュームの新規バックアップは、前の操作が完了するまで実行できません。 ただし、ローカル ボリュームでは、バックアップはボリュームがオンラインになった後で可能になります。</li></ul> |
| 階層化ボリュームの復元および複製による復旧時間 |2 分未満 |<ul><li>ボリュームは、そのサイズにかかわらず、復元または複製の操作から 2 分以内に利用可能になります。</li><li>最初は、データやメタデータの大部分がまだクラウドにあるため、ボリュームのパフォーマンスが通常より低下する場合があります。 クラウドから StorSimple デバイスに移されるデータが増えるにつれて、パフォーマンスが向上することがあります。</li><li>メタデータのダウンロードにかかる合計時間は、割り当てられたボリューム サイズによって異なります。 割り当てられたボリューム データの TB (テラバイト) あたり 5 分の速度で、メタデータはバック グラウンドで自動的にデバイスに移されます。 この速度はクラウドへのインターネット帯域幅によって影響を受けることがあります。</li><li>すべてのメタデータがデバイスに移動した時点で、復元または複製の操作は完了します。</li><li>復元または複製の操作が完全に完了するまで、バックアップ操作は実行できません。 |
| ローカル固定ボリュームの復元回復時間 |2 分未満 |<ul><li>ボリュームは、そのサイズにかかわらず、復元操作から 2 分以内に利用可能になります。</li><li>最初は、データやメタデータの大部分がまだクラウドにあるため、ボリュームのパフォーマンスが通常より低下する場合があります。 クラウドから StorSimple デバイスに移されるデータが増えるにつれて、パフォーマンスが向上することがあります。</li><li>メタデータのダウンロードにかかる合計時間は、割り当てられたボリューム サイズによって異なります。 割り当てられたボリューム データの TB (テラバイト) あたり 5 分の速度で、メタデータはバック グラウンドで自動的にデバイスに移されます。 この速度はクラウドへのインターネット帯域幅によって影響を受けることがあります。</li><li>階層化ボリュームとは異なり、ローカル固定ボリュームでは、ボリューム データもデバイスにローカルにダウンロードされます。 ボリュームのすべてのデータがデバイスに移行した時点で、復元操作が完了します。</li><li>復元操作は長くなる可能性があります。 復元が完了するまでにかかる時間は、プロビジョニングされたローカル ボリューム、インターネットの帯域幅、およびデバイス上の既存のデータのサイズによって異なります。 復元操作の進行中に、ローカル固定ボリュームのバックアップ操作を実行できます。 |
| クラウド スナップショットの処理速度 |15 分/TB |<ul><li>バックアップの割り当て済みボリューム データ 1 TB のクラウド スナップショットをアップロードできるように準備する最小時間。 </li><li> 総クラウド スナップショット時間は、この時間をスナップショット アップロード時間に加算して計算します。アップロード時間は、クラウドへのインターネット帯域幅によって影響を受けます。 |
| クライアントによる最大読み取り/書き込みスループット (SSD 層から提供された場合)* |920/720 MB/秒 (単一の 10 GbE ネットワーク インターフェイスを使用) |単一の MPIO と 2 つのネットワーク インターフェイスを使用する場合は最大 2x。 |
| クライアントによる最大読み取り/書き込みスループット (HDD 層から提供された場合)* |120/250 MB/秒 | |
| Update 3 以降** のクライアントによる最大読み取り/書き込みスループット (クラウド層から提供された場合)* |40/60 MB/秒 (階層化ボリュームの場合)<br><br>60/80 MB/秒 (ボリュームの作成時に選択されたアーカイブ オプションを使用した階層化ボリュームの場合) |読み取りスループットは、十分な I/O キューの深さを生成および維持するクライアントに依存します。 <br><br>実際の速度は、使用する基になるストレージ アカウントの速度によって異なります。 |

&#42; I/O の種類ごとの最大スループットは、読み取り率と書き込み率が 100% の場合のシナリオにおいて測定されたものです。 実際のスループットはこれより低くなる可能性があり、I/O の組み合わせとネットワーク状態によって異なります。

&#42;&#42; Update 3 より前のパフォーマンスの数値は低くなる可能性があります。

## <a name="next-steps"></a>次のステップ
[StorSimple のシステム要件](storsimple-8000-system-requirements.md)を確認します。

