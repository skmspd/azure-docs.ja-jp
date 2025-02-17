---
title: クイック スタート:Java (Windows、Linux、macOS) 用 Speech SDK のプラットフォームのセットアップ - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Services SDK と一緒に Java (Windows、Linux、macOS) を使用するためにプラットフォームを設定するには、このガイドを使用します。
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/11/2019
ms.author: erhopf
ms.openlocfilehash: 2814327bdc434dbdae5644bd40b09d0506b21df9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73504341"
---
このガイドでは、64 ビット Java 8 JRE 用 [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) をインストールする方法について説明します。

> [!NOTE]
> Speech Devices SDK および Roobo デバイスについては、[Speech Devices SDK](~/articles/cognitive-services/speech-service/speech-devices-sdk.md) を参照してください。

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="supported-operating-systems"></a>サポートされているオペレーティング システム

- 以下のオペレーティング システム用の Java Speech SDK パッケージを入手できます。
  - Windows:64 ビットのみ
  - Mac: macOS X バージョン 10.13 以降
  - Linux:64 ビットのみ (Ubuntu 16.04、Ubuntu 18.04、または Debian 9)

## <a name="prerequisites"></a>前提条件

- [Java 8](https://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) または [JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

- [Eclipse Java IDE](https://www.eclipse.org/downloads/) (Java が既にインストールされている必要があります)
- サポートされている Linux プラットフォームでは、特定のライブラリがインストールされている必要があります (Secure Sockets Layer サポート用に `libssl`、サウンド サポート用に `libasound2`)。 これらのライブラリの正しいバージョンをインストールするために必要なコマンドについては、下記のディストリビューションを参照してください。

  - Ubuntu では、以下のコマンドを実行して、必要なパッケージをインストールします。

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.0 libasound2
        ```

  - Debian 9 では、以下のコマンドを実行して、必要なパッケージをインストールします。

        ```sh
        sudo apt-get update
        sudo apt-get install build-essential libssl1.0.2 libasound2
        ```

- Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 パッケージを初めてインストールする場合、このガイドを続行する前に Windows の再起動が必要になる場合があることに注意してください。

## <a name="create-an-eclipse-project-and-install-the-speech-sdk"></a>Eclipse プロジェクトを作成して Speech SDK をインストールする

[!INCLUDE [](~/includes/cognitive-services-speech-service-quickstart-java-create-proj.md)]

## <a name="next-steps"></a>次の手順

[!INCLUDE [windows](../quickstart-list.md)]
