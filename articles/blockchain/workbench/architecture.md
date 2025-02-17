---
title: Azure Blockchain Workbench アーキテクチャ
description: Azure Blockchain Workbench Preview アーキテクチャとそのコンポーネントの概要。
ms.date: 09/05/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 1fff70ef2eeb1dc27d33769fd48fe5923f56717b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96021607"
---
# <a name="azure-blockchain-workbench-architecture"></a>Azure Blockchain Workbench アーキテクチャ

Azure Blockchain Workbench Preview では、複数の Azure コンポーネントを使用したソリューションを提供することで、ブロックチェーン アプリケーションの開発を簡単にします。 Blockchain Workbench は、Azure Marketplace のソリューション テンプレートを使用してデプロイできます。 このテンプレートを使用すると、ブロックチェーン スタック、クライアント アプリケーションの種類、IoT 統合のサポートなど、デプロイするモジュールやコンポーネントを選択できます。 デプロイが完了すると、Blockchain Workbench によって Web アプリ、iOS アプリ、Android アプリへのアクセスが提供されます。

![Blockchain Workbench アーキテクチャ](./media/architecture/architecture.png)

## <a name="identity-and-authentication"></a>ID と認証

Blockchain Workbench を使用すると、コンソーシアムは Azure Active Directory (Azure AD) を使用してエンタープライズ ID をフェデレーションできます。 Workbench は、Azure AD に格納されているエンタープライズ ID を使用して、オンチェーン ID 用に新しいユーザー アカウントを生成します。 この ID マッピングにより、クライアント API およびアプリケーションに対する認証ログインが容易になり、組織の認証ポリシーが使用されます。 また、Workbench には、指定したスマート コントラクト内の特定のロールにエンタープライズ ID を関連付ける機能も用意されています。 さらに、Workbench には、それらのロールが実行できるアクションとその時刻を識別するメカニズムも備わっています。

Blockchain Workbench がデプロイされたら、ユーザーは、クライアント アプリケーション、REST ベースのクライアント API、メッセージング API のいずれかを介して Blockchain Workbench と対話します。 どの場合も、Azure Active Directory (Azure AD) またはデバイス固有の資格情報を介して対話を認証する必要があります。

ユーザーは、参加者のメール アドレス宛てに招待メールを送信することで、自分の ID をコンソーシアムの Azure AD にフェデレーションします。 このようなユーザーは、ログイン時に、名前、パスワード、ポリシーを使用して認証されます。 たとえば、組織の 2 要素認証があります。

Azure AD は、Blockchain Workbench にアクセスできるすべてのユーザーの管理に使用されます。 スマート コントラクトに接続しているデバイスはそれぞれ、Azure AD にも関連付けられます。

また、Azure AD は、ユーザーを特別な管理者グループに割り当てる際にも使用されます。 管理者グループに関連付けられているユーザーには、Blockchain Workbench 内での権利やアクションへのアクセスが付与されます。これには、コントラクトのデプロイや、ユーザーがコントラクトにアクセスするためのアクセス許可の付与が含まれます。 このグループ外のユーザーは管理者のアクションにアクセスできません。

## <a name="client-applications"></a>クライアント アプリケーション

Workbench では、Web およびモバイル (iOS、Android) 用に自動生成されたクライアント アプリケーションを提供します。このようなクライアント アプリケーションは、ブロックチェーン アプリケーションの検証、テスト、表示に使用できます。 アプリケーションのインターフェイスは、スマート コントラクト メタデータに基づいて動的に生成されるため、すべてのユース ケースに対応できます。 クライアント アプリケーションは、Blockchain Workbench によって生成された完全なブロックチェーン アプリケーションに対して、ユーザーに対応するフロントエンドを提供します。 クライアント アプリケーションは、Azure Active Directory (Azure AD) を通じてユーザーを認証した後、スマート コントラクトのビジネス コンテキストに合わせたユーザー エクスペリエンスを提示します。 このユーザー エクスペリエンスにより、承認された個人による新しいスマート コントラクト インスタンスの作成が可能になった後、スマート コントラクトが表すビジネス プロセスの適切な時点で特定の種類のトランザクションを実行する機能が示されます。

Web アプリケーションでは、承認されたユーザーが Administrator Console にアクセスできます。 このコンソールを使用できるのは Azure AD の管理者グループのユーザーです。ここから次の機能にアクセスできます。

