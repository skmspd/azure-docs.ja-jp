---
title: Video Indexer とは
titleSuffix: Azure Media Services
description: Azure Media Services Video Indexer サービスの概要。
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/23/2019
ms.author: juliako
ms.openlocfilehash: 9f6a5fa96034e0bf43ddd573a425de8d114d47ce
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73575109"
---
# <a name="what-is-video-indexer"></a>Video Indexer とは

Video Indexer (VI) は、Azure Media Services AI ソリューションであり、Azure Cognitive Services ブランドの一部です。 Video Indexer では、複数のチャンネル (音声、ボーカル、ビジュアル) に基づく機械学習モデルを使用して、(データ分析やコーディング スキルを必要としない) 詳細な分析情報を抽出する機能を提供します。 さらに、モデルのカスタマイズとトレーニングを行うことができます。 このサービスを使用すると、ディープ検索が可能になり、運用コストが削減され、新しい収益化の機会が可能になり、(エントリ バリアが低い) ビデオの大規模なアーカイブで新しいユーザー エクスペリエンスを生み出します。

Video Indexer で分析情報の抽出を開始するには、アカウントを作成してビデオをアップロードする必要があります。 Video Indexer にビデオをアップロードすると、さまざまな AI モデルを実行することで、ビジュアルとオーディオの両方が分析されます。 Video Indexer でビデオを分析すると、分析情報が AI モデルによって抽出されます。

次の図はイラストであり、バックエンドで Video Indexer がどのように機能するかについての技術的な説明ではありません。

![Azure Media Services Video Indexer フローの図](./media/video-indexer-overview/model-chart.png)

## <a name="what-can-i-do-with-video-indexer"></a>Video Indexer を使って何ができますか?

Video Indexer の分析情報は、次のような多くのシナリオに適用できます。

* *ディープ検索*: ビデオから抽出された分析情報を使用して、ビデオ ライブラリ全体での検索エクスペリエンスを強化します。 たとえば、話されている語句と顔にインデックスを作成すると、人物が特定の単語をいつ話したかや、2 人の人物がいつ会っていたかを検索できるようになります。 ビデオからのこのような分析情報に基づいた検索は、通信社、教育機関、放送局、エンターテイメント コンテンツの所有者、エンタープライズ LOB アプリにとって利用価値があり、一般には、ユーザーが検索の対象にするビデオ ライブラリを保有するすべての業界が対象になります。
* *コンテンツの作成*: Video Indexer によってコンテンツから抽出する分析情報に基づいて、トレーラー、ハイライト リール、ソーシャル メディア コンテンツまたはニュース クリップを作成します。 ユーザーとラベルの外観用のキーフレーム、シーン マーカー、タイムスタンプにより、作成プロセスがより滑らかで簡単になり、作成中のコンテンツに必要なビデオの部分にアクセスできるようになります。
* *アクセシビリティ*: 障碍のあるユーザーがコンテンツを利用できるようにする場合も、コンテンツをさまざまな言語を使用して異なるリージョンに配布する場合も、複数の言語の Video Indexer によって提供される文字起こしと翻訳を使用できます。
* *収益化*: Video Indexer は、ビデオの値の向上に役立ちます。 たとえば、広告収入に依存している業界 (ニュース メディア、ソーシャル メディアなど) では、抽出した分析情報を広告サーバーへの追加のシグナルとして利用することで、関連広告を提供できます。
* *コンテンツ モデレーション*: テキストとビジュアルのコンテンツ モデレーション モデルを使用して、不適切なコンテンツからユーザーの安全を維持し、公開したコンテンツが組織の値と一致することを検証します。 コンテンツに関して、特定のビデオを自動的にブロックしたり、ユーザーに通知したりすることができます。
* *推奨事項*:ビデオの分析情報は、ユーザーに関連のあるビデオ モーメントを強調表示することで、ユーザー エンゲージメントを向上させるために使用できます。 各ビデオに追加のメタデータをタグ付けすることで、ユーザーに最も関連性の高いビデオをお勧めし、ニーズに合うビデオの部分を強調表示することができます。

