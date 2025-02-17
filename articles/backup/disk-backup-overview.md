---
title: Azure ディスク バックアップの概要
description: Azure ディスク バックアップ ソリューションについて説明します。
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 4db2a5f3f02322f18fcf9203c3560905cde86996
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98915510"
---
# <a name="overview-of-azure-disk-backup-in-preview"></a>Azure ディスク バックアップ (プレビュー) の概要

>[!IMPORTANT]
>Azure ディスク バックアップは、サービス レベル アグリーメントのないプレビュー段階であるため、運用ワークロードにはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 利用可能なリージョンについては、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。
>
>プレビューにサインアップするには、[このフォームに入力](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)してください。

Azure ディスク バックアップは、マネージド ディスク内のデータを保護するネイティブのクラウドベースのバックアップ ソリューションです。 これはわずかな手順でマネージド ディスクの保護を構成できる、シンプルで安全なコスト効率の高いソリューションです。 これによって災害時にデータを復旧できるようになります。

Azure ディスク バックアップは、スナップショットの定期的な作成を自動化し、バックアップ ポリシーを使用して構成された期間にわたってそのスナップショットを保持することで、マネージド ディスクのスナップショット ライフサイクル管理を提供する、ターンキー ソリューションを提供します。 インフラストラクチャ コストなしでディスク スナップショットを管理でき、カスタム スクリプトも管理費用も必要ありません。 これは、1 日に複数のバックアップをサポートする[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用してマネージド ディスクの特定時点のバックアップを作成する、クラッシュ整合性バックアップ ソリューションです。 また、エージェントレスのソリューションでもあり、運用アプリケーションのパフォーマンスに影響しません。 実行中の Azure 仮想マシンに OS とデータ ディスク (共有ディスクを含む) が現在接続されているかどうかにかかわらず、この両方のバックアップと復元がサポートされます。

データ ディスクを含む仮想マシンのアプリケーション整合性バックアップが必要な場合、またはバックアップから仮想マシン全体を復元したり、ファイルまたはフォルダーを復元したり、セカンダリ リージョンに復元したりするオプションが必要な場合は、[Azure VM バックアップ](backup-azure-vms-introduction.md) ソリューションを使用します。 Azure Backup では、[Azure VM バックアップ](./backup-azure-vms-introduction.md) ソリューションに加えて、ディスク バックアップを使用したマネージド ディスクのバックアップの並列サポートが提供されます。 これは、仮想マシンの 1 日 1 回のアプリケーション整合性バックアップと、運用アプリケーションのパフォーマンスに影響を与えることなく OS ディスクまたは特定のデータ ディスクのより頻繁なクラッシュ整合性バックアップも行う必要がある場合に便利です。

Azure ディスク バックアップは、バックアップ センターに統合され、これによって企業が Azure でバックアップを大規模に管理、監視、操作、分析するための **1 つに統合された管理エクスペリエンス** が実現されます。

## <a name="key-benefits-of-disk-backup"></a>ディスク バックアップの主な利点

Azure ディスク バックアップは、[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用する、エージェントレスでクラッシュ整合性のあるソリューションで、次のような利点があります。

- 仮想マシンを中断することなく、より頻繁に高速なバックアップを行うことができます。
- 運用アプリケーションのパフォーマンスには影響しません。
- カスタム スクリプトの実行もエージェントのインストールも不要であるため、セキュリティ上の懸念がありません。
- 仮想マシン全体をバックアップする場合と比べて、特定のディスクをバックアップするコスト効率に優れたソリューションです。

Azure ディスク バックアップ ソリューションは、次のようなシナリオで役立ちます。

- アプリケーションを休止することなく、1 日のうちに頻繁にバックアップする必要がある
- クラスター シナリオで実行されているアプリ: Windows Server フェールオーバー クラスターと Linux クラスターの両方で共有ディスクへの書き込みが行われている
- アプリケーションのセキュリティまたはパフォーマンスに関する懸念があるため、エージェントレス バックアップを特に行う必要がある
- 基幹業務アプリでボリューム シャドウ コピー サービス (VSS) がサポートされていないため、VM のアプリケーション整合性バックアップを実現できない

次のシナリオでは、Azure ディスク バックアップを検討してください。

- ミッション クリティカルなアプリケーションが実行されている Azure 仮想マシンにおいて、回復ポイントの目標を達成するために 1 日に複数回のバックアップが必要であるが、運用環境やアプリケーションのパフォーマンスに影響を与えてはならない
- セキュリティ上の懸念があるため、組織または業界の規制によりエージェントのインストールが制限されている
- カスタムの事前または事後スクリプトを実行し、Linux 仮想マシンで凍結と解凍を呼び出してアプリケーション整合性バックアップを取得すると、利用可能な運用ワークロードに対して過度のオーバーヘッドが発生する
- Azure Kubernetes Service (AKS クラスター) 上で実行されているコンテナ化されたアプリケーションが、マネージド ディスクを永続ストレージとして使用している。 現在、管理が困難な自動化スクリプトを使用して、マネージド ディスクをバックアップする必要がある。
- マネージド ディスクが、重要なビジネス データを保持しているか、ファイル共有として使用されているか、データベース バックアップ ファイルを格納しており、Azure VM のバックアップに投資せずにバックアップ コストを最適化する必要がある
- Web サーバーまたはステートレス マシンをホストしているか、アプリケーション構成設定を持つステージング環境として機能している多くの Linux および Windows の単一ディスク仮想マシン (OS ディスクのみ存在してデータ ディスクが接続されていない仮想マシン) がある場合に、OS ディスクを保護するためのコスト効率の高いバックアップ ソリューションを必要としている。 たとえば、仮想マシンをアップグレードするか修正プログラムを適用する前に、迅速なオンデマンド バックアップをトリガーする場合。
- Azure VM バックアップ ソリューションでサポートされていない OS 構成 (Windows Server 2008 32 ビット版など) が仮想マシンで実行されている

## <a name="how-the-backup-and-restore-process-works"></a>バックアップと復元のプロセスのしくみ

- Azure マネージド ディスクのバックアップを構成するための最初の手順は、[バックアップ コンテナー](backup-vault-overview.md)を作成することです。 このコンテナーは、さまざまなワークロードにわたって構成されているバックアップの統合ビューを提供します。

- 次に、バックアップの頻度と保有期間を構成できるバックアップ ポリシーを作成します。

- バックアップを構成するには、バックアップ コンテナーに移動し、バックアップ ポリシーを割り当てて、バックアップが必要なマネージド ディスクを選択し、スナップショットを格納および管理するリソース グループを指定します。 Azure Backup はバックアップの頻度に応じて、ディスクの増分スナップショットを作成するスケジュールされたバックアップ ジョブを自動的にトリガーします。 古いスナップショットは、バックアップ ポリシーで指定された保有期間に従って削除されます。

- Azure Backup ではマネージド ディスクの[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md#restrictions)が使用されます。 増分スナップショットは、前回のスナップショット以降のディスクへの差分変更に対して課金される、コスト効率に優れたマネージド ディスクの特定時点のバックアップです。 これは常に、親ディスクの記憶域の種類に関係なく、最もコスト効率の高い記憶域である Standard HDD ストレージに格納されます。 ディスクの最初のスナップショットには、ディスクの使用済みサイズの部分が収容され、後続の増分スナップショットには、最後のスナップショット以降のディスクへの差分変更が格納されます。

- マネージド ディスクのバックアップを構成すると、バックアップ コンテナー内にバックアップ インスタンスが作成されます。 バックアップ インスタンスを使用すると、バックアップ操作の正常性を確認したり、オンデマンド バックアップをトリガーしたり、復元操作を実行したりできます。 単一のウィンドウを提供するバックアップ センターを使用して、複数のコンテナーおよびバックアップ インスタンスにわたってバックアップの正常性を表示することもできます。

- 復元する場合は、ディスクを復元する回復ポイントを選択するだけです。 スナップショットから復元されたディスクを作成するリソース グループを指定します。 スナップショットはお使いのサブスクリプションにローカルに格納されるため、Azure Backup は瞬時に復元を行うことができます。

- バックアップ コンテナーは、マネージド ID を使用して他の Azure リソースにアクセスします。 マネージド ディスクのバックアップを構成して、以前のバックアップから復元するには、バックアップ コンテナーのマネージド ID に、ソース ディスク、スナップショットを作成および管理するスナップショット リソース グループ、およびバックアップを復元するターゲット リソース グループに対する一連のアクセス許可が必要です。 マネージド ID には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してアクセス許可を付与できます。 マネージド ID は、Azure リソースでのみ使用できる特殊な種類のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。

- 現在 Azure ディスク バックアップでは、マネージド ディスクの運用バックアップがサポートされており、バックアップがバックアップ コンテナー ストレージにコピーされません。 サポート対象およびサポート対象外のシナリオの詳細な一覧については、[サポート マトリックス](disk-backup-support-matrix.md)を参照してください。

## <a name="pricing"></a>価格

Azure Backup では、Azure ディスクを保護するためのスナップショット ライフサイクル管理ソリューションが提供されています。 Azure Backup によって作成されたディスク スナップショットはお使いの Azure サブスクリプション内のリソース グループに格納され、**スナップショット ストレージ** 料金が発生します。 スナップショットの価格の詳細については、「[Managed Disks の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」を参照してください。 スナップショットはバックアップ コンテナーにコピーされないため、Azure Backup では **保護されたインスタンス** の料金が課金されず、**バックアップ ストレージ** コストが適用されません。 さらに、増分スナップショットには前回のスナップショット以降の差分変更が収容され、親マネージド ディスクのストレージの種類に関係なく、常に Standard Storage に格納され、Standard Storage の価格に従って課金されます。 このため、Azure ディスク バックアップはコスト効率の高いソリューションになります。

## <a name="next-steps"></a>次の手順

- [Azure ディスク バックアップのサポート マトリックス](disk-backup-support-matrix.md)
