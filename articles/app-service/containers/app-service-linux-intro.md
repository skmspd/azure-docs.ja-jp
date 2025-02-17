---
title: App Service on Linux の概要 - Azure | Microsoft Docs
description: Azure App Service on Linux について説明します。
keywords: Azure App Service, Linux, OSS
services: app-service
documentationcenter: ''
author: msangapu-msft
manager: gwallace
editor: ''
ms.assetid: bc85eff6-bbdf-410a-93dc-0f1222796676
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: overview
ms.date: 1/11/2019
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 1bbcd5e4f8c6a429def84ad77d7dd93fa11b7324
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819689"
---
# <a name="introduction-to-azure-app-service-on-linux"></a>Azure App Service on Linux の概要

[Azure App Service](../overview.md) は、Web サイトと Web アプリケーションをホストするために最適化された、フル マネージドのコンピューティング プラットフォームです。 顧客は App Service on Linux を使用して、サポートされているアプリケーション スタック向けに Web アプリを Linux 上でネイティブにホストすることができます。

## <a name="languages"></a>Languages

App Service on Linux では、開発者の生産性を向上させるために、多数の組み込みイメージがサポートされています。 以下の言語が含まれます。Node.js、Java (JRE 8 と JRE 11)、PHP、Python、.NET Core、および Ruby。 [`az webapp list-runtimes --linux`](https://docs.microsoft.com/cli/azure/webapp?view=azure-cli-latest#az-webapp-list-runtimes) を実行して、最新の言語とサポートされているバージョンを表示します。 アプリケーションに必要なランタイムが組み込みイメージでサポートされていない場合は、[独自の Docker イメージを作成](tutorial-custom-docker-image.md)して Web App for Containers にデプロイする方法があります。

## <a name="deployments"></a>デプロイメント

* FTP
* ローカル Git
* GitHub
* Bitbucket

## <a name="devops"></a>DevOps

* ステージング環境
* [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/container-registry-intro) と DockerHub CI/CD

## <a name="console-publishing-and-debugging"></a>コンソール、公開、およびデバッグ

* 環境
* デプロイメント
* 基本コンソール
* SSH

## <a name="scaling"></a>スケーリング

* [App Service プラン](https://docs.microsoft.com/azure/app-service/overview-hosting-plans?toc=%2fazure%2fapp-service-web%2ftoc.json)のレベルを変更することで、Web アプリのスケールアップとスケールダウンを実行できます。

## <a name="locations"></a>場所

[Azure ステータス ダッシュボード](https://azure.microsoft.com/status)を確認します。

## <a name="limitations"></a>制限事項

Azure Portal では、Web App for Containers で現在使用できる機能のみが表示されます。 他の機能は、有効になったときにポータルに表示されるようになります。

App Service on Linux は [Free、Basic、Standard、および Premium](https://azure.microsoft.com/pricing/details/app-service/plans/) App Service プランでのみサポートされており、[Shared](https://azure.microsoft.com/pricing/details/app-service/plans/) レベルはありません。 Linux 以外の Web アプリを既にホストしている App Service プランで Linux Web アプリを作成することはできません。  

現在の制限に基づいて、同じリソース グループの場合、Windows アプリと Linux アプリを同じリージョン内に混在させることはできません。

## <a name="troubleshooting"></a>トラブルシューティング

アプリケーションが起動しない場合、またはアプリのログ記録を調べる場合は、LogFiles ディレクトリの Docker のログを確認してください。 このディレクトリには、SCM サイトまたは FTP 経由でアクセスできます。 コンテナーから `stdout` および `stderr` をログに記録するには、 **[App Service ログ]** で **[Docker Container ログ]** を有効にする必要があります。 設定はすぐに有効になります。 App Service で変更が検出され、自動的にコンテナーが再起動されます。

SCM サイトには、 **[開発ツール]** メニューの **[Advanced Tools]** からアクセスできます。

![Kudu を使用した Docker のログの表示][1]

## <a name="next-steps"></a>次の手順

以下の記事では、さまざまな言語で記述した Web アプリを使用して App Service on Linux での作業を開始することができます。

* [.NET Core](quickstart-dotnetcore.md)
* [PHP](https://docs.microsoft.com/azure/app-service/containers/quickstart-php)
* [Node.JS](quickstart-nodejs.md)
* [Java](quickstart-java.md)
* [Python](quickstart-python.md)
* [Ruby](quickstart-ruby.md)
* [Go](quickstart-docker-go.md)
* [複数コンテナー アプリ](quickstart-multi-container.md)

App Service on Linux の詳細については、次のページを参照してください。

* [App Service on Linux の FAQ](app-service-linux-faq.md)
* [App Service on Linux での SSH のサポート](app-service-linux-ssh-support.md)
* [App Service でステージング環境を設定する](../../app-service/deploy-staging-slots.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
* [Docker Hub の継続的なデプロイ](app-service-linux-ci-cd.md)

質問や問題は、[フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview)に投稿できます。

<!--Image references-->
[1]: ./media/app-service-linux-intro/kudu-docker-logs.png
[2]: ./media/app-service-linux-intro/logging.png
