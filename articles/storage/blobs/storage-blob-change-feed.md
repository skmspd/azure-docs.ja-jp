---
title: Azure Blob Storage の変更フィード (プレビュー) | Microsoft Docs
description: Azure Blob Storage の変更フィード ログとその使用方法を学習します。
author: normesta
ms.author: normesta
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 07123fd5701e9041ff377ea5309cf1291e737ca6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693812"
---
# <a name="change-feed-support-in-azure-blob-storage-preview"></a>Azure Blob Storage の変更フィードのサポート (プレビュー)

変更フィードの目的は、ストレージ アカウント内の BLOB と BLOB メタデータに対して行われるすべての変更のトランザクション ログを提供することです。 変更フィードでは、これらの変更の**順序指定済み**、**保証済み**、**永続**、**不変**、**読み取り専用**ログが提供されます。 クライアント アプリケーションでは、ストリーミングまたはバッチ モードで、いつでもこれらのログを読み取ることができます。 変更フィードを使用すると、低コストで、BLOB ストレージ アカウントで発生する変更イベントを処理する効率的でスケーラブルなソリューションを構築できます。

> [!NOTE]
> 変更フィードはパブリック プレビュー段階にあり、**westcentralus** と **westus2** リージョンで利用できます。 この記事の[条件](#conditions)に関するセクションを参照してください。 プレビューに登録する場合は、この記事の[サブスクリプションの登録](#register)に関するセクションを参照してください。

変更フィードは、Standard [BLOB の料金](https://azure.microsoft.com/pricing/details/storage/blobs/)コストで、ストレージ アカウントの特別なコンテナーに [BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) として格納されます。 これらのファイルの保有期間は、要件に基づいて制御できます (現在のリリースの[条件](#conditions)を参照)。 変更イベントは、[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 形式仕様のレコードとして変更フィードに追加されます。これは、インライン スキーマを使用して豊富なデータ構造を提供するコンパクトで高速なバイナリ形式です。 この形式は Hadoop エコシステム、Stream Analytics、Azure Data Factory で幅広く使用されています。

これらのログは、非同期的、段階的、または完全に処理できます。 任意の数のクライアント アプリケーションで、個別に変更フィードを並列で、および独自のペースで読み取ることができます。 [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) や [Apache Spark](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) などの分析アプリケーションでは、Avro ファイルとして直接ログを使用できます。これにより、低コストで、また高帯域幅で処理することができ、カスタム アプリケーションを作成する必要はありません。

変更フィードのサポートは、変更されたオブジェクトに基づいてデータを処理するシナリオに適しています。 たとえば、アプリケーションでは次のことが可能です。

  - セカンダリ インデックスを更新し、キャッシュ、検索エンジンまたはその他のコンテンツ管理シナリオと同期する。
  
  - ストリーミング方式またはバッチ モードで、オブジェクトに対して行われた変更に基づいて、ビジネス分析の洞察とメトリックを引き出す。
  
  - エンタープライズ データ管理のためのセキュリティ、コンプライアンスまたはインテリジェンスを確保するために、一定期間にわたるオブジェクトへの変更を格納、監査および分析する。

  - 災害管理またはコンプライアンスのためにアカウント内のオブジェクトの状態をバックアップ、ミラー化またはレプリケートするソリューションを構築する。

  - 作成されたオブジェクトまたは変更されたオブジェクトに基づいて、変更イベントまたはスケジュールの実行に対応する、接続されたアプリケーション パイプラインを構築する。

> [!NOTE]
> [Blob Storage イベント](storage-blob-event-overview.md)では、Azure Functions またはアプリケーションで BLOB に対して行われた変更に対応できるようにする、リアルタイムのワンタイム イベントが提供されます。 変更フィードでは、変更の永続的な順序付けされたログ モデルが提供されます。 変更フィードの変更は、変更後数分以内に順に変更フィードで使用可能となります。 アプリケーションでこれよりはるかに高速にイベントに対応する必要がある場合は、代わりに [Blob Storage イベント](storage-blob-event-overview.md)を使用することを検討してください。 Blob Storage イベントを使用すると、Azure Functions またはアプリケーションで個々のイベントにリアルタイムで対応できるようになります。

## <a name="enabling-and-disabling-the-change-feed"></a>変更フィードの有効化と無効化

変更のキャプチャを開始するには、変更フィードを有効にする必要があります。 変更のキャプチャを停止するには、変更フィードを無効にします。 ポータルまたは Powershell で Azure Resource Manager テンプレートを使用して、変更を有効または無効にすることができます。

### <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

Azure portal を使用してテンプレートをデプロイするには:

1. Azure portal で、 **[リソースの作成]** を選択します。

2. **[Marketplace を検索]** で「**template deployment**」と入力し、**Enter** キーを押します。

3. **[テンプレートのデプロイ]** 、 **[作成]** 、 **[エディターで独自のテンプレートを作成する]** の順に選択します。

5. テンプレート エディターで、次の json を貼り付けます。 `<accountName>` プレースホルダーは、実際のストレージ アカウントの名前に置き換えます。

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [{
        "type": "Microsoft.Storage/storageAccounts/blobServices",
        "apiVersion": "2019-04-01",
        "name": "<accountName>/default",
        "properties": {
            "changeFeed": {
            "enabled": true
            }
        } 
     }]
}
```
4. **[保存]** ボタンを選択し、アカウントのリソース グループを指定してから、 **[購入]** ボタンを選んで変更フィードを有効にします。

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してテンプレートをデプロイするには:

1. 最新の PowershellGet をインストールします。

   ```powershell
   install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Powershell コンソールを閉じてから、再度開きます。

3. **Az.Storage** プレビュー モジュールをインストールします。

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 1.8.1-preview –AllowPrerelease –AllowClobber –Force
   ```

4. `Connect-AzAccount` コマンドで Azure サブスクリプションにサインインし、画面上の指示に従って認証を行います。

   ```powershell
   Connect-AzAccount
   ```

5. ストレージ アカウントの変更フィードを有効にします。

   ```powershell
   Update-AzStorageBlobServiceProperty -ResourceGroupName -StorageAccountName -EnableChangeFeed $true
   ```

---

変更フィードを有効にする際に注意する点をいくつか以下に示します。

- 各ストレージ アカウントには、Blob service の変更フィードが 1 つだけあります。 

- 変更は Blob service レベルでのみキャプチャされます。

- 変更フィードでは、アカウントで発生したすべての使用可能なイベントの*すべて*の変更をキャプチャします。 クライアント アプリケーションでは、必要に応じて、イベントの種類をフィルターで除外できます (現在のリリースの[条件](#conditions)を参照)。

- 階層型名前空間があるアカウントはサポートされていません。

## <a name="consuming-the-change-feed"></a>変更フィードの使用

変更フィードによって、いくつかのメタデータとログ ファイルが生成されます。 これらのファイルは、ストレージ アカウントの **$blobchangefeed** コンテナーにあります。 

>[!NOTE]
> 現在のリリースでは、 **$blobchangefeed** コンテナーは Storage Explorer や Azure portal に表示されません。 

クライアント アプリケーションでは、SDK に付属している BLOB 変更フィード プロセッサ ライブラリを使って、変更フィードを使用できます。 

[Azure Blob Storage での変更フィード ログの処理](storage-blob-change-feed-how-to.md)に関するページを参照してください。

## <a name="understanding-change-feed-organization"></a>変更フィードの構成について

<a id="segment-index"></a>

### <a name="segments"></a>セグメント

変更フィードは、**時間単位**の*セグメント*に構成される変更のログです (「[仕様](#specifications)」を参照)。 これにより、クライアント アプリケーションは、ログ全体を検索することなく、特定の期間内に行われた変更を使用できるようになります。

変更フィードの利用可能な時間単位のセグメントは、そのセグメントの変更フィード ファイルへのパスを指定するマニフェスト ファイルに記述されています。 `$blobchangefeed/idx/segments/` 仮想ディレクトリのリストには、これらのセグメントが時間順に表示されます。 セグメントのパスは、セグメントで表される時間単位の時間範囲の開始を示します (「[仕様](#specifications)」を参照)。 このリストを使用して、関心のあるログのセグメントをフィルターで除外することができます。

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> 変更フィードを有効にすると、`$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` が自動的に作成されます。 このファイルは無視してかまいません。 これは常に空です。 

セグメント マニフェスト ファイル (`meta.json`) では、`chunkFilePaths` プロパティにそのセグメントの変更フィード ファイルのパスが表示されます。 以下は、セグメント マニフェスト ファイルの例です。

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> ストレージ アカウント内のコンテナーをリストする場合、アカウントで変更フィード機能を有効にした後にのみ、`$blobchangefeed` コンテナーが表示されます。 コンテナーを表示するには、変更フィードを有効にしてから数分待つ必要があります。

<a id="log-files"></a>

### <a name="change-event-records"></a>変更イベント レコード

変更フィード ファイルには、一連の変更イベント レコードが含まれています。 各変更イベント レコードは、個々の BLOB に対する 1 つの変更に対応しています。 レコードは、[Apache Avro](https://avro.apache.org/docs/1.8.2/spec.html) 形式の仕様を使用して、シリアル化され、ファイルに書き込まれます。 これらのレコードは、Avro ファイル形式の仕様を使用して読み取ることができます。 その形式のファイルを処理するために使用できるライブラリがいくつかあります。

変更フィード ファイルは、[追加 BLOB](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) として `$blobchangefeed/log/` 仮想ディレクトリに格納されます。 各パスの下にある最初の変更フィード ファイルでは、ファイル名に `00000` が含まれます (`00000.avro` など)。 そのパスに追加された後続の各ログ ファイルの名前は、1 ずつ増加します (例: `00001.avro`)。

以下は、変更フィード ファイルから Json に変換された変更イベント レコードの例です。

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}

```
各プロパティの説明については、「[Azure Event Grid の Blob Storage 用のイベント スキーマ](https://docs.microsoft.com/azure/event-grid/event-schema-blob-storage?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties)」を参照してください。

> [!NOTE]
> セグメントの変更フィード ファイルは、セグメントの作成後すぐには表示されません。 遅延の長さは、変更後数分以内である、変更フィードの公開待機時間の通常の間隔内です。

<a id="specifications"></a>

## <a name="specifications"></a>仕様

- 変更イベント レコードは変更フィードにのみ追加されます。 これらのレコードは、追加されると不変になり、レコード位置が安定します。 クライアント アプリケーションでは、変更フィードの読み取り位置で独自のチェックポイントを維持できます。

- 変更イベント レコードは、変更後数分以内に順に追加されます。 クライアント アプリケーションでは、ストリーム アクセスのために、またはその他の時点に一括で追加されるときにレコードを使用するように選択できます。

- 変更イベント レコードは、**BLOB ごと**の変更順で並べ替えられます。 BLOB 全体の変更の順序は、Azure Blob Storage では定義されていません。 前のセグメントのすべての変更は、後続のセグメントのすべての変更の前になります。

- 変更イベント レコードは、[Apache Avro 1.8.2](https://avro.apache.org/docs/1.8.2/spec.html) 形式仕様を使用して、ログ ファイルにシリアル化されます。

- `eventType` の値が `Control` である変更イベント レコードは内部システム レコードであり、アカウント内のオブジェクトに対する変更は反映されません。 これらは無視してください。

- `storageDiagnonstics` プロパティ バッグの値は内部でのみ使用され、アプリケーションで使用するように設計されていません。 アプリケーションに、そのデータに対する契約上の依存関係があってはなりません。

- セグメントで表される時間は、15 分の範囲の**概数**です。 したがって、指定された時間内にすべてのレコードを確実に使用するには、連続した前と次の 1 時間のセグメントを使います。

- 各セグメントの `chunkFilePaths` の数は異なる場合があります。 これは、公開スループットを管理するためにログ ストリームが内部でパーティション分割されているためです。 各 `chunkFilePath` のログ ファイルは相互に排他的な BLOB が含まれることが保証され、反復処理中の BLOB ごとの変更の順序付けに違反することなく、並列で使用して処理することができます。

- セグメントは `Publishing` の状態で始まります。 セグメントへのレコードの追加が完了すると、`Finalized` になります。 日付が `$blobchangefeed/meta/Segments.json` ファイル内の `LastConsumable` プロパティの日付の後であるすべてのセグメントのログ ファイルは、アプリケーションでは使用しないでください。 `$blobchangefeed/meta/Segments.json` ファイルの `LastConsumable` プロパティの例を以下に示します。

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="register"></a>

## <a name="register-your-subscription-preview"></a>サブスクリプションを登録する (プレビュー)

変更フィードはパブリックプ レビュー段階にすぎないため、この機能を使用するにはサブスクリプションを登録する必要があります。

### <a name="register-by-using-powershell"></a>PowerShell を使用して登録する

PowerShell コンソールで、これらのコマンドを実行します。

   ```powershell
   Register-AzProviderFeature -FeatureName Changefeed -ProviderNamespace Microsoft.Storage
   Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
   ```
### <a name="register-by-using-azure-cli"></a>Azure CLI を使用して登録する

Azure Cloud Shell で、これらのコマンドを実行します。

```cli
az feature register --namespace Microsoft.Storage --name Changefeed
az provider register --namespace 'Microsoft.Storage'
```

<a id="conditions"></a>

## <a name="conditions-and-known-issues-preview"></a>条件と既知の問題 (プレビュー)

このセクションでは、変更フィードの現在のパブリック プレビューにおける既知の問題と条件について説明します。

- 変更フィードでは、作成、更新、削除、およびコピー操作のみをキャプチャします。
- 1 つの変更の変更イベント レコードは、変更フィードに複数回表示される場合があります。
- 時間ベースの保持ポリシーを設定することによって、変更フィード ログ ファイルの有効期間を管理することはまだできません。
- ログ ファイルの `url` プロパティは常に空です。
- segments.json ファイルの `LastConsumable` プロパティでは、変更フィードで終了する最初のセグメントがリストされません。 この問題は、最初のセグメントが終了した後でのみ発生します。 最初の 1 時間後の後続のすべてのセグメントは、`LastConsumable` プロパティで正確にキャプチャされます。

## <a name="next-steps"></a>次の手順

- .NET クライアント アプリケーションを使用して変更フィードを読み取る方法の例を確認します。 [Azure Blob Storage での変更フィード ログの処理](storage-blob-change-feed-how-to.md)に関するページを参照してください。
- リアルタイムでイベントに対応する方法について学習します。 「[Blob Storage イベントへの対応](storage-blob-event-overview.md)」を参照してください
