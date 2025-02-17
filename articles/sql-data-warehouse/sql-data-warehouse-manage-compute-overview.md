---
title: コンピューティング リソースの管理
description: Azure SQL Data Warehouse のパフォーマンス スケールアウト機能について説明します。 DWU を調整してスケールアウトしたり、データ ウェアハウスを一時停止してコストを削減したりします。
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 11/12/2019
ms.author: kevin
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 936d92d085420e1386e29a924470b9bac9200d43
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039097"
---
# <a name="manage-compute-in-azure-sql-data-warehouse"></a>Azure SQL Data Warehouse でのコンピューティングの管理
Azure SQL Data Warehouse でコンピューティング リソースを管理する方法について説明します。 また、データ ウェアハウスを一時停止してコストを削減したり、データ ウェアハウスをパフォーマンス需要に応じてスケーリングしたりする方法についても説明します。 

## <a name="what-is-compute-management"></a>コンピューティングの管理とは
SQL Data Warehouse のアーキテクチャではストレージとコンピューティングを分離して、それぞれを個別にスケーリングできます。 その結果、データ ストレージとは無関係に、パフォーマンス需要に応じてコンピューティングをスケーリングできます。 コンピューティング リソースは、一時停止して再開することもできます。 このアーキテクチャでは、当然、コンピューティングとストレージに対する[課金](https://azure.microsoft.com/pricing/details/sql-data-warehouse/)は別々に行われます。 データ ウェアハウスをしばらく使用する必要がない場合は、コンピューティングを一時停止して、コンピューティング コストを節約できます。 

## <a name="scaling-compute"></a>コンピューティングのスケーリング
コンピューティングをスケールアウトまたはスケールバックするには、データ ウェアハウスの[データ ウェアハウス ユニット](what-is-a-data-warehouse-unit-dwu-cdwu.md)設定を調整します。 データ ウェアハウス ユニットを追加していくと、読み込みとクエリのパフォーマンスが直線的に増加していきます。 

スケールアウトの手順については、[Azure Portal](quickstart-scale-compute-portal.md)、[PowerShell](quickstart-scale-compute-powershell.md)、または [T-SQL](quickstart-scale-compute-tsql.md) のクイックスタートに関する記事を参照してください。 [REST API](sql-data-warehouse-manage-compute-rest-api.md#scale-compute) を使用して、スケールアウト操作を実行することもできます。

スケール操作を実行する場合、SQL Data Warehouse は、最初にすべての受信クエリを中止し、次にトランザクションをロールバックして一貫性のある状態を確保します。 スケーリングは、トランザクションのロールバックが完了して初めて実行されます。 スケール操作では、コンピューティング ノードからストレージ レイヤーがデタッチされ、コンピューティング ノードが追加され、ストレージ レイヤーがコンピューティング レイヤーに再アタッチされます。 各データ ウェアハウスは、60 のディストリビューションとして保存され、これがコンピューティング ノードに均等に分配されます。 コンピューティング ノードを追加していくと、コンピューティング能力も向上していきます。 コンピューティング ノードの数が増加すると、それにつれてコンピューティング ノードあたりのディストリビューションの数が減少し、クエリ用のコンピューティング能力がより多く得られます。 同様に、データ ウェアハウス ユニットを減らすと、コンピューティング ノードの数が減少し、クエリ用のコンピューティング リソースが減ります。

次の表は、データ ウェアハウス ユニットが変化すると、コンピューティング ノードあたりのディストリビューションの数がどのように変化するかを示しています。  DWU6000 は、60 のコンピューティング ノードを提供し、DWU100 よりはるかに高いクエリ パフォーマンスを達成します。 

| データ ウェアハウス ユニット  | \# コンピューティング ノードの数 | \#ノードあたりのディストリビューションの数 |
| -------- | ---------------- | -------------------------- |
| DW100c   | 1                | 60                         |
| DW200c   | 1                | 60                         |
| DW300c   | 1                | 60                         |
| DW400c   | 1                | 60                         |
| DW500c   | 1                | 60                         |
| DW1000c  | 2                | 30                         |
| DW1500c  | 3                | 20                         |
| DW2000c  | 4                | 15                         |
| DW2500c  | 5                | 12                         |
| DW3000c  | 6                | 10                         |
| DW5000c  | 10               | 6                          |
| DW6000c  | 12               | 5                          |
| DW7500c  | 15               | 4                          |
| DW10000c | 20               | 3                          |
| DW15000c | 30               | 2                          |
| DW30000c | 60               | 1                          |


## <a name="finding-the-right-size-of-data-warehouse-units"></a>データ ウェアハウス ユニットの適正サイズの確認

スケールアウトのパフォーマンス上のメリット (特に、大規模なデータ ウェアハウス ユニットのスケールアウトのパフォーマンス上の メリット) を確認するには、少なくとも 1 TB のデータ セットを使用する必要があります。 データ ウェアハウスの最適なデータ ウェアハウス ユニット数を確認するには、スケールアップとスケールダウンを試します。 データを読み込んだ後、さまざまなデータ ウェアハウス ユニット数でいくつかのクエリを実行します。 スケーリングは簡単に行えるので、1 時間以内でさまざまなパフォーマンス レベルを試すことができます。 

最適なデータ ウェアハウス ユニット数を確認する際の推奨事項を以下に示します。

- 開発中のデータ ウェアハウスの場合は、少ない数のデータ ウェアハウス ユニットを選択することから始めます。  手始めとしては、DW400 または DW200 が適しています。
- アプリケーションのパフォーマンスを監視し、選択したデータ ウェアハウス ユニットの数に対するパフォーマンスの変化を観察します。
- 線形スケールを想定し、データ ウェアハウス ユニットをどれだけ増減する必要があるかを確認します。 
- ビジネス要件に応じた最適なパフォーマンス レベルに到達するまで調整を行います。

## <a name="when-to-scale-out"></a>スケールアウトを実行するタイミング
データ ウェアハウス ユニットをスケールアウトすると、次のパフォーマンスに影響があります。

- スキャン、集計、CTAS ステートメントに関するシステムのパフォーマンスが直線的に向上します。
- データ読み込み用のリーダーとライターの数が増えます。
- コンカレント クエリとコンカレンシー スロットの最大数。

データ ウェアハウス ユニットをスケールアウトするタイミングについての推奨事項を以下に示します。

- 大量データの読み込みまたは変換操作を実行する前に、データが短時間で使用可能になるようにスケールアウトします。
- 営業時間のピーク時は、より多くの同時実行クエリに対応できるようにスケールアウトします。 

## <a name="what-if-scaling-out-does-not-improve-performance"></a>スケール アウトしてもパフォーマンスが向上しない場合の対処

データ ウェアハウス ユニットを追加すると、並列性が増加します。 作業がコンピューティング ノード間で均等に分割されている場合、並列性を追加すると、クエリのパフォーマンスが向上します。 スケール アウトしてもパフォーマンスが変化しない場合は、その理由がいくつか存在します。 ディストリビューション全体でデータが傾斜しているか、クエリによって大量のデータ移動が発生している可能性があります。 クエリのパフォーマンスの問題を調査するには、[パフォーマンスのトラブルシューティングに関する記事](sql-data-warehouse-troubleshoot.md#performance)を参照してください。 

## <a name="pausing-and-resuming-compute"></a>コンピューティングの一時停止と再開
コンピューティングを一時停止すると、ストレージ レイヤーがコンピューティング ノードからデタッチされます。 コンピューティング リソースは、アカウントからリリースされます。 コンピューティングが一時停止中は、コンピューティングに対する課金はありません。 コンピューティングを再開すると、ストレージがコンピューティング ノードに再アタッチされ、コンピューティングの課金が再開されます。 データ ウェアハウスを一時停止すると、以下のようになります。

* コンピューティング リソースとメモリ リソースは、データ センターで使用可能なリソースのプールに返されます。
* 一時停止の期間中、データ ウェアハウス ユニットのコストは 0 になります。
* データ ストレージは影響を受けず、データはそのまま残ります。 
* SQL Data Warehouse では、実行中またはキューに格納されたすべての操作を取り消します。

データ ウェアハウスを再開すると、以下のようになります。

* SQL Data Warehouse がデータ ウェアハウス ユニット設定のコンピューティング リソースとメモリ リソースを取得します。
* データ ウェアハウス ユニットのコンピューティングの課金が再開されます。
* データが使用可能になります。
* データ ウェアハウスがオンラインになった後、ワークロード クエリを再開する必要があります。

常にデータ ウェアハウスにアクセスできることが必要な場合は、一時停止ではなく、最小サイズへのスケールダウンを検討してください。 

一時停止と再開の手順については、[Azure Portal](pause-and-resume-compute-portal.md) または [PowerShell](pause-and-resume-compute-powershell.md) のクイックスタートに関する記事を参照してください。 [一時停止 REST API](sql-data-warehouse-manage-compute-rest-api.md#pause-compute) または [再開 REST API](sql-data-warehouse-manage-compute-rest-api.md#resume-compute) を使用することもできます。

## <a name="drain-transactions-before-pausing-or-scaling"></a>一時停止またはスケールの前にトランザクションを排出する
一時停止操作またはスケール操作を開始する前に、既存のトランザクションを完了することをお勧めします。

SQL Data Warehouse を一時停止またはスケールすると、要求の一時停止またはスケールを開始したときに、バックグラウンドでクエリが取り消されます。  単純な SELECT クエリの取り消しは、短時間で終わる処理であるため、インスタンスを一時停止またはスケールするのにかかる時間にほとんど影響しません。  ただし、データやデータ構造を変更するトランザクション クエリは、すぐに停止できない場合があります。  **トランザクション クエリについては、当然、すべてが完了するか、変更をロールバックする必要があります。**  トランザクション クエリが完了した作業をロールバックするには、クエリが元の変更の適用にかかった時間と同じか、それよりも長くかかる場合があります。  たとえば、行の削除を既に 1 時間実行しているクエリを取り消した場合、削除された行を挿入し直すのに 1 時間かかる可能性があります。  トランザクションの実行中に一時停止またはスケールを実行した場合、一時停止またはスケールするには、ロールバックが完了するのを待機する必要があるため、時間がかかることがあります。

[トランザクションの概要](sql-data-warehouse-develop-transactions.md)と[トランザクションの最適化](sql-data-warehouse-develop-best-practices-transactions.md)に関するページも参照してください。

## <a name="automating-compute-management"></a>コンピューティングの管理の自動化
コンピューティングの管理操作を自動化するには、[Azure の機能を使用したコンピューティングの管理に関する記事](manage-compute-with-azure-functions.md)を参照してください。

スケール アウト、一時停止、再開の各操作は、完了するのに数分かかる場合があります。 スケーリング、一時停止、または再開を自動化する場合は、別のアクションに進む前に特定の操作を確実に完了させるロジックを実装することをお勧めします。 さまざまなエンドポイントを通じてデータ ウェアハウスの状態を確認することで、このような操作の自動化を適切に実装できます。 

データ ウェアハウスの状態を確認するには、 [PowerShell](quickstart-scale-compute-powershell.md#check-data-warehouse-state) または [T-SQL](quickstart-scale-compute-tsql.md#check-data-warehouse-state) のクイックスタートに関する記事を参照してください。 [REST API](sql-data-warehouse-manage-compute-rest-api.md#check-database-state) を使用して、データ ウェアハウスの状態を確認することもできます。


## <a name="permissions"></a>アクセス許可

データ ウェアハウスをスケーリングするには、[「ALTER DATABASE」(データベースの変更)](/sql/t-sql/statements/alter-database-azure-sql-data-warehouse) で説明されているアクセス許可が必要です。  一時停止と再開には、[SQL DB Contributor](../role-based-access-control/built-in-roles.md#sql-db-contributor) のアクセス許可、具体的には Microsoft.Sql/servers/databases/action が必要です。


## <a name="next-steps"></a>次の手順
[コンピューティングの管理](manage-compute-with-azure-functions.md)については、ハウツー ガイドを参照してください。コンピューティング リソースの管理の別の側面として、個々のクエリへの異なるコンピューティング リソースの割り当てがあります。 詳細については、[「ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」を参照してください。
