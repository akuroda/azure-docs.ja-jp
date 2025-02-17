---
title: Windows Server 用 Azure Automanage
description: Azure Automanage for virtual machines で、Windows Server マシン用に自動的にオンボードおよび構成されるサービスのベスト プラクティスについて説明します。
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: a3bb0a2877c71d19b05f424dc44e302c69577b2d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101660455"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanage for virtual machines のベスト プラクティス - Windows Server

Windows Server VM で Automanage for virtual machines (VM) を使用すると、これらの Azure サービスが自動的にオンボードされます。 それらはベスト プラクティスのホワイト ペーパーに不可欠なものであり、[クラウド導入フレームワーク](/azure/cloud-adoption-framework/manage/azure-server-management)を参照してください。

これらのすべてのサービスについては、Microsoft が自動オンボード、自動構成、ドリフトの監視、ドリフトが検出された場合の修復を行います。 このプロセスの詳細については、「[Azure Automanage for virtual machines](automanage-virtual-machines.md)」を参照してください。

## <a name="supported-windows-server-versions"></a>サポートされている Windows Server のバージョン

Automanage でサポートされている Windows Server のバージョンは、次のとおりです。

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>対象となるサービス

|サービス    |説明    |サポートされている環境<sup>1</sup>    |サポートされている基本設定<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM の分析情報の監視    |Azure Monitor for VMs では、実行中のプロセスや他のリソースへの依存関係など、仮想マシンのパフォーマンスと正常性が監視されます。 [詳細情報](../azure-monitor/vm/vminsights-overview.md)。    |Production    |いいえ    |
|バックアップ    |Azure Backup では、VM 上のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 [詳細情報](../backup/backup-azure-vms-introduction.md)。 料金は、保護対象の VM の数とサイズに基づいています。 [詳細情報](https://azure.microsoft.com/pricing/details/backup/)。    |Production    |Yes    |
|Azure Security Center    |Azure Security Center は統合されたインフラストラクチャ セキュリティ管理システムであり、データ センターのセキュリティ体制を強化し、クラウド内のハイブリッド ワークロード全体にわたる高度な脅威保護が提供されます。 [詳細情報](../security-center/security-center-introduction.md)。  VM が存在するサブスクリプションは、Automanage によって、Azure Security Center の Free レベルのオファリングに構成されます。 ご利用のサブスクリプションが Azure Security Center に既にオンボードされている場合、Automanage によって再構成されることはありません。    |運用、Dev/Test    |No    |
|Microsoft Antimalware    |Azure に対する Microsoft マルウェア対策は、ウイルス、スパイウェアなどの悪意のあるソフトウェアの特定や駆除に役立つリアルタイムの保護です。 これにより、既知の悪意あるソフトウェアや望ましくないソフトウェアが Azure システム上にソフトウェア自体をインストールまたは実行しようとしたときに、アラートが生成されます。 [詳細情報](../security/fundamentals/antimalware.md)。 |運用、Dev/Test    |Yes    |
|更新管理    |Azure Automation の Update Management を使用して、仮想マシンのオペレーティング システムの更新プログラムを管理することができます。 すべてのエージェント マシンで利用可能な更新プログラムの状態をすばやく評価し、サーバーに必要な更新プログラムをインストールするプロセスを管理できます。 [詳細情報](../automation/update-management/overview.md)。    |運用、Dev/Test    |No    |
|変更履歴とインベントリ    |変更履歴とインベントリでは、変更履歴とインベントリの機能を組み合わせて、仮想マシンとサーバー インフラストラクチャの変更を追跡できます。 このサービスを利用すれば、環境内のサービス、デーモン、ソフトウェア、レジストリ、ファイル全体にわたって変更を追跡し、不要な変更を診断したりアラートを生成したりすることができます。 インベントリのサポートにより、ゲスト リソースでクエリを実行して、インストール済みのアプリケーションやその他の構成アイテムを可視化できます。  [詳細情報](../automation/change-tracking/overview.md)。    |運用、Dev/Test    |No    |
|Azure ゲスト構成    | ゲスト構成ポリシーは、マシンのコンプライアンスに関するレポートを作成し、構成を監視するために使用されます。 ゲスト構成拡張機能を使用して、Automanage サービスによって [Windows セキュリティ ベースライン](/windows/security/threat-protection/windows-security-baselines)がインストールされます。 Windows マシンの場合、準拠していない場合はゲスト構成サービスによってベースライン設定が自動的に再適用されます。 [詳細情報](../governance/policy/concepts/guest-configuration.md)。    |運用、Dev/Test    |No    |
|Azure Automation アカウント    |Azure Automation は、インフラストラクチャとアプリケーションのライフサイクル全体にわたる管理をサポートします。 [詳細情報](../automation/automation-intro.md)。    |運用、Dev/Test    |No    |
|Log Analytics ワークスペース    |Azure Monitor では、ログ データが Log Analytics ワークスペースに格納されます。Log Analytics ワークスペースは Azure リソースであり、データが収集、集計され、管理境界として機能するコンテナーです。 [詳細情報](../azure-monitor/logs/design-logs-deployment.md)。    |運用、Dev/Test    |いいえ    |


<sup>1</sup> Automanage を有効にすると、環境の選択を使用できます。 [詳細情報](automanage-virtual-machines.md#environment-configuration)。 環境の既定の設定を調整し、ベスト プラクティスの制約内で独自の基本設定を指定することもできます。


## <a name="next-steps"></a>次の手順

Azure portal でAzure Automanage for virtual machines を有効にしてみてください。

> [!div class="nextstepaction"]
> [Azure portal で Azure Automanage for virtual machines を有効にする](quick-create-virtual-machines-portal.md)