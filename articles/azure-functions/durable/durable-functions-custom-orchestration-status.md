---
title: Durable Functions でのカスタムオーケストレーションの状態 - Azure
description: Durable Functions 用のカスタム オーケストレーションの状態を構成し使用する方法を説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d3b3ee1fabf59ae3b87185c4c9eb2f85aa8acd91
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614917"
---
# <a name="custom-orchestration-status-in-durable-functions-azure-functions"></a>Durable Functions でのカスタムオーケストレーションの状態 (Azure Functions)

カスタムオーケストレーションの状態を使用すると、オーケストレーター関数のカスタム状態値を設定できます。 この状態は、HTTP GetStatus API または `DurableOrchestrationClient.GetStatusAsync` API を通してのみ提供されます。

## <a name="sample-use-cases"></a>サンプル ユース ケース

> [!NOTE]
> 次のサンプルでは、C# および JavaScript でカスタム ステータス機能を使用する方法を示しています。 C# の例は Durable Functions 2.x 用に記述されており、Durable Functions 1.x とは互換性がありません。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="visualize-progress"></a>進行状況を視覚化

クライアントは、状態の終了点をポーリングし、進行状況の現在の実行のステージを視覚化する UI を表示できます。 次の例では、進行状況の共有を示しています:

#### <a name="c"></a>C#

```csharp
[FunctionName("E1_HelloSequence")]
public static async Task<List<string>> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var outputs = new List<string>();

    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Tokyo"));
    context.SetCustomStatus("Tokyo");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "Seattle"));
    context.SetCustomStatus("Seattle");
    outputs.Add(await context.CallActivityAsync<string>("E1_SayHello", "London"));
    context.SetCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
}

[FunctionName("E1_SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const outputs = [];

    outputs.push(yield context.df.callActivity("E1_SayHello", "Tokyo"));
    context.df.setCustomStatus("Tokyo");
    outputs.push(yield context.df.callActivity("E1_SayHello", "Seattle"));
    context.df.setCustomStatus("Seattle");
    outputs.push(yield context.df.callActivity("E1_SayHello", "London"));
    context.df.setCustomStatus("London");

    // returns ["Hello Tokyo!", "Hello Seattle!", "Hello London!"]
    return outputs;
});
```

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```

`CustomStatus`フィールドが"London"に設定されている時のみ、クライアントは、オーケストレーションの出力を受け取ります:

#### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    DurableOrchestrationStatus durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    while (durableOrchestrationStatus.CustomStatus.ToString() != "London")
    {
        await Task.Delay(200);
        durableOrchestrationStatus = await starter.GetStatusAsync(instanceId);
    }

    HttpResponseMessage httpResponseMessage = new HttpResponseMessage(HttpStatusCode.OK)
    {
        Content = new StringContent(JsonConvert.SerializeObject(durableOrchestrationStatus))
    };

    return httpResponseMessage;
  }
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    let durableOrchestrationStatus = await client.getStatus(instanceId);
    while (status.customStatus.toString() !== "London") {
        await new Promise((resolve) => setTimeout(resolve, 200));
        durableOrchestrationStatus = await client.getStatus(instanceId);
    }

    const httpResponseMessage = {
        status: 200,
        body: JSON.stringify(durableOrchestrationStatus),
    };

    return httpResponseMessage;
};
```

> [!NOTE]
> JavaScript では、`customStatus` フィールドは次の `yield` または `return` アクションがスケジュールされたときに設定されます。

### <a name="output-customization"></a>出力のカスタマイズ

もう 1 つの興味深いシナリオは、固有の特性や相互作用に基づいてカスタマイズされた出力を返すことによってユーザーをセグメント化することです。 カスタムオーケストレーションの状態のヘルプで、クライアント側のコードはジェネラルのままになります。 すべての主な変更は、次の例に示すように、サーバー側で行われます:

#### <a name="c"></a>C#

