---
title: ベスト プラクティス - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS アプリのモデルから最良の結果を得るための LUIS のベスト プラクティスについて説明します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 64d67edaf5affbc908fba7b6c261096589bc84d0
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487625"
---
# <a name="best-practices-for-building-a-language-understanding-app-with-cognitive-services"></a>Cognitive Services を使用して Language Understanding アプリを構築するためのベスト プラクティス
アプリの作成プロセスを使用して、ご自身の LUIS アプリをビルドします。 

* 言語モデル (意図とエンティティ) をビルドする
* 発話のトレーニング例をいくつか追加する (意図あたり 15 個から 30 個)
* エンドポイントに公開する
* エンドポイントからテストする 

ご自身のアプリが[公開](luis-how-to-publish-app.md)されたら、開発ライフサイクルを使用して機能を追加および公開し、エンドポイントからテストします。 次の作成サイクルを発話の例を追加すること開始することはしないでください。それでは LUIS に実際のユーザー発話でモデルを学習させることにはならないからです。 

現在の発話の例とエンドポイント発話の両方で、高い予測スコアが確実に返されるまでは、発話を増やさないようにします。 [アクティブ ラーニング](luis-concept-review-endpoint-utterances.md)を使用してスコアを上げてください。 




## <a name="do-and-dont"></a>すべきことと、やってはいけないこと
LUIS アプリのベスト プラクティスを次に示します。

|すべきこと|やってはいけないこと|
|--|--|
|[意図を個別に定義する](#do-define-distinct-intents)<br>[意図に記述子を追加する](#do-add-descriptors-to-intents) |[多数の発話の例を意図に追加する](#dont-add-many-example-utterances-to-intents)<br>[少数または単純なエンティティを使用する](#dont-use-few-or-simple-entities) |
|[意図の汎用性と専用性のスイート スポットを見つける](#do-find-sweet-spot-for-intents)|[LUIS をトレーニング プラットフォームとして使用する](#dont-use-luis-as-a-training-platform)|
|[バージョンを使用してアプリを反復的にビルドする](#do-build-your-app-iteratively-with-versions)<br>[モデル分解のためのエンティティをビルドする](#do-build-for-model-decomposition)|[同じ形式の発話の例を多数追加して、他の形式を無視する](#dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats)|
|[後のイテレーションでパターンを追加する](#do-add-patterns-in-later-iterations)|[意図とエンティティの定義を一緒にする](#dont-mix-the-definition-of-intents-and-entities)|
|None 意図を除く[すべての意図の間で発話のバランスを取る](#balance-your-utterances-across-all-intents)。<br>[発話の例を None 意図に追加する](#do-add-example-utterances-to-none-intent)|[指定できるすべての値で記述子を作成する](#dont-create-descriptors-with-all-the-possible-values)|
|[アクティブ ラーニングの提案機能を活用する](#do-leverage-the-suggest-feature-for-active-learning)|[非常に多くのパターン追加する](#dont-add-many-patterns)|
|[バッチ テストによりアプリのパフォーマンスを監視する](#do-monitor-the-performance-of-your-app)|[追加されたすべての発話の例でトレーニングして公開する](#dont-train-and-publish-with-every-single-example-utterance)|

## <a name="do-define-distinct-intents"></a>すべきこと: 意図を個別に定義する
各意図のボキャブラリがその意図のみを対象としていること、また、別の意図と重複していないことを確認します。 たとえば、飛行機のフライトやホテルなど、旅行の手配を処理するアプリが必要な場合は、これらの主題領域を、それぞれ個別の意図として定義するか、発話内に特定のデータのエンティティを含む 1 つの意図として定義するかを選択できます。

2 つの意図のボキャブラリが同じ場合は、意図を結合し、エンティティを使用します。 

たとえば、次の発話の例について考えます。

|発話の例|
|--|
|Book a flight|
|Book a hotel|

`Book a flight` と `Book a hotel` は、`book a ` という同じ語彙を使用します。 この形式は同じであるため、`flight` と `hotel` という異なる単語を抽出されたエンティティとして含む同じ意図として定義する必要があります。 

## <a name="do-add-descriptors-to-intents"></a>すべきこと: 意図に記述子を追加する

記述子は、意図の機能を説明するのに役立ちます。 記述子には、その意図にとって重要な語句リストや、その意図にとって重要なエンティティを指定できます。 

## <a name="do-find-sweet-spot-for-intents"></a>すべきこと: 意図のスイート スポットを見つける
LUIS の予測データを使用して、意図が重複していないかどうかを判断します。 意図が重複していると LUIS が混乱します。 その結果、上位スコアの意図と他の意図が非常に近くなります。 LUIS では、トレーニング用のデータから毎回同じパスを使用するわけではないため、重複している意図は、トレーニングで 1 位または 2 位になる可能性があります。 各意図の発話のスコアは、このフリップフロップが発生しないように、それぞれ差をつける必要があります。 意図が適切に区別されていれば、最上位の意図は毎回最上位にくるはずです。 
 
<a name="#do-build-the-app-iteratively"></a>

## <a name="do-build-your-app-iteratively-with-versions"></a>すべきこと: バージョンを使用してアプリを反復的にビルドする

各作成サイクルは、既存のバージョンから複製された新しい[バージョン](luis-concept-version.md)内にある必要があります。 

## <a name="do-build-for-model-decomposition"></a>すべきこと: モデル分解のためにビルドする

モデル分解では、一般的に次のようなプロセスを取ります。

* クライアント アプリのユーザーの意図に基づいて**意図**を作成する
* 実際のユーザー入力に基づいて 15 個から 30 個のサンプル発話を追加する
* 発話の例の最上位レベルのデータの概念にラベルを付ける
* データの概念をサブコンポーネントに分割する
* サブコンポーネントに記述子 (機能) を追加する
* 意図に記述子 (機能) を追加する 

意図を作成して発話の例を追加したところで、次の例でエンティティの分解について説明します。 

まず、発話を使用して抽出する完全なデータの概念を特定します。 これは、指定した機械学習エンティティです。 次に、そのフレーズをパーツに分解します。 これには、サブコンポーネントを (エンティティとして) 識別子や制約と共に識別することが含まれます。 

たとえば、住所を抽出する場合の最上位の機械学習エンティティは、`Address` と呼ばれることがあります。 住所の作成時に、番地、市区町村、都道府県、郵便番号などのサブコンポーネントを特定します。 

郵便番号を正規表現に**制約**することで、引き続きそれらの要素を分解します。 住所を (あらかじめ構築された番号を使用して) 番地、通りの名前、通りの種類のパーツに分解します。 通りの種類は、avenue、circle、road、lane など、**記述子**一覧から記述できます。

V3 オーサリング API がモデルの分解を可能にします。 

## <a name="do-add-patterns-in-later-iterations"></a>すべきこと: 後のイテレーションでパターンを追加する

[パターン](luis-concept-patterns.md)を追加する前に、アプリがどのように動作するかを理解しておく必要があります。パターンは、発話の例よりも高い重み付けがされており、信頼性を歪めることになるためです。 

アプリがどのように動作するかを理解したうえで、パターンがアプリに適用される際に追加します。 [イテレーション](luis-concept-app-iteration.md)ごとに追加する必要はありません。 

それらをモデル設計の開始時に追加しても害はありませんが、モデルを発話でテストした後のほうが、各パターンがモデルをどのように変えるか確認しやすくなります。 
 
<!--

### Phrase lists

[Phrase lists](luis-concept-feature.md) allow you to define dictionaries of words related to your app domain. Seed your phrase list with a few words then use the suggest feature so LUIS knows about more words in the vocabulary specific to your app. A Phrase List improves intent detection and entity classification by boosting the signal associated with words or phrases that are significant to your app. 

Don't add every word to the vocabulary since the phrase list isn't an exact match. 

For more information:
* Concept: [Phrase list features in your LUIS app](luis-concept-feature.md)
* How-to: [Use phrase lists to boost signal of word list](luis-how-to-add-features.md)



### Patterns

Real user utterances from the endpoint, very similar to each other, may reveal patterns of word choice and placement. The [pattern](luis-concept-patterns.md) feature takes this word choice and placement along with regular expressions to improve your prediction accuracy. A regular expression in the pattern allows for words and punctuation you intend to ignore while still matching the pattern. 

Use pattern's [optional syntax](luis-concept-patterns.md) for punctuation so punctuation can be ignored. Use the [explicit list](luis-concept-patterns.md#explicit-lists) to compensate for pattern.any syntax issues. 

For more information:
* Concept: [Patterns improve prediction accuracy](luis-concept-patterns.md)
* How-to: [How to add Patterns to improve prediction accuracy](luis-how-to-model-intent-pattern.md)
-->

<a name="balance-your-utterances-across-all-intents"></a>

## <a name="do-balance-your-utterances-across-all-intents"></a>すべきこと: すべての意図の間で発話のバランスを取る

LUIS による予測を正確に行うには、各意図 (None 意図を除く) の発話の例の量が同程度である必要があります。 

発話の例が 100 個の意図と発話の例が 20 個の意図がある場合、発話が 100 個の意図の方が予測率が高くなります。  

## <a name="do-add-example-utterances-to-none-intent"></a>すべきこと: 発話の例を None 意図に追加する

この意図は代替用の意図で、お使いのアプリケーション外のものすべてを示しています。 ご自身の残りの LUIS アプリでは、発話の例 10 個それぞれに 1 つの発話の例を None 意図に追加してください。

## <a name="do-leverage-the-suggest-feature-for-active-learning"></a>すべきこと: アクティブ ラーニングの提案機能を活用する

発話の例をさらに多く意図に追加するのではなく、[アクティブ ラーニング](luis-how-to-review-endpoint-utterances.md)の**エンドポイントの発話の確認**を定期的に使用します。 アプリでは常にエンドポイント発話を受信しているため、このリストは拡大および変化し続けています。

## <a name="do-monitor-the-performance-of-your-app"></a>すべきこと: ご自身のアプリのパフォーマンスを監視する

[バッチ テスト](luis-concept-batch-test.md) セットを使用して予測精度を監視します。 

[発話の例](luis-concept-utterance.md)またはエンドポイント発話として使用されていない発話のセットを個別に保持します。 ご自身のテスト セット用のアプリは改善し続けてください。 実際のユーザーの発話が反映されるようにテスト セットを調整します。 このテスト セットを使用して、アプリの各イテレーションまたはバージョンを評価します。 

## <a name="dont-add-many-example-utterances-to-intents"></a>やってはいけないこと: 多数の発話の例を意図に追加する

アプリの公開後は、開発ライフサイクルのプロセスでアクティブ ラーニングから発話を追加するだけにします。 発話が似ている場合は、パターンを追加します。 

## <a name="dont-use-few-or-simple-entities"></a>やってはいけないこと: 少数または単純なエンティティを使用する

エンティティは、データの抽出や予測のためにビルドされています。 各意図には、その意図のデータを記述する機械学習エンティティがあることが重要です。 これにより、クライアント アプリケーションで抽出されたエンティティを使用する必要がない場合でも、LUIS での意図の予測を支援します。 

## <a name="dont-use-luis-as-a-training-platform"></a>やってはいけないこと: LUIS をトレーニング プラットフォームとして使用する

LUIS は、言語モデルのドメインに固有です。 一般的な自然言語トレーニング プラットフォームとして動作するようには作られていません。 

## <a name="dont-add-many-example-utterances-of-the-same-format-ignoring-other-formats"></a>やってはいけないこと: 同じ形式の発話の例を多数追加して、他の形式を無視する

LUIS では、意図の発話にバリエーションが必要です。 発話は全体的に意味が同じでも、さまざまな形があります。 バリエーションには、発話の長さ、単語の選択、単語の配置などが含まれます。 

|同じ形式を使用しない|さまざまな形式を使用する|
|--|--|
|シアトルまでのチケットを購入する<br>パリまでのチケットを購入する<br>オーランドまでのチケットを購入する|シアトル行きのチケットを 1 枚購入する<br>パリまでの夜行便を、次の月曜日に二席予約する<br>春休みの間にオーランドへのチケットを 3 枚確保したいのですが|

2 番目の列では、それぞれ異なる動詞 (購入、予約、確保)、数量 (1、二、3)、語順が使用されていますが、旅行のために航空券を購入するという意図はすべて同じです。 

## <a name="dont-mix-the-definition-of-intents-and-entities"></a>やってはいけないこと: 意図とエンティティの定義を一緒にする

お使いのボットで取得されるすべてのアクションに対して意図を作成します。 そのアクションを可能にするためのエンティティをパラメーターとして使用します。 

飛行機のフライトを予約するボットについては、**BookFlight** 意図を作成します。 すべての航空会社、またはすべての目的地に対して、意図を作成しないでください。 これらのデータ部分を[エンティティ](luis-concept-entity-types.md)として使用し、発話の例でマークします。 

## <a name="dont-create-descriptors-with-all-the-possible-values"></a>やってはいけないこと: 指定できるすべての値で記述子を作成する

記述子の[フレーズ リスト](luis-concept-feature.md)には、少数のサンプルを指定します。すべての単語は指定しないでください。 LUIS で汎用化が行われ、コンテキストが考慮されます。 

## <a name="dont-add-many-patterns"></a>やってはいけないこと: 多数のパターンを追加する

追加する[パターン](luis-concept-patterns.md)は、多くなりすぎないようにします。 LUIS は、少ない例をすばやく学習するように作られています。 不必要にシステムに負荷をかけないでください。

## <a name="dont-train-and-publish-with-every-single-example-utterance"></a>やってはいけないこと: すべての発話の例でトレーニングして公開する

10 ～ 15 個の発話を追加したら、トレーニングして公開します。 これにより、予測精度への影響を確認することができます。 1 つの発話を追加しても、スコアに目に見える影響は出ない可能性があります。 

## <a name="next-steps"></a>次の手順

* お使いの LUIS アプリで[ご自身のアプリを計画](luis-how-plan-your-app.md)する方法を確認します。
