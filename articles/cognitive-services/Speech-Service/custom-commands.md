---
title: カスタム コマンド (プレビュー) - Speech Service
titleSuffix: Azure Cognitive Services
description: 音声アシスタントを作成するためのソリューションであるカスタム コマンド (プレビュー) の特徴、機能、および制限の概要。
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: travisw
ms.openlocfilehash: 62210bf480d09ce2a256a44b7554ac53aa06eb0c
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579702"
---
# <a name="custom-commands-preview"></a>カスタム コマンド (プレビュー)

[音声アシスタント](voice-assistants.md)はユーザーの音声を聞き取り、応答としてアクションを実行します (多くの場合は音声で応答します)。 これは、[音声テキスト変換](speech-to-text.md)を使用してユーザーの音声を文字に変換し、そのテキストの自然言語理解に対してアクションを実行します。 このアクションには、多くの場合、[テキスト読み上げ](text-to-speech.md)で生成されたアシスタントからの音声出力が含まれます。 デバイスは、Speech SDK の `DialogServiceConnector` オブジェクトを使用してアシスタントに接続します。

**カスタム コマンド (プレビュー)** は、音声アシスタントを作成するための簡素化されたソリューションです。 これにより、統一された作成エクスペリエンスと自動ホスティング モデルが提供され、[Direct Line Speech](direct-line-speech.md) など他のアシスタント作成オプションと比べて複雑さが低くなっています。 ただし、この簡素化と引き替えに柔軟性が低下します。 そのため、カスタム コマンド (プレビュー) は、タスクの完了やコマンドと制御のシナリオに最適です。 特に、モノのインターネット (IoT) やヘッドレス デバイスに適しています。

複雑な対話操作や、[仮想アシスタント ソリューションおよびエンタープライズ テンプレート](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview)などの他のソリューションとの統合については、Direct Line Speech を使用することをお勧めします。

カスタム コマンド (プレビュー) の適切な候補には、適切に定義された変数セットを持つ固定の語彙があります。 たとえば、サーモスタットの制御などのホーム オートメーションのタスクが理想的です。

   ![タスク完了シナリオの例](media/voice-assistants/task-completion-examples.png "タスク完了の例")

## <a name="getting-started-with-custom-commands-preview"></a>カスタム コマンド (プレビュー) の開始

カスタム コマンド (プレビュー) を使用して音声アシスタントを作成する最初の手順として、[音声サブスクリプション キーを取得し](get-started.md)、[Speech Studio](https://speech.microsoft.com) のカスタム コマンド (プレビュー) ビルダーにアクセスします。 そこから、新しいカスタム コマンド (プレビュー) アプリケーションを作成して発行することができます。その後、デバイス上のアプリケーションが Speech SDK を使用してそれと通信できるようになります。

   ![カスタム コマンド (プレビュー) の作成フロー](media/voice-assistants/custom-commands-flow.png "カスタム コマンド (プレビュー) の作成フロー")

10 分もかからずにコードを実行できるように設計されたクイック スタートが用意されています。

* [カスタム コマンド (プレビュー) アプリケーションを作成する](quickstart-custom-speech-commands-create-new.md)
* [パラメーターを使用してカスタム コマンド (プレビュー) アプリケーションを作成する](quickstart-custom-speech-commands-create-parameters.md)
* [Speech SDK、C# を使用してカスタム コマンド (プレビュー) アプリケーションに接続する](quickstart-custom-speech-commands-speech-sdk.md)

## <a name="sample-code"></a>サンプル コード

カスタム コマンド (プレビュー) を使用して音声アシスタントを作成するためのサンプル コードは、GitHub から入手できます。

* [音声アシスタントのサンプル (SDK)](https://aka.ms/csspeech/samples)

## <a name="customization"></a>カスタマイズ

Azure Speech Services を使用して構築された音声アシスタントでは、[音声変換](speech-to-text.md)、[テキスト読み上げ](text-to-speech.md)、および[カスタム キーワードの選択](speech-devices-sdk-create-kws.md)に利用できるさまざまなカスタム オプションを使用できます。

> [!NOTE]
> カスタマイズのオプションは、言語やロケールによって異なります ([サポートされる言語](supported-languages.md)に関するページを参照してください)。

## <a name="reference-docs"></a>リファレンス ドキュメント

* [Speech SDK](speech-sdk-reference.md)

## <a name="next-steps"></a>次の手順

* [Speech Services のサブスクリプション キーを無料で取得する](get-started.md)
* [Speech SDK を取得する](speech-sdk.md)
