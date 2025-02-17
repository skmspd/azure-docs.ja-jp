---
title: Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)
description: C# を使用して Azure Functions を開発する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure Functions, 機能, イベント処理, Webhook, 動的コンピューティング, サーバーなしのアーキテクチャ
ms.service: azure-functions
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/12/2018
ms.author: glenga
ms.openlocfilehash: c3c13b7e28ef7c17fd45682d828f318de5326542
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72293872"
---
# <a name="azure-functions-c-developer-reference"></a>Azure Functions C# developer reference (Azure Functions C# 開発者向けリファレンス)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-reference-csharp.md -->

この記事では、.NET クラス ライブラリの C# を使用した Azure Functions 開発の概要を示します。

Azure Functions では、C# および C# スクリプト プログラミング言語をサポートします。 [Azure Portal での C# の使用](functions-create-function-app-portal.md)に関するガイダンスを探している場合は、[C# スクリプト (.csx) 開発者向けリファレンス](functions-reference-csharp.md)をご覧ください。

この記事では、既に次の記事に目を通していることを前提とします。

* [Azure Functions の開発者向けガイド](functions-reference.md)
* [Azure Functions 向けの Visual Studio 2019 Tools](functions-develop-vs.md)

## <a name="supported-versions"></a>サポートされているバージョン

Azure Functions 2.x ランタイムでは、.NET Core 2.2 が使用されます。 関数のコードでは、Visual Studio プロジェクトの設定を更新することにより、.NET Core 2.2 API を使用できます。 .NET Core 2.2 をインストールしていない顧客に悪影響を及ぼさないように、関数テンプレートの既定は .NET Core 2.2 ではありません。

## <a name="functions-class-library-project"></a>関数クラス ライブラリ プロジェクト

Visual Studio では、**Azure Functions** プロジェクト テンプレートは、次のファイルを含む C# クラス ライブラリ プロジェクトを作成します。

