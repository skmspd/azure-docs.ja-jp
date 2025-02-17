---
title: クイック スタート:Azure Blob Storage ライブラリ v12 - .NET
description: このクイックスタートでは、.NET 用 Azure Blob Storage クライアント ライブラリ バージョン 12 を使用して、BLOB (オブジェクト) ストレージ内にコンテナーと BLOB を作成する方法について説明します。 次に、ローカル コンピューターに BLOB をダウンロードする方法と、コンテナー内のすべての BLOB を一覧表示する方法について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/05/2019
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: 18f4053a2f8b1b5c880b46cf6eeb46c0dbf97f15
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/08/2019
ms.locfileid: "73825397"
---
# <a name="quickstart-azure-blob-storage-client-library-v12-for-net"></a>クイック スタート:.NET 用 Azure Blob Storage クライアント ライブラリ v12

.NET 用 Azure Blob Storage クライアント ライブラリ v12 を使用してみましょう。 Azure Blob Storage は、Microsoft のクラウド用オブジェクト ストレージ ソリューションです。 手順に従ってパッケージをインストールし、基本タスクのコード例を試してみましょう。 Blob Storage は、テキスト データやバイナリ データなどの大量の非構造化データを格納するために最適化されています。

> [!NOTE]
> 以前の SDK バージョンを使ってみるには、「[クイックスタート: .NET 用 Azure Blob Storage クライアント ライブラリ](storage-quickstart-blobs-dotnet-legacy.md)」を参照してください。

.NET 用 Azure Blob Storage クライアント ライブラリ v12 を使用すると、以下のことができます。

* コンテナーを作成する
* Azure Storage へ BLOB をアップロードする
* コンテナー内のすべての BLOB を一覧表示する
* ローカル コンピューターに BLOB をダウンロードする
* コンテナーを削除する