## <a name="features"></a>機能

次の一覧は、Video Indexer のビデオとオーディオ モデルを使用して、ビデオから取得できる分析情報を示しています。

### <a name="video-insights"></a>ビデオの分析情報

* **顔検出**:ビデオに表示される顔を検出し、グループ化します。
* **著名人の識別**:Video Indexer では、世界中のリーダー、男優、女優、アスリート、研究者、ビジネス リーダー、技術リーダーなど、100 万人を超える著名人を自動的に識別します。 これらの著名人に関するデータは、さまざまな Web サイト (IMDB、Wikipedia など) でも見つけることができます。
* **アカウントベースの顔識別**:Video Indexer では、特定のアカウントのモデルをトレーニングします。 その後、トレーニングされたモデルに基づいてビデオ内の顔を認識します。 詳細については、「[Video Indexer Web サイトから人物モデルをカスタマイズする](customize-person-model-with-website.md)」と「[Video Indexer API を使用して人物モデルをカスタマイズする](customize-person-model-with-api.md)」をご覧ください。
* **顔のサムネイルの抽出** ("最適な顔"):顔の各グループでキャプチャされた最適な顔を (品質、サイズ、正面位置に基づいて) 自動的に識別し、それをイメージ アセットとして抽出します。
* **ビジュアル テキストの認識** (OCR):ビデオ内に視覚的に表示されるテキストを抽出します。
* **ビジュアル コンテンツ モデレーション**:成人向けやわいせつなビジュアルを検出します。
* **ラベルの識別**:表示されるビジュアル オブジェクトとアクションを識別します。
* **シーンのセグメント化**: 視覚的な手掛かりに基づいて、ビデオ内でシーンが変化するタイミングを決定します。シーンは単一のイベントを表し、意味的に関連する一連の連続したショットで構成されます。
* **ショット検出**:視覚的な手掛かりに基づいて、ビデオ内のショットが変化するタイミングを決定します。ショットは、同じ動画カメラから撮影された一連のフレームです。 詳細については、「[Scenes, shots, and keyframes](scenes-shots-keyframes.md)」(シーン、ショット、キーフレーム) を参照してください。
* **黒フレームの検出**:ビデオに表示された黒フレームを識別します。
* **キーフレームの抽出**:ビデオ内の安定したキーフレームを検出します。
* **ローリング クレジット**: テレビ番組や映画の終わりにあるローリング クレジットの始まりと終わりを識別します。
* **アニメーション キャラクターの検出** (プレビュー): [Cognitive Services の Custom Vision](https://azure.microsoft.com/services/cognitive-services/custom-vision-service/) との統合によって、アニメ化されたコンテンツのキャラクターの検出、グループ化、および認識を行います。 詳細については、「[アニメーション キャラクターの検出](animated-characters-recognition.md)」を参照してください。
* **編集ショット タイプの検出**: タイプに基づくショットのタグ付け (ワイド ショット、ミディアム ショット、クローズアップ、エクストリーム クローズアップ、2 ショット、複数の人物、屋外、室内など)。 詳細については、「[編集ショット タイプの検出](scenes-shots-keyframes.md#editorial-shot-type-detection)」を参照してください。

### <a name="audio-insights"></a>オーディオの分析情報

* **自動言語検出**:主な音声言語を自動的に識別します。 英語、スペイン語、フランス語、ドイツ語、イタリア語、簡体中国語、日本語、ロシア語、ポルトガル語 (ブラジル) などの言語がサポートされています。 言語を確実に識別できない場合、Video Indexer では音声言語が英語と想定されます。 詳細については、[言語識別モデル](language-identification-model.md)に関する記事を参照してください。
* **複数言語の音声識別と文字起こし** (プレビュー):音声から異なるセグメントにある音声言語を自動的に識別します。 書き起こされるようにメディア ファイルの各セグメントを送信した後、文字起こしが 1 つの統合された文字起こしに結合されます。 詳細については、「[複数言語のコンテンツを自動的に識別および文字起こしする](multi-language-identification-transcription.md)」を参照してください。
* **音声の文字起こし**:12 の言語で音声をテキストに変換します。拡張機能を使用できます。 英語、スペイン語、フランス語、ドイツ語、イタリア語、簡体中国語、日本語、アラビア語、ロシア語、ポルトガル語 (ブラジル)、ヒンディー語、韓国語などの言語がサポートされています。
* **字幕**:VTT、TML、SRT という 3 つの形式で字幕を作成します。
* **2 チャネル処理**:個別のトランスクリプトを自動検出し、1 つのタイムラインに結合します。
* **ノイズリダクション**:(Skype フィルターに基づいて) テレフォニー音声やノイズの多い録音を明瞭にします。
* **トランスクリプトのカスタマイズ** (CRIS):音声テキスト変換のカスタム モデルをトレーニングして、業界固有のトランスクリプトを作成します。 詳細については、「[Video Indexer Web サイトから言語モデルをカスタマイズする](customize-language-model-with-website.md)」と「[Video Indexer API を使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)」をご覧ください。
* **話者の列挙**:どの話者がどの言葉をいつ話したかをマップして認識します。
* **話者の統計情報**:話者の音声率の統計情報を提供します。
* **テキストのコンテンツ モデレーション**:音声トランスクリプト内の明示的なテキストを検出します。
* **音声効果**:拍手、発言、沈黙などの音声効果を識別します。
* **感情の検出**:音声 (話されている内容) と口調 (話し方) に基づいて感情を識別します。 この感情は、喜び、悲しみ、怒り、または恐怖の可能性があります。
* **翻訳**:音声トランスクリプトの、54 の異なる言語への翻訳を作成します。

### <a name="audio-and-video-insights-multi-channels"></a>オーディオとビデオの分析情報 (マルチチャンネル)

1 つのチャンネルでインデックスを付けるときは、これらのモデルの部分的な結果を利用できます

* **キーワードの抽出**:音声と視覚テキストからキーワードを抽出します。
* **名前付きエンティティの抽出**:自然言語処理 (NLP) を使用して、音声および視覚テキストからブランド、場所、および人物を抽出します。
* **トピックの推論**:トランスクリプトから主なトピックを推論します。 第 2 レベルの IPTC 分類が含まれています。
* **成果物**:各モデルについて、"次のレベルの詳細情報" 成果物の豊富なセットを抽出します。
* **センチメント分析**:音声と視覚テキストから、ポジティブ、ネガティブ、ニュートラルのセンチメントを識別します。

## <a name="how-can-i-get-started-with-video-indexer"></a>Video Indexer を使い始めるにはどうすればよいですか?

Video Indexer の機能には、次の 3 つの方法でアクセスできます。

* Video Indexer ポータル: 製品の評価、アカウントの管理、モデルのカスタマイズを可能にする、使いやすいソリューションです。

    ポータルの詳細については、「[Video Indexer Web サイトでの作業の開始](video-indexer-get-started.md)」を参照してください。  

* API 統合: Video Indexer の機能はすべて、REST API を通じて利用できます。これにより、ソリューションをご利用のアプリとインフラストラクチャに統合できます。

    開発者として使い始めるには、「 [Video Indexer REST API の使用](video-indexer-use-apis.md)」を参照してください。

* 埋め込み可能なウィジェット: Video Indexer の分析情報、プレーヤー、エディターのエクスペリエンスをご利用のアプリに埋め込むことができます。

    詳細については、 [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)方法に関するページを参照してください。

Web サイトを使用している場合は、分析情報がメタデータとして追加され、ポータルに表示されます。 API を使用している場合、分析情報は JSON ファイルとして入手できます。

## <a name="next-steps"></a>次の手順

これで、Video Indexer の使用を開始する準備ができました。 詳細については、次の記事を参照してください。

- [Video Indexer Web サイトの使用を開始する](video-indexer-get-started.md)。
- [Video Indexer REST API を使用してコンテンツを処理する](video-indexer-use-apis.md)。
- [アプリケーションにビジュアル ウィジェットを埋め込む](video-indexer-embed-widgets.md)。
