---
title: Durable Functions での外部イベントの処理 - Azure
description: Azure Functions の Durable Functions 拡張機能で外部イベントを処理する方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 1c2a2f08c31c427241bbd5e482906118a90ee178
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614845"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Durable Functions での外部イベントの処理 (Azure Functions)

オーケストレーター関数には、外部イベントを待ち受ける機能があります。 [Durable Functions](durable-functions-overview.md) のこの機能は、多くの場合、人による操作または他の外部トリガーを処理するときに便利です。

> [!NOTE]
> 外部イベントは、一方向の非同期操作です。 それらは、イベントを送信するクライアントが、オーケストレーター関数からの同期応答を必要とする状況には適していません。

## <a name="wait-for-events"></a>イベントを待つ

[オーケストレーション トリガー バインド](durable-functions-bindings.md#orchestration-trigger)の `WaitForExternalEvent` (.NET) および `waitForExternalEvent` (JavaScript) メソッドを使うと、オーケストレーター関数では外部イベントを非同期に待機してリッスンできるようになります。 リスニング オーケストレーター関数は、受信するイベントの "*名前*" と "*データのシェイプ*" を宣言します。

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

> [!NOTE]
> 前の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext`の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

前の例では、特定の 1 つのイベントをリッスンし、受信したときにアクションを実行します。

次の例に示すように、複数のイベントを同時にリッスンできます。この例では、発生する可能性がある 3 つのイベント通知のいずれかを待っています。

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

> [!NOTE]
> 前の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext`の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

前の例では、複数のイベントの "*いずれか*" をリッスンします。 "*すべて*" のイベントを待つこともできます。

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

> [!NOTE]
> 前のコードは Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext`の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

`WaitForExternalEvent` では、入力を無期限に待機します。  関数アプリは、待機中に安全にアンロードすることができます。 これは、このオーケストレーション インスタンスのイベントが到着すると、自動的に起動され、すぐにそのイベントを処理します。

> [!NOTE]
> 関数アプリで従量課金プランを使用する場合、オーケストレーター関数が `WaitForExternalEvent` (.NET) または `waitForExternalEvent` (JavaScript) からのタスクを待っている間は、待ち時間がどんなに長くなっても課金されません。

.NET では、イベント ペイロードを想定された型 `T` に変換できない場合、例外がスローされます。

## <a name="send-events"></a>送信イベント

[オーケストレーション クライアント バインド](durable-functions-bindings.md#orchestration-client)の `RaiseEventAsync` (.NET) または `raiseEvent` (JavaScript) メソッドからは、`WaitForExternalEvent` (.NET) または `waitForExternalEvent` (JavaScript) で待機するイベントが送信されます。  `RaiseEventAsync` メソッドでは、*eventName* と *eventData* がパラメーターとして使用されます。 イベント データは、JSON でシリアル化できる必要があります。

次の例は、キューによってトリガーされる関数で、"承認" イベントをオーケストレーター関数インスタンスに送信します。 オーケストレーション インスタンス ID は、キュー メッセージの本文から取得されます。

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> 前の C# コードは Durable Functions 2.x 用です。 Durable Functions 1.x では、`DurableClient` 属性の代わりに `OrchestrationClient` 属性を使用する必要があります。また、`IDurableOrchestrationClient` ではなく `DurableOrchestrationClient` パラメーター型を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

内部的には、`RaiseEventAsync` (.NET) または `raiseEvent` (JavaScript) は、待機オーケストレーター関数によって取得されるメッセージをエンキューします。 指定した "*イベント名*" でインスタンスが待機していない場合、イベント メッセージがインメモリ キューに追加されます。 オーケストレーション インスタンスが後でその "*イベント名*" のリッスンを開始した場合、キューにイベント メッセージがあるかどうかを確認します。

> [!NOTE]
> 指定した "*インスタンス ID*" のオーケストレーション インスタンスが存在しない場合、イベント メッセージは破棄されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [エラー処理の実装方法を学習する](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [人による操作を待つサンプルを実行する](durable-functions-phone-verification.md)