---
title: Import/Export サービスに長い時間がかかる
description: Azure SQL Database Import/Export サービスでのデータベースのインポートまたはエクスポートに時間がかかる
ms.custom: seo-lt-2019
services: sql-database
ms.service: sql-database
ms.topic: troubleshooting
author: v-miegge
ms.author: ramakoni
ms.reviewer: ''
ms.date: 09/27/2019
ms.openlocfilehash: 29edd7da42a5eb4586cd52cc0f913a56e199a5ba
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808274"
---
# <a name="azure-sql-database-importexport-service-takes-a-long-time-to-import-or-export-a-database"></a>Azure SQL Database Import/Export サービスでのデータベースのインポートまたはエクスポートに時間がかかる

Azure SQL Database Import/Export サービスを利用するとき、処理に予想以上の時間がかかることがあります。 この記事では、この遅延の考えられる原因と代替解決手段ついて説明します。

## <a name="azure-sql-database-importexport-service"></a>Azure SQL Database Import/Export サービス

Azure SQL Database Import/Export サービスは、すべての Azure データセンターで実行される REST ベースの Web サービスです。 このサービスは、[[データベースのインポート]](https://docs.microsoft.com/azure/sql-database/sql-database-import#import-from-a-bacpac-file-in-the-azure-portal) オプションまたは [[エクスポート]](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-azure-portal) オプションを使用して Azure portal で SQL データベースを移動するときに呼び出されます。 このサービスでは、Azure SQL データベースと Azure Blob ストレージの間でインポート/エクスポートを実行するための、要求を待ち行列に入れるサービスや計算処理のサービスが無料で提供されます。

インポート操作とエクスポート操作は従来の物理的なデータベース バックアップではなく、特別な BACPAC 形式を使用するデータベースの論理バックアップです。 BACPAC 形式を使用すると、Microsoft SQL Server と Azure SQL Database のバージョンごとに異なる可能性がある物理形式を使用する必要がなくなります。 そのため、これを使用して、データベースを SQL Server データベースおよび SQL データベースに安全に復元できます。

## <a name="what-causes-delays-in-the-process"></a>処理の遅延を引き起こしている原因は何か。

Azure SQL Database Import/Export サービスでは、インポート操作とエクスポート操作を処理するために、リージョンごとに制限された数のコンピューティング仮想マシン (VM) が提供されます。 インポートまたはエクスポートにより複数リージョンにわたる帯域幅の遅延と課金が生じるのを回避するために、コンピューティング VM はリージョンごとにホストされています。 同じリージョンで同時に行われる要求が多すぎると、それらの操作の処理で大幅な遅延が発生することがあります。 要求を完了するために必要な時間は、数秒から長時間までさまざまです。

> [!NOTE]
> 要求が 4 日以内に処理されない場合、サービスによって自動的に要求がキャンセルされます。

## <a name="recommended-solutions"></a>推奨されるソリューション

データベースのエクスポートが偶発的なデータ削除からの復旧のみに使用されている場合、Azure SQL Database のすべてのエディションには、システムによって生成されるバックアップからのセルフサービス復元機能が用意されています。 ただし、その他の理由でこれらのエクスポートが必要な場合や、一貫して高速または予測可能なインポートまたはエクスポートのパフォーマンスが必要な場合は、次のオプションを検討してください。

* [SQLPackage ユーティリティを使用して BACPAC ファイルにエクスポートする](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-the-sqlpackage-utility)。
* [SQL Server Management Studio (SSMS) を使用して BACPAC ファイルにエクスポートする](https://docs.microsoft.com/azure/sql-database/sql-database-export#export-to-a-bacpac-file-using-sql-server-management-studio-ssms)。
* Microsoft SQL Server Data-Tier Application Framework (DacFx) API を使用して、BACPAC のインポートまたはエクスポートをコード内で直接実行する。 追加情報については、次を参照してください。
  * [データ層アプリケーションのエクスポート](https://docs.microsoft.com/sql/relational-databases/data-tier-applications/export-a-data-tier-application)
  * [Microsoft.SqlServer.Dac 名前空間](https://docs.microsoft.com/dotnet/api/microsoft.sqlserver.dac)
  * [DACFx のダウンロード](https://www.microsoft.com/download/details.aspx?id=55713)

## <a name="things-to-consider-when-you-export-or-import-an-azure-sql-database"></a>Azure SQL データベースをエクスポートまたはインポートするときの考慮事項

* この記事で説明するすべての方法でデータベース トランザクション ユニット (DTU) クォータが使い尽くされ、Azure SQL Database サービスによる調整が発生します。 [Azure portal でデータベースの DTU 統計を表示](https://docs.microsoft.com/azure/sql-database/sql-database-monitor-tune-overview#monitor-database-performance)できます。 データベースがそのリソース上限に達している場合は、[サービス レベルをアップグレードして](https://docs.microsoft.com/azure/sql-database/sql-database-scale-resources)さらにリソースを追加します。
* 理想的には、お使いの SQL データベースと同じリージョンで VM からクライアント アプリケーション (sqlpackage ユーティリティやカスタム DAC アプリケーションなど) を実行してください。 そうしないと、ネットワークの遅延に関連するパフォーマンスの問題が発生する可能性があります。
* インデックスがクラスター化されていない大規模なテーブルの場合、エクスポートが非常に遅くなり、エクスポートに失敗することもあります。 テーブルを分割できず、並列でエクスポートできないことがこの動作の原因です。 そのため、1 つのトランザクションでエクスポートしなければならず、特にテーブルが大きい場合、パフォーマンスの低下やエクスポートの失敗を引き起こします。


## <a name="related-documents"></a>関連ドキュメント

[Azure SQL データベースをエクスポートする際の考慮事項](https://docs.microsoft.com/azure/sql-database/sql-database-export#considerations-when-exporting-an-azure-sql-database)
