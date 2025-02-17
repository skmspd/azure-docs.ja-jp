---
title: Durable Functions のバージョンの概要 - Azure Functions
description: Durable Functions のバージョンについて説明します。
author: cgillum
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: azfuncdf
ms.openlocfilehash: 5d6c8bcf610bfc8900e0f2a5237228208cd633ca
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614547"
---
# <a name="durable-functions-versions-overview"></a>Durable Functions のバージョンの概要

*Durable Functions* は、サーバーレス環境でステートフル関数を記述できる、[Azure Functions](../functions-overview.md) と [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) の拡張機能です。 この拡張機能は状態、チェックポイント、再起動を管理します。 Durable Functions にまだ慣れていない場合は、[概要ドキュメント](durable-functions-overview.md)をご覧ください。

## <a name="new-features-in-2x"></a>2\.x の新機能

このセクションでは、バージョン 2.x で追加された Durable Functions の機能について説明します。

### <a name="durable-entities"></a>持続エンティティ

Durable Functions 2.x では、新しい[エンティティ関数](durable-functions-entities.md)の概念が導入されました。

エンティティ関数では、"*持続エンティティ*" と呼ばれる小さい状態の読み取りと更新のための操作が定義されています。 オーケストレーター関数と同様、エンティティ関数は特殊なトリガー型である "*エンティティ トリガー*" を含む関数です。 オーケストレーター関数とは異なり、エンティティ関数には特定のコードの制約はありません。 また、エンティティ関数では、制御フローを介して状態を表す暗黙的ではなく、明示的に状態が管理されます。

詳細については、「[持続エンティティ](durable-functions-entities.md)」の記事を参照してください。

### <a name="durable-http"></a>持続的 HTTP

Durable Functions 2.x では、次のことを実行できる新しい[持続的 HTTP](durable-functions-http-features.md#consuming-http-apis) 機能が導入されました。

* オーケストレーション関数から HTTP API シリーズを直接呼び出す (ドキュメントに記載されているいくつかの制限事項があります)。
* 自動クライアント側 HTTP 202 状態ポーリングを実装する。
* [Azure マネージド ID](../../active-directory/managed-identities-azure-resources/overview.md) の組み込みサポート。

詳細については、[HTTP 機能](durable-functions-http-features.md#consuming-http-apis)に関する記事を参照してください。

## <a name="migrate-from-1x-to-2x"></a>1\.x から 2.x に移行する

このセクションでは、既存のバージョン 1.x の Durable Functions をバージョン 2.x に移行し、新機能を活用する方法について説明します。

### <a name="upgrade-the-extension"></a>拡張機能をアップグレードする

プロジェクトに [Durable Functions バインド拡張機能](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DurableTask)のバージョン 2.x をインストールします。 詳細については、「[Azure Functions バインド拡張機能を登録する](../functions-bindings-register.md)」を参照してください。

### <a name="update-your-code"></a>コードを更新する

Durable Functions 2.x では、いくつかの重大な変更が導入されています。 コードを変更しない場合、Durable Functions 1.x アプリケーションは、Durable Functions 2.x と互換性がありません。 このセクションでは、バージョン 1.x の関数を 2.x にアップグレードするときに行う必要がある変更の一部を示します。

#### <a name="hostjson-schema"></a>Host.json スキーマ

Durable Functions 2.x では、新しいホストの json スキーマが使用されます。 1\.x からの主な変更点は次のとおりです。

* ストレージ固有の構成用の `"storageProvider"` (および `"azureStorage"` サブセクション)。
* 追跡とログ記録の構成用の `"tracking"`。
* イベント グリッド通知構成用の `"notifications"` (および `"eventGrid"` サブセクション)

詳細については、[Durable Functions host. json のリファレンス ドキュメント](durable-functions-bindings.md#durable-functions-2-0-host-json)を参照してください。

#### <a name="public-interface-changes-net-only"></a>パブリック インターフェイスの変更 (.NET のみ)

バージョン 1.x では、Durable Functions によってサポートされるさまざまな_コンテキスト_ オブジェクトには、単体テストでの使用が意図される抽象基本クラスが含まれました。 Durable Functions 2.x の一部として、これらの抽象基本クラスはインターフェイスに置き換えられます。

次の表では、主な変更を示します。

| 1.x | 2.x |
|----------|----------|
| `DurableOrchestrationClientBase` | `IDurableOrchestrationClient` または `IDurableClient` |
| `DurableOrchestrationContext` または `DurableOrchestrationContextBase` | `IDurableOrchestrationContext` |
| `DurableActivityContext` または `DurableActivityContextBase` | `IDurableActivityContext` |
| `OrchestrationClientAttribute` | `DurableClientAttribute` |

抽象基本クラスに仮想メソッドが含まれていた場合、これらの仮想メソッドは `DurableContextExtensions` で定義される拡張メソッドに置き換えられています。

#### <a name="functionjson-changes-javascript-and-c-script"></a>function.json の変更 (JavaScript および C# スクリプト)

Durable Functions 1.x では、オーケストレーション クライアントのバインドには `orchestrationClient` の `type` が使用されます。 バージョン 2.x では、代わりに `durableClient` が使用されます。
