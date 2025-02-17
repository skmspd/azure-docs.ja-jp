---
title: Azure Data Factory のマッピング データ フロー
description: Azure Data Factory のマッピング データ フローの概要
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/7/2019
ms.openlocfilehash: ed2502ffebbacf5e66e3e4738e2e88ce7fb8a562
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681555"
---
# <a name="what-are-mapping-data-flows"></a>マッピング データ フローとは

マッピング データ フローは、Azure Data Factory における視覚的に設計されたデータ変換です。 データ フローを使用すると、データ エンジニアは、コードを記述することなくグラフィカルなデータ変換ロジックを開発できます。 生成されたデータ フローは、スケールアウトされた Spark クラスターを使用する Azure Data Factory パイプライン内のアクティビティとして実行されます。 データ フロー アクティビティは、既存の Data Factory のスケジュール設定、制御、フロー、および監視機能を通して運用可能にすることができます。

マッピング データ フローは、コーディングを必要としない、完全に視覚的なエクスペリエンスを提供します。 データ フローは独自の実行クラスター上で実行されるため、データ処理をスケールアウトできます。 コードの翻訳、パスの最適化、データ フロー ジョブの実行はすべて、Azure Data Factory によって処理されます。

## <a name="getting-started"></a>使用の開始

データ フローを作成するには、 **[Factory Resources]\(Factory リソース\)** の下にあるプラス記号アイコンを選択して、 **[データ フロー]** を選択します。 

![新しいデータ フロー](media/data-flow/newdataflow2.png "新しいデータ フロー")

これにより、変換ロジックを作成できるデータ フロー キャンバスに移動します。 **[ソースの追加]** を選択すると、ソース変換の構成が開始します。 詳細については、[ソース変換](data-flow-source.md)に関するページを参照してください。

## <a name="data-flow-canvas"></a>データ フロー キャンバス

データ フロー キャンバスは、上部バー、グラフ、および構成パネルの 3 つの部分に分かれています。 

![キャンバス](media/data-flow/canvas1.png "キャンバス")

### <a name="graph"></a>Graph

グラフには変換ストリームが表示されます。 ここにはソース データが 1 つ以上のシンクに流れるときのソース データの系列が表示されます。 新しいソースを追加するには、 **[ソースの追加]** を選択します。 新しい変換を追加するには、既存の変換の右下にあるプラス記号を選択します。

![キャンバス](media/data-flow/canvas2.png "キャンバス")

### <a name="azure-integration-runtime-data-flow-properties"></a>Azure Integration Runtime のデータ フローのプロパティ

![デバッグ ボタン](media/data-flow/debugbutton.png "デバッグ ボタン")

ADF でデータ フローを初めて使う場合は、ブラウザー UI の上部にあるデータ フロー用 "デバッグ" スイッチをオンにすることをお勧めします。 これにより、対話型デバッグ、データ プレビュー、およびパイプライン デバッグ実行用の Azure Databricks クラスターが起動します。 使用するクラスターのサイズは、カスタム [Azure Integration Runtime](concepts-integration-runtime.md) を選択することで設定できます。 デバッグ セッションの有効期限は、最後のデータ プレビューまたは最後のデバッグ パイプライン実行から最長で 60 分間です。

データ フロー アクティビティが含まれるパイプラインを運用化した場合、ADF では、"実行対象" プロパティで[アクティビティ](control-flow-execute-data-flow-activity.md)と関連付けられている Azure Integration Runtime が使用されます。

既定の Azure Integration Runtime は、コア数 4、ワーカー ノード数 1 の小規模クラスターです。このクラスターは、最小限のコストで、データをプレビューし迅速にデバッグ パイプラインを実行するためのものです。 大規模なデータセットに対して操作を実行する場合は、より大きな Azure IR 構成を設定してください。

ADF でクラスター リソース (VM) のプールが維持されるようにするには、Azure IR データ フローのプロパティで TTL を設定します。 このようにすると、以降のアクティビティでのジョブの実行時間が短縮されます。

