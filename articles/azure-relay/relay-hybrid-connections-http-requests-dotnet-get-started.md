---
title: Azure Relay ハイブリッド接続 - .NET での HTTP 要求
description: .NET での Azure Relay ハイブリッド接続 HTTP 要求用 C# コンソール アプリケーションを作成します。
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 7a11abb984da3601a4d6aa921224e01f94d0871c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922584"
---
# <a name="get-started-with-relay-hybrid-connections-http-requests-in-net"></a>.NET での Relay ハイブリッド接続 HTTP 要求の概要
[!INCLUDE [relay-selector-hybrid-connections](../../includes/relay-selector-hybrid-connections.md)]

このクイック スタートでは、HTTP プロトコルを使用してメッセージを送受信する .NET のセンダー アプリケーションとレシーバー アプリケーションを作成します。 これらのアプリケーションには、Azure Relay のハイブリッド接続機能が使用されます。 Azure Relay 全般については、[Azure Relay](relay-what-is-it.md) に関するページを参照してください。 

このクイック スタートでは、以下の手順を実行します。

1. Azure Portal を使用した Relay 名前空間の作成
2. Azure Portal を使用した、その名前空間内のハイブリッド接続の作成
3. メッセージを受信するサーバー (リスナー) コンソール アプリケーションの作成
4. メッセージを送信するクライアント (送信側) コンソール アプリケーションの作成
5. アプリケーションの実行 

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次の前提条件を用意しておく必要があります。

* [Visual Studio 2015 またはそれ以降](https://www.visualstudio.com)。 このチュートリアルの例では、Visual Studio 2017 を使用します。
* Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-namespace"></a>名前空間の作成
[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-a-hybrid-connection"></a>ハイブリッド接続の追加
[!INCLUDE [relay-create-hybrid-connection-portal](../../includes/relay-create-hybrid-connection-portal.md)]

## <a name="create-a-server-application-listener"></a>サーバー アプリケーション (リスナー) の作成
Visual Studio で C# コンソール アプリケーションを作成して、Relay からのメッセージをリッスンおよび受信します。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-server](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-server.md)]

## <a name="create-a-client-application-sender"></a>クライアント アプリケーション (センダー) の作成
Visual Studio で C# コンソール アプリケーションを作成して、Relay にメッセージを送信します。

[!INCLUDE [relay-hybrid-connections-http-requests-dotnet-get-started-client](../../includes/relay-hybrid-connections-http-requests-dotnet-get-started-client.md)]

## <a name="run-the-applications"></a>アプリケーションの実行
1. サーバー アプリケーションを実行します。 コンソール ウィンドウに次のテキストが表示されます。

    ```
    Online
    Server listening
    ```
1. クライアント アプリケーションを実行します。 クライアント ウィンドウに `hello!` が表示されます。 クライアントは HTTP 要求をサーバーに送信し、サーバーは `hello!` で応答しました。 
3. ここで、コンソール ウィンドウを閉じるには、両方のコンソール ウィンドウで **Enter** キーを押します。 

これで、完全なハイブリッド接続のアプリケーションが作成されました。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、HTTP を使用してメッセージを送受信する .NET のクライアント アプリケーションとサーバー アプリケーションを作成しました。 Azure Relay のハイブリッド接続機能は、WebSocket を使用したメッセージの送受信もサポートしています。 Azure Relay のハイブリッド接続で WebSocket を使用する方法については、[WebSocket のクイック スタート](relay-hybrid-connections-dotnet-get-started.md)を参照してください。

このクイック スタートでは、.NET Framework を使用してクライアント アプリケーションとサーバー アプリケーションを作成しました。 Node.js を使用してクライアント アプリケーションとサーバー アプリケーションを作成する方法については、[Node.js WebSocket のクイック スタート](relay-hybrid-connections-node-get-started.md)または [Node.js HTTP のクイック スタート](relay-hybrid-connections-http-requests-dotnet-get-started.md)を参照してください。
