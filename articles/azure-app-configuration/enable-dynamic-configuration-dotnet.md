---
title: Azure App Configuration の動的構成を .NET Framework アプリで使用するためのチュートリアル | Microsoft Docs
description: このチュートリアルでは、.NET Framework アプリの構成データを動的に更新する方法を学習します
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: 7e28cdacce8eac4774683013ae1c30ca34ebfaad
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72821629"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>チュートリアル:.NET Framework アプリで動的な構成を使用する

App Configuration .NET クライアント ライブラリでは、アプリケーションを再起動せずに、必要に応じて一連の構成設定を更新できます。 これは、最初に構成プロバイダーのオプションから `IConfigurationRefresher` のインスタンスを取得した後、コード内の任意の場所でそのインスタンスの `Refresh` を呼び出すことによって実装できます。

設定の更新を維持しながら、構成ストアの呼び出しが多くなりすぎないようにするため、キャッシュが各設定に使用されます。 設定のキャッシュされた値の有効期限が切れるまで、構成ストアの値が変更された場合でも、更新操作で値は更新されません。 各要求の既定の有効期間は 30 秒ですが、必要な場合はオーバーライドできます。

このチュートリアルでは、自分が作成するコードに、構成の動的更新を実装する方法について説明します。 これは、クイック スタートで紹介されているアプリに基づいています。 先に進む前に、[App Configuration を使用した .NET Framework アプリの作成](./quickstart-dotnet-app.md)を完了しておいてください。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * オンデマンドによりアプリ構成ストアで構成を更新するようにアプリケーションを設定する。
> * アプリケーションのコントローラーに最新の構成を挿入する。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.1 以降](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>アプリ構成ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[Configuration Explorer]\(構成エクスプローラー)**  >  **[+ 作成]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

## <a name="create-a-net-console-app"></a>.NET コンソール アプリを作成する

1. Visual Studio を起動し、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール]** プロジェクトの種類をフィルターで選択し、 **[コンソール アプリ (.NET Framework)]** をクリックします。 **[次へ]** をクリックします。

1. **[新しいプロジェクトの構成]** で、プロジェクト名を入力します。 **[フレームワーク]** で、 **.NET Framework 4.7.1** 以上を選択します。 **Create** をクリックしてください。

## <a name="reload-data-from-app-configuration"></a>App Configuration からデータを再度読み込む
1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで、*Microsoft.Extensions.Configuration.AzureAppConfiguration* NuGet パッケージを検索してプロジェクトに追加します。 見つからない場合は、 **[プレリリースを含める]** チェック ボックスをオンにします。

1. *Program.cs* を開き、.NET Core App Configuration プロバイダーへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. 構成に関連したオブジェクトを格納するために 2 つの変数を追加します。

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. 指定した更新オプションを使用して App Configuration に接続するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                            .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });

            _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }
    ```
    更新操作がトリガーされたときに、構成データをアプリ構成ストアで更新するために使用する設定を指定するには、`ConfigureRefresh` メソッドを使います。 `AddAzureAppConfiguration` メソッドに指定されたオプションで `GetRefresher` メソッドを呼び出すことによって `IConfigurationRefresher` のインスタンスを取得でき、このインスタンスの `Refresh` メソッドを使ってコード内の任意の場所で更新操作をトリガーできます。

    > [!NOTE]
    > 構成設定の既定のキャッシュ有効期限は 30 秒ですが、`ConfigureRefresh` メソッドへの引数として渡されるオプション初期化子で `SetCacheExpiration` メソッドを呼び出すことにより、オーバーライドできます。

1. App Configuration から構成データの手動更新をトリガーする `PrintMessage()` というメソッドを追加します。

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.Refresh();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数に、アプリ構成ストアへのアクセス キーを設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

1. 変更を有効にするために、Visual Studio を再起動します。 

1. Ctrl + F5 キーを押して、アプリをビルドし、実行します。

    ![ローカルでのアプリの起動](./media/dotnet-app-run.png)

1. [Azure Portal](https://portal.azure.com) にサインインします。 **[すべてのリソース]** を選択し、クイック スタートで作成したアプリ構成ストア インスタンスを選択します。

1. **[Configuration Explorer]\(構成エクスプローラー)** を選択して次のキーの値を更新します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ - 更新済み |

1. 実行中のアプリケーションに戻り、Enter キーを押して更新をトリガーし、コマンド プロンプト ウィンドウまたは PowerShell ウィンドウに更新された値を表示します。

    ![ローカルでのアプリの更新](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > 更新操作の構成を指定するときに `SetCacheExpiration` メソッドを使ってキャッシュの有効期限を 10 秒に設定したため、構成設定の値は、その設定の前回の更新から少なくとも 10 秒が経過した場合にのみ更新されます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure マネージド サービス ID を追加して、App Configuration へのアクセスを効率化し、アプリの資格情報管理を改善しました。 App Configuration へのアクセスを効率化する Azure で管理されたサービス ID を追加する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [マネージド ID の統合](./howto-integrate-azure-managed-service-identity.md)
