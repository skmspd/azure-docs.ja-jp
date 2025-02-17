---
title: Azure Data Factory のマッピング データ フロー機能のシンク変換を設定する
description: マッピング データ フローのシンク変換を設定する方法について説明します。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 7cfe0cf291e8c39a4600234632090c39ab5cd78e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73519330"
---
# <a name="sink-transformation-for-a-data-flow"></a>データ フローのシンク変換

データ フローを変換した後、データを変換先のデータセットにシンクできます。 シンク変換では、変換先の出力データのデータセット定義を選択します。 データ フローに必要な数だけシンク変換を設定できます。

スキーマの誤差や受信データの変更に対処するには、出力データセットに定義済みのスキーマがない状態で出力データをフォルダーにシンクします。 ソースで **[Allow schema drift]** (スキーマの誤差を許可) を選択することで、ソース内の列変更に対処することもできます。 この場合、シンク内のすべてのフィールドが自動マッピングされます。

![[Auto Map]\(自動マップ) オプションを含む [シンク] タブのオプション](media/data-flow/sink1.png "シンク 1")

すべての受信フィールドをシンクするには、 **[Auto Map]** (自動マップ) をオンにします。 変換先にシンクするフィールドを選択したり、変換先でのフィールドの名前を変更するには、 **[Auto Map]** (自動マップ) をオフにします。 続いて **[マッピング]** タブを開いて、出力フィールドをマップします。

![[マッピング] タブのオプション](media/data-flow/sink2.png "シンク 2")

## <a name="output"></a>Output 
Azure BLOB ストレージまたは Data Lake Storage のシンクの種類の場合、変換されたデータをフォルダーに出力します。 Spark は、シンク変換で使用されるパーティション分割スキームに基づいて、パーティション分割された出力データ ファイルを生成します。 

このパーティション分割スキームは、 **[最適化]** タブから設定できます。Data Factory で 1 つのファイルに出力をマージさせる場合は、 **[単一パーティション]** を選択します。

![[最適化] タブのオプション](media/data-flow/opt001.png "シンク オプション")

## <a name="field-mapping"></a>フィールドのマッピング
シンク変換の **[マッピング]** タブでは、左側の受信の列を右側の変換先にマップできます。 データ フローをファイルにシンクした場合、Data Factory は常に、新しいファイルをフォルダーに書き込みます。 データベース データセットにマップする場合は、挿入、更新、upsert、または削除するデータベース テーブル操作のオプションを選択します。

![[マッピング] タブ](media/data-flow/sink2.png "シンク")

マッピング テーブルでは、複数選択を行って、複数の列をリンクしたり、複数の列のリンクを削除したり、複数の行を同じ列名にマップしたりできます。

常に受信フィールド セットをターゲットにマップしておき、柔軟なスキーマ定義を完全に受け入れるには、 **[Allow schema drift]** (スキーマの誤差を許可) を選択します。

![[マッピング] タブ、データセット内の列にマップされたフィールドを表示](media/data-flow/multi1.png "複数のオプション")

列マッピングをリセットするには、 **[Re-map]** (再マップ) を選択します。

![[シンク] タブ](media/data-flow/sink1.png "シンク 1")

スキーマが変更された場合にシンクを失敗させるには、 **[スキーマの検証]** を選択します。

変換先ファイルをターゲット フォルダーに書き込む前に、そのシンク フォルダーの内容を切り捨てるには、 **[Clear the folder]** (フォルダーのクリア) を選択します。

## <a name="fixed-mapping-vs-rule-based-mapping"></a>固定マッピングとルールベースのマッピング
自動マッピングを無効にすると、列ベースのマッピング (固定マッピング) とルール ベースのマッピングのいずれかを追加するオプションを利用できます。 ルール ベースのマッピングでは、パターン マッチングを使用した式を作成でき、固定マッピングでは、論理列名と物理列名がマップされます。

![ルールベースのマッピング](media/data-flow/rules4.png "ルールベースのマッピング")

ルールベースのマッピングを選択することは、ADF に対して、受信パターン ルールに一致するように一致式を評価し、送信フィールド名を定義するように指示することになります。 フィールドとルールベースの両方のマッピングの任意の組み合わせを追加することもできます。 フィールド名は、ソースからの受信メタデータに基づいて、実行時に ADF によって生成されます。 生成されたフィールドの名前は、デバッグ中に表示することも、[データ プレビュー] ウィンドウを使用して表示することもできます。

パターン マッチングの詳細については、[列パターンに関するドキュメント](concepts-data-flow-column-pattern.md)を参照してください。

また、行を展開し、[名前の一致:] の横に正規表現を入力することで、ルールベースのマッチングを使用するときに正規表現パターンを入力することもできます。

![正規表現マッピング](media/data-flow/scdt1g4.png "正規表現マッピング")

