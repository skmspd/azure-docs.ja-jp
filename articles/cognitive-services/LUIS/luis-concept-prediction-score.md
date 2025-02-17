---
title: 予測スコア - LUIS
titleSuffix: Azure Cognitive Services
description: 予測スコアは、LUIS API サービスがユーザーの発話に基づいて予測結果に対して持つ信頼度を示します。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 5b8d97005d8f404a296ddb45e92b65e4aa811aa3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486777"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>意図とエンティティの予測精度を示す予測スコア

予測スコアは、LUIS がユーザーの発話の予測結果に対して持つ信頼度を示します。

予測スコアは、0 と 1 の間です。 十分に信頼できる LUIS スコアの例は 0.99 です。 信頼度の低いのスコアの例は 0.01 です。 

|スコアの値|Confidence|
|--|--|
|1|明確に一致|
|0.99|信頼度が高い|
|0.01|信頼度が低い|
|0|明確に不一致|

## <a name="top-scoring-intent"></a>トップ スコアの意図

すべての発話予測で、トップ スコアの意図が返されます。 この予測は予測スコアの数値を比較したものです。 

## <a name="proximity-of-scores-to-each-other"></a>スコアの相互近接性

上位 2 つのスコアでは、違いが非常に小さいことがあります。 LUIS でこの近接性が示されるのは、上位のスコアを返すときのみです。  

## <a name="return-prediction-score-for-all-intents"></a>すべての意図について予測スコアを返す

テストまたはエンドポイントの結果には、すべての意図を含めることができます。 この構成は、正しい名前/値ペアのクエリ文字列を使用してエンドポイントで設定します。

|Prediction API|クエリ文字列名|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>スコアが近い意図を確認する

正しい意図が識別されているだけでなく、どの発話でも 2 番目に識別される意図のスコアが常にかなり低いことを検証するために、すべての意図のスコアを確認することをお勧めします。

発話のコンテキストに基づいて、複数の意図の予測スコアが近い場合、LUIS によって意図が切り替えられる場合があります。 このような状況を解決するには、コンテキストの違いが多様な発話を各意図に追加し続けるか、上位 2 つの意図の処理方法をプログラムで選択するチャット ボットなどのクライアント アプリケーションを利用します。

スコアが非常に近い 2 つの意図は、**非決定性トレーニング**のために順序が逆になる可能性があります。 トップ スコアがセカンド トップになり、セカンド トップ スコアがファースト トップ スコアになることがあります。 この状況を防ぐには、上位 2 つの各意図に、2 つの意図を区別する単語選択とコンテキストを含む発話の例を追加します。 2 つの意図の発話例の数はだいたい同じにする必要があります。 トレーニングによる逆転を防ぐために分離の目安は、スコアの差を 15% にすることです。

[すべてのデータを使用してトレーニングする](luis-how-to-train.md#train-with-all-data)ことで、**非決定性トレーニング**をオフにすることができます。

## <a name="differences-with-predictions-between-different-training-sessions"></a>異なるトレーニング セッション間の予測との違い

別のアプリで同じモデルをトレーニングしたときに、同じスコアにならない場合、その違いの原因は**非決定性トレーニング** (ランダムな要素) があるためです。 また、複数の意図に対して 1 つの発話が重複して設定されていると、同じ発話であるのにトップの意図がトレーニング次第で変わる可能性があります。

意図の信頼性を示すためにチャットボットで特定の LUIS スコアを必要とする場合、上位 2 つの意図のスコアの差異を使用する必要があります。 このような状況では、トレーニングのバリエーションが柔軟になります。

[すべてのデータを使用してトレーニングする](luis-how-to-train.md#train-with-all-data)ことで、**非決定性トレーニング**をオフにすることができます。

## <a name="e-exponent-notation"></a>E (指数) 表記

予測スコアでは、`9.910309E-07` など、0 から 1 の範囲を超えているように "*見える*" 指数表記が使用される場合があります。 このスコアは、非常に**小さな**数を表します。

|E 表記スコア |実際のスコア|
|--|--|
|9.910309E-07|.0000009910309|

## <a name="punctuation"></a>句読点

句読点を使用する方法または無視する方法の[詳細を確認](luis-concept-utterance.md#punctuation-marks)してください。 

## <a name="next-steps"></a>次の手順

[エンティティの追加](luis-how-to-add-entities.md)に関するページで、LUIS アプリにエンティティを追加する方法の詳細を確認します。