* 一般的なシナリオで Microsoft が提供するスマート コントラクトをデプロイします。 たとえば、資産移動のシナリオ。
* 独自のスマート コントラクトをアップロードしてデプロイします。
* 特定のロールのコンテキストにおけるスマート コントラクトへのアクセス権をユーザーに割り当てます。

詳細については、[GitHub で Azure Blockchain Workbench のサンプル クライアント アプリケーション](https://github.com/Azure-Samples/blockchain-devkit/tree/master/connect/mobile)を参照してください。

## <a name="gateway-service-api"></a>ゲートウェイ サービス API

Blockchain Workbench には、REST ベースのゲートウェイ サービス API が用意されています。 ブロックチェーンに書き込むと、この API によってメッセージが生成され、イベント ブローカーに配信されます。 API によってデータが要求されると、オフチェーン データベースにクエリが送信されます。 この データベースには、サポートされているスマート コントラクトのコンテキストと構成情報を提供するオンチェーン データとメタデータのレプリカが含まれています。 クエリによって、コントラクトのメタデータによって通知される形式でオフチェーン レプリカから必要なデータが返されます。

開発者は、Blockchain Workbench クライアント アプリに依存することなく、ゲートウェイ サービス API にアクセスしてブロックチェーン ソリューションを構築または統合できます。

> [!NOTE]
> API への認証済みのアクセスを有効にするために、2 つのクライアント アプリケーションが Azure Active Directory に登録されています。 Azure Active Directory では、アプリケーションの種類 (ネイティブと Web) ごとに別々のアプリケーション登録が必要です。

## <a name="message-broker-for-incoming-messages"></a>受信メッセージのメッセージ ブローカー

Blockchain Workbench に直接メッセージを送信したい開発者は、Service Bus に直接メッセージを送信できます。 たとえば、メッセージ API は、システム間の統合や IoT デバイスに使用できます。

## <a name="message-broker-for-downstream-consumers"></a>ダウンストリーム コンシューマーのメッセージ ブローカー

アプリケーションのライフサイクル中にイベントが発生します。 イベントは、ゲートウェイ API によってトリガーすることも、台帳でトリガーすることもできます。 イベント通知は、イベントに基づいたダウンストリーム コードを開始できます。

Blockchain Workbench によって、2 種類のイベント コンシューマーが自動的にデプロイされます。 1 つのコンシューマーはブロックチェーン イベントによってトリガーされ、オフチェーンの SQL ストアを設定します。 もう 1 つのコンシューマーは、ドキュメントのアップロードと格納に関連した API によって生成されるイベントのメタデータを取得します。

## <a name="message-consumers"></a>メッセージ コンシューマー

 メッセージ コンシューマーは、Service Bus からメッセージを取得します。 メッセージ コンシューマーの基になるイベント モデルでは、追加のサービスやシステムの拡張が考慮されます。 たとえば、Azure Streaming Analytics を使用して CosmosDB へのデータ設定やメッセージの評価に対するサポートを追加できます。 以降のセクションでは、Blockchain Workbench に含まれているメッセージ コンシューマーについて説明します。

### <a name="distributed-ledger-consumer"></a>分散型台帳コンシューマー

分散型台帳テクノロジ (DLT) メッセージには、ブロックチェーンに書き込まれるトランザクションのメタデータが含まれています。 コンシューマーは、メッセージを取得し、そのデータをトランザクションの作成者、署名者、ルーターにプッシュします。

### <a name="database-consumer"></a>データベース コンシューマー

データベース コンシューマーは、Service Bus からメッセージを取得し、そのデータを接続されたデータベース (Azure SQL Database のデータベースなど) にプッシュします。

### <a name="storage-consumer"></a>ストレージ コンシューマー

ストレージ コンシューマーは、Service Bus からメッセージを取得し、データを接続されたストレージにプッシュします。 たとえば、ハッシュされたドキュメントを Azure Storage に保存します。

## <a name="transaction-builder-and-signer"></a>トランザクション ビルダーと署名者

受信メッセージ ブローカー上のメッセージは、ブロックチェーンに書き込む必要がある場合に DLT コンシューマーによって処理されます。 DLT コンシューマーはサービスであり、必要なトランザクションで実行するメタデータを含むメッセージを取得した後、その情報を "*トランザクション ビルダーと署名者*" に送信します。 "*トランザクション ビルダーと署名者*" は、データおよび必要なブロックチェーンの変換先に基づいてブロックチェーン トランザクションを組み立てます。 組み立てが完了すると、トランザクションは署名されます。 秘密キーは Azure Key Vault に格納されます。

 Blockchain Workbench は Key Vault から適切な秘密キーを取得し、Key Vault 外のトランザクションに署名します。 署名が完了すると、トランザクションはトランザクション ルーターと台帳に送信されます。

## <a name="transaction-routers-and-ledgers"></a>トランザクション ルーターと台帳

トランザクション ルーターと台帳は、署名されたトランザクションを取得し、適切なブロックチェーンにルーティングします。 現在、Blockchain Workbench では、ターゲット ブロックチェーンとして Ethereum をサポートしています。

## <a name="dlt-watcher"></a>DLT の監視

分散型台帳テクノロジ (DLT) の監視では、Blockchain Workbench に接続されたブロックチェーンで発生しているイベントを監視します。
イベントには、個人およびシステムに関連する情報が反映されています。 たとえば、新しいコントラクト インスタンスの作成、トランザクションの実行、状態の変更があります。 イベントはキャプチャされ、送信メッセージ ブローカーに送信されるため、ダウンストリーム コンシューマーによって消費される可能性があります。

たとえば、SQL コンシューマーはイベントを監視、消費し、含まれている値をデータベースに設定します。 このコピーにより、オフチェーン ストアではオンチェーン データのレプリカの再作成が可能になります。

## <a name="azure-sql-database"></a>Azure SQL データベース

Blockchain Workbench に接続されたデータベースには、コントラクト定義、構成メタデータ、ブロックチェーンに格納されたデータの SQL からアクセスできるレプリカが格納されます。 このデータは、データベースに直接アクセスすることによって、簡単に照会、視覚化、または分析できます。 開発者やその他のユーザーは、レポート、分析、その他のデータ中心の統合にこのデータベースを使用できます。 たとえば、ユーザーは、Power BI を使用してトランザクション データを視覚化することができます。

このオフチェーン ストレージを使用すると、企業組織はブロックチェーン台帳ではなく SQL のデータを照会できるようになります。 また、オフチェーン ストレージは、ブロックチェーン テクノロジ スタックには依存しない標準スキーマを標準化することで、レポートやその他の成果物をプロジェクト、シナリオ、組織全体で再利用することが可能になります。

## <a name="azure-storage"></a>Azure Storage

Azure Storage は、コントラクトおよびコントラクトに関連付けられたメタデータの格納に使用されます。

発注書や船荷証券から、ニュースや医療画像に使用される画像、警察のボディ カメラやメジャーな映画などの連続体から生じる動画まで、ドキュメントは多くのブロックチェーン中心のシナリオに役立ちます。 ドキュメントは、ブロックチェーンに直接配置することに適していません。

Blockchain Workbench では、ブロックチェーンのビジネス ロジックを使用してドキュメントやその他のメディア コンテンツを追加する機能がサポートされています。 ドキュメントまたはメディア コンテンツのハッシュはブロックチェーンに格納され、実際のドキュメントまたはメディア コンテンツは Azure Storage に格納されます。 関連付けられたトランザクション情報は、受信メッセージ ブローカーに配信され、パッケージ化および署名され、ブロックチェーンにルーティングされます。 イベントは、このプロセスによってトリガーされると、送信メッセージ ブローカーを介して共有されます。 SQL DB はこの情報を使用し、後で照会するために DB に送信します。 また、ダウンストリーム システムは、これらのイベントを適切に動作させるために使用することができます。

## <a name="monitoring"></a>監視

Workbench には、Application Insights および Azure Monitor を使用したアプリケーション ログ記録が用意されています。 Application Insights は、Blockchain Workbench からログに記録された情報をすべて格納するために使用されます。これには、エラー、警告、成功した操作が含まれます。 Application Insights は、開発者が Blockchain Workbench の問題をデバッグするために使用できます。 

Azure Monitor は、ブロックチェーン ネットワークの正常性に関する情報を提供します。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench のデプロイ](./deploy.md)