#### <a name="azure-integration-runtime-and-data-flow-strategies"></a>Azure Integration Runtime とデータ フローの戦略

##### <a name="execute-data-flows-in-parallel"></a>データ フローの並列実行

パイプライン内のデータ フローを並列実行する場合、ADF により、各アクティビティに関連付けられている Azure Integration Runtime の設定に基づいて、アクティビティの実行ごとに個別の Azure Databricks クラスターが起動されます。 ADF パイプラインで並列実行を設計するには、UI で優先順位制約のないデータ フロー アクティビティを追加します。

ここで紹介する 3 つの方法のうち、この方法は最も実行時間が短くなる可能性があります。 ただし、並列データ フローはそれぞれ別々のクラスター上で同時に実行されるので、イベントの順序は不確定になります。

##### <a name="overload-single-data-flow"></a>単一データ フローのオーバーロード

単一のデータ フロー内にすべてのロジックを配置すると、ADF により、すべて単一の Spark クラスター インスタンス上において同一のジョブ実行コンテキストで実行されます。

この方法ではビジネス ルールとビジネス ロジックが混ざり合ってしまうので、他の方法に比べて追跡やトラブルシューティングが困難になることがあります。 また、この方法には再利用可能性がほとんどありません。

##### <a name="execute-data-flows-serially"></a>データ フローの順次実行

パイプラインでデータ フロー アクティビティを順次実行し、かつ Azure IR 構成で TTL を設定している場合、ADF によって計算リソース (VM) が再利用されます。これにより、後続の実行時間を短縮できます。 この場合も、実行のたびに新しい Spark コンテキストを受け取ることになります。

ここで紹介する 3 つの方法のうち、この方法は全体的な実行時間が最も長くなる可能性があります。 ただし、各データ フロー ステップ内の論理操作を明確に分離することができます。

### <a name="configuration-panel"></a>構成パネル

構成パネルには、現在選択されている変換に固有の設定が表示されます。 変換が選択されていない場合は、データ フローが表示されます。 データ フロー全般の構成では、 **[General]\(全般\)** タブで名前および説明を編集したり、 **[Parameters]\(パラメーター\)** タブを使用してパラメーターを追加したりできます。詳しくは、「[マッピング データ フローのパラメーター](parameters-data-flow.md)」を参照してください。

各変換には、少なくとも 4 つの構成タブがあります。

#### <a name="transformation-settings"></a>変換設定

各変換の構成ウィンドウの最初のタブには、その変換に固有の設定が含まれています。 詳しくは、各変換のドキュメント ページを参照してください。

![[ソースの設定] タブ](media/data-flow/source1.png "[ソースの設定] タブ")

#### <a name="optimize"></a>最適化

**[最適化]** タブには、パーティション分割を構成するためのオプション設定が含まれています。

![最適化](media/data-flow/optimize1.png "最適化")

既定の設定は、 **[Use current partitioning]\(現在のパーティション分割を使用\)** で、この場合 Azure Data Factory では、Spark 上で実行されているデータ フローにネイティブのパーティション構成が使用されます。 ほとんどのシナリオでは、この設定が推奨されます。

ただし、パーティション分割の調整が必要な場合もあります。 たとえば、変換をレイク内の 1 つのファイルに出力する場合は、シンク変換で **[単一パーティション]** を選択します。

パーティション構成の制御が必要になるもう 1 つのケースは、パフォーマンスを最適化する場合です。 パーティション分割を調整すると、全体的なデータ フローのパフォーマンスに好影響を与えることも、悪影響も与えることもある、各計算ノードへのデータの分散とデータの局所性の最適化を制御できます。 詳細については、[データ フローのパフォーマンス ガイド](concepts-data-flow-performance.md)に関するページを参照してください。

いずれかの変換でパーティション分割を変更する場合は、 **[最適化]** タブを選択し、 **[Set Partitioning]\(パーティションの設定\)** を選択します。 パーティション分割用の一連のオプションが表示されます。 パーティション分割の最適な方法は、データ ボリューム、候補キー、null 値、およびカーディナリティに応じて異なります。 

