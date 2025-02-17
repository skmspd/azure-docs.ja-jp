---
title: Hyperscale でのパフォーマンス診断
description: この記事では、Azure SQL Database での Hyperscale のパフォーマンスの問題をトラブルシューティングする方法について説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: seo-lt-2019
ms.topic: troubleshooting
author: denzilribeiro
ms.author: denzilr
ms.reviewer: sstein
ms.date: 10/18/2019
ms.openlocfilehash: a7c64284c958fa8b3ec89c2b27515fe167a04011
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73811143"
---
# <a name="sql-hyperscale-performance-troubleshooting-diagnostics"></a>SQL Hyperscale のパフォーマンスのトラブルシューティング診断


Hyperscale データベースでのパフォーマンスの問題のトラブルシューティングを行うには、Azure SQL データベースの計算ノードに対する[一般的なパフォーマンスのチューニング方法](sql-database-monitor-tune-overview.md)が、パフォーマンス調査の開始点となります。 ただし、Hyperscale の[分散アーキテクチャ](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)を考慮して、役立つ診断がさらに追加されています。 この記事では、Hyperscale 固有の診断データについて説明します。


## <a name="log-rate-throttling-waits"></a>ログ速度調整の待機


すべての Azure SQL Database サービス レベルには、[ログ速度ガバナンス](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)によって適用されるログ生成速度制限があります。 Hyperscale では、サービス レベルに関係なく、ログ生成の制限は現在 100 MB/秒に設定されています。 ただし、復元可能性 SLA を維持するために、プライマリ コンピューティング レプリカのログ生成速度を調整する必要がある場合もあります。 この調整は、[ページ サーバーまたはその他のコンピューティング レプリカ](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)がログ サービスからの新しいログ レコードの適用で大幅に遅れている場合に発生します。

次の待機の種類 ([sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql/) 内) は、プライマリ コンピューティング レプリカでログ速度を調整できる理由を示しています。

|待機の種類    |説明                         |
|-------------          |------------------------------------|
|RBIO_RG_STORAGE        | ページ サーバーでのログ使用の遅延が原因で Hyperscale データベースのプライマリ 計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_DESTAGE        | 長期ログ ストレージによるログ使用の遅延が原因で Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_REPLICA        | 読み取り可能なセカンダリ レプリカによるログ使用の遅延が原因で、Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |
|RBIO_RG_LOCALDESTAGE   | ログ サービスによるログ使用の遅延が原因で Hyperscale データベースの計算ノードのログ生成速度が調整されているときに発生します。         |


## <a name="page-server-reads"></a>ページ サーバーの読み取り

コンピューティング レプリカでは、データベースの完全なコピーがローカルにキャッシュされません。 コンピューティング レプリカに対してローカルなデータは、バッファー プール (メモリ内) と、データ ページの部分的な (カバーしていない) キャッシュである弾性バッファー プール拡張 (RBPEX) ローカル キャッシュに格納されます。 このローカル RBPEX キャッシュは、コンピューティング サイズに比例してサイズが調整され、コンピューティング レベルのメモリの 3 倍です。 RBPEX は、最も頻繁にアクセスされるデータがあるという点で、バッファー プールに似ています。 一方、各ページ サーバーには、保持するデータベースの部分をカバーする RBPEX キャッシュがあります。
 
コンピューティング レプリカに対して読み取りが発行されると、データがバッファー プールまたはローカル RBPEX キャッシュに存在しない場合、getPage(pageId, LSN) 関数呼び出しが発行され、対応するページ サーバーからそのページが取り込まれます。 ページ サーバーからの読み取りはリモート読み取りであるため、ローカル RBPEX からの読み取りよりも低速です。 IO 関連のパフォーマンスの問題のトラブルシューティングを行うときは、比較的低速なリモート ページ サーバー読み取りによって IO が何回実行されたのかを把握できる必要があります。

複数の DMV および拡張イベントの列とフィールドがあり、ページ サーバーからのリモート読み取りの回数が示されています。これを、合計読み取り数と比較できます。 

