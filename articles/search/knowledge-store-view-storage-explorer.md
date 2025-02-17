---
title: Storage Explorer を使ってナレッジ ストア (プレビュー) を表示する
titleSuffix: Azure Cognitive Search
description: Azure portal の Storage Explorer で Azure Cognitive Search のナレッジ ストアを表示して分析します。 ナレッジ ストアは現在、パブリック プレビューの段階です。
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: e3ea879a419aa14d3a6693e23f4f120aca8d9d51
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720054"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Storage Explorer でナレッジ ストアを表示する

> [!IMPORTANT] 
> ナレッジ ストアは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 プレビュー機能は [REST API バージョン 2019-05-06-Preview](search-api-preview.md) で提供しています。 現時点でポータルによるサポートは一部のみにとどまります。また、.NET SDK によるサポートはありません。

この記事では、Azure portal の Storage Explorer を使用してナレッジ ストアに接続し、探索する方法を例を用いて説明します。

## <a name="prerequisites"></a>前提条件

+ [Azure portal でのナレッジ ストアの作成](knowledge-store-create-portal.md)に関するページか、「[REST を使用して Azure Cognitive Search のナレッジ ストアを作成する](knowledge-store-create-rest.md)」の手順に従って、このチュートリアルで使用するサンプル ナレッジ ストアを作成します。

+ ナレッジ ストアの作成に使用した Azure Storage アカウントの名前とそのアクセス キー (Azure portal から入手) も必要になります。

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Storage Explorer でのナレッジ ストアの表示、編集、クエリ

1. Azure portal で、ナレッジ ストアの作成に使用した [Storage アカウントを開きます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/)。

1. ストレージ アカウントの左側のナビゲーション ウィンドウで、 **[Storage Explorer]** をクリックします。

1. **[テーブル]** リストを展開し、ホテル レビュー サンプル データに対して**データ インポート** ウィザードを実行するときに作成した Azure テーブル プロジェクションを一覧表示します。

いずれかのテーブルを選択すると、エンリッチされたデータ (キー フレーズのセンチメント スコア、緯度と経度の位置データなど) が表示されます。

   ![Storage Explorer でテーブルを表示する](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Storage Explorer でテーブルを表示する")

テーブルの値のデータ型を変更したり、テーブル内の値を個別に変更したりするには、 **[編集]** をクリックします。 1 つのテーブル行について列のデータ型を変更すると、すべての行に適用されます。

   ![Storage Explorer でテーブルを編集する](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Storage Explorer でテーブルを編集する")

クエリを実行するには、コマンド バーの **[クエリ]** をクリックして条件を入力します。  

   ![Storage Explorer のクエリ テーブル](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Storage Explorer のクエリ テーブル")

## <a name="clean-up"></a>クリーンアップ

独自のサブスクリプションを使用している場合は、プロジェクトの最後に、作成したリソースがまだ必要かどうかを確認してください。 リソースを実行したままにすると、お金がかかる場合があります。 リソースは個別に削除することも、リソース グループを削除してリソースのセット全体を削除することもできます。

ポータルの左側のナビゲーション ウィンドウにある **[すべてのリソース]** または **[リソース グループ]** リンクを使って、リソースを検索および管理できます。

無料サービスを使っている場合は、3 つのインデックス、インデクサー、およびデータソースに制限されることに注意してください。 ポータルで個別の項目を削除して、制限を超えないようにすることができます。

## <a name="next-steps"></a>次の手順

このナレッジ ストアを Power BI に接続して詳細な分析を行うか、または REST API と Postman を使用してコーディングを進め、別のナレッジストアを作成します。

> [!div class="nextstepaction"]
> [Power BI を使用して接続する](knowledge-store-connect-power-bi.md)
> [REST でナレッジ ストアを作成する](knowledge-store-howto.md)
