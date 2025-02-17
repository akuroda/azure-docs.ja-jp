---
title: StorSimple 8000 シリーズのハードウェア コンポーネントと状態 | Microsoft Docs
description: StorSimple デバイス マネージャー サービスを使用して StorSimple デバイスのハードウェア コンポーネントを監視する方法について説明します。
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2018
ms.author: alkohli
ms.openlocfilehash: 6b750659bf22e856ee8ad7368e3fea64dd7245df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94960361"
---
# <a name="use-the-storsimple-device-manager-service-to-monitor-hardware-components-and-status"></a>StorSimple デバイス マネージャー サービスを使用してハードウェア コンポーネントと状態を監視する

## <a name="overview"></a>概要
この記事では、オンプレミスの StorSimple 8000 シリーズ デバイス内のさまざまな物理コンポーネントと論理コンポーネントについて説明します。 また、StorSimple デバイス マネージャー サービスの **[状態とハードウェアの正常性]** ブレードを使用して、デバイス コンポーネントの状態を監視する方法についても説明します。

**[状態とハードウェアの正常性]** ブレードには、StorSimple デバイスのすべてのコンポーネントのハードウェアの状態が表示されます。

8100 のコンポーネントの一覧には、次に説明する 3 つのセクションがあります。

* **共有コンポーネント** : コントローラーの一部ではないコンポーネント (ディスク ドライブ、エンクロージャ、電源および冷却モジュール (PCM) コンポーネント、PCM 温度センサー、ライン電圧センサー、ライン電流センサーなど)。
* **コントローラー 0 のコンポーネント** : コントローラー 0 にあるコンポーネント (コントローラー、SAS エキスパンダーとコネクタ、コントローラー温度センサー、各種ネットワーク インターフェイスなど)。
* **コントローラー 1 のコンポーネント**: コントローラー 1 を構成するコンポーネント。詳細はコントローラー 0 と同様。

8600 デバイスには、Extended Bunch of Disks (EBOD) エンクロージャに対応する追加のコンポーネントがあります。 コンポーネントの一覧には 5 つのセクションがあります。 そのうち 3 つのセクションには主エンクロージャのコンポーネントが含まれており、8100 で説明したものと同じです。 そのほかに、次に説明する EBOD エンクロージャの 2 つのセクションがあります。

* **EBOD コントローラー 0 のコンポーネント** : EBOD エンクロージャ 0 にあるコンポーネント (EBOD コントローラー、SAS エキスパンダーとコネクタ、コントローラー温度センサーなど)。
* **EBOD コントローラー 1 のコンポーネント**: EBOD エンクロージャ 1 を構成するコンポーネント。詳細は EBOD エンクロージャ 0 と同様。
* **EBOD エンクロージャ共有コンポーネント** : EBOD エンクロージャにあるコンポーネントと EBOD コントローラーに含まれない PCM。

> [!NOTE]
> **StorSimple Cloud Appliance (8010/8020) のハードウェアの状態は入手できません。**


## <a name="monitor-the-hardware-status"></a>ハードウェアの状態を監視する
デバイス コンポーネントのハードウェアの状態を表示するには、次の手順を実行します。

1. **[デバイス]** に移動し、特定の StorSimple デバイスを選択します。 **[モニター]、[ハードウェアの正常性]** の順に移動します。

    ![デバイス マネージャー サービスの [設定] ブレードを示すスクリーンショット。 [モニター] の [ハードウェアの正常性] が選択されている。](./media/storsimple-8000-monitor-hardware-status/hw-health1.png)

