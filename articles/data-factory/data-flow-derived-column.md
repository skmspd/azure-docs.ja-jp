---
title: Azure Data Factory マッピング データ フローの派生列変換
description: Azure Data Factory で、マッピング データ フローの派生列変換を使用して、大規模にデータを変換する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 387af25b87fdedff5d15ccb6a03819ad8b020e9a
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676882"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>マッピング データ フローの派生列変換

データ フロー内に新しい列を生成したり、既存のフィールドを変更したりするには、派生列変換を使用します。

## <a name="derived-column-settings"></a>派生列変換の設定

既存の列を上書きするには、列のドロップダウンからそれを選択します。 それ以外の場合は、列選択フィールドをテキスト ボックスとして使用して、新しい列の名前を入力します。 派生列の式を作成するには、[式の入力] ボックスをクリックして [Data Flow 式ビルダー](concepts-data-flow-expression-builder.md)を開きます。

![派生列変換の設定](media/data-flow/dc1.png "派生列変換の設定")

派生列を追加するには、既存の派生列にカーソルを合わせ、プラス アイコンをクリックします。 **[列の追加]** または **[列パターンの追加]** のいずれかを選択します。 列名がソースからの変数の場合は、列パターンが役に立つ可能性があります。 詳細については、「[列パターン](concepts-data-flow-column-pattern.md)」を参照してください。

![新しい派生列の選択](media/data-flow/columnpattern.png "新しい派生列の選択")

## <a name="data-flow-script"></a>データ フローのスクリプト

### <a name="syntax"></a>構文

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>例

次の例は、受信ストリーム `MoviesYear` を受け取り 2 つの派生列を作成する、`CleanData` という名前の派生列です。 最初の派生列は、列 `Rating` を整数型の Rating の値で置き換えます。 2 番目の派生列は、名前が 'movies' で始まる各列に一致するパターンです。 一致した列ごとに、一致する列の値と等しい列 `movie` を、'movie_' というプレフィックスを付けて作成します。 

Data Factory UX では、この変換は次の図のようになります。

![派生の例](media/data-flow/derive-script1.png "派生の例")

この変換のデータ フロー スクリプトは、次のスニペットに含まれています。

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>次の手順

- 「[Mapping Data Flow の式言語](data-flow-expression-functions.md)」で詳細を確認します。