ベスト プラクティスは、既定のパーティション分割から開始して、その後、別のパーティション分割のオプションを試す方法です。 パイプラインのデバッグ実行を使用してテストし、監視ビューで各変換グループの実行時間およびパーティションの使用状況を確認することができます。 詳しくは、[データ フローの監視](concepts-data-flow-monitoring.md)に関するページを参照してください。

利用可能なパーティション分割オプションは次のとおりです。

##### <a name="round-robin"></a>ラウンド ロビン 

ラウンド ロビンは、自動的に各パーティションにデータを均等に分散するシンプルなパーティションです。 堅固でスマートなパーティション分割戦略を実装するための適切な候補がないときは、ラウンド ロビンを使用します。 物理パーティションの数を設定できます。

##### <a name="hash"></a>Hash

Azure Data Factory は、同様の値を持つ行が同じパーティション内に分類されるように、列のハッシュを生成して統一されたパーティションを生成します。 [ハッシュ] オプションを使用するときは、起こり得るパーティションのスキューについてテストします。 物理パーティションの数を設定できます。

##### <a name="dynamic-range"></a>動的範囲

動的範囲では、指定した列または式に基づく Spark の動的範囲が使用されます。 物理パーティションの数を設定できます。 

##### <a name="fixed-range"></a>固定範囲

パーティション分割されたデータ列内の値に対する固定の範囲を提供する式を作成します。 パーティションのスキューを避けるため、このオプションを使用する際は、自分のデータについて十分に理解する必要があります。 式に入力する値は、パーティション関数の一部として使用されます。 物理パーティションの数を設定できます。

##### <a name="key"></a>Key

データのカーディナリティを十分に理解している場合は、キー パーティション分割が適切な戦略になるでしょう。 キー パーティション分割では、列内の一意の値ごとにパーティションが作成されます。 パーティションの数は、データ内の一意の値に基づくため、設定することはできません。

#### <a name="inspect"></a>検査

**[Inspect]\(検査\)** タブには、変換するデータ ストリームのメタデータのビューが表示されます。 列数、変更された列、追加された列、データ型、列の順序、および列の参照を確認できます。 **[Inspect]\(検査\)** は、メタデータの読み取り専用ビューです。 **[Inspect]\(検査\)** ペインでメタデータを表示するためにデバッグ モードを有効にする必要はありません。

![検査](media/data-flow/inspect1.png "検査")

変換を使ってデータの形状を変更すると、メタデータの変更が **[Inspect]\(検査\)** ペインに反映されます。 ソースの変換に定義済みのスキーマがない場合、メタデータは **[Inspect]\(検査\)** ペインに表示されません。 スキーマの誤差シナリオでは、メタデータがないことは一般的です。

#### <a name="data-preview"></a>データのプレビュー

デバッグ モードがオンの場合、 **[データのプレビュー]** タブには、各変換のデータの対話型スナップショットが表示されます。 詳細については、[デバッグ モードでのデータのプレビュー](concepts-data-flow-debug-mode.md#data-preview)に関するセクションを参照してください。

### <a name="top-bar"></a>上部バー

上部バーには、保存や検証など、データ フロー全体に影響を与えるアクションが含まれています。 **[グラフの表示]** および **[グラフの非表示]** ボタンを使用して、グラフ モードと構成モードを切り替えることもできます。

![グラフの非表示](media/data-flow/hideg.png "Hide graph (グラフを非表示にする)")

グラフを非表示にした場合は、 **[前へ]** ボタンと **[次へ]** ボタンを使用して、変換ノード間を横方向へ移動することができます。

![[前へ] ボタンと [次へ] ボタン](media/data-flow/showhide.png "[前へ] ボタンと [次へ] ボタン")

## <a name="next-steps"></a>次の手順

* [ソース変換](data-flow-source.md)を作成する方法について学習します。
* データ フローを[デバッグ モード](concepts-data-flow-debug-mode.md)で構築する方法について学習します。
