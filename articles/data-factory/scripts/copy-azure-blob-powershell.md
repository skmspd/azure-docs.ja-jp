---
title: 'PowerShell スクリプト: Azure Data Factory を使用してクラウド内のデータをコピーする '
description: この PowerShell スクリプトでは、Azure Blob Storage 内のある場所から同じ Blob Storage 内の別の場所にデータをコピーします。
services: data-factory
author: linda33wj
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/12/2017
ms.author: jingwang
ms.openlocfilehash: 7ff9bd39a6a2d9785009d0f8bd8cb4c46fc09882
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684389"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-in-the-cloud"></a>PowerShell を使用して、クラウド内のデータをコピーするための Data Factory パイプラインを作成する

このサンプル PowerShell スクリプトは、Azure Blob Storage　内のある場所から別の場所にデータをコピーするパイプラインを Azure Data Factory に作成します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>前提条件
* **Azure Storage アカウント**。 BLOB ストレージを、**ソース**と**シンク**の両方のデータ ストアとして使用します。 Azure ストレージ アカウントがない場合、作成方法については、「[ストレージ アカウントの作成](../../storage/common/storage-quickstart-create-account.md)」をご覧ください。 
* Blob Storage に **BLOB コンテナー**を作成し、コンテナーに入力**フォルダー**を作成して、フォルダーにいくつかのファイルをアップロードします。 [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) などのツールを使用して、Azure Blob Storage への接続、BLOB コンテナーの作成、入力ファイルのアップロード、出力ファイルの検証を行うことができます。

## <a name="sample-script"></a>サンプル スクリプト

> [!IMPORTANT]
> このスクリプトは、Data Factory エンティティ (リンクされたサービス、データセット、およびパイプライン) を定義する JSON ファイルをハード ドライブ上の c:\ フォルダー内に作成します。

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-azure-blob-to-blob/copy-from-azure-blob-to-blob.ps1 "Copy from Blob Storage -> Blob Storage")]


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

このサンプル スクリプトを実行した後、次のコマンドを使用して、リソース グループとそれに関連付けられたすべてのリソースを削除できます。

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
リソース グループからデータ ファクトリを削除するには、次のコマンドを実行します。 

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは以下のコマンドを使用します。 

| command | メモ |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | すべてのリソースを格納するリソース グループを作成します。 |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | データ ファクトリを作成します。 |
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | データ ファクトリ内にリンクされたサービスを作成します。 リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | データ ファクトリ内にデータセットを作成します。 データセットは、パイプライン内のアクティビティの入出力を表します。 | 
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | データ ファクトリ内にパイプラインを作成します。 パイプラインには、特定の操作を実行する 1 つ以上のアクティビティが含まれています。 このパイプラインでは、コピー アクティビティが Azure Blob Storage 内のある場所から別の場所にデータをコピーします。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | パイプラインの実行を作成します。 つまり、パイプラインを実行します。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | パイプライン内のアクティビティの実行 (アクティビティ実行) に関する詳細情報を取得します。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Factory のその他の PowerShell サンプル スクリプトについては、[Azure Data Factory の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
