---
title: クイック スタート:Anomaly Detector REST API および C# を使用して時系列データ内の異常を検出する
titleSuffix: Azure Cognitive Services
description: Anomaly Detector API を使用して、データ系列の異常をバッチとして、またはストリーミング データで検出します。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: aahi
ms.openlocfilehash: 222fb5d37065bc40e9c96a9ff3487a7ea8ad0570
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554761"
---
# <a name="quickstart-detect-anomalies-in-your-time-series-data-using-the-anomaly-detector-rest-api-and-c"></a>クイック スタート:Anomaly Detector REST API および C# を使用して時系列データ内の異常を検出する 

このクイック スタートを使用して、Anomaly Detector API の 2 つの検出モードの使用を開始し、時系列データでの異常を検出します。 この C# アプリケーションは、JSON 形式の時系列データを格納している 2 つの API 要求を送信し、応答を取得します。

| API 要求                                        | アプリケーションの出力                                                                                                                         |
|----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| バッチとして異常を検出する                        | 時系列データの各データ ポイントの異常状態 (および他のデータ) と検出された異常の位置を含んだ JSON 応答。 |
| 最新のデータ ポイントの異常状態を検出する | 時系列データの最新のデータ ポイントの異常状態 (および他のデータ) を含んだ JSON 応答。                                                                                                                                         |

 このアプリケーションは C# で記述されていますが、API はほとんどのプログラミング言語と互換性のある RESTful Web サービスです。

## <a name="prerequisites"></a>前提条件

- [Visual Studio 2017 またはそれ以降](https://visualstudio.microsoft.com/downloads/)の任意のエディション。

- NuGet パッケージとして入手できる [Json.NET](https://www.newtonsoft.com/json) フレームワーク。 Visual Studio に Newtonsoft.Json を NuGet パッケージとしてインストールするには、次の手順に従います。
    
    1. **ソリューション エクスプローラー**で､プロジェクトを右クリックします｡
    2. **[NuGet パッケージの管理]** を選択します。
    3. *Newtonsoft.json* を探してパッケージをインストールします。

- Linux/macOS を使用している場合、このアプリケーションは [Mono](https://www.mono-project.com/) を使用して実行できます。

- 時系列データ ポイントを含む JSON ファイル。 このクイック スタートのサンプル データは、[GitHub](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/request-data.json) にあります。

### <a name="create-an-anomaly-detector-resource"></a>Anomaly Detector リソースを作成する

[!INCLUDE [anomaly-detector-resource-creation](../../../../includes/cognitive-services-anomaly-detector-resource-cli.md)]

## <a name="create-a-new-application"></a>新しいアプリケーションを作成する

1. Visual Studio で新しいコンソール ソリューションを作成し、次のパッケージを追加します。 

    [!code-csharp[using statements](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=usingStatements)]


2. サブスクリプション キーとエンドポイントの変数を作成します。 異常検出に使用できる URI を以下に示します。 これらは、API 要求 URL を作成するために、後からサービス エンドポイントに付加されます。

    |検出方法  |URI  |
    |---------|---------|
    |バッチ検出    | `/anomalydetector/v1.0/timeseries/entire/detect`        |
    |最新のデータ ポイントでの検出     | `/anomalydetector/v1.0/timeseries/last/detect`        |
    
    [!code-csharp[initial variables for endpoint, key and data file](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=vars)]

## <a name="create-a-function-to-send-requests"></a>要求を送信する関数を作成する

1. 上記で作成した変数を取る `Request` という新しい非同期関数を作成します。

2. `HttpClient`オブジェクトを使用して、クライアントのセキュリティ プロトコルとヘッダー情報を設定します。 `Ocp-Apim-Subscription-Key` ヘッダーには必ずサブスクリプション キーを追加してください。 その後、要求の `StringContent` オブジェクトを作成します。

3. `PostAsync()` を使用して要求を送信してから、応答を返します。

    [!code-csharp[Request method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=requestMethod)]

## <a name="detect-anomalies-as-a-batch"></a>バッチとして異常を検出する

1. `detectAnomaliesBatch()` という新しい関数を作成します。 要求を構成し、エンドポイント、サブスクリプション キー、バッチ異常検出の URL、および時系列データを使用して `Request()` 関数を呼び出すことで送信します。

2. JSON オブジェクトを逆シリアル化し、それをコンソールに書き込みます。

3. 応答に `code` フィールドが含まれる場合は、エラー コードとエラー メッセージを印刷します。 

4. そうでない場合は、データ セット内の異常の位置を検索します。 応答の `isAnomaly` フィールドには、ブール値の配列が含まれており、各値はデータ ポイントが異常であるかどうかを示します。 応答オブジェクトの `ToObject<bool[]>()` 関数を使用して、これを文字列配列に変換します。 配列を反復処理して、すべての `true` 値のインデックスを出力します。 これらの値は、異常なデータ ポイントが見つかった場合、そのインデックスに対応します。

    [!code-csharp[Detect anomalies batch](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesBatch)]


## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>最新のデータ ポイントの異常状態を検出する

1. `detectAnomaliesLatest()` という新しい関数を作成します。 要求を構成し、エンドポイント、サブスクリプション キー、ポイント異常検出の URL、および時系列データを使用して `Request()` 関数を呼び出すことで送信します。

2. JSON オブジェクトを逆シリアル化し、それをコンソールに書き込みます。

[!code-csharp[Detect anomalies latest](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=detectAnomaliesLatest)]

## <a name="load-your-time-series-data-and-send-the-request"></a>時系列データを読み込み、要求を送信する

1. アプリケーションの main メソッドで、`File.ReadAllText()` を使用して JSON 時系列データを読み込みます。 

2. 上記で作成した異常検出関数を呼び出します。 `System.Console.ReadKey()` を使用して、アプリケーションの実行後にコンソール ウィンドウを開いたままにします。

    [!code-csharp[Main method](~/samples-anomaly-detector/quickstarts/csharp-detect-anomalies.cs?name=main)]

### <a name="example-response"></a>応答の例

成功応答が JSON 形式で返されます。 下のリンクをクリックして、GitHub で JSON 応答を表示します。
* [バッチ検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/batch-response.json)
* [最新のポイント検出応答の例](https://github.com/Azure-Samples/anomalydetector/blob/master/example-data/latest-point-response.json)

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
>[Azure Databricks を使用した異常検出のストリーム配信](../tutorials/anomaly-detection-streaming-databricks.md)

* [Anomaly Detector API](../overview.md) とは
* Anomaly Detector API を使用する場合の[ベスト プラクティス](../concepts/anomaly-detection-best-practices.md)。
* このサンプルのソース コードは、[GitHub](https://github.com/Azure-Samples/AnomalyDetector/blob/master/quickstarts/sdk/csharp-sdk-sample.cs) にあります。
