---
title: クイック スタート:長い形式の音声の非同期合成 (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: Long Audio API を使用し、テキストを音声に非同期で変換し、サービスにより提供される URI からオーディオ出力を取得します。 この REST API は、10,000 文字または 50 段落を超えるテキスト ファイルを合成音声に変換する必要があるコンテンツ プロバイダーに最適です。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 2c1e6ef84a472ccec5116b12e18ad80c92b68960
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681878"
---
# <a name="quickstart-asynchronous-synthesis-for-long-form-audio-in-python-preview"></a>クイック スタート:Python の長い形式の音声の非同期合成 (プレビュー)

このクイックスタートでは、Long Audio API を使用し、テキストを音声に非同期で変換し、サービスにより提供される URI からオーディオ出力を取得します。 この REST API は、10,000 文字または 50 段落を超えるテキスト ファイルを合成音声に変換する必要があるコンテンツ プロバイダーに最適です。 詳細については、「[Long Audio API](../../long-audio-api.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは以下が必要です。

* Python 2.7.x または 3.x。
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)、[Visual Studio Code](https://code.visualstudio.com/download)、または任意のテキスト エディター。
* Azure サブスクリプションと Speech サービスのサブスクリプション キー。 [Azure アカウントを作成](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account)し、[Speech リソースを作成](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure)してキーを取得します。 Speech リソースを作成する際は、価格レベルが **S0** に設定されていること、また場所が[サポート対象リージョン](../../regions.md#standard-and-neural-voices)に設定されていることを確認してください。

## <a name="create-a-project-and-import-required-modules"></a>プロジェクトの作成と必要なモジュールのインポート

普段使用している IDE またはエディターで、新しい Python プロジェクトを作成します。 次に、このコード スニペットを `voice_synthesis_client.py` という名前のファイルにコピーします。

```python
import argparse
import json
import ntpath
import urllib3
import requests
import time
from json import dumps, loads, JSONEncoder, JSONDecoder
import pickle

urllib3.disable_warnings(urllib3.exceptions.InsecureRequestWarning)
```

> [!NOTE]
> これらのモジュールを使用していない場合は、プログラムを実行する前にこれらをインストールする必要があります。 これらのパッケージをインストールするには、`pip install requests urllib3` を実行します。

これらのモジュールは、引数を解析し、HTTP 要求を構築し、テキスト読み上げの長い音声 REST API を呼び出すために使用されます。

## <a name="get-a-list-of-supported-voices"></a>サポートされている音声の一覧を取得する

このコードからは、テキスト読み上げの変換に利用できる音声の一覧が取得されます。 このコード `voice_synthesis_client.py` を追加します。

```python
parser = argparse.ArgumentParser(description='Cris client tool to submit voice synthesis requests.')
parser.add_argument('--voices', action="store_true", default=False, help='print voice list')
parser.add_argument('-key', action="store", dest="key", required=True, help='the cris subscription key, like ff1eb62d06d34767bda0207acb1da7d7 ')
parser.add_argument('-region', action="store", dest="region", required=True, help='the region information, could be centralindia, canadacentral or uksouth')
args = parser.parse_args()
baseAddress = 'https://%s.cris.ai/api/texttospeech/v3.0-beta1/' % args.region

def getVoices():
    response=requests.get(baseAddress+"voicesynthesis/voices", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    voices = json.loads(response.text)
    return voices

if args.voices:
    voices = getVoices()
    print("There are %d voices available:" % len(voices))
    for voice in voices:
        print ("Name: %s, Description: %s, Id: %s, Locale: %s, Gender: %s, PublicVoice: %s, Created: %s" % (voice['name'], voice['description'], voice['id'], voice['locale'], voice['gender'], voice['isPublicVoice'], voice['created']))
```

### <a name="test-your-code"></a>コードのテスト

ここまでの内容をテストしてみましょう。 このコマンドを実行します。`<your_key>` は音声サブスクリプション キーに、`<region>` は音声リソースが作成されたリージョン (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。

```console
python voice_synthesis_client.py --voices -key <your_key> -region <Region>
```

出力は次のようになります。

```console
There are xx voices available:

Name: Microsoft Server Speech Text to Speech Voice (en-US, xxx), Description: xxx , Id: xxx, Locale: en-US, Gender: Male, PublicVoice: xxx, Created: 2019-07-22T09:38:14Z
Name: Microsoft Server Speech Text to Speech Voice (zh-CN, xxx), Description: xxx , Id: xxx, Locale: zh-CN, Gender: Female, PublicVoice: xxx, Created: 2019-08-26T04:55:39Z
```

## <a name="convert-text-to-speech"></a>テキストを音声に変換する

次の手順では、入力テキスト ファイルを準備します。 プレーンテキストか SSML にできますが、10,000 文字以上か 50 段落以上にする必要があります。 要件の完全な一覧については、「[Long Audio API](../../long-audio-api.md)」を参照してください。

テキスト ファイルを準備したら、 次の手順では、音声合成用のコードをプロジェクトに追加します。 このコードを `voice_synthesis_client.py` に追加します。

> [!NOTE]
> 既定では、オーディオ出力は riff-16khz-16bit-mono-pcm に設定されています。 サポートされているオーディオ出力の詳細については、「[Long Audio API](../../long-audio-api.md#audio-output-formats)」を参照してください。

```python
parser.add_argument('--submit', action="store_true", default=False, help='submit a synthesis request')
parser.add_argument('--concatenateResult', action="store_true", default=False, help='If concatenate result in a single wave file')
parser.add_argument('-file', action="store", dest="file", help='the input text script file path')
parser.add_argument('-voiceId', action="store", nargs='+', dest="voiceId", help='the id of the voice which used to synthesis')
parser.add_argument('-locale', action="store", dest="locale", help='the locale information like zh-CN/en-US')
parser.add_argument('-format', action="store", dest="format", default='riff-16khz-16bit-mono-pcm', help='the output audio format')

def submitSynthesis():
    modelList = args.voiceId
    data={'name': 'simple test', 'description': 'desc...', 'models': json.dumps(modelList), 'locale': args.locale, 'outputformat': args.format}
    if args.concatenateResult:
        properties={'ConcatenateResult': 'true'}
        data['properties'] = json.dumps(properties)
    if args.file is not None:
        scriptfilename=ntpath.basename(args.file)
        files = {'script': (scriptfilename, open(args.file, 'rb'), 'text/plain')}
    response = requests.post(baseAddress+"voicesynthesis", data, headers={"Ocp-Apim-Subscription-Key":args.key}, files=files, verify=False)
    if response.status_code == 202:
        location = response.headers['Operation-Location']
        id = location.split("/")[-1]
        print("Submit synthesis request successful")
        return id
    else:
        print("Submit synthesis request failed")
        print("response.status_code: %d" % response.status_code)
        print("response.text: %s" % response.text)
        return 0

def getSubmittedSynthesis(id):
    response=requests.get(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    synthesis = json.loads(response.text)
    return synthesis

if args.submit:
    id = submitSynthesis()
    if (id == 0):
        exit(1)

    while(1):
        print("\r\nChecking status")
        synthesis=getSubmittedSynthesis(id)
        if synthesis['status'] == "Succeeded":
            r = requests.get(synthesis['resultsUrl'])
            filename=id + ".zip"
            with open(filename, 'wb') as f:  
                f.write(r.content)
                print("Succeeded... Result file downloaded : " + filename)
            break
        elif synthesis['status'] == "Failed":
            print("Failed...")
            break
        elif synthesis['status'] == "Running":
            print("Running...")
        elif synthesis['status'] == "NotStarted":
            print("NotStarted...")
        time.sleep(10)
```

### <a name="test-your-code"></a>コードのテスト

入力ファイルをソースとして使用してテキストを合成するように要求してみましょう。 下の要求でいくつかの項目を更新する必要があります。

* `<your_key>` をお使いの音声サービスのサブスクリプションに置き換えます。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<region>` を音声リソースが作成された場所 (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。
* `<input>` を、テキスト読み上げから変換するテキスト ファイルのパスに置換します。
* `<locale>` を必要な出力ロケールに置換します。 詳細については、「[言語サポート](../../language-support.md#neural-voices)」を参照してください。
* `<voice_guid>` を、オーディオ出力に必要な音声に置換します。 「[サポートされている音声の一覧を取得する](#get-a-list-of-supported-voices)」から返される音声の 1 つを使用するか、[言語サポート](../../language-support.md#neural-voices)で提供されるニューラル音声の一覧を使用します。

このコマンドでテキストを音声に変換します。

```console
python voice_synthesis_client.py --submit -key <your_key> -region <Region> -file <input> -locale <locale> -voiceId <voice_guid>
```

> [!NOTE]
> "concatenateResult" は省略可能なパラメーターです。このパラメーターが指定されない場合、出力は複数の wave ファイルとして提供されます。行ごとに 1 つとなります。

出力は次のようになります。

```console
Submit synthesis request successful

Checking status
NotStarted...

Checking status
Running...

Checking status
Running...

Checking status
Succeeded... Result file downloaded : xxxx.zip
```

与えられた結果には、入力テキストと、サービスによって生成されたオーディオ出力ファイルが含まれます。 これらは zip としてダウンロードされます。

## <a name="remove-previous-requests"></a>前の要求を削除する

サブスクリプションごとに 2000 要求という上限があります。 そのため、前に送信した要求を削除しないと新しい要求を作成できない場合があります。 既存の要求を削除しない場合、2,000 を超過したときにエラーが表示されます。

このコードを `voice_synthesis_client.py` に追加します。

```python
parser.add_argument('--syntheses', action="store_true", default=False, help='print synthesis list')
parser.add_argument('--delete', action="store_true", default=False, help='delete a synthesis request')
parser.add_argument('-synthesisId', action="store", nargs='+', dest="synthesisId", help='the id of the voice synthesis which need to be deleted')

def getSubmittedSyntheses():
    response=requests.get(baseAddress+"voicesynthesis", headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
    syntheses = json.loads(response.text)
    return syntheses

def deleteSynthesis(ids):
    for id in ids:
        print("delete voice synthesis %s " % id)
        response = requests.delete(baseAddress+"voicesynthesis/"+id, headers={"Ocp-Apim-Subscription-Key":args.key}, verify=False)
        if (response.status_code == 204):
            print("delete successful")
        else:
            print("delete failed, response.status_code: %d, response.text: %s " % (response.status_code, response.text))

if args.syntheses:
    synthese = getSubmittedSyntheses()
    print("There are %d synthesis requests submitted:" % len(synthese))
    for synthesis in synthese:
        print ("ID : %s , Name : %s, Status : %s " % (synthesis['id'], synthesis['name'], synthesis['status']))

if args.delete:
    deleteSynthesis(args.synthesisId)
```

### <a name="test-your-code"></a>コードのテスト

このコマンドを実行します。`<your_key>` は音声サブスクリプション キーに、`<region>` は音声リソースが作成されたリージョン (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。

```console
python voice_synthesis_client.py – syntheses -key <your_key> -region <Region>
```

これで、要求した合成の一覧が返されます。 出力は次のようになります。

```console
There are <number> synthesis requests submitted:
ID : xxx , Name : xxx, Status : Succeeded
ID : xxx , Name : xxx, Status : Running
ID : xxx , Name : xxx : Succeeded
```

それでは、いくつかの値を使用し、前に送信した要求を削除してみましょう。 このコマンドを実行します。`<your_key>` は音声サブスクリプション キーに、`<region>` は音声リソースが作成されたリージョン (`eastus` や `westus` など) に置換します。 この情報は、[Azure portal](https://aka.ms/azureportal) のご利用のリソースの **[概要]** タブで確認できます。 `<synthesis_id>` は、前の要求で返された値のいずれかにしてください。

> [!NOTE]
> 状態が "実行中"/"待機中" の要求は削除できません。

```console
python voice_synthesis_client.py – delete -key <your_key> -region <Region> -synthesisId <synthesis_id>
```

出力は次のようになります。

```console
delete voice synthesis xxx
delete successful
```

## <a name="get-the-full-client"></a>完全なクライアントを取得する

完全な `voice_synthesis_client.py` は [GitHub](https://github.com/Azure-Samples/Cognitive-Speech-TTS/blob/master/CustomVoice-API-Samples/Python/voiceclient.py) からダウンロードできます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Long Audio API について学習する](../../long-audio-api.md)