[API のリファレンスのドキュメント](/dotnet/api/azure.storage.blobs) | [ライブラリのソース コード](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs) | [パッケージ (NuGet)](https://www.nuget.org/packages/Azure.Storage.Blobs/12.0.0) | [サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
* Azure Storage アカウント - [ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
* 使用するオペレーティング システム用の最新の [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core)。 ランタイムではなく、必ず SDK を入手してください。

## <a name="setting-up"></a>設定

このセクションでは、.NET 用 Azure Blob Storage クライアント ライブラリ v12 を操作するためのプロジェクトの準備について説明します。

### <a name="create-the-project"></a>プロジェクトを作成する

*BlobQuickstartV12* という名前の .NET Core アプリケーションを作成します。

1. コンソール ウィンドウ (cmd、PowerShell、Bash など) 上で、`dotnet new` コマンドを使用し、*BlobQuickstartV12* という名前の新しいコンソール アプリを作成します。 このコマンドにより、1 つのソース ファイルを使用する単純な "Hello World" C# プロジェクトが作成されます。*Program.cs*。

   ```console
   dotnet new console -n BlobQuickstartV12
   ```

1. 新しく作成した *BlobQuickstartV12* ディレクトリに切り替えます。

   ```console
   cd BlobQuickstartV12
   ```

1. *BlobQuickstartV12* ディレクトリ内に、*data* という別のディレクトリを作成します。 BLOB データ ファイルは、ここに作成され格納されます。

    ```console
    mkdir data
    ```

### <a name="install-the-package"></a>パッケージをインストールする

まだアプリケーション ディレクトリにいる間に、`dotnet add package` コマンドを使用して、.NET 用の Azure Blob Storage クライアント ライブラリ パッケージをインストールします。

```console
dotnet add package Azure.Storage.Blobs
```

### <a name="set-up-the-app-framework"></a>アプリのフレームワークを設定する

プロジェクト ディレクトリで次の操作を行います。

1. ご使用のエディターで *Program.cs* ファイルを開きます
1. `Console.WriteLine("Hello World!");` ステートメントを削除します
1. `using` ディレクティブを追加します
1. 非同期コードをサポートするように `Main` メソッドの宣言を更新します

コードは次のとおりです。

```csharp
using Azure.Storage;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System;
using System.IO;
using System.Threading.Tasks;

namespace BlobQuickstartV12
{
    class Program
    {
        static async Task Main()
        {
        }
    }
}
```

### <a name="copy-your-credentials-from-the-azure-portal"></a>Azure Portal で資格情報をコピーする

サンプル アプリケーションから Azure Storage に対して要求を実行するときは、承認されている必要があります。 要求を承認するには、ストレージ アカウントの資格情報を接続文字列としてアプリケーションに追加します。 次の手順に従って、ストレージ アカウントの資格情報を表示します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 自分のストレージ アカウントを探します。
3. ストレージ アカウントの概要の **[設定]** セクションで、 **[アクセス キー]** を選択します。 ここでは、アカウント アクセス キーと各キーの完全な接続文字列を表示できます。
4. **[Key1]** の **[接続文字列]** の値を見つけ、 **[コピー]** ボタンを選択して接続文字列をコピーします。 すぐ後の手順で、接続文字列の値を環境変数に追加します。

    ![Azure portal から接続文字列をコピーする方法を示すスクリーンショット](../../../includes/media/storage-copy-connection-string-portal/portal-connection-string.png)

### <a name="configure-your-storage-connection-string"></a>ストレージ接続文字列の構成

接続文字列をコピーした後、アプリケーションを実行しているローカル マシンの新しい環境変数にそれを書き込みます。 環境変数を設定するには、コンソール ウィンドウを開いて、お使いのオペレーティング システムの手順に従います。 `<yourconnectionstring>` は、実際の接続文字列に置き換えてください。

#### <a name="windows"></a>Windows

```cmd
setx CONNECT_STR "<yourconnectionstring>"
```

Windows で環境変数を追加した後、コマンド ウィンドウの新しいインスタンスを開始する必要があります。

#### <a name="linux"></a>Linux

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="macos"></a>macOS

```bash
export CONNECT_STR="<yourconnectionstring>"
```

#### <a name="restart-programs"></a>プログラムの再起動

環境変数を追加した後、環境変数の読み取りを必要とする実行中のプログラムをすべて再起動します。 たとえば、続行する前に、ご使用の開発環境またはエディターを再起動します。

## <a name="object-model"></a>オブジェクト モデル

Azure Blob Storage は、大量の非構造化データを格納するために最適化されています。 非構造化データとは、特定のデータ モデルや定義に従っていないデータであり、テキスト データやバイナリ データなどがあります。 Blob Storage には、3 種類のリソースがあります。

* ストレージ アカウント
* ストレージ アカウント内のコンテナー
* コンテナー内の BLOB

次の図に、これらのリソースの関係を示します。

![Blob Storage のアーキテクチャ図](./media/storage-blob-introduction/blob1.png)

これらのリソースとやり取りするには、以下の .NET クラスを使用します。

* [BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient):`BlobServiceClient` クラスを使用して、Azure Storage リソースと BLOB コンテナーを操作できます。
* [BlobContainerClient](/dotnet/api/azure.storage.blobs.blobcontainerclient):`BlobContainerClient` クラスを使用して、Azure Storage コンテナーとその BLOB を操作できます。
* [BlobClient](/dotnet/api/azure.storage.blobs.blobclient):`BlobClient` クラスを使用して、Azure Storage BLOB を操作できます。
* [BlobDownloadInfo](/dotnet/api/azure.storage.blobs.models.blobdownloadinfo):`BlobDownloadInfo` クラスは、BLOB のダウンロードによって返されるプロパティとコンテンツを表します。

## <a name="code-examples"></a>コード例

以下のサンプル コード スニペットは、.NET 用 Azure Blob Storage クライアント ライブラリを使用して以下を実行する方法を示します。

* [接続文字列を取得する](#get-the-connection-string)
* [コンテナーの作成](#create-a-container)
* [コンテナーに BLOB をアップロードする](#upload-blobs-to-a-container)
* [コンテナー内の BLOB を一覧表示する](#list-the-blobs-in-a-container)
* [BLOB をダウンロードする](#download-blobs)
* [コンテナーの削除](#delete-a-container)

### <a name="get-the-connection-string"></a>接続文字列を取得する

以下のコードでは、「[ストレージ接続文字列の構成](#configure-your-storage-connection-string)」セクションで作成した環境変数から、ストレージ アカウントに対する接続文字列を取得します。

このコードを `Main` メソッド内に追加します。

```csharp
Console.WriteLine("Azure Blob storage v12 - .NET quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called CONNECT_STR. If the
// environment variable is created after the application is launched in a
// console or with Visual Studio, the shell or application needs to be closed
// and reloaded to take the environment variable into account.
string connectionString = Environment.GetEnvironmentVariable("CONNECT_STR");
```

### <a name="create-a-container"></a>コンテナーを作成する

新しいコンテナーの名前を決定します。 次のコードでは、確実に一意になるように、コンテナー名に GUID 値を追加します。

> [!IMPORTANT]
> コンテナーの名前は小文字にする必要があります。 コンテナーと BLOB の名前付けの詳細については、「[Naming and Referencing Containers, Blobs, and Metadata (コンテナー、BLOB、メタデータの名前付けと参照)](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata)」を参照してください。

[BlobServiceClient](/dotnet/api/azure.storage.blobs.blobserviceclient) クラスのインスタンスを作成します。 次に、[CreateBlobContainerAsync](/dotnet/api/azure.storage.blobs.blobserviceclient.createblobcontainerasync) メソッドを呼び出して、ストレージ アカウントにコンテナーを作成します。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Create a BlobServiceClient object which will be used to create a container client
BlobServiceClient blobServiceClient = new BlobServiceClient(connectionString);

//Create a unique name for the container
string containerName = "quickstartblobs" + Guid.NewGuid().ToString();

// Create the container and return a container client object
BlobContainerClient containerClient = await blobServiceClient.CreateBlobContainerAsync(containerName);
```

### <a name="upload-blobs-to-a-container"></a>コンテナーに BLOB をアップロードする

次のコード スニペット:

1. ローカルの *data* ディレクトリにテキスト ファイルを作成します。
1. 「[コンテナーを作成する](#create-a-container)」セクションからのコンテナー上で [GetBlobClient](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobclient) メソッドを呼び出すことで、[BlobClient](/dotnet/api/azure.storage.blobs.blobclient) オブジェクトへの参照を取得します。
1. [UploadAsync](/dotnet/api/azure.storage.blobs.blobclient.uploadasync) メソッドを呼び出して、ローカル テキスト ファイルを BLOB にアップロードします。 このメソッドは、BLOB がまだ存在しない場合は作成し、既に存在する場合は上書きします。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Create a local file in the ./data/ directory for uploading and downloading
string localPath = "./data/";
string fileName = "quickstart" + Guid.NewGuid().ToString() + ".txt";
string localFilePath = Path.Combine(localPath, fileName);

// Write text to the file
await File.WriteAllTextAsync(localFilePath, "Hello, World!");

// Get a reference to a blob
BlobClient blobClient = containerClient.GetBlobClient(fileName);

Console.WriteLine("Uploading to Blob storage as blob:\n\t {0}\n", blobClient.Uri);

// Open the file and upload its data
using FileStream uploadFileStream = File.OpenRead(localFilePath);
await blobClient.UploadAsync(uploadFileStream);
uploadFileStream.Close();
```

### <a name="list-the-blobs-in-a-container"></a>コンテナー内の BLOB を一覧表示する

[GetBlobsAsync](/dotnet/api/azure.storage.blobs.blobcontainerclient.getblobsasync) メソッドを呼び出して、コンテナー内の BLOB を一覧表示します。 この場合、コンテナーに BLOB が 1 つだけ追加されているので、一覧表示操作ではその 1 つの BLOB だけが返されます。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
Console.WriteLine("Listing blobs...");

// List all blobs in the container
await foreach (BlobItem blobItem in containerClient.GetBlobsAsync())
{
    Console.WriteLine("\t" + blobItem.Name);
}
```

### <a name="download-blobs"></a>BLOB をダウンロードする

[DownloadAsync](/dotnet/api/azure.storage.blobs.specialized.blobbaseclient.downloadasync) メソッドを呼び出して、以前に作成した BLOB をダウンロードします。 サンプル コードは、ローカル ファイル システム内で両方のファイルを見ることができるように、ファイル名に "DOWNLOADED" というサフィックスを追加します。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Download the blob to a local file
// Append the string "DOWNLOAD" before the .txt extension so you can see both files in MyDocuments
string downloadFilePath = localFilePath.Replace(".txt", "DOWNLOAD.txt");

Console.WriteLine("\nDownloading blob to\n\t{0}\n", downloadFilePath);

// Download the blob's contents and save it to a file
BlobDownloadInfo download = await blobClient.DownloadAsync();

using FileStream downloadFileStream = File.OpenWrite(downloadFilePath);
await download.Content.CopyToAsync(downloadFileStream);
downloadFileStream.Close();
```

### <a name="delete-a-container"></a>コンテナーを削除する

次のコードでは、[DeleteAsync](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.deleteasync) を使用してコンテナー全体を削除することで、アプリによって作成されたリソースをクリーンアップします。 また、アプリによって作成されたローカル ファイルも削除します。

アプリでは、BLOB、コンテナー、およびローカル ファイルを削除する前に、`Console.ReadLine` を呼び出すことで、ユーザーの入力を一時停止します。 このとき、リソースが削除される前に、実際に正しく作成されたことを確認できます。

`Main` メソッドの末尾に次のコードを追加します。

```csharp
// Clean up
Console.Write("Press any key to begin clean up");
Console.ReadLine();

Console.WriteLine("Deleting blob container...");
await containerClient.DeleteAsync();

Console.WriteLine("Deleting the local source and downloaded files...");
File.Delete(localFilePath);
File.Delete(downloadFilePath);

Console.WriteLine("Done");
```

## <a name="run-the-code"></a>コードの実行

このアプリは、ローカルの *[マイ ドキュメント]* フォルダーにテスト ファイルを作成し、BLOB ストレージにアップロードします。 次に、コンテナー内の BLOB を一覧表示し、ファイルを新しい名前でダウンロードして、古いファイルと新しいファイルを比較できるようにします。

お使いのアプリケーションのディレクトリに移動し、アプリケーションをビルドして実行します。

```console
dotnet build
```

```console
dotnet run
```

アプリの出力は、次の例のようになります。

```output
Azure Blob storage v12 - .NET quickstart sample

Uploading to Blob storage as blob:
         https://mystorageacct.blob.core.windows.net/quickstartblobs60c70d78-8d93-43ae-954d-8322058cfd64/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Listing blobs...
        quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31.txt

Downloading blob to
        ./data/quickstart2fe6c5b4-7918-46cb-96f4-8c4c5cb2fd31DOWNLOADED.txt

Press any key to begin clean up
Deleting blob container...
Deleting the local source and downloaded files...
Done
```

クリーンアップ プロセスを開始する前に、 *[MyDocuments]* フォルダー内の 2 つのファイルをチェックします。 それらを開いて、同じであるかどうかを確認します。

ファイルを確認した後、**Enter** キーを押してテスト ファイルを削除し、デモを終了します。

## <a name="next-steps"></a>次の手順

このクイック スタートでは、.NET を使用して BLOB をアップロード、ダウンロード、および一覧表示する方法について説明しました。

BLOB ストレージのサンプル アプリを確認するには、以下に進んでください。

> [!div class="nextstepaction"]
> [Azure BLOB ストレージ SDK v12 の .NET サンプル](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Blobs/samples)

* チュートリアル、サンプル、クイックスタートなどのドキュメントについては、「[.NET および .NET Core 開発者向けの Azure](/dotnet/azure/)」を参照してください。
* .NET Core の詳細については、「[Get started with .NET in 10 minutes (10 分で .NET を使い始める)](https://www.microsoft.com/net/learn/get-started/)」を参照してください。
