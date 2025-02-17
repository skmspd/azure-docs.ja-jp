---
title: Azure SignalR Service についてよく寄せられる質問
description: Azure SignalR Service に関する FAQ。
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 771124d0b8ca15bf72501fdeff8c31d0a43050b8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73578683"
---
# <a name="azure-signalr-service-faq"></a>Azure SignalR Service の FAQ

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Azure SignalR Service は運用環境で利用できますか?

はい。
一般提供の発表については、「[Azure SignalR Service の一般提供](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/)」を参照してください。 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) は完全にサポートされています。

ASP.NET SignalR のサポートはまだ*パブリック プレビュー*段階です。 [コード例](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom)はこちらです。

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>クライアント接続が閉じられ、"利用可能なサーバーがありません" というエラー メッセージが表示されます。 これはどういうことですか?

このエラーは、クライアントが SignalR Service にメッセージを送信している場合にのみ発生します。

アプリケーション サーバーがなく、SignalR Service REST API のみを使用している場合、この動作は**仕様**です。
サーバーレス アーキテクチャの場合、クライアント接続は**リッスン** モードになり、SignalR Service にメッセージを送信しません。
[REST API](./signalr-quickstart-rest-api.md) の詳細を参照してください。

アプリケーション サーバーがある場合、このエラー メッセージはアプリケーション サーバーが SignalR Service インスタンスに接続されていないことを示します。

次の原因が考えられます。
- SignalR Service と接続されているアプリケーション サーバーがない。 アプリケーション サーバーのログを調べて、接続エラーの可能性を確認してください。 これは、アプリケーション サーバーが複数ある高可用性設定ではまれです。
- SignalR Service インスタンスとの接続に問題があります。 この問題は一時的なもので、自動的に復旧します。
1 時間以上続く場合は、[GitHub で問題をオープン](https://github.com/Azure/azure-signalr/issues/new)するか、[Azure でサポート要求を作成](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)してください。

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>複数のアプリケーション サーバーが存在する場合、クライアント メッセージはすべてのサーバーに送信されますか? それとも 1 つのサーバーだけに送信されますか?

クライアントとアプリケーション サーバーのマッピングは 1 対 1 です。 クライアントからのメッセージは常に同じアプリケーション サーバーに送信されます。

クライアントとアプリケーション サーバーのマッピングは、クライアントまたはアプリケーション サーバーが切断されるまで保持されます。

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>いずれかのアプリケーション サーバーがダウンした場合に、それを見つけたり、通知を受け取ったりするにはどうすればよいですか?

SignalR Service では、アプリケーション サーバーからのハートビートを監視します。
指定された期間ハートビートを受信しなかった場合、アプリケーション サーバーはオフラインと見なされます。 このアプリケーション サーバーにマップされているすべてのクライアント接続が切断されます。

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>ASP.NET Core SignalR SDK から Azure SignalR Service SDK に切り替えると、カスタムの `IUserIdProvider` が例外をスローするのはなぜですか?

ASP.NET Core SignalR SDK と Azure SignalR Service SDK では、`IUserIdProvider` を呼び出すときのパラメーター `HubConnectionContext context` が異なります。

ASP.NET Core SignalR の場合、`HubConnectionContext context` はすべてのプロパティに有効な値が指定された物理クライアント接続からのコンテキストです。

Azure SignalR Service SDK の場合、`HubConnectionContext context` は論理クライアント接続からのコンテキストです。 物理クライアント接続は SignalR Service インスタンスに接続されます。そのため、限られた数のプロパティのみが指定されます。

現在のところ、`HubConnectionContext.GetHttpContext()` と `HubConnectionContext.User` のみがアクセス可能です。
ソース コードは、[こちら](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs)で確認できます。

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>ASP.NET Core SignalR を使用した場合のサーバー側での構成と同じように、SignalR Service で使用できる転送を構成できますか? たとえば、WebSocket 転送を無効にできますか?

No.

Azure SignalR Service は、ASP.NET Core SignalR が既定でサポートする 3 つの転送をすべて提供します。 これは構成できません。 SignalR Service がすべてのクライアント接続の接続と転送を処理します。

[こちら](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports)に記載されているように、クライアント側の転送は構成できます。
