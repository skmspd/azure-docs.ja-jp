---
title: Durable Functions のエラー処理 - Azure
description: Azure Functions の Durable Functions 拡張機能で発生したエラーを処理する方法について説明します。
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: adc23cad4ad7c55ce81096b1550520c496f744c1
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614867"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Durable Functions のエラー処理 (Azure Functions)

Durable Function のオーケストレーションはコードで実装され、プログラミング言語の組み込みエラー処理機能を使用できます。 エラー処理と補正をオーケストレーションに追加するために学習する必要がある新しい概念は、実際にはありません。 ただし、注意する必要があるいくつかの動作があります。

## <a name="errors-in-activity-functions"></a>アクティビティ関数のエラー

アクティビティ関数でスローされた例外はオーケストレーター関数に戻され、`FunctionFailedException` としてスローされます。 ニーズに合ったエラー処理と補正コードをオーケストレーター関数内に記述できます。

たとえば、ある口座の資金を別の口座に送金する次のオーケストレーター関数を検討します。

### <a name="precompiled-c"></a>プリコンパイル済み C#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="c-script"></a>C# スクリプト

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(IDurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

最初の **CreditAccount** 関数の呼び出しが失敗した場合、オーケストレーター関数は、資金を送金元口座に戻すことで、これを補正します。

## <a name="automatic-retry-on-failure"></a>エラー発生時の自動再試行

アクティビティ関数またはサブオーケストレーション関数を呼び出すときに、自動再試行ポリシーを指定できます。 次の例では、関数の呼び出しを最大 3 回試行し、次の再試行まで 5 秒間待機します。

### <a name="precompiled-c"></a>プリコンパイル済み C#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task Run(IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync` (.NET) または `callActivityWithRetry` (JavaScript) API は、`RetryOptions` パラメーターを取ります。 `CallSubOrchestratorWithRetryAsync` (.NET) または `callSubOrchestratorWithRetry` (JavaScript) API を使用するサブオーケストレーション呼び出しは、これらの同じ再試行ポリシーを使用できます。

自動再試行ポリシーをカスタマイズするために、次のようないくつかのオプションがあります。

* **最大試行回数**: 再試行の最大数。
* **1 回目の再試行の間隔**: 1 回目の再試行の前に待つ時間。
* **バックオフ係数**: バックオフの増加率を決定するために使用される係数。 既定値は 1 です。
* **最大再試行間隔**: 再試行の間に待つ最長時間。
* **再試行タイムアウト**: 再試行の実行に費やす最長時間。 既定の動作は、無限の再試行です。
* **ハンドル**: 関数を再試行する必要があるかどうかを決定するために、ユーザー定義のコールバックを指定できます。

## <a name="function-timeouts"></a>関数のタイムアウト

オーケストレーター関数内の関数呼び出しは、完了に時間がかかりすぎる場合は破棄できます。 現時点でこれを適切に行うには、次の例のように、`context.CreateTimer` (.NET) または `context.df.createTimer` (JavaScript) を `Task.WhenAny` (.NET) または `context.df.Task.any` (JavaScript) と組み合わせて使用して[持続的タイマー](durable-functions-timers.md)を作成します。

### <a name="precompiled-c"></a>プリコンパイル済み C#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

### <a name="c-script"></a>C# スクリプト

```csharp
public static async Task<bool> Run(IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> 前述の C# の例は Durable Functions 2.x 用です。 Durable Functions 1.x の場合、`IDurableOrchestrationContext` の代わりに `DurableOrchestrationContext` を使用する必要があります。 バージョン間の相違点の詳細については、[Durable Functions のバージョン](durable-functions-versions.md)に関する記事を参照してください。

### <a name="javascript-functions-20-only"></a>JavaScript (Functions 2.0 のみ)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> このメカニズムは、実際には、進行中のアクティビティ関数の実行を終了するのではなく、 オーケストレーター関数が、単に結果を無視して、次に進めるようにするだけです。 詳細については、[タイマー](durable-functions-timers.md#usage-for-timeout)に関するドキュメントをご覧ください。

## <a name="unhandled-exceptions"></a>ハンドルされない例外

オーケストレーター関数がハンドルされない例外で失敗した場合、例外の詳細がログに記録され、インスタンスは `Failed` 状態で完了します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [永続的オーケストレーションについて学習する](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [問題を診断する方法を知る](durable-functions-diagnostics.md)
