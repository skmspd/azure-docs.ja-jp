---
title: Personalizer とは
titleSuffix: Azure Cognitive Services
description: Personalizer は、ユーザーのリアルタイムの動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにするクラウドベースの API サービスです。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 6b55ce851bb12e37aed37039889aa8e69223a286
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73467197"
---
# <a name="what-is-personalizer"></a>Personalizer とは

Azure Personalizer は、ユーザーのリアルタイムの集団動作から学習し、ユーザーに表示する最良のエクスペリエンスを選択できるようにするクラウドベースの API サービスです。

* ユーザーに関する情報とコンテンツを提供し、ユーザーに表示する最上位のアクションを受信します。 
* Personalizer を使用する前に、データのクリーンアップやラベル付けを行う必要はありません。
* 都合の良い時に Personalizer にフィードバックを提供します。 
* リアルタイム分析を表示します。 

[Personalizer のしくみ](https://personalizercontentdemo.azurewebsites.net/)のデモを見る

## <a name="how-does-personalizer-work"></a>Personalizer のしくみ

Personalizer は、機械学習モデルを使用して、コンテキスト内で最上位に順位付けされるアクションを検出します。 クライアント アプリケーションは実行可能なアクションとアクションに関する情報の一覧と、コンテキストに関する情報 (ユーザーやデバイスなどに関する情報を含む場合がある) を提供します。Personalizer は、実行するアクションを決定します。 クライアント アプリケーションで選択されたアクションが使用されると、報酬スコアの形式で Personalizer にフィードバックが提供されます。 フィードバックの受信後、Personalizer によって、今後の順位付けのために使用される独自のモデルが自動的に更新されます。 Personalizer は、時間の経過と共に 1 つのモデルをトレーニングします。このモデルは、それぞれの特徴に基づいて各コンテキストで選択する最適なアクションを提案することができます。

## <a name="how-do-i-use-the-personalizer"></a>Personalizer の使用方法

![ユーザーに表示するビデオを選択するための Personalizer の使用](media/what-is-personalizer/personalizer-example-highlevel.png)

1. アプリで個人用に設定するエクスペリエンスを選択します。
1. Azure portal で個人用設定サービスのインスタンスを作成して構成します。 各インスタンスは、Personalizer ループです。
1. [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) を使用して、ユーザーに関する情報 (_特徴_) とコンテンツ (_アクション_) で Personalizer を呼び出します。 Personalizer を使用する前にクリーンアップやラベル付けされたデータを提供する必要はありません。 API は、直接呼び出すことも、さまざまなプログラミング言語で使用可能な SDK を使用して呼び出すこともできます。
1. クライアント アプリケーションで、Personalizer によって選択されたアクションをユーザーに表示します。
1. [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) を使用して、ユーザーが Personalizer のアクションを選択したかどうかを示すフィードバックを Personalizer に提供します。 これは " _[報酬スコア](concept-rewards.md)_ " です。
1. Azure portal で分析を表示し、システムの動作方法とデータが個人設定に役立っているかを評価します。

## <a name="where-can-i-use-personalizer"></a>Personalizer を使用できる状況

たとえば、次の用途のためにクライアント アプリケーションに Personalizer を追加できます。

* ニュース Web サイトで強調表示する記事を個人設定します。    
* Web サイトでの広告配置を最適化します。
* 買い物 Web サイトに個人設定されたお勧めの品目を表示します。
* 特定の写真に適用するフィルターなど、ユーザー インターフェイス要素を提案します。
* チャット ボットの応答を選択し、ユーザーの意図を明確にするか、アクションを提案します。
* ビジネス プロセスの次のステップとして、ユーザーが行う必要がある候補のアクションを優先順付けします。

Personalizer は、ユーザー プロファイル情報を保持および管理したり、個々のユーザーの基本設定や履歴を記録したりするサービスではありません。 Personalizer は、コンテキストにおけるアクションの各相互作用の特徴から、同様の特徴が発生したときに最大の報酬を得ることができる単一のモデルを学習します。 

## <a name="personalization-for-developers"></a>開発者向けの個人用設定

Personalizer サービスには、次の 2 つの API があります。

* ユーザーに関する情報 (_特徴_) および個人用に設定するコンテンツ (_アクション_) を送信します。 Personalizer は最上位のアクションで応答します。
* ランク付けが[報酬スコア](concept-rewards.md)としてどの程度機能したかについて Personalizer にフィードバックを送信します。 

![個人用設定のイベントの基本的なシーケンス](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>次の手順

* [Personalizer の新機能](whats-new.md)
* [Personalizer のしくみ](how-personalizer-works.md)
* [強化学習とは](concepts-reinforcement-learning.md)
* [Rank 要求の機能とアクションについて学習します](concepts-features.md)
* [Reward 要求のスコアの特定について学習します](concept-rewards.md)
* [対話型デモを使用する](https://personalizationdemo.azurewebsites.net/)