- ページ サーバー読み取りを報告する列は、実行 DMV で利用でき、次のようなものがあります。
    - [sys.dm_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-requests-transact-sql/)
    - [sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql/)
    - [sys.dm_exec_procedure_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-procedure-stats-transact-sql/)
    - [sys.dm_exec_trigger_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-trigger-stats-transact-sql/)
- ページ サーバー読み取りは、次の拡張イベントに追加されます。
    - sql_statement_completed
    - sp_statement_completed
    - sql_batch_completed
    - rpc_completed
    - scan_stopped
    - query_store_begin_persist_runtime_stat
    - query-store_execution_runtime_info
- ActualPageServerReads/ActualPageServerReadAheads が、実際のプランのクエリ プラン XML に追加されます。 例:

`<RunTimeCountersPerThread Thread="8" ActualRows="90466461" ActualRowsRead="90466461" Batches="0" ActualEndOfScans="1" ActualExecutions="1" ActualExecutionMode="Row" ActualElapsedms="133645" ActualCPUms="85105" ActualScans="1" ActualLogicalReads="6032256" ActualPhysicalReads="0" ActualPageServerReads="0" ActualReadAheads="6027814" ActualPageServerReadAheads="5687297" ActualLobLogicalReads="0" ActualLobPhysicalReads="0" ActualLobPageServerReads="0" ActualLobReadAheads="0" ActualLobPageServerReadAheads="0" />`

> [!NOTE]
> SSMS のクエリ プラン プロパティのウィンドウでこれらの属性を表示するには、SSMS 18.3 以降が必要です。


## <a name="virtual-file-stats-and-io-accounting"></a>仮想ファイルの統計と IO アカウンティング

Azure SQL Database では、SQL Server IO を監視する主な方法は、[sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) DMF です。 Hyperscale の IO 特性は、その[分散アーキテクチャ](sql-database-service-tier-hyperscale.md#distributed-functions-architecture)によって異なります。 このセクションでは、この DMF で表示されるデータ ファイルへの IO (読み取りと書き込み) に焦点を当てます。 Hyperscale では、この DMF で表示される各データ ファイルは、1 つのリモート ページ サーバーに対応します。 ここで説明する RBPEX キャッシュは、コンピューティング レプリカ上のカバーしていないキャッシュである SSD ベースのローカル キャッシュです。


### <a name="local-rbpex-cache-usage"></a>ローカル RBPEX キャッシュの使用

ローカル RBPEX キャッシュは、ローカル SSD ストレージの計算ノード上に存在します。 そのため、この RBPEX キャッシュの IO は、リモート ページ サーバーの IO より高速です。 現在、Hyperscale データベースの [sys.dm_io_virtual_file_stats()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql/) には、コンピューティング レプリカのローカル RBPEX キャッシュで実行された IO を報告する特別な行があります。 この行の `database_id` と `file_id` の両方の列の値は 0 です。 たとえば、次のクエリでは、データベースの起動以降の RBPEX 使用状況の統計が返されます。

`select * from sys.dm_io_virtual_file_stats(0,NULL);`

RBPEX で実行された読み取りと、他のすべてのデータ ファイルに対して行われた集約読み取りとの比率から、RBPEX キャッシュ ヒット率が得られます。


### <a name="data-reads"></a>データ読み取り

- コンピューティング レプリカの SQL Server エンジンによって発行された読み取りは、ローカル RBPEX キャッシュまたはリモート ページ サーバーのいずれかによって、または複数ページを読み取る場合はこの 2 つの組み合わせによって処理されることがあります。
- コンピューティング レプリカが特定のファイル (file_id 1 など) から一部のページを読み取るとき、このデータがローカル RBPEX キャッシュにのみ存在する場合、この読み取りのすべての IO は file_id 0 (RBPEX) に対するものと見なされます。 そのデータの一部がローカル RBPEX キャッシュにあり、一部がリモート ページ サーバーにある場合、IO は、RBPEX から提供される部分については file_id 0 に対するものと見なされ、リモート ページ サーバーから提供される部分については file_id 1 に対するものと見なされます。 
- コンピューティング レプリカがページ サーバーから特定の [LSN](/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide/) でページを要求したときに、ページ サーバーが、要求された LSN に追いついていない場合、コンピューティング レプリカでの読み取りは、ページ サーバーが追いつくまで待機してから、そのページがコンピューティング レプリカに返されます。 コンピューティング レプリカでのページ サーバーからの読み取りでは、その IO で待機している場合、PAGEIOLATCH_* の待機の種類が表示されます。 この待機時間には、要求された LSN までページ サーバー上の要求されたページに追いつくための時間と、ページ サーバーからコンピューティング レプリカへのページ転送に必要な時間の両方が含まれます。
- 先読みなどの大規模な読み取りは、多くの場合、["スキャッター/ギャザー" 読み取り](/sql/relational-databases/reading-pages/)を使用して行われます。 これにより、一度に最大 4 MB のページを読み取ることができ、これは SQL Server エンジンでの 1 回の読み取りと見なされます。 ただし、読み取り中のデータが RBPEX に存在する場合、バッファー プールと RBPEX で常に 8 KB のページが使用されるため、これらの読み取りは複数の個別の 8 KB の読み取りと見なされます。 その結果、RBPEX に対して表示される読み取り IO の数が、エンジンによって実行された実際の IO 数よりも大きくなる場合があります。


### <a name="data-writes"></a>データ書き込み

- プライマリ コンピューティング レプリカは、ページ サーバーに直接書き込みません。 代わりに、ログ サービスからのログ レコードは、対応するページ サーバーで再生されます。 
- コンピューティング レプリカで発生する書き込みは、主にローカル RBPEX (file_id 0) に書き込まれます。 8 KB を超える論理ファイルの書き込み (つまり、[ギャザー書込み](/sql/relational-databases/writing-pages/)を使用して実行されるもの) では、バッファー プールと RBPEX は常に 8 KB のページを使用するため、各書き込み操作は RBPEX への複数の 8 KB の個別書き込みに変換されます。 その結果、RBPEX に対して表示される書き込み IO の数が、エンジンによって実行された実際の IO 数よりも大きくなる場合があります。
- RBPEX 以外のファイル、またはページ サーバーに対応する file_id 0 以外のデータ ファイルにも、書き込み回数が表示されます。 Hyperscale サービス レベルでは、コンピューティング レプリカがページ サーバーに直接書き込むことはないため、これらの書き込みはシミュレートされます。 書き込み IOPS とスループットは、コンピューティング レプリカで発生すると見なされますが、file_id 0 以外のデータ ファイルの待機時間は、ページ サーバーの書き込みの実際の待機時間を反映しません。

### <a name="log-writes"></a>ログ書き込み

- プライマリ コンピューティングでは、ログ書き込みは sys.dm_io_virtual_file_stats の file_id 2 に相当します。 プライマリ コンピューティングでのログ書き込みは、ログ ランディング ゾーンへの書き込みです。
- ログ レコードは、セカンダリ レプリカにはコミット時に書き込まれません。 Hyperscale では、ログは Xlog サービスによってリモート レプリカに適用されます。 セカンダリ レプリカではログの書き込みは実際には行われないため、セカンダリ レプリカでのログ IO のアカウンティングは、追跡のみを目的としています。

## <a name="additional-resources"></a>その他のリソース

- ハイパースケールの単一データベースに対する仮想コア リソースの制限については、[Hyperscale サービス レベルの仮想コアの制限](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5)に関する記事を参照してください
- Azure SQL Database のパフォーマンスのチューニングについては、[Azure SQL Database でのクエリのパフォーマンス](sql-database-performance-guidance.md)に関する記事を参照してください
- クエリ ストアを使用したパフォーマンスのチューニングについては、[クエリ ストアを使用したパフォーマンス監視](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store/)に関する記事を参照してください
- DMV の監視スクリプトについては、「[動的管理ビューを使用して Azure SQL Database のパフォーマンスを監視する](sql-database-monitoring-with-dmvs.md)」を参照してください
