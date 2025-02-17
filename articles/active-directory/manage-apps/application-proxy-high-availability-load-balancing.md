---
title: 高可用性と負荷分散 - Azure AD アプリケーション プロキシ
description: アプリケーション プロキシの展開でのトラフィック分散のしくみ。 コネクタのパフォーマンスを最適化する方法、およびバックエンド サーバーに対して負荷分散を使用する方法に関するヒントが含まれます。
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: kenwith
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fda858b0811eb6308b8e5588eaeae9bff5a1730
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99259391"
---
# <a name="high-availability-and-load-balancing-of-your-application-proxy-connectors-and-applications"></a>アプリケーション プロキシ コネクタとアプリケーションの高可用性と負荷分散

この記事では、アプリケーション プロキシの展開でのトラフィック分散のしくみについて説明します。 次について説明します。

- ユーザー間およびコネクタ間にトラフィックが分散される方法と、コネクタのパフォーマンスを最適化するためのヒント

- コネクタとバックエンド アプリ サーバーの間をトラフィックが流れるしくみと、複数のバックエンド サーバー間での負荷分散に関する推奨事項

## <a name="traffic-distribution-across-connectors"></a>コネクタ間のトラフィック分散

コネクタでは、高可用性の原則に基づいて接続が確立されます。 トラフィックが常にコネクタ間で均等に分散される保証はなく、セッション アフィニティはありません。 そうではなく、使用状況は変化し、要求はアプリケーション プロキシ サービス インスタンスにランダムに送信されます。 結果として、トラフィックは通常、コネクタ全体に均等に分散されます。 次の図と手順では、ユーザーとコネクタの間の接続がどのように確立されるかを示します。

![ユーザーとコネクタの間の接続を示す図](media/application-proxy-high-availability-load-balancing/application-proxy-connections.png)

1. クライアント デバイスのユーザーが、アプリケーション プロキシ経由で公開されているオンプレミスのアプリケーションにアクセスを試みます。
2. 要求は Azure Load Balancer を経由し、要求を実行するアプリケーション プロキシ サービス インスタンスが決定されます。 リージョンごとに、要求を受け入れることができるインスタンスが数十個あります。 この方法により、トラフィックをサービス インスタンス間に均等に分散させることができます。
3. 要求は [Service Bus](../../service-bus-messaging/index.yml) に送信されます。
4. Service Bus により、使用可能なコネクタにシグナルされます。 その後、コネクタによって Service Bus から要求が取得されます。
   - ステップ 2 で、要求は異なるアプリケーション プロキシ サービス インスタンスに送られるため、接続が異なるコネクタで処理される可能性がいっそう高くなります。 その結果、グループ内のコネクタはほぼ均等に使用されます。
5. コネクタによって、アプリケーションのバックエンド サーバーに要求が渡されます。 その後、アプリケーションにより応答がコネクタに返送されます。
6. コネクタでは、要求元のサービス インスタンスへの送信接続を開くことで、応答が完了されます。 その後、この接続はすぐに閉じられます。 既定では、各コネクタの同時送信接続の数は 200 に制限されています。
7. その後、応答がサービス インスタンスからクライアントに返されます。
8. 同じ接続からの後続の要求では、上記の手順が繰り返されます。

多くの場合、アプリケーションには多くのリソースがあり、読み込まれると複数の接続が開かれます。 各接続は、上記の手順によってサービス インスタンスに割り当てられ、接続がまだコネクタとペアリングされていない場合は、新しい使用可能なコネクタが選択されます。


## <a name="best-practices-for-high-availability-of-connectors"></a>コネクタの高可用性のベスト プラクティス

- 高可用性のためにコネクタ間にトラフィックが分散される方法により、コネクタ グループには常に少なくとも 2 つのコネクタが必要です。 コネクタ間に追加のバッファーを提供するため、3 つのコネクタが推奨されます。 必要なコネクタの適切な数を決定するには、容量計画に関するドキュメントを参照してください。

- 単一障害点を回避するには、異なる送信接続にコネクタを配置します。 複数のコネクタで同じ送信接続を使用した場合、接続に関するネットワークの問題により、その接続を使用しているすべてのコネクタが影響を受ける可能性があります。

- 運用アプリケーションに接続されているときは、コネクタを強制的に再起動しないようにします。 それを行うと、コネクタ間のトラフィックの分散に悪影響が及ぶ可能性があります。 コネクタを再起動すると、いっそう多くのコネクタが使用できなくなり、使用可能な残りのコネクタへの接続が強制されます。 結果として、最初のコネクタの使用が不均一になります。

- コネクタと Azure の間の送信 TLS 通信に対してすべての形式のインライン検査を行わないでください。 この種のインライン検査では、通信フローが低下します。

- コネクタで自動更新が実行されていることを確認します。 アプリケーション プロキシ コネクタ アップデーター サービスを実行している場合、コネクタは自動的に更新され、最新のアップグレードを受け取ります。 サーバーにコネクタ アップデーター サービスが見つからない場合は、コネクタを再インストールして更新プログラムを取得する必要があります。

## <a name="traffic-flow-between-connectors-and-back-end-application-servers"></a>コネクタとバックエンド アプリケーション サーバーの間のトラフィック フロー

