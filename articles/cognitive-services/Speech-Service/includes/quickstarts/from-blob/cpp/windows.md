---
title: クイック スタート:BLOB ストレージに格納された音声を認識する (C++) - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/28/2019
ms.author: erhopf
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 2173dbabc83ff0a03c0cfd18e02a6f3183ef90e2
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506093"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [ソース ファイルを Azure BLOB にアップロードする](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)
> * [使用する開発環境を設定する](../../../../quickstarts/setup-platform.md?tabs=dotnet)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md?tabs=dotnet)

## <a name="open-your-project-in-visual-studio"></a>Visual Studio でプロジェクトを開きます。

最初の手順として、ご利用のプロジェクトを Visual Studio で開いていることを確認します。

1. Visual Studio 2019 を起動します。
2. プロジェクトを読み込んで `helloworld.cpp` を開きます。

## <a name="add-a-references"></a>参照を追加する

短期間でコードを開発するために、外部コンポーネントをいくつか使用します。
* [CPP Rest SDK](https://github.com/microsoft/cpprestsdk) REST サービスに REST 呼び出しを行うためのクライアント ライブラリ。
* [nlohmann/json](https://github.com/nlohmann/json) 便利な JSON 解析/シリアル化/逆シリアル化ライブラリ。

いずれも [vcpkg](https://github.com/Microsoft/vcpkg/) でインストールできます。

```
vcpkg install cpprestsdk cpprestsdk:x64-windows
vcpkg install nlohmann-json
```

## <a name="start-with-some-boilerplate-code"></a>スケルトン コードを使用して開始する

このプロジェクトのスケルトンとして機能するコードを追加してみましょう。

[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-32,187-190,300-309)]
(`YourSubscriptionKey`、`YourServiceRegion`、および `YourFileUrl` の値を独自の値に置き換える必要があります。)

## <a name="json-wrappers"></a>JSON ラッパー

REST API は JSON 形式で要求を受け取り、結果も JSON で返すため、それらとは文字列のみを使用してやりとりすることが可能ですが、これについてはお勧めしていません。
要求と応答をより管理しやすくするために、JSON のシリアル化/逆シリアル化と nlohmann/json を支援するいくつかのメソッドに使用するクラスをいくつか宣言します。

先に進み、`recognizeSpeech` の前に宣言を置きます。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=33-185)]

## <a name="create-and-configure-an-http-client"></a>Http クライアントを作成して構成する
最初に必要なものは、正しいベース URL と認証セットを持つ Http クライアントです。
このコードを `recognizeSpeech` [!code-cpp に挿入します。[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=191-197)]

## <a name="generate-a-transcription-request"></a>文字起こし要求を生成する
次に、文字起こし要求を生成します。 このコードを `recognizeSpeech` に追加します。[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=199-203)]

## <a name="send-the-request-and-check-its-status"></a>要求を送信し、その状態を確認する
次に、Speech Service に要求を投稿し、初期の応答コードを確認します。 この応答コードは、サービスで要求が受信されたかどうかを単に示すものです。 文字起こしの状態が格納される場所である URL が応答ヘッダーに入れられて、サービスから返されます。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=204-216)]

## <a name="wait-for-the-transcription-to-complete"></a>文字起こしが完了するのを待つ
サービスでは文字起こしが非同期的に処理されるため、その状態を頻繁にポーリングする必要があります。 5 秒ごとにチェックします。

状態を確認するには、要求が投稿されたときに取得した URL でコンテンツを取得します。 コンテンツを取得したら、それをヘルパー クラスの 1 つに逆シリアル化することで、やりとりがさらに容易になります。

ここではポーリング コードと、すべての場合の状態の表示を示します。ただし、次に取り上げる正常な完了は除きます。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=222-245,285-299)]

## <a name="display-the-transcription-results"></a>文字起こしの結果を表示する
サービスによる文字起こしが正常に完了すると、その結果は、状態の応答から取得できる別の URL に格納されます。

この URL の内容をダウンロードし、JSON を逆シリアル化し、結果をループ処理し、表示テキストを出力します。
[!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=246-284)]

## <a name="check-your-code"></a>コードを確認する
この時点で、コードは次のようになります。(このバージョンにはいくつかのコメントを追加してあります) [!code-cpp[](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-blob/helloworld.cpp?range=7-308)]

## <a name="build-and-run-your-app"></a>アプリをビルドして実行する

これで、アプリをビルドし、Speech サービスを使用して音声認識をテストする準備ができました。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]