---
title: チュートリアル - Azure Cosmos DB バックエンドを使用してセッションやデバイス間で Azure Spatial Anchors を共有する | Microsoft Docs
description: このチュートリアルでは、バックエンド サービスと Azure Cosmos DB を使用して、Unity で Azure Spatial Anchors 識別子を Android/iOS デバイス間で共有する方法について説明します。
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: tutorial
ms.service: azure-spatial-anchors
ms.openlocfilehash: 7ddbff563f79992f21aef5182177f4fb60c61dab
ms.sourcegitcommit: 7efb2a638153c22c93a5053c3c6db8b15d072949
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/24/2019
ms.locfileid: "72882161"
---
# <a name="tutorial-sharing-azure-spatial-anchors-across-sessions-and-devices-with-an-azure-cosmos-db-back-end"></a>チュートリアル:Azure Cosmos DB バックエンドを使用してセッションやデバイス間で Azure Spatial Anchors を共有する

このチュートリアルは、「[セッションやデバイス間での Azure Spatial Anchors の共有](../../../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md)」の続きです。 ここでは、セッションとデバイス間で Azure Spatial Anchors を共有しながら、Azure Cosmos DB をバックエンド ストレージとして機能させるための、いくつかの機能を追加するプロセスについて説明します。

![オブジェクトの永続性を示す GIF](./media/persistence.gif)

このチュートリアルでは Unity と Azure Cosmos DB を使用しますが、これは Spatial Anchors 識別子を複数デバイス間で共有する方法の例を示すことのみを目的としています。 同じ目的を実現するために、他の言語やバックエンド テクノロジを使用することもできます。 また、このチュートリアルで使用する ASP.NET Core Web アプリは .NET Core 2.2 SDK を必要とします。 このアプリは、Windows 用 Web Apps では正常に動作しますが、Linux 用 Web Apps では現在実行できません。

## <a name="create-a-database-account"></a>データベース アカウントの作成

先ほど作成したリソース グループに Azure Cosmos データベースを追加します。 

[!INCLUDE [cosmos-db-create-dbaccount-table](../../../includes/cosmos-db-create-dbaccount-table.md)]

後で必要になるため、`Connection String` をコピーします。

## <a name="make-minor-changes-to-the-sharingservice-files"></a>SharingService ファイルに軽微な変更を加える

**ソリューション エクスプローラー**で、`SharingService\Startup.cs` を開きます。

ファイルの先頭にある `#define INMEMORY_DEMO` を見つけ、その行をコメントアウトします。ファイルを保存します。

**ソリューション エクスプローラー**で、`SharingService\appsettings.json` を開きます。

`StorageConnectionString` プロパティを見つけて、その値を、[データベース アカウントの作成手順](#create-a-database-account)でコピーした `Connection String` 値と同じになるように設定します。 ファイルを保存します。

共有サービスを再度発行し、サンプル アプリを実行できます。

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Cosmos DB を使用して、デバイス間でアンカーの識別子を共有しました。 新しい Unity HoloLens アプリで Azure Spatial Anchors を使用する方法をさらに学習する場合は、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [新しい Unity HoloLens アプリを開始する](./tutorial-new-unity-hololens-app.md)
