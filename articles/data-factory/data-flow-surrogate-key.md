---
title: Azure Data Factory マッピング データ フローの代理キー変換
description: Azure Data Factory のマッピング データ フロー代理キー変換を使用して順次キー値を生成する方法
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387161"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>マッピング データ フローの代理キー変換



代理キー変換を使用すると、任意の非ビジネス キーの増分する値をデータ フローの行セットに追加できます。 これは、スター スキーマ分析データ モデルでディメンション テーブルを設計するときに便利です。このモデルでは、ディメンション テーブル内の各メンバーが、一意の非ビジネス キーを持つ必要があります (キンボールの DW 方法論の一環として)。

![代理キー変換](media/data-flow/surrogate.png "代理キー変換")

[キー列] は、新しい代理キー列に付ける名前です。

[開始値] は、増分する値の開始ポイントです。

## <a name="increment-keys-from-existing-sources"></a>既存のソースからの増分キー

ソース内に存在する値からシーケンスを開始する場合、代理キー変換の直後に派生列変換を使用して、2 つの値をまとめて追加することができます。

![SK での Max の追加](media/data-flow/sk006.png "代理キー変換での Max の追加")

以前の最大によってキー値をシードするには、次の 2 つの手法を使用できます。

### <a name="database-sources"></a>データベース ソース

"クエリ" オプションを使用し、ソース変換を利用してお使いのソースからの MAX() を選択します。

![代理キー クエリ](media/data-flow/sk002.png "代理キー変換のクエリ")

### <a name="file-sources"></a>ファイル ソース

以前の最大値がファイル内にある場合は、集計変換と共にソース変換を利用し、MAX() 式関数を使用して以前の最大値を取得できます。

![代理キー ファイル](media/data-flow/sk008.png "代理キー ファイル")

どちらの場合でも、以前の最大値を含むソースと受信する新しいデータを結合する必要があります。

![代理キー結合](media/data-flow/sk004.png "代理キー結合")

## <a name="next-steps"></a>次の手順

これらの例では、[結合](data-flow-join.md)と[派生列](data-flow-derived-column.md)の変換を使用します。