* [host.json](functions-host-json.md) - ローカルまたは Azure 内で実行される場合に、プロジェクト内のすべての関数に影響を及ぼす構成設定を格納します。
* [local.settings.json](functions-run-local.md#local-settings-file) - ローカルで実行される場合に使用されるアプリ設定および接続文字列を格納します。 このファイルにはシークレットが含まれていて、Azure の関数アプリには公開されません。 代わりに、[関数アプリにアプリ設定を追加](functions-develop-vs.md#function-app-settings)します。

プロジェクトをビルドするときに、次の例のようなフォルダー構造がビルドの出力ディレクトリに作成されます。

```
<framework.version>
 | - bin
 | - MyFirstFunction
 | | - function.json
 | - MySecondFunction
 | | - function.json
 | - host.json
```

このディレクトリは、Azure 上で関数アプリにデプロイされるディレクトリです。 Functions ランタイムの[バージョン 2.x](functions-versions.md) に必要なバインディング拡張機能は、[NuGet パッケージとしてプロジェクトに追加](./functions-bindings-register.md#vs)されます。

> [!IMPORTANT]
> ビルド処理では、関数ごとに *function.json* ファイルが作成されます。 この *function.json* ファイルに対しては、直接編集は行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 関数を無効にする方法については、[関数を無効にする方法](disable-function.md#functions-2x---c-class-libraries)に関するページをご覧ください。


## <a name="methods-recognized-as-functions"></a>関数として認識されるメソッド

クラス ライブラリでは、次の例に示すように 関数は `FunctionName` を使用した静的メソッドです。

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

`FunctionName` 属性は、関数のエントリ ポイントとしてメソッドをマークします。 名前はプロジェクト内で一意であり、文字で始まり、英数字、`_`、`-` のみが含まれ、127 文字以下にする必要があります。 プロジェクト テンプレートでは、多くの場合、`Run` という名前のメソッドが作成されますが、有効な C# メソッド名であればメソッド名として使用できます。

トリガー属性は、トリガーの種類を指定し、メソッド パラメーターに入力データをバインドします。 例の関数は 1 つのクエリ メッセージによってトリガーされ、そのクエリ メッセージは `myQueueItem` パラメーターでメソッドに渡されます。

## <a name="method-signature-parameters"></a>メソッド シグネチャのパラメーター

メソッド シグネチャには、トリガー属性で使用されているもの以外のパラメーターが含まれる場合があります。 以下に、含めることができる追加のパラメーターの一部を示します。

* 属性で修飾することによってそのようにマークした[入出力のバインド](functions-triggers-bindings.md)。  
* [ログ記録](#logging)のための `ILogger` または `TraceWriter` ([バージョン 1.x のみ](functions-versions.md#creating-1x-apps)) パラメーター。
* [グレースフル シャットダウン](#cancellation-tokens)のための `CancellationToken` パラメーター。
* トリガー メタデータを取得するための[バインド式](./functions-bindings-expressions-patterns.md)パラメーター。

関数シグネチャのパラメーターの順序は関係ありません。 たとえば、トリガー パラメーターは、他のバインドの前後に配置できます。また、ロガー パラメーターは、トリガー パラメーターまたはバインド パラメーターの前後に配置できます。

### <a name="output-binding-example"></a>出力バインドの例

次の例では、出力キュー バインドを追加することで、前の属性を変更しています。 この関数は、関数をトリガーするキュー メッセージを、異なるキュー内の新しいキュー メッセージに書き込みます。

```csharp
public static class SimpleExampleWithOutput
{
    [FunctionName("CopyQueueMessage")]
    public static void Run(
        [QueueTrigger("myqueue-items-source")] string myQueueItem, 
        [Queue("myqueue-items-destination")] out string myQueueItemCopy,
        ILogger log)
    {
        log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
        myQueueItemCopy = myQueueItem;
    }
}
```

バインドの参照についての記事 (たとえば「[Storage キュー](functions-bindings-storage-queue.md)」) では、トリガー、入力、または出力のバインド属性で、どのパラメーターの種類を使用できるかを説明しています。

### <a name="binding-expressions-example"></a>バインド式の例

次のコードでは、アプリ設定から監視するキューの名前を取得し、`insertionTime` パラメーターで、キュー メッセージの作成時刻を取得しています。

```csharp
public static class BindingExpressionsExample
{
    [FunctionName("LogQueueMessage")]
    public static void Run(
        [QueueTrigger("%queueappsetting%")] string myQueueItem,
        DateTimeOffset insertionTime,
        ILogger log)
    {
        log.LogInformation($"Message content: {myQueueItem}");
        log.LogInformation($"Created at: {insertionTime}");
    }
}
```

## <a name="autogenerated-functionjson"></a>自動生成される function.json

ビルド処理では、ビルド フォルダー内の関数フォルダーに *function.json*ファイルを作成します。 前述のとおり、このファイルに対しては直接編集が行われません。 このファイルを編集して、バインド構成を変更したり、関数を無効にしたりすることはできません。 

このファイルの目的は、[従量課金プランに関する決定事項を評価](functions-scale.md#how-the-consumption-and-premium-plans-work)する際に使用するスケール コントローラーに、情報を提供することです。 このため、ファイルはトリガー情報だけを含み、入力または出力バインドは含まれません。

生成された *function.json* ファイルには、*function.json* 構成ではなく、バインドの .NET 属性を使用するようにランタイムに指示する `configurationSource` プロパティが含まれます。 次に例を示します。

```json
{
  "generatedBy": "Microsoft.NET.Sdk.Functions-1.0.0.0",
  "configurationSource": "attributes",
  "bindings": [
    {
      "type": "queueTrigger",
      "queueName": "%input-queue-name%",
      "name": "myQueueItem"
    }
  ],
  "disabled": false,
  "scriptFile": "..\\bin\\FunctionApp1.dll",
  "entryPoint": "FunctionApp1.QueueTrigger.Run"
}
```

## <a name="microsoftnetsdkfunctions"></a>Microsoft.NET.Sdk.Functions

*function.json* ファイルの生成は、NuGet パッケージ ([Microsoft\.NET\.Sdk\.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions)) によって実行されます。 

Functions ランタイムのバージョン 1.x と 2.x では同じパッケージが使用されます。 1\.x プロジェクトと 2.x プロジェクトの違いはターゲット フレームワークです。 次に示すのは *.csproj* ファイルの関連する部分で、ターゲット フレームが異なっていることと、`Sdk` パッケージが同じであることがわかります。

**Functions 1.x**

```xml
<PropertyGroup>
  <TargetFramework>net461</TargetFramework>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

**Functions 2.x**

```xml
<PropertyGroup>
  <TargetFramework>netcoreapp2.1</TargetFramework>
  <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
  <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.8" />
</ItemGroup>
```

`Sdk` パッケージ間の依存関係はトリガーとバインドです。 1\.x のトリガーとバインドの対象は .NET Framework であるため、1.x プロジェクトは 1.x のトリガーとバインドを参照します。一方、2.x のトリガーとバインドの対象は .NET Core です。

`Sdk` パッケージも、[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json) に依存しており、間接的に [WindowsAzure.Storage](https://www.nuget.org/packages/WindowsAzure.Storage) に依存します。 これらの依存関係により、ユーザーのプロジェクトでは、必ずそのプロジェクト用の Functions ランタイム バージョンで動作するパッケージ バージョンが使用されます。 たとえば、`Newtonsoft.Json` のバージョンが .NET Framework 4.6.1 用のバージョン 11 だとします。ところが、.NET Framework 4.6.1 を対象とする Functions ランタイムは `Newtonsoft.Json` 9.0.1 としか互換性がありません。 この場合は、そのプロジェクトの関数コードも `Newtonsoft.Json` 9.0.1 を使用する必要があります。

`Microsoft.NET.Sdk.Functions` のソース コードは、GitHub リポジトリ ([azure\-functions\-vs\-build\-sdk](https://github.com/Azure/azure-functions-vs-build-sdk)) で利用できます。

## <a name="runtime-version"></a>ランタイム バージョン

Visual Studio では、[Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools) を使用して、Functions プロジェクトを実行します。 Core Tools は、Functions ランタイム用のコマンド ライン インターフェイスです。

npm を使用して Core Tools をインストールする場合、これは Visual Studio で使用される Core Tools バージョンには影響しません。 Functions ランタイム バージョン 1.x の場合、Visual Studio は Core Tools のバージョンを *%USERPROFILE%\AppData\Local\Azure.Functions.Cli* に格納し、そこに格納されている中で最も新しいバージョンを使用します。 Functions 2.x の場合、Core Tools は **Azure Functions と Web ジョブ ツール**の拡張機能に含まれます。 1\.x と 2.x の両方について、使用されているバージョンは、Functions プロジェクトを実行するときに、コンソール出力で確認できます。

```terminal
[3/1/2018 9:59:53 AM] Starting Host (HostId=contoso2-1518597420, Version=2.0.11353.0, ProcessId=22020, Debug=False, Attempt=0, FunctionsExtensionVersion=)
```

## <a name="supported-types-for-bindings"></a>バインドでサポートされる型

各バインドには独自にサポートされる型があります。たとえば、BLOB トリガー属性は文字列パラメーター、POCO パラメーター、`CloudBlockBlob` パラメーター、またはサポートされるその他の複数の型のいずれかに適用できます。 [BLOB バインディングのバインド リファレンス](functions-bindings-storage-blob.md#trigger---usage)に関する記事に、サポートされるすべてのパラメーター型の一覧が示されています。 詳細については、[トリガーとバインド](functions-triggers-bindings.md)に関する記事と、[各バインドの種類に対応するバインド リファレンス ドキュメント](functions-triggers-bindings.md#next-steps)をご覧ください。

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="binding-to-method-return-value"></a>メソッドの戻り値へのバインド

出力バインドのメソッドの戻り値を使用するには、属性をメソッドの戻り値に適用します。 例については、[トリガーとバインディング](./functions-bindings-return-value.md)に関するページを参照してください。 

正常な関数の実行によって、常に戻り値が出力バインドに渡される場合のみ、戻り値を使用してください。 それ以外の場合は、次のセクションに示すように `ICollector` または `IAsyncCollector` を使用してください。

## <a name="writing-multiple-output-values"></a>複数の出力値の書き込み

1 つの出力バインドに複数の値を書き込むため、または正常な関数の呼び出しによって出力バインドに渡される値がない場合、[`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) 型を使用してください。 これらの型は、メソッド完了時に出力バインドに書き込まれる、書き込み専用接続です。

この例では、`ICollector` を使用して複数のキュー メッセージを同じキューに書き込みます。

```csharp
public static class ICollectorExample
{
    [FunctionName("CopyQueueMessageICollector")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-3")] string myQueueItem,
        [Queue("myqueue-items-destination")] ICollector<string> myDestinationQueue,
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
        myDestinationQueue.Add($"Copy 1: {myQueueItem}");
        myDestinationQueue.Add($"Copy 2: {myQueueItem}");
    }
}
```

## <a name="logging"></a>ログの記録

出力を C# のストリーミング ログにログ記録するために、[ILogger](https://docs.microsoft.com/dotnet/api/microsoft.extensions.logging.ilogger) 型の引数を含めます。 次の例のように `log` と名前を付けることをお勧めします。  

```csharp
public static class SimpleExample
{
    [FunctionName("QueueTrigger")]
    public static void Run(
        [QueueTrigger("myqueue-items")] string myQueueItem, 
        ILogger log)
    {
        log.LogInformation($"C# function processed: {myQueueItem}");
    }
} 
```

Azure Functions では `Console.Write` を使用しないでください。 詳細については、**Azure Functions の監視**に関する記事の [C# 関数でのログの書き込み](functions-monitoring.md#write-logs-in-c-functions)についての説明を参照してください。

## <a name="async"></a>非同期

関数を[非同期](https://docs.microsoft.com/dotnet/csharp/programming-guide/concepts/async/)にするには、`async` キーワードを使用して `Task` オブジェクトを返します。

```csharp
public static class AsyncExample
{
    [FunctionName("BlobCopy")]
    public static async Task RunAsync(
        [BlobTrigger("sample-images/{blobName}")] Stream blobInput,
        [Blob("sample-images-copies/{blobName}", FileAccess.Write)] Stream blobOutput,
        CancellationToken token,
        ILogger log)
    {
        log.LogInformation($"BlobCopy function processed.");
        await blobInput.CopyToAsync(blobOutput, 4096, token);
    }
}
```

非同期関数では `out` パラメーターを使用できません。 出力バインドには、代わりに[関数の戻り値](#binding-to-method-return-value)または[コレクター オブジェクト](#writing-multiple-output-values)を使用します。

## <a name="cancellation-tokens"></a>キャンセル トークン

関数は [CancellationToken](/dotnet/api/system.threading.cancellationtoken) パラメーターを受け付けることができます。これにより、オペレーティング システムは、その関数をいつ終了しようとしているかをコードに通知できます。 この通知を使用すれば、関数が予期せず終了してデータが不整合な状態になることを防止できます。

次の例は、関数の終了が迫っているかどうかを確認する方法を示しています。

```csharp
public static class CancellationTokenExample
{
    public static void Run(
        [QueueTrigger("inputqueue")] string inputText,
        TextWriter logger,
        CancellationToken token)
    {
        for (int i = 0; i < 100; i++)
        {
            if (token.IsCancellationRequested)
            {
                logger.WriteLine("Function was cancelled at iteration {0}", i);
                break;
            }
            Thread.Sleep(5000);
            logger.WriteLine("Normal processing for queue message={0}", inputText);
        }
    }
}
```

## <a name="environment-variables"></a>環境変数

環境変数またはアプリ設定値を取得するには、次のコード例のように、 `System.Environment.GetEnvironmentVariable`を使用します。

```csharp
public static class EnvironmentVariablesExample
{
    [FunctionName("GetEnvironmentVariables")]
    public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, ILogger log)
    {
        log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
        log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
        log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
    }

    public static string GetEnvironmentVariable(string name)
    {
        return name + ": " +
            System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
    }
}
```

アプリ設定は、ローカルでの開発中と Azure での実行中の両方で、環境変数から読み取ることができます。 ローカルでの開発時、アプリ設定は *local.settings.json* ファイルの `Values` コレクションから取得します。 ローカルと Azure の両方の環境において、`GetEnvironmentVariable("<app setting name>")` は名前付きアプリ設定の値を取得します。 たとえば、ローカルでの実行中、*local.settings.json* ファイルに `{ "Values": { "WEBSITE_SITE_NAME": "My Site Name" } }` が含まれている場合は、"My Site Name" が返されます。

[System.Configuration.ConfigurationManager.AppSettings](https://docs.microsoft.com/dotnet/api/system.configuration.configurationmanager.appsettings) プロパティは、アプリ設定値を取得するための代替 API ですが、次に示すように `GetEnvironmentVariable` を使用することをお勧めします。

## <a name="binding-at-runtime"></a>実行時のバインド

C# および他の .NET 言語では、属性の[*宣言型*](https://en.wikipedia.org/wiki/Declarative_programming)のバインドではなく[命令型](https://en.wikipedia.org/wiki/Imperative_programming)のバインド パターンを使用できます。 命令型のバインドは、設計時ではなくランタイム時にバインド パラメーターを計算する必要がある場合に便利です。 このパターンを使用すると、サポートされている入力バインドと出力バインドに関数コード内でバインドできます。

次のように命令型のバインドを定義します。

- 必要な命令型のバインドの関数署名に属性を含め**ないでください。**
- 入力パラメーター [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) または [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) を渡します。
- 次の C# パターンを使用してデータ バインドを実行します。

  ```cs
  using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
  {
      ...
  }
  ```

  `BindingTypeAttribute` はバインドを定義する .NET 属性、`T` はそのバインドの種類でサポートされている入力または出力の型です。 `T` を `out` パラメーター型 (`out JObject` など) にすることはできません。 たとえば、Mobile Apps テーブルの出力バインドは [6 種類の出力](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22)をサポートしますが、強制バインドに使用できるのは [ICollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) または [IAsyncCollector\<T>](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) のみです。

### <a name="single-attribute-example"></a>単一属性の例

次のコード例は、実行時に BLOB パスが定義された [Storage Blob の出力バインド](functions-bindings-storage-blob.md#output)を作成し、この BLOB に文字列を書き込みます。

```cs
public static class IBinderExample
{
    [FunctionName("CreateBlobUsingBinder")]
    public static void Run(
        [QueueTrigger("myqueue-items-source-4")] string myQueueItem,
        IBinder binder,
        ILogger log)
    {
        log.LogInformation($"CreateBlobUsingBinder function processed: {myQueueItem}");
        using (var writer = binder.Bind<TextWriter>(new BlobAttribute(
                    $"samples-output/{myQueueItem}", FileAccess.Write)))
        {
            writer.Write("Hello World!");
        };
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/BlobAttribute.cs) は [Storage Blob](functions-bindings-storage-blob.md) の入力バインドまたは出力バインドを定義します。[TextWriter](/dotnet/api/system.io.textwriter) はサポートされている出力バインドの種類です。

### <a name="multiple-attribute-example"></a>複数属性の例

前の例では、関数アプリのメイン ストレージ アカウント接続文字列 (`AzureWebJobsStorage`) のアプリ設定を取得します。 ストレージ アカウントに使用するカスタム アプリ設定を指定するには、[StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) を追加し、属性の配列を `BindAsync<T>()` に渡します。 `IBinder`ではなく、`Binder` パラメーターを使用します。  例:

```cs
public static class IBinderExampleMultipleAttributes
{
    [FunctionName("CreateBlobInDifferentStorageAccount")]
    public async static Task RunAsync(
            [QueueTrigger("myqueue-items-source-binder2")] string myQueueItem,
            Binder binder,
            ILogger log)
    {
        log.LogInformation($"CreateBlobInDifferentStorageAccount function processed: {myQueueItem}");
        var attributes = new Attribute[]
        {
        new BlobAttribute($"samples-output/{myQueueItem}", FileAccess.Write),
        new StorageAccountAttribute("MyStorageAccount")
        };
        using (var writer = await binder.BindAsync<TextWriter>(attributes))
        {
            await writer.WriteAsync("Hello World!!");
        }
    }
}
```

## <a name="triggers-and-bindings"></a>トリガーとバインド 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [トリガーとバインドの詳細を確認する](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Azure Functions のベスト プラクティスを確認する](functions-best-practices.md)
