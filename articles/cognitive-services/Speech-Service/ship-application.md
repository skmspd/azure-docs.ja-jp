---
title: Speech SDK でアプリを開発する - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech SDK を使用してアプリを作成する方法について説明します。
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: jhakulin
ms.custom: seodec18
ms.openlocfilehash: 166ae00085f07ef24d746b60947a31e7680a0f00
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491009"
---
# <a name="ship-an-application"></a>アプリケーションの出荷

Azure Cognitive Services Speech SDK を配布するときは、[Speech SDK ライセンス](https://aka.ms/csspeech/license201809)だけでなく、[サード パーティ ソフトウェアに関する通知](https://csspeechstorage.blob.core.windows.net/drop/1.0.0/ThirdPartyNotices.html)も確認します。 さらに、[Microsoft のプライバシーに関する声明](https://aka.ms/csspeech/privacy)も確認してください。

プラットフォームによって、ご自身のアプリケーションを実行するための依存関係には違いがあります。

## <a name="windows"></a>Windows

Cognitive Services Speech SDK は、Windows 10 および Windows Server 2016 でテストされています。

Cognitive Services Speech SDK には、[Visual Studio 2019 の Microsoft Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。 最新バージョンの `Microsoft Visual C++ Redistributable for Visual Studio 2019` のインストーラーはこちらからダウンロードできます。

- [Win32](https://aka.ms/vs/16/release/vc_redist.x86.exe)
- [x64](https://aka.ms/vs/16/release/vc_redist.x64.exe)

お使いのアプリケーションでマネージド コードを使用する場合は、ターゲット マシンに `.NET Framework 4.6.1` 以降が必要です。

マイク入力のために、Media Foundation ライブラリをインストールする必要があります。 これらのライブラリは、Windows 10 および Windows Server 2016 に含まれます。 マイクがオーディオ入力デバイスとして使用されていない場合は、これらのライブラリがなくても、Speech SDK を使用できます。

必要な Speech SDK ファイルは、お使いのアプリケーションと同じディレクトリに展開できます。 この方法で、お使いのアプリケーションはライブラリに直接アクセスできます。 必ず、お使いのアプリケーションと一致する正しいバージョン (Win32/x64) を選択してください。

| 名前 | Function
|:-----|:----|
| `Microsoft.CognitiveServices.Speech.core.dll` | Core SDK。ネイティブおよびマネージド展開に必要
| `Microsoft.CognitiveServices.Speech.csharp.dll` | マネージド展開に必要

>[!NOTE]
> リリース 1.3.0 以降、(以前のリリースで提供されていた) `Microsoft.CognitiveServices.Speech.csharp.bindings.dll` ファイルは不要になりました。 この機能はコア SDK に統合されました。

>[!NOTE]
> Windows フォーム アプリケーション (.NET Framework) の C# プロジェクトの場合は、ライブラリがプロジェクトのデプロイ設定に含まれていることを確認してください。 これは `Properties -> Publish Section` で確認できます。 `Application Files` ボタンをクリックし、一覧を下にスクロールして、対応するライブラリを見つけます。 値が `Included` に設定されていることを確認します。 プロジェクトが発行またはデプロイされると、Visual Studio にこのファイルが組み込まれます。

## <a name="linux"></a>Linux

現在、Speech SDK では、Ubuntu 16.04、Ubuntu 18.04、および Debian 9 のディストリビューションがサポートされています。
ネイティブ アプリケーションについては、Speech SDK ライブラリ `libMicrosoft.CognitiveServices.Speech.core.so` を配布する必要があります。
必ず、お使いのアプリケーションと一致するバージョン (x86、x64) を選択してください。 Linux バージョンによっては、次の依存関係を追加しなければならない場合もあります。

* GNU C ライブラリの共有ライブラリ (POSIX Threads Programming ライブラリ `libpthreads` など)
* OpenSSL ライブラリ (`libssl.so.1.0.0` または `libssl.so.1.0.2`)
* ALSA アプリケーションの共有ライブラリ (`libasound.so.2`)

Ubuntu では、GNU C ライブラリが既定でインストールされています。 残りの 3 つをインストールするには、次のコマンドを使用します。

```sh
sudo apt-get update
sudo apt-get install libssl1.0.0 libasound2
```

Debian9 では、次のパッケージをインストールします。

```sh
sudo apt-get update
sudo apt-get install libssl1.0.2 libasound2
```

## <a name="next-steps"></a>次の手順

* [Speech 試用版サブスクリプションを取得する](https://azure.microsoft.com/try/cognitive-services/)
* [C# で音声を認識する方法を確認する](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-csharp&tabs=dotnet)