ルールベースのマッピングと固定マッピングのごく基本的な例として、すべての入力フィールドをターゲットの同じ名前にマップする場合があります。 固定マッピングの場合、テーブルに個々の列を一覧表示することがあります。 ルールベースのマッピングでは、```true()``` を使用して、すべてのフィールドを ```$$```で表される同じ入力フィールド名にマップする 1 つのルールを使用する場合があります。

### <a name="sink-association-with-dataset"></a>シンクとデータセットの関連付け

シンク用に選択したデータセットには、データセット定義にスキーマが定義されている場合と、定義されていない場合があります。 スキーマが定義されていない場合、スキーマの誤差を許可する必要があります。 固定マッピングを定義した場合、シンク変換で論理名と物理名のマッピングが保持されます。 データセットのスキーマ定義を変更すると、シンク マッピングが壊れる可能性があります。 これを回避するには、ルールベースのマッピングを使用します。 ルールベースのマッピングは一般化されているため、データセットのスキーマ変更によってマッピングが壊れることはありません。

## <a name="file-name-options"></a>ファイル名のオプション

ファイルの名前付けを設定します。 

   * **既定**:Spark は PART の既定値に基づいて、ファイルに名前を付けることができます。
   * **パターン**:出力ファイルのパターンを入力します。 たとえば、**loans[n]** と入力すると、loans1.csv、loans2.csv のように作成されます。
   * **Per partition**(パーティションあたり):パーティションごとに 1 つのファイル名を入力します。
   * **As data in column**(列内のデータとして):出力ファイルを列の値に設定します。
   * **Output to a single file**(1 つのファイルに出力する):このオプションを使用すると、ADF は、パーティション分割された出力ファイルを単一の名前付きファイルに結合します。 このオプションを使用するには、データセットは、フォルダー名に解決する必要があります。 また、このマージ操作はノード サイズに基づいて失敗する可能性があることに注意してください。

> [!NOTE]
> Data Flow の実行アクティビティを実行している場合にのみ、ファイル操作は開始します。 これらは、Data Flow のデバッグ モードでは開始しません。

## <a name="database-options"></a>データベース オプション

データベース設定を選択します。

![SQL シンクのオプションを示す [設定] タブ](media/data-flow/alter-row2.png "SQL オプション")

* **Update method**(更新方法):既定では、挿入を許可します。 ソースからの新しい行の挿入を停止させる場合は、 **[Allow insert]** (挿入の許可) をオフにします。 行を更新、アップサート、または削除するには、最初に、それらのアクションに対して行をタグ付けするための行の変更変換を追加します。 
* **Recreate table**(テーブルの再作成):データ フローが完了する前に、対象のテーブルを破棄したり作成します。
* **[Truncate table]** (テーブルの切り詰め):データ フローが完了する前に、対象のテーブルからすべて行を削除します。
* **バッチ サイズ**: 書き込みをチャンクにまとめる数値を入力します。 大量のデータの読み込みにこのオプションを使用します。 
* **Enable staging**(ステージングの有効化):シンク データセットとして Azure Data Warehouse を読み込むときに、PolyBase を使用します。
* **[Pre and Post SQL scripts] (事前および事後 SQL スクリプト)** : データがシンク データベースに書き込まれる前 (前処理) と書き込まれた後 (後処理) に実行される複数行の SQL スクリプトを入力します。

![事前および事後 SQL 処理スクリプト](media/data-flow/prepost1.png "SQL 処理スクリプト")

> [!NOTE]
> Data Flow で、ターゲット データベースに新しいテーブル定義を作成するように Data Factory に指示できます。 テーブル定義を作成するには、新しいテーブル名を持つシンク変換でデータセットを設定します。 SQL データセットのテーブル名の下で、 **[編集]** をクリックして新しいテーブル名を入力します。 次に、シンク変換で、 **[Allow schema drift]** (スキーマの誤差を許可) をオンにします。 **[スキーマのインポート]** を **[なし]** に設定します。

![テーブル名を編集する場所を示した、SQL データセット設定](media/data-flow/dataset2.png "SQL スキーマ")

> [!NOTE]
> データベース シンク内の行を更新または削除するときに、キー列を設定する必要があります。 この設定は、行の変更変換で、データ移動ライブラリ (DML) の一意の行を決定できるようにします。

### <a name="cosmosdb-specific-settings"></a>CosmosDB 固有の設定

CosmosDB にデータを配置する場合、次の追加オプションを検討する必要があります。

* パーティション キー:これは必須フィールドです。 コレクションのパーティション キーを表す文字列を入力します。 例: ```/movies/title```
* スループット: このデータ フローの実行ごとに CosmosDB コレクションに適用する RU の数のオプション値を設定します。 最小は 400 です。

## <a name="next-steps"></a>次の手順
これでデータ フローが作成されたので、[Data Flow アクティビティをパイプラインに](concepts-data-flow-overview.md)追加します。