高可用性が要因であるもう 1 つの重要な部分は、コネクタとバックエンド サーバーの間の接続です。 アプリケーションが Azure AD アプリケーション プロキシを通じて発行される場合、ユーザーからアプリケーションへのトラフィックはすべて、次の 3 つのホップを経由して送信されます。

1. ユーザーが、Azure 上の Azure AD アプリケーション プロキシ サービスのパブリック エンドポイントに接続します。 クライアントの発信元クライアント IP アドレス (パブリック) と、アプリケーション プロキシ エンドポイントの IP アドレスとの間で、接続が確立されます。
2. アプリケーション プロキシ コネクタによって、アプリケーション プロキシ サービスからクライアントの HTTP 要求が取得されます。
3. アプリケーション プロキシ コネクタは、ターゲット アプリケーションに接続します。 コネクタでは、接続を確立するために独自の IP アドレスが使われます。

![アプリケーション プロキシ経由でアプリケーションに接続しているユーザーの図](media/application-proxy-high-availability-load-balancing/application-proxy-three-hops.png)

### <a name="x-forwarded-for-header-field-considerations"></a>X-Forwarded-For ヘッダー フィールドに関する考慮事項
状況によっては (監査、負荷分散など)、外部クライアントの発信元 IP アドレスを、オンプレミス環境と共有する必要があります。 その要件に対応するため、Azure AD アプリケーション プロキシ コネクタでは、送信元クライアント IP アドレス (パブリック) を含む X-Forwarded-For ヘッダー フィールドが HTTP 要求に追加されます。 適切なネットワーク デバイス (ロード バランサー、ファイアウォール)、Web サーバー、またはバックエンド アプリケーションでは、その情報を読み取って使用できます。

## <a name="best-practices-for-load-balancing-among-multiple-app-servers"></a>複数のアプリ サーバー間での負荷分散のベスト プラクティス
アプリケーション プロキシ アプリケーションに割り当てられているコネクタ グループに 2 つ以上のコネクタがあり、バックエンド Web アプリケーションを複数のサーバー (サーバー ファーム) で実行している場合は、適切な負荷分散戦略が必要です。 適切な戦略により、サーバーでクライアント要求が均等に取得され、サーバー ファーム内のサーバーの過剰使用または過少使用を防ぐことができます。
### <a name="scenario-1-back-end-application-does-not-require-session-persistence"></a>シナリオ 1:バックエンド アプリケーションでセッション永続化が必要ない
最も簡単なシナリオでは、バックエンド Web アプリケーションにおいてセッション持続性 (セッション永続化) が必要ありません。 ユーザーからのすべての要求は、サーバー ファーム内の任意のバックエンド アプリケーション インスタンスで処理できます。 レイヤー 4 のロード バランサーを使用し、アフィニティなしで構成することができます。 一部のオプションには、Microsoft ネットワーク負荷分散と Azure Load Balancer または別のベンダーのロード バランサーが含まれます。 または、ラウンドロビン DNS を構成することもできます。
### <a name="scenario-2-back-end-application-requires-session-persistence"></a>シナリオ 2: バックエンド アプリケーションでセッション永続化が必要である
このシナリオでは、認証されたセッションの間にバックエンド Web アプリケーションでセッション持続性 (セッション永続化) が必要です。 ユーザーからのすべての要求は、サーバー ファーム内の同じサーバーで実行されているバックエンド アプリケーション インスタンスによって処理される必要があります。
通常、このシナリオでは、クライアントでアプリケーション プロキシ サービスへの複数の接続を確立するため、より複雑になる可能性があります。 異なる接続での要求は、ファーム内の異なるコネクタおよびサーバーに到着する場合があります。 各コネクタではこの通信に対して独自の IP アドレスが使われるため、ロード バランサーではコネクタの IP アドレスに基づくセッションの持続性を保証できません。 ソース IP アフィニティも使用できません。
シナリオ 2 のいくつかのオプションを次に示します。

- オプション 1: ロードバランサーによって設定されたセッション Cookie を基にして、セッションの永続化を実現します。 このオプションは、バックエンド サーバー間に負荷をいっそう均等に分散できるため推奨されます。 この機能を備えたレイヤー 7 ロード バランサーが必要であり、HTTP トラフィックを処理して、TLS 接続を終了することができます。 Azure Application Gateway (セッション アフィニティ) または別のベンダーのロード バランサーを使用できます。

- オプション 2:セッションの永続化のベースとして、X-Forwarded-For ヘッダー フィールドを使用します。 このオプションでは、この機能を備えたレイヤー 7 ロード バランサーが必要であり、HTTP トラフィックを処理して、TLS 接続を終了することができます。  

- オプション 3:セッションの永続化が必要ないようにバックエンド アプリケーションを構成します。

バックエンド アプリケーションの負荷分散の要件については、ソフトウェア ベンダーのドキュメントを参照してください。

## <a name="next-steps"></a>次のステップ

- [アプリケーション プロキシを有効にする](application-proxy-add-on-premises-application.md)
- [シングル サインオンを有効にする](application-proxy-configure-single-sign-on-with-kcd.md)
- [条件付きアクセスを有効にする](application-proxy-integrate-with-sharepoint-server.md)
- [アプリケーション プロキシで発生した問題のトラブルシューティングを行う](application-proxy-troubleshoot.md)
- [Azure AD アーキテクチャで高可用性がサポートされるしくみについて学習する](../fundamentals/active-directory-architecture.md)