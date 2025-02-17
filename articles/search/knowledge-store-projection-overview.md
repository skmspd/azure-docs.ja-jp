---
title: ナレッジ ストアでのプロジェクションの操作 (プレビュー)
titleSuffix: Azure Cognitive Search
description: 全文検索以外のシナリオで使用するために、AI エンリッチメントのインデックス作成パイプラインからエンリッチされたデータをナレッジ ストアに保存して整形します。 ナレッジ ストアは現在、パブリック プレビューの段階です。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bb6af4be232810c1f5d135e459238e2e4f2cd5d8
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720047"
---
# <a name="working-with-projections-in-a-knowledge-store-in-azure-cognitive-search"></a>Azure Cognitive Search のナレッジ ストアでのプロジェクションの操作

> [!IMPORTANT] 
> ナレッジ ストアは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能は [REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供しています。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

Azure Cognitive Search では、インデックス作成の一環として組み込みのコグニティブ スキルとカスタム スキルを使ってコンテンツをエンリッチすることができるようになっています。 強化によってドキュメントに構造が追加され、検索がより効果的になります。 多くの場合、強化されたドキュメントは、ナレッジ マイニングなど、検索以外のシナリオに役立ちます。

[ナレッジ ストア](knowledge-store-concept-intro.md)のコンポーネントであるプロジェクションは、ナレッジ マイニングの目的で物理ストレージに保存できる強化されたドキュメントのビューです。 プロジェクションを使用すると、Power BI などのツールで追加の作業なしでデータを読み取ることができるように、関係を維持しながら、ニーズに合った形状にデータを "射影" できます。

プロジェクションは、Azure Table ストレージの行と列に格納されたデータ、または Azure Blob ストレージに格納された JSON オブジェクトを使用して表形式にすることができます。 データが強化されると、データの複数のプロジェクションを定義できます。 同じデータを個々のユース ケースに応じて異なる方法で整形したい場合には、プロジェクションを複数用意することが有用です。

ナレッジ ストアは 3 種類のプロジェクションをサポートしています。

+ **テーブル**:行と列として最も適切に表現されているデータの場合、テーブル プロジェクションを使用すると、テーブル ストレージでスキーマ化された形状またはプロジェクションを定義できます。

+ **オブジェクト**:データと強化の JSON 表現が必要な場合は、オブジェクトのプロジェクションを BLOB として保存します。

+ **ファイル**: ドキュメントから抽出した画像を保存する必要がある場合には、ファイル プロジェクションを使うと、正規化済みの画像を保存できます。

コンテキスト内の定義済みプロジェクションを確認する方法については、[ナレッジ ストアを使い始める方法](knowledge-store-howto.md)に関する手順を参照してください。

## <a name="projection-groups"></a>プロジェクション グループ

場合によっては、強化されたデータをさまざまな目的に合わせて異なる形状で射影する必要があります。 ナレッジ ストアを使用すると、複数のプロジェクション グループを定義できます。 プロジェクション グループには、次のように相互排他性と関連性の重要な特性があります。

### <a name="mutual-exclusivity"></a>相互排他性

1 つのグループに射影されるすべてのコンテンツは、他のプロジェクション グループに射影されるデータとは無関係です。
このような独立性が確保されていることによって、同じデータを異なる形状で持ち、各プロジェクション グループで繰り返すことができるようになっています。

### <a name="relatedness"></a>関連性

プロジェクション グループにより、プロジェクションの種類をまたいでリレーションシップを保持しつつ、ドキュメントをさまざまな種類のプロジェクションに射影できるようになりました。 1 つのプロジェクション グループ内に射影されたコンテンツはすべて、プロジェクションの種類の垣根を越えてデータのリレーションシップが保持されます。 テーブル内部では、リレーションシップは生成されたキーに基づいており、各子ノードは親ノードへの参照を保持します。 1 つのノードをさまざまな種類 (テーブル、オブジェクト、ファイル) に射影した場合でも、種類をまたいでリレーションシップが保持されます。 たとえば、画像とテキストが含まれるドキュメントがあるシナリオを考えてみましょう。 テーブルまたはオブジェクトにファイルの URL を表すプロパティがあれば、テキストをテーブルまたはオブジェクト、画像をファイルに、それぞれ射影できます。

## <a name="input-shaping"></a>入力の整形

データを正しい形状や構造にすることは、テーブルでもオブジェクトでも、効果的に使用するために重要です。 予定するアクセス方法と使用方法に基づいてデータを整形または構造化する機能は、スキルセット内で **Shaper** スキルとして公開されている重要な機能です。  

プロジェクションのスキーマに一致するオブジェクトが強化ツリーにあると、プロジェクションの定義が簡単になります。 新しくなった [Shaper スキル](cognitive-search-skill-shaper.md) を使用すると、強化ツリーのさまざまなノードからオブジェクトを構成し、それらを新しいノード以下の子にすることができます。 **Shaper** スキルを使用すると、入れ子になったオブジェクトを含む複合型を定義することができます。

射影する必要があるすべての要素を含む新しい形状を定義すると、プロジェクションのソースとして、または別のスキルへの入力としてその形状を使用できます。

## <a name="projection-slicing"></a>プロジェクションのスライス

プロジェクション グループを定義するときに、エンリッチメント ツリー内の 1 つのノードを関連する複数のテーブルまたはオブジェクトにスライスできます。 既存のプロジェクションの子であるソース パスを含むプロジェクションを追加すると、子ノードが親ノードからスライスされ、関連する新しいテーブルまたはオブジェクトに射影されます。 この手法により、すべてのプロジェクションのソースとして使用できる 1 つのノードをシェーパー スキルで定義できます。

## <a name="table-projections"></a>テーブル プロジェクション

インポートが容易になるため、Power BI を使用したデータ探索にはテーブル プロジェクションをお勧めします。 さらに、テーブル プロジェクションによって、テーブルのリレーションシップ間のカーディナリティを変更することができます。 

関係を維持しながら、インデックス内の 1 つのドキュメントを複数のテーブルに射影できます。 複数のテーブルに射影する場合、子ノードが同じグループ内の別のテーブルのソースでない限り、完全な形状が各テーブルに射影されます。

### <a name="defining-a-table-projection"></a>テーブル プロジェクションの定義

スキルセットの `knowledgeStore` 要素内にテーブル プロジェクションを定義するときは、まず強化ツリーのノードをテーブル ソースにマップします。 通常、このノードは、テーブルに射影するために必要な特定の形状を生成するスキルの一覧に追加した **Shaper** スキルの出力です。 射影対象として選択したノードは、スライスして、複数のテーブルに射影することができます。 テーブル定義は、射影するテーブルの一覧です。

各テーブルには 3 つのプロパティが必要です。

+ tableName:Azure Storage 内のテーブルの名前。

+ generatedKeyName:この行を一意に識別するキーの列名。

+ source:強化のソースとする強化ツリーのノード。 このノードは通常、シェーパーの出力ですが、スキルのいずれかの出力の可能性もあります。

テーブル プロジェクションの例を次に示します。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

この例で示すように、キー フレーズとエンティティは異なるテーブルにモデル化されており、各行の親 (MainTable) への参照が含まれます。

次の図は、[ナレッジ ストアの使用を開始する方法](knowledge-store-howto.md)に関するページの Case-law 演習を参考にしています。 ケースに複数の意見があり、各意見がその中に含まれるエンティティを識別することで強化されるシナリオでは、ここに示すようにプロジェクションをモデル化できます。

![テーブル内のエンティティとリレーションシップ](media/knowledge-store-projection-overview/TableRelationships.png "テーブル プロジェクションのリレーションシップのモデル化")

## <a name="object-projections"></a>オブジェクト プロジェクション

オブジェクト プロジェクションは、任意のノードをソースにすることができる強化ツリーの JSON 表現です。 多くのケースでは、テーブル プロジェクションの作成と同じ **Shaper** スキルを使用してオブジェクト プロジェクションを生成できます。 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "Reviews", 
              "format": "json", 
              "source": "/document/Review", 
              "key": "/document/Review/Id" 
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

オブジェクト プロジェクションを生成するには、いくつかのオブジェクト固有の属性が必要です。

+ storageContainer:オブジェクトが保存されるコンテナー
+ source:プロジェクションのルートとなる強化ツリーのノードのパス
+ key:格納するオブジェクトの一意のキーを表すパス。 コンテナー内の BLOB 名を作成するために使用されます。

## <a name="file-projection"></a>ファイル プロジェクション

ファイル プロジェクションは、オブジェクト プロジェクションによく似たものであり、`normalized_images` コレクションに対してのみ機能します。 ファイル プロジェクションはオブジェクト プロジェクションと同じく、ドキュメント ID を base64 でエンコードした値をフォルダーのプレフィックスとした BLOB コンテナーに保存されます。 ファイル プロジェクションとオブジェクト プロジェクションのコンテナーを同じにすることはできないため、両者は別々のコンテナーに射影する必要があります。

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>プロジェクションのライフサイクル

プロジェクションには、データ ソース内のソース データに関連付けられているライフサイクルがあります。 データが更新され、インデックスが再作成されると、エンリッチメントの結果と共にプロジェクションが更新され、最終的にプロジェクションがデータ ソースのデータと一致するようになります。 プロジェクションは、インデックスに構成した削除ポリシーを継承します。 インデクサーまたは検索サービス自体を削除した場合でも、プロジェクションが削除されることはありません。

## <a name="using-projections"></a>プロジェクションの使用

インデクサーの実行後に、プロジェクションによって指定したコンテナーまたはテーブル内の射影されたデータを読み取ることができます。

分析に関しては、Power BI での探索は、Azure Table ストレージをデータ ソースとして設定する場合と同じくらい簡単です。 内部のリレーションシップを利用すると、データを簡単に視覚化できます。

また、データ サイエンス パイプラインで強化されたデータを使用する必要がある場合は、[BLOB から Pandas DataFrame にデータを読み込む](../machine-learning/team-data-science-process/explore-data-blob.md)こともできます。

最後に、ナレッジ ストアからデータをエクスポートする必要がある場合、Azure Data Factory には、データをエクスポートして任意のデータベースに格納するコネクタがあります。 

## <a name="next-steps"></a>次の手順

次の手順として、サンプル データと手順を使用して最初のナレッジ ストアを作成します。

> [!div class="nextstepaction"]
> [ナレッジ ストアを作成する方法](knowledge-store-howto.md)。
