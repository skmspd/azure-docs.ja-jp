---
title: クイック スタート:音声をオーディオ ファイルに合成する (Python) - Speech Service
titleSuffix: Azure Cognitive Services
description: TBD
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: d6f6519ea5df630a914243046e74c315b4bd7db9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505397"
---
## <a name="prerequisites"></a>前提条件

* Speech Services 用の Azure サブスクリプション キー。 [無料で 1 つ取得します](~/articles/cognitive-services/Speech-Service/get-started.md)。
* [Python 3.5 以降](https://www.python.org/downloads/)。
* 以下のオペレーティング システム用の Python Speech SDK パッケージを入手できます。
    * Windows: x64 および x86。
    * Mac: macOS X バージョン 10.12 以降。
    * Linux:x64 上の Ubuntu 16.04、Ubuntu 18.04、Debian 9。
* Linux では、以下のコマンドを実行して、必要なパッケージをインストールします。

  * Ubuntu の場合:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * Debian 9 の場合:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* Windows では、お使いのプラットフォームに対応した [Microsoft Visual Studio 2019 の Visual C++ 再頒布可能パッケージ](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)が必要です。

## <a name="install-the-speech-sdk"></a>Speech SDK のインストール

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

次のコマンドは、[PyPI](https://pypi.org/) から Speech SDK 用の Python パッケージをインストールします。

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>サポートと更新プログラム

Speech SDK Python パッケージの更新プログラムは、PyPI を通じて配布され、[リリース ノート](~/articles/cognitive-services/Speech-Service/releasenotes.md)で発表されます。
新しいバージョンが利用可能な場合は、コマンド `pip install --upgrade azure-cognitiveservices-speech` を使用してそれに更新できます。
`azure.cognitiveservices.speech.__version__` 変数を調べて、現在インストールされているバージョンを確認します。

問題があるか、機能が欠落している場合は、「[サポート オプションとヘルプ オプション](~/articles/cognitive-services/Speech-Service/support.md)」を参照してください。

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Speech SDK を使用する Python アプリケーションの作成

### <a name="run-the-sample"></a>サンプルを実行する

このクイック スタートからソース ファイル `quickstart.py` に[サンプル コード](#sample-code)をコピーして、お使いの IDE またはコンソールで実行することができます。

```sh
python quickstart.py
```

また、このクイック スタート チュートリアルを [Speech SDK のサンプル リポジトリ](https://github.com/Azure-Samples/cognitive-services-speech-sdk/)から [Jupyter](https://jupyter.org) ノートブックとしてダウンロードして、ノートブックとして実行することもできます。

### <a name="sample-code"></a>サンプル コード

````Python

import azure.cognitiveservices.speech as speechsdk

# Creates an instance of a speech config with specified subscription key and service region.
# Replace with your own subscription key and service region (e.g., "westus").
speech_key, service_region = "YourSubscriptionKey", "YourServiceRegion"
speech_config = speechsdk.SpeechConfig(subscription=speech_key, region=service_region)

# Creates an audio configuration that points to an audio file.
# Replace with your own audio filename.
audio_filename = "helloworld.wav"
audio_output = speechsdk.AudioOutputConfig(filename=audio_filename)

# Creates a synthesizer with the given settings
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_output)

# Synthesizes the text to speech.
# Replace with your own text.
text = "Hello world!"
result = speech_synthesizer.speak_text_async(text).get()

# Checks result.
if result.reason == speechsdk.ResultReason.SynthesizingAudioCompleted:
    print("Speech synthesized to [{}] for text [{}]".format(audio_filename, text))
elif result.reason == speechsdk.ResultReason.Canceled:
    cancellation_details = result.cancellation_details
    print("Speech synthesis canceled: {}".format(cancellation_details.reason))
    if cancellation_details.reason == speechsdk.CancellationReason.Error:
        if cancellation_details.error_details:
            print("Error details: {}".format(cancellation_details.error_details))
    print("Did you update the subscription info?")
````

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Speech SDK と Visual Studio Code をインストールして使用する

1. [Python](https://www.python.org/downloads/) の 64 ビット バージョン (3.5 以降) をお使いのコンピューターにダウンロードして、インストールします。
1. [Visual Studio Code](https://code.visualstudio.com/Download) をダウンロードして、インストールします。
1. Visual Studio Code を開いて、Python 拡張機能をインストールします。 メニューで **[ファイル]**  >  **[基本設定]**  >  **[拡張機能]** の順に選択します。 **Python** を検索します。

   ![Python 拡張機能のインストール](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. プロジェクトの保存先となるフォルダーを作成します。 たとえば、エクスプローラーを使用して行います。
1. Visual Studio Code の **[ファイル]** アイコンを選択します。 次に、作成したフォルダーを開きます。

   ![フォルダーを開く](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. 新しいファイル アイコンを選択して、新しい Python ソース ファイル `speechsdk.py` を作成します。

   ![ファイルを作成する](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. 新しく作成されたファイルに [Python コード](#sample-code)をコピーして貼り付け、保存します。
1. お使いの Speech Services のサブスクリプション情報を挿入します。
1. Python インタープリターが選択されている場合は、ウィンドウの下部にあるステータス バーの左側にそれが表示されます。
   そうでない場合は、使用可能な Python インタープリターの一覧が表示されます。 コマンド パレットを開いて (Ctrl + Shift + P)、「**Python: Select Interpreter**」と入力します。 適切なものを選択します。
1. Visual Studio Code 内から Speech SDK Python パッケージをインストールできます。 選択した Python インタープリター用にまだインストールされていない場合は、インストールします。
   Speech SDK パッケージをインストールするには、ターミナルを開きます。 コマンド パレットをもう一度開いて (Ctrl + Shift + P)、「**Terminal: Create New Integrated Terminal**」と入力してターミナルを開きます。
   開いたターミナルに、コマンド `python -m pip install azure-cognitiveservices-speech` か、システムに応じた適切なコマンドを入力します。
1. サンプル コードを実行するには、エディター内のどこかを右クリックします。 **[Run Python File in Terminal]\(ターミナル内の Python ファイルを実行する\)** を選択します。
   テキストは音声に変換され、指定されたオーディオ データで保存されます。

   ```text
   Speech synthesized to [helloworld.wav] for text [Hello world!]
   ```

これらの手順で問題が発生した場合は、より詳しい [Visual Studio Code Python チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>関連項目

- [カスタム音声を作成する](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [カスタム音声サンプルを録音する](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