```csharp
[FunctionName("CityRecommender")]
public static void Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  int userChoice = context.GetInput<int>();

  switch (userChoice)
  {
    case 1:
    context.SetCustomStatus(new
    {
      recommendedCities = new[] {"Tokyo", "Seattle"},
      recommendedSeasons = new[] {"Spring", "Summer"}
     });
      break;
    case 2:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Seattle, London"},
        recommendedSeasons = new[] {"Summer"}
      });
        break;
      case 3:
      context.SetCustomStatus(new
      {
        recommendedCities = new[] {"Tokyo, London"},
        recommendedSeasons = new[] {"Spring", "Summer"}
      });
        break;
  }

  // Wait for user selection and refine the recommendation
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userChoice = context.df.getInput();

    switch (userChoice) {
        case 1:
            context.df.setCustomStatus({
                recommendedCities: [ "Tokyo", "Seattle" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
        case 2:
            context.df.setCustomStatus({
                recommendedCities: [ "Seattle", "London" ],
                recommendedSeasons: [ "Summer" ],
            });
            break;
        case 3:
            context.df.setCustomStatus({
                recommendedCity: [ "Tokyo", "London" ],
                recommendedSeasons: [ "Spring", "Summer" ],
            });
            break;
    }

    // Wait for user selection and refine the recommendation
});
```

### <a name="instruction-specification"></a>手順の仕様

オーケストレーターは、カスタムの状態を使用してクライアントに固有の指示を提供できます。 カスタム状態の手順は、オーケストレーション コードでの手順にマップされます:

#### <a name="c"></a>C#

```csharp
[FunctionName("ReserveTicket")]
public static async Task<bool> Run(
  [OrchestrationTrigger] IDurableOrchestrationContext context)
{
  string userId = context.GetInput<string>();

  int discount = await context.CallActivityAsync<int>("CalculateDiscount", userId);

  context.SetCustomStatus(new
  {
    discount = discount,
    discountTimeout = 60,
    bookingUrl = "https://www.myawesomebookingweb.com",
  });

  bool isBookingConfirmed = await context.WaitForExternalEvent<bool>("BookingConfirmed");

  context.SetCustomStatus(isBookingConfirmed
    ? new {message = "Thank you for confirming your booking."}
    : new {message = "The booking was not confirmed on time. Please try again."});

  return isBookingConfirmed;
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const userId = context.df.getInput();

    const discount = yield context.df.callActivity("CalculateDiscount", userId);

    context.df.setCustomStatus({
        discount,
        discountTimeout = 60,
        bookingUrl = "https://www.myawesomebookingweb.com",
    });

    const isBookingConfirmed = yield context.df.waitForExternalEvent("bookingConfirmed");

    context.df.setCustomStatus(isBookingConfirmed
        ? { message: "Thank you for confirming your booking." }
        : { message: "The booking was not confirmed on time. Please try again." }
    );

    return isBookingConfirmed;
});
```

## <a name="sample"></a>サンプル

次のサンプルでは、カスタムの状態が先に設定します:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { nextActions = new [] {"A", "B", "C"}, foo = 2, };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { nextActions: [ "A", "B", "C" ], foo: 2, };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

オーケストレーションの実行中に、外部クライアントはこのカスタム状態を取り込むことができます:

```http
GET /runtime/webhooks/durabletask/instances/instance123
```

クライアントは次の応答を取得します:

```json
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "output": null,
  "createdTime": "2019-10-06T18:30:24Z",
  "lastUpdatedTime": "2019-10-06T19:40:30Z"
}
```

> [!WARNING]
> カスタム状態ペイロードは、Azure Table Storage の列に収まる必要があるため、16 KB の UTF-16 JSON テキストに制限されています。 これより大きなペイロードが必要な場合は、外部ストレージを使用することをお勧めします。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [持続的タイマーについて学習する](durable-functions-timers.md)
