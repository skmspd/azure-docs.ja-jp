---
title: クイック スタート:マイクから音声を認識する、Python - Speech Service
titleSuffix: Azure Cognitive Services
description: このガイドでは、Python 用 Speech SDK を使用する音声テキスト変換コンソール アプリケーションを作成します。 完了すると、お使いのコンピューターのマイクを使用して、リアルタイムに文字起こし (音声テキスト変換) することができます。
services: cognitive-services
author: chlandsi
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: chlandsi
ms.openlocfilehash: 7fe11876a7ba20981f784bc41ce8094178591371
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505773"
---
## <a name="prerequisites"></a>前提条件

開始する前に、必ず次のことを行ってください。

> [!div class="checklist"]
> * [Azure Speech リソースを作成する](../../../../get-started.md)
> * [LUIS アプリケーションを作成し、エンドポイント キーを取得する](../../../../quickstarts/create-luis.md)
> * [使用する開発環境をセットアップする](../../../../quickstarts/setup-platform.md)
> * [空のサンプル プロジェクトを作成する](../../../../quickstarts/create-project.md)

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

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

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
   メッセージが表示されたら、数単語を発声します。 少し後に、変換されたテキストが表示されます。

   ![サンプルを実行する](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run.png)

これらの手順で問題が発生した場合は、より詳しい [Visual Studio Code Python チュートリアル](https://code.visualstudio.com/docs/python/python-tutorial)を参照してください。

## <a name="next-steps"></a>次の手順

[!INCLUDE [footer](./footer.md)]
