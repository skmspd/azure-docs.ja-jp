---
title: DMV を利用してワークロードを監視する
description: DMV を利用してワークロードを監視するについて説明します。
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 08/23/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 629ba904d055977fe70f749a46fbbec71be71b79
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083647"
---
# <a name="monitor-your-workload-using-dmvs"></a>DMV を利用してワークロードを監視する
この記事では、動的管理ビュー (DMV) を使用してワークロードを監視する方法について説明します。 Azure SQL Data Warehouse でのクエリの実行の調査が含まれます。

## <a name="permissions"></a>アクセス許可
この記事で DMV をクエリするには、VIEW DATABASE STATE または CONTROL のいずれかのアクセス許可が必要です。 通常は、さらに制限の厳しい VIEW DATABASE STATE のアクセス許可を付与することが推奨されます。

```sql
GRANT VIEW DATABASE STATE TO myuser;
```

## <a name="monitor-connections"></a>接続を監視する
SQL Data Warehouse へのすべてのログインは、[sys.dm_pdw_exec_sessions][sys.dm_pdw_exec_sessions] に記録されます。  この DMV には、過去 10,000 件のログインが含まれています。  プライマリ キーである session_id が、新規ログオンのたびに順次割り当てられます。

```sql
-- Other Active Connections
SELECT * FROM sys.dm_pdw_exec_sessions where status <> 'Closed' and session_id <> session_id();
```

## <a name="monitor-query-execution"></a>クエリ実行を監視する
SQL Data Warehouse で実行されるすべてのクエリは、[sys.dm_pdw_exec_requests][sys.dm_pdw_exec_requests] に記録されます。  この DMV には、実行された過去 10,000 件のクエリが含まれています。  request_id により各クエリが一意に識別されます。これはこの DMV のプライマリ キーです。  request_id は、新しいクエリごとに順番に割り当てられ、クエリ ID を表す QID がプレフィックスとして付加されます。  特定の session_id のこの DMV にクエリを実行すると、そのログオンのクエリがすべて表示されます。

> [!NOTE]
> ストアド プロシージャでは複数の要求 ID が使用されます。  要求 ID は順次割り当てられます。 
> 
> 

クエリ実行プラン、および特定のクエリの実行時間を調査するには、次の手順に従います。

### <a name="step-1-identify-the-query-you-wish-to-investigate"></a>手順 1: 調査するクエリを特定する
```sql
-- Monitor active queries
SELECT * 
FROM sys.dm_pdw_exec_requests 
WHERE status not in ('Completed','Failed','Cancelled')
  AND session_id <> session_id()
ORDER BY submit_time DESC;

-- Find top 10 queries longest running queries
SELECT TOP 10 * 
FROM sys.dm_pdw_exec_requests 
ORDER BY total_elapsed_time DESC;

```

上記のクエリ結果から、調査するクエリの **要求 ID を書き留めます** 。

アクティブな実行中のクエリが多数あるため、**中断**状態のクエリをキューに入れることができます。 これらのクエリは、UserConcurrencyResourceType 型の [sys.dm_pdw_waits](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql) 待機クエリにも表示されます。 コンカレンシーの制限に関する情報については、[パフォーマンス レベル](/azure/sql-data-warehouse/what-is-a-data-warehouse-unit-dwu-cdwu#performance-tiers-and-data-warehouse-units)に関する記事、または「[ワークロード管理用のリソース クラス](resource-classes-for-workload-management.md)」を参照してください。 クエリの待機は、オブジェクト ロックなど、他の理由によっても発生します。  クエリがリソースを待っている場合は、この記事の下にある [リソースを待機しているクエリの調査][Investigating queries waiting for resources] に関するトピックをご覧ください。

[sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql) テーブル内のクエリの検索を簡素化するには、[LABEL][LABEL] を使用して、sys.dm_pdw_exec_requests ビューで検索できるクエリにコメントを割り当てます。

```sql
-- Query with Label
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query')
;

-- Find a query with the Label 'My Query'
-- Use brackets when querying the label column, as it it a key word
SELECT  *
FROM    sys.dm_pdw_exec_requests
WHERE   [label] = 'My Query';
```

### <a name="step-2-investigate-the-query-plan"></a>手順 2: クエリ プランを調査する
要求 ID を使用して、[sys.dm_pdw_request_steps][sys.dm_pdw_request_steps] からクエリの分散 SQL (DSQL) プランを取得します。

```sql
-- Find the distributed query plan steps for a specific query.
-- Replace request_id with value from Step 1.

SELECT * FROM sys.dm_pdw_request_steps
WHERE request_id = 'QID####'
ORDER BY step_index;
```

DSQL プランに予想よりも時間がかかる場合は、多くの DSQL 手順が存在する複雑なプランであったり、1 つの手順に長い時間を要することが原因であったりする可能性があります。  プランにいくつかの移動操作を含む多くの手順が存在する場合は、テーブルのディストリビューションを最適化して、データの移動を削減することを検討してください。 [テーブルのディストリビューション][Table distribution] に関する記事では、データを移動してクエリを解決する必要がある理由と、データの移動を最小限に抑えるためのいくつかのディストリビューションの方法について説明します。

1 つの手順に関する詳細を調査するには、実行時間の長いクエリ手順の *operation_type* 列を確認し、**手順インデックス**を書き留めます。

* 手順 3a の **SQL 操作** に進みます (OnOperation、RemoteOperation、ReturnOperation)。
* 手順 3b の**データ移動操作** に進みます (ShuffleMoveOperation、BroadcastMoveOperation、TrimMoveOperation、PartitionMoveOperation、MoveOperation、CopyOperation)。

### <a name="step-3a-investigate-sql-on-the-distributed-databases"></a>手順 3a: 分散データベースでの SQL を調査する
要求 ID と手順インデックスを使用して、[sys.dm_pdw_sql_requests][sys.dm_pdw_sql_requests] から詳細情報を取得します。これには、配布されたすべてのデータベースに対するクエリの実行情報が含まれます。

```sql
-- Find the distribution run times for a SQL step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_sql_requests
WHERE request_id = 'QID####' AND step_index = 2;
```

クエリ手順が実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定の配布で実行中の手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server execution plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(1, 78);
```

### <a name="step-3b-investigate-data-movement-on-the-distributed-databases"></a>手順 3 b: 分散データベース上のデータ移動を調査する
要求 ID と手順インデックスを利用し、[sys.dm_pdw_dms_workers][sys.dm_pdw_dms_workers] から各配布で実行されているデータ移動手順に関する情報を取得します。

```sql
-- Find the information about all the workers completing a Data Movement Step.
-- Replace request_id and step_index with values from Step 1 and 3.

SELECT * FROM sys.dm_pdw_dms_workers
WHERE request_id = 'QID####' AND step_index = 2;
```

* *total_elapsed_time* 列で、特定の配布で他の配布よりデータ移動に大幅に時間がかかっていないか確認します。
* 実行時間の長い配布に対して、*rows_processed* 列で、その配布から移動された行の数が他の配布より大幅に大きいか確認します。 大きい場合、個の検出は、基になるデータの傾斜を示していることがあります。

クエリが実行中の場合は、[DBCC PDW_SHOWEXECUTIONPLAN][DBCC PDW_SHOWEXECUTIONPLAN] を使用して、特定の配布内で現在実行中の SQL 手順に対する SQL Server プラン キャッシュから、SQL Server 推定プランを取得できます。

```sql
-- Find the SQL Server estimated plan for a query running on a specific SQL Data Warehouse Compute or Control node.
-- Replace distribution_id and spid with values from previous query.

DBCC PDW_SHOWEXECUTIONPLAN(55, 238);
```

<a name="waiting"></a>

## <a name="monitor-waiting-queries"></a>待機クエリを監視する
クエリがリソースを待っていることが原因で進行状況に変化がないことがわかった場合は、次のクエリを使用すると、待機対象のすべてのリソースが表示されます。

```sql
-- Find queries 
-- Replace request_id with value from Step 1.

SELECT waits.session_id,
      waits.request_id,  
      requests.command,
      requests.status,
      requests.start_time,  
      waits.type,
      waits.state,
      waits.object_type,
      waits.object_name
FROM   sys.dm_pdw_waits waits
   JOIN  sys.dm_pdw_exec_requests requests
   ON waits.request_id=requests.request_id
WHERE waits.request_id = 'QID####'
ORDER BY waits.object_name, waits.object_type, waits.state;
```

クエリが別のクエリからのリソースを積極的に待っている場合、状態は **AcquireResources**になります。  クエリに必要なリソースがすべて揃っている場合、状態は **Granted**になります。

## <a name="monitor-tempdb"></a>tempdb を監視する
tempdb は、クエリ実行中に中間結果を保持するために使用されます。 tempdb データベースの使用率が高いと、クエリのパフォーマンスが低下する可能性があります。 Azure SQL Data Warehouse の各ノードには、tempdb 用の約 1 TB の生の領域があります。 tempdb の使用状況を監視するためのヒントと、クエリでの tempdb 使用率を減らすためのヒントを以下に示します。 

### <a name="monitoring-tempdb-with-views"></a>ビューでの tempdb の監視
tempdb の使用状況を監視するには、まず、「[Microsoft Toolkit for SQL Data Warehouse](https://github.com/Microsoft/sql-data-warehouse-samples/tree/master/solutions/monitoring)」(SQL Data Warehouse 向け Microsoft Toolkit) から [microsoft.vw_sql_requests](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/solutions/monitoring/scripts/views/microsoft.vw_sql_requests.sql) ビューをインストールします。 その後、次のクエリを実行し、実行したすべてのクエリのノードごとの tempdb 使用状況を確認することができます。

```sql
-- Monitor tempdb
SELECT
    sr.request_id,
    ssu.session_id,
    ssu.pdw_node_id,
    sr.command,
    sr.total_elapsed_time,
    es.login_name AS 'LoginName',
    DB_NAME(ssu.database_id) AS 'DatabaseName',
    (es.memory_usage * 8) AS 'MemoryUsage (in KB)',
    (ssu.user_objects_alloc_page_count * 8) AS 'Space Allocated For User Objects (in KB)',
    (ssu.user_objects_dealloc_page_count * 8) AS 'Space Deallocated For User Objects (in KB)',
    (ssu.internal_objects_alloc_page_count * 8) AS 'Space Allocated For Internal Objects (in KB)',
    (ssu.internal_objects_dealloc_page_count * 8) AS 'Space Deallocated For Internal Objects (in KB)',
    CASE es.is_user_process
    WHEN 1 THEN 'User Session'
    WHEN 0 THEN 'System Session'
    END AS 'SessionType',
    es.row_count AS 'RowCount'
FROM sys.dm_pdw_nodes_db_session_space_usage AS ssu
    INNER JOIN sys.dm_pdw_nodes_exec_sessions AS es ON ssu.session_id = es.session_id AND ssu.pdw_node_id = es.pdw_node_id
    INNER JOIN sys.dm_pdw_nodes_exec_connections AS er ON ssu.session_id = er.session_id AND ssu.pdw_node_id = er.pdw_node_id
    INNER JOIN microsoft.vw_sql_requests AS sr ON ssu.session_id = sr.spid AND ssu.pdw_node_id = sr.pdw_node_id
WHERE DB_NAME(ssu.database_id) = 'tempdb'
    AND es.session_id <> @@SPID
    AND es.login_name <> 'sa' 
ORDER BY sr.request_id;
```

クエリで大量のメモリを消費しているか、tempdb の割り当てに関するエラー メッセージが表示された場合は、非常に大きな [CREATE TABLE AS SELECT (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) または [INSERT SELECT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql) ステートメントが実行されていることが原因の可能性があります。この場合、最終的なデータ移動操作に失敗します。 これは通常、最終的な INSERT SELECT の直前の、分散クエリ プランの ShuffleMove 操作として識別できます。  ShuffleMove 操作を監視するには、[sys.dm_pdw_request_steps](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql) を使用します。 

最も一般的な軽減策は、データ ボリュームが tempdb の制限である 1 TB (ノードあたり) を超えないように、CTAS または INSERT SELECT ステートメントを複数の LOAD ステートメントに分割することです。 また、クラスターをより大きなサイズにスケーリングすることができます。これにより、より多くのノードに tempdb サイズが分散され、個々のノードの tempdb が減ります。

CTAS ステートメントと INSERT SELECT ステートメントだけでなく、大規模で複雑なクエリがメモリ不足の状態で実行されると、tempdb に書き込まれ、クエリが失敗する可能性があります。  tempdb への書き込みを避けるために、より大きな[リソース クラス](https://docs.microsoft.com/azure/sql-data-warehouse/resource-classes-for-workload-management)を使用して実行することを検討してください。

## <a name="monitor-memory"></a>メモリを監視する

メモリが、パフォーマンスの低下とメモリ不足の問題の根本原因になることがあります。 クエリの実行中に SQL Server のメモリ使用率が制限に達していることがわかった場合は、データ ウェアハウスのスケーリングを検討してください。

次のクエリでは、ノードあたりの SQL Server のメモリ使用率とメモリ負荷が返されます。   
```sql
-- Memory consumption
SELECT
  pc1.cntr_value as Curr_Mem_KB, 
  pc1.cntr_value/1024.0 as Curr_Mem_MB,
  (pc1.cntr_value/1048576.0) as Curr_Mem_GB,
  pc2.cntr_value as Max_Mem_KB,
  pc2.cntr_value/1024.0 as Max_Mem_MB,
  (pc2.cntr_value/1048576.0) as Max_Mem_GB,
  pc1.cntr_value * 100.0/pc2.cntr_value AS Memory_Utilization_Percentage,
  pc1.pdw_node_id
FROM
-- pc1: current memory
sys.dm_pdw_nodes_os_performance_counters AS pc1
-- pc2: total memory allowed for this SQL instance
JOIN sys.dm_pdw_nodes_os_performance_counters AS pc2 
ON pc1.object_name = pc2.object_name AND pc1.pdw_node_id = pc2.pdw_node_id
WHERE
pc1.counter_name = 'Total Server Memory (KB)'
AND pc2.counter_name = 'Target Server Memory (KB)'
```
## <a name="monitor-transaction-log-size"></a>トランザクション ログのサイズを監視する
次のクエリでは、配布ごとのトランザクション ログのサイズが返されます。 ログ ファイルのいずれかが 160 GB に達している場合は、インスタンスのスケールアップまたはトランザクション サイズの制限を検討する必要があります。 
```sql
-- Transaction log size
SELECT
  instance_name as distribution_db,
  cntr_value*1.0/1048576 as log_file_size_used_GB,
  pdw_node_id 
FROM sys.dm_pdw_nodes_os_performance_counters 
WHERE 
instance_name like 'Distribution_%' 
AND counter_name = 'Log File(s) Used Size (KB)'
```
## <a name="monitor-transaction-log-rollback"></a>トランザクション ログのロールバックを監視する
クエリが失敗するか、続行するのに長時間かかる場合は、トランザクションのロールバックを確認および監視できます。
```sql
-- Monitor rollback
SELECT 
    SUM(CASE WHEN t.database_transaction_next_undo_lsn IS NOT NULL THEN 1 ELSE 0 END),
    t.pdw_node_id,
    nod.[type]
FROM sys.dm_pdw_nodes_tran_database_transactions t
JOIN sys.dm_pdw_nodes nod ON t.pdw_node_id = nod.pdw_node_id
GROUP BY t.pdw_node_id, nod.[type]
```

## <a name="monitor-polybase-load"></a>PolyBase の読み込みを監視する
次のクエリでは、読み込みの進行状況を概算で見積もります。 このクエリでは、現在処理中のファイルのみが表示されます。 

```sql

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="next-steps"></a>次の手順
DMV の詳細については、[システム ビュー][System views]に関するページを参照してください。


<!--Image references-->

<!--Article references-->
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[System views]: ./sql-data-warehouse-reference-tsql-system-views.md
[Table distribution]: ./sql-data-warehouse-tables-distribute.md
[Investigating queries waiting for resources]: ./sql-data-warehouse-manage-monitor.md#waiting

<!--MSDN references-->
[sys.dm_pdw_dms_workers]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm_pdw_exec_sessions]: https://msdn.microsoft.com/library/mt203883.aspx
[sys.dm_pdw_request_steps]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm_pdw_sql_requests]: https://msdn.microsoft.com/library/mt203889.aspx
[DBCC PDW_SHOWEXECUTIONPLAN]: https://msdn.microsoft.com/library/mt204017.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[LABEL]: https://msdn.microsoft.com/library/ms190322.aspx