2. **[ハードウェア コンポーネント]** セクションを見つけ、使用可能なコンポーネントから選択します。 コンポーネントのラベルをクリックするだけで一覧が展開され、さまざまなデバイス コンポーネントの状態が表示されます。 [主エンクロージャのコンポーネントの詳細な一覧](#component-list-for-primary-enclosure-of-storsimple-device)と [EBOD エンクロージャのコンポーネントの詳細な一覧](#component-list-for-ebod-enclosure-of-storsimple-device)をご覧ください。

    ![[ハードウェア コンポーネント] セクションで 1 つのコンポーネントが選択されていることを示すスクリーンショット。 隣接するブレードに、そのコンポーネントのパーツの名前と状態が表示されている。](./media/storsimple-8000-monitor-hardware-status/hw-health2.png)

3. コンポーネントの状態を判断するには、次の色の設定スキームを使用します。
   
   * **緑色のチェック**: **[OK]** 状態にある、正常なコンポーネントを示します。
   * **黄色**: **[警告]** 状態にある、機能が低下しているコンポーネントを示します。
   * **赤色の感嘆符**: **[失敗]** 状態にある、失敗したコンポーネントを示します。
   * **白地に黒色のテキスト** : 存在しないコンポーネントを示します。
   
   次のスクリーン ショットは、コンポーネントの状態が **[OK]** 、 **[警告]** 、および **[失敗]** であるデバイスを示しています。
       
   ![[ハードウェア コンポーネント] セクションの 3 つのコンポーネントを示すスクリーンショット。1 つは正常状態、1 つはエラー状態、1 つは警告状態にある。](./media/storsimple-8000-monitor-hardware-status/hw-health3.png)

   **[共有コンポーネント]** を展開することで、NVRAM とクラスターの機能が低下していることを確認できます。

   ![[共有コンポーネント] 項目が選択されていることを示すスクリーンショット。 隣接するブレードで、NVRAM とクラスターが警告状態になっている。](./media/storsimple-8000-monitor-hardware-status/hw-health5.png)

   **[コントローラー 1 のコンポーネント]** を展開することで、クラスター ノードが失敗していることを確認できます。  

   ![[コントローラー 1 コンポーネント] 項目が選択されていることを示すスクリーンショット。 隣接するブレードで、クラスター ノードがエラー状態になっている。](./media/storsimple-8000-monitor-hardware-status/hw-health4.png)  

4. **正常** な状態にないコンポーネントを見つけた場合は、Microsoft サポートにお問い合わせください。 デバイスでアラートが有効になっている場合は、電子メール アラートを受信します。 障害が発生したハードウェア コンポーネントを交換する必要がある場合は、「 [StorSimple のハードウェア コンポーネントの交換](./storsimple-8000-hardware-component-replacement.md)」をご覧ください。

## <a name="component-list-for-primary-enclosure-of-storsimple-device"></a>StorSimple デバイスの主エンクロージャのコンポーネントの一覧
次の表では、オンプレミスの StorSimple デバイスの主エンクロージャ (8100 と 8600 の両方に存在します) に含まれる物理コンポーネントと論理コンポーネントについて説明します。

| コンポーネント | Module | Type | 場所 | 現場交換可能ユニット (FRU) である | 説明 |
| --- | --- | --- | --- | --- | --- |
| スロット [0 ～ 11] のドライブ |ディスク ドライブ |物理 |共有 |はい |主エンクロージャの SSD ドライブまたは HDD ドライブごとに 1 ラインあります。 |
| 周辺温度センサー |エンクロージャ |物理 |共有 |いいえ |シャーシ内の温度を測定します。 |
| ミッドプレーン温度センサー |エンクロージャ |物理 |共有 |いいえ |ミッドプレーンの温度を測定します。 |
| 可聴アラーム |エンクロージャ |物理 |共有 |いいえ |シャーシ内の可聴アラーム サブシステムが機能しているかどうかを示します。 |
| エンクロージャ |エンクロージャ |物理 |共有 |はい |シャーシがあることを示します。 |
| エンクロージャの設定 |エンクロージャ |物理 |共有 |いいえ |シャーシの前面パネルを示します。 |
| ライン電圧センサー |PCM 0 |物理 |共有 |いいえ |多数のライン電圧センサーではその状態が表示され、測定された電圧が許容範囲内であるかどうかを示します。 |
| ライン電流センサー |PCM 0 |物理 |共有 |いいえ |多数のライン電流センサーではその状態が表示され、測定された電流が許容範囲内であるかどうかを示します。 |
| PCM 内温度センサー |PCM 0 |物理 |共有 |いいえ |インレット センサーやホットスポット センサーなど多数の温度センサーではその状態が表示され、測定された温度が許容範囲内であるかどうかを示します。 |
| 電源装置 [0 ～ 1] |PCM 0 |物理 |共有 |はい |デバイスの背面にある 2 つの PCM の各電源装置に 1 つのラインがあります。 |
| 冷却 [0 ～ 1] |PCM 0 |物理 |共有 |はい |2 つの PCM 内にある 4 つの冷却ファンごとに 1 つのラインがあります。 |
| バッテリ [0 ～ 1] |PCM 0 |物理 |共有 |はい |PCM 内に取り付けられているバックアップ バッテリ モジュールごとに 1 つのラインがあります。 |
| メティス |該当なし |論理 |共有 |該当なし |バッテリの状態が表示されます。充電が必要かどうか、寿命が近いかどうかを示します。 |
| クラスター |該当なし |論理 |共有 |該当なし |2 つの統合コントローラー モジュール間に作成されているクラスターの状態が表示されます。 |
| クラスター ノード |該当なし |論理 |共有 |該当なし |クラスターの一部としてコントローラーの状態を示します。 |
| クラスター クォーラム |該当なし |論理 | |該当なし |HDD の記憶域プール内のマジョリティ ディスク メンバーシップがあるかどうかを示します。 |
| HDD のデータ領域 |該当なし |論理 |共有 |該当なし |ハード ディスク ドライブ (HDD) の記憶域プール内のデータに使用されるストレージ領域。 |
| HDD の管理領域 |該当なし |論理 |共有 |該当なし |管理タスク用に HDD の記憶域プール内に確保されている領域。 |
| HDD のクォーラム領域 |該当なし |論理 |共有 |該当なし |クラスター クォーラム用に HDD の記憶域プール内に確保されている領域。 |
| HDD の交換領域 |該当なし |論理 |共有 |該当なし |コントローラー交換用に HDD の記憶域プール内に確保されている領域。 |
| SSD のデータ領域 |該当なし |論理 |共有 |該当なし |ソリッド ステート ドライブ (SSD) の記憶域プール内のデータに使用されるストレージ領域。 |
| SSD の NVRAM 領域 |該当なし |論理 |共有 |該当なし |NVRAM ロジック専用の SSD の記憶域プール内のストレージ領域。 |
| HDD の記憶域プール |該当なし |論理 |共有 |該当なし |デバイス HDD から作成される論理記憶域プールの状態が表示されます。 |
| SSD の記憶域プール |該当なし |論理 |共有 |該当なし |デバイス SSD から作成される論理記憶域プールの状態が表示されます。 |
| コントローラー [0 ～ 1] [状態] |I/O |物理 |コントローラー |はい |コントローラーの状態と、コントローラーがシャーシ内でアクティブ モードとスタンバイ モードのどちらであるかが表示されます。 |
| コントローラー内温度センサー |I/O |物理 |コントローラー |いいえ |I/O モジュール、CPU 温度、DIMM、PCIe センサーなど多数の温度センサーではその状態が表示され、温度が許容範囲内かどうかを示します。 |
| SAS エキスパンダー |I/O |物理 |コントローラー |いいえ |統合ストレージをコントローラーに接続するために使用される Serial Attached SCSI (SAS) エキスパンダーの状態を示します。 |
| SAS コネクタ [0 ～ 1] |I/O |物理 |コントローラー |いいえ |統合ストレージを SAS エキスパンダーに接続するために使用される各 SAS コネクタの状態を示します。 |
| SBB ミッドプレーン インターコネクト |I/O |物理 |コントローラー |いいえ |各コントローラーをミッドプレーンに接続するために使用されるミッドプレーン コネクタの状態を示します。 |
| プロセッサ コア |I/O |物理 |コントローラー |いいえ |各コントローラー内のプロセッサ コアの状態を示します。 |
| エンクロージャ エレクトロニクス電源 |I/O |物理 |コントローラー |いいえ |エンクロージャで使用される電源システムの状態を示します。 |
| エンクロージャ エレクトロニクス診断 |I/O |物理 |コントローラー |いいえ |コントローラーによって提供される診断サブシステムの状態を示します。 |
| ベースボード管理コントローラー (BMC) |I/O |物理 |コントローラー |いいえ |ベースボード管理コントローラー (BMC) の状態を示します。これは、センサーでハードウェア デバイスを監視する特殊なサービス プロセッサであり、独立した接続を介してシステム管理者と通信します。 |
| イーサネット |I/O |物理 |コントローラー |いいえ |各ネットワーク インターフェイス、つまりコントローラー上に提供される管理ポートとデータ ポートの状態を示します。 |
| NVRAM |I/O |物理 |コントローラー |いいえ |電源障害が発生した場合にアプリケーションに不可欠な情報を保持する、バッテリによってバックアップされる NVRAM (非揮発性ランダム アクセス メモリ) の状態を示します。 |

## <a name="component-list-for-ebod-enclosure-of-storsimple-device"></a>StorSimple デバイスの EBOD エンクロージャのコンポーネントの一覧
次の表では、オンプレミスの StorSimple デバイスの EBOD エンクロージャ (8600 モデルにのみ存在します) に含まれる物理コンポーネントと論理コンポーネントについて説明します。

| コンポーネント | Module | Type | 場所 | FRU である | 説明 |
| --- | --- | --- | --- | --- | --- |
| スロット [0 ～ 11] のドライブ |ディスク ドライブ |物理 |共有 |はい |EBOD エンクロージャの前面にある HDD ドライブごとに 1 ラインあります。 |
| 周辺温度センサー |エンクロージャ |物理 |共有 |いいえ |シャーシ内の温度を測定します。 |
| ミッドプレーン温度センサー |エンクロージャ |物理 |共有 |いいえ |ミッドプレーンの温度を測定します。 |
| 可聴アラーム |エンクロージャ |物理 |共有 |いいえ |シャーシ内の可聴アラーム サブシステムが機能しているかどうかを示します。 |
| エンクロージャ |エンクロージャ |物理 |共有 |はい |シャーシがあることを示します。 |
| エンクロージャの設定 |エンクロージャ |物理 |共有 |いいえ |シャーシの OPS またはフロント パネルを表します。 |
| ライン電圧センサー |PCM 0 |物理 |共有 |いいえ |多数のライン電圧センサーではその状態が表示され、測定された電圧が許容範囲内であるかどうかを示します。 |
| ライン電流センサー |PCM 0 |物理 |共有 |いいえ |多数のライン電流センサーではその状態が表示され、測定された電流が許容範囲内であるかどうかを示します。 |
| PCM 内温度センサー |PCM 0 |物理 |共有 |いいえ |インレット センサーやホットスポット センサーなど多数の温度センサーではその状態が表示され、測定された温度が許容範囲内であるかどうかを示します。 |
| 電源装置 [0 ～ 1] |PCM 0 |物理 |共有 |はい |デバイスの背面にある 2 つの PCM の各電源装置に 1 つのラインがあります。 |
| 冷却 [0 ～ 1] |PCM 0 |物理 |共有 |はい |2 つの PCM 内にある 4 つの冷却ファンごとに 1 つのラインがあります。 |
| ローカル ストレージ [HDD] |該当なし |論理 |共有 |該当なし |デバイス HDD から作成される論理記憶域プールの状態が表示されます。 |
| コントローラー [0 ～ 1] [状態] |I/O |物理 |コントローラー |はい |EBOD モジュール内のコントローラーの状態が表示されます。 |
| EBOD 内温度センサー |I/O |物理 |コントローラー |いいえ |各コントローラーの多数の温度センサーではその状態が表示され、温度が許容範囲内であるかどうかを示します。 |
| SAS エキスパンダー |I/O |物理 |コントローラー |いいえ |統合ストレージをコントローラーに接続するために使用される SAS エキスパンダーの状態を示します。 |
| SAS コネクタ [0 ～ 2] |I/O |物理 |コントローラー |いいえ |統合ストレージを SAS エキスパンダーに接続するために使用される各 SAS コネクタの状態を示します。 |
| SBB ミッドプレーン インターコネクト |I/O |物理 |コントローラー |いいえ |各コントローラーをミッドプレーンに接続するために使用されるミッドプレーン コネクタの状態を示します。 |
| エンクロージャ エレクトロニクス電源 |I/O |物理 |コントローラー |いいえ |エンクロージャで使用される電源システムの状態を示します。 |
| エンクロージャ エレクトロニクス診断 |I/O |物理 |コントローラー |いいえ |コントローラーによって提供される診断サブシステムの状態を示します。 |
| デバイス コントローラーへの接続 |I/O |物理 |コントローラー |いいえ |EBOD I/O モジュールとデバイス コントローラー間の接続の状態を示します。 |

## <a name="next-steps"></a>次のステップ
* StorSimple デバイス マネージャー サービスを使用してデバイスを管理する方法については、[StorSimple デバイス マネージャー サービスを使用した StorSimple デバイスの管理](storsimple-8000-manager-service-administration.md)を参照してください。
* 機能低下または障害の状態のデバイス コンポーネントのトラブルシューティングが必要な場合は、「 [StorSimple モニタリング インジケーター](storsimple-monitoring-indicators.md)」をご覧ください。
* 障害が発生したハードウェア コンポーネントを交換するには、「 [StorSimple のハードウェア コンポーネントの交換](./storsimple-8000-hardware-component-replacement.md)」をご覧ください。
* デバイスの問題が解決しない場合は、 [Microsoft サポートにお問い合わせください](storsimple-8000-contact-microsoft-support.md)。