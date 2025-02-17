---
title: 'PowerShell スクリプト - Data Factory を使用してクラウド内のデータを変換する '
description: この PowerShell スクリプトは、Azure HDInsight Spark クラスター上で Spark プログラムを実行して、クラウド内のデータを変換します。
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/12/2017
ms.openlocfilehash: 53f64103819476e4efe918bd7a7be4ee2a1bcca3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684309"
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>PowerShell スクリプト - Azure Data Factory を使用してクラウド内のデータを変換する

このサンプル PowerShell スクリプトは、Azure HDInsight Spark クラスター上で Spark プログラムを実行して、クラウド内のデータを変換するパイプラインを作成します。 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>前提条件
* **Azure Storage アカウント**。 Python スクリプトと入力ファイルを作成し、Azure ストレージにアップロードします。 Spark プログラムからの出力は、このストレージ アカウントに格納されます。 オンデマンドの Spark クラスターでは、同じストレージ アカウントがプライマリ ストレージとして使用されます。  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Python スクリプトを BLOB ストレージ アカウントにアップロードする
1. 次の内容が含まれた、**WordCount_Spark.py** という名前の Python ファイルを作成します。 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. **&lt;storageAccountName&gt;** を Azure ストレージ アカウントの名前に置き換えます。 その後、ファイルを保存します。 
3. Azure BLOB ストレージで、**adftutorial** という名前のコンテナーを作成します (存在しない場合)。 
4. **spark** という名前のフォルダーを作成します。
5. **spark** フォルダーの下に、**script** という名前のサブフォルダーを作成します。 
6. **WordCount_Spark.py** ファイルを **script** サブフォルダーにアップロードします。 


### <a name="upload-the-input-file"></a>入力ファイルをアップロードする
1. **minecraftstory.txt** という名前のファイルを作成し、任意のテキストを入力しておきます。 このテキストの単語数が Spark プログラムによってカウントされます。 
2. BLOB コンテナーの `spark` フォルダーに、`inputfiles` という名前のサブフォルダーを作成します。 
3. `minecraftstory.txt` を `inputfiles` サブフォルダーにアップロードします。 

## <a name="sample-script"></a>サンプル スクリプト
> [!IMPORTANT]
> このスクリプトは、Data Factory エンティティ (リンクされたサービス、データセット、およびパイプライン) を定義する JSON ファイルをハード ドライブ上の c:\ フォルダー内に作成します。

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

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
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/set-Azdatafactoryv2linkedservice) | データ ファクトリ内にリンクされたサービスを作成します。 リンクされたサービスは、データ ストアまたは計算をデータ ファクトリにリンクします。 |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/set-Azdatafactoryv2pipeline) | データ ファクトリ内にパイプラインを作成します。 パイプラインには、特定の操作を実行する 1 つ以上のアクティビティが含まれています。 このパイプラインで、spark アクティビティは、Azure HDInsight Spark クラスターでプログラムを実行してデータを変換します。 |
| [Invoke-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/invoke-Azdatafactoryv2pipeline) | パイプラインの実行を作成します。 つまり、パイプラインを実行します。 |
| [Get-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | パイプライン内のアクティビティの実行 (アクティビティ実行) に関する詳細情報を取得します。 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | 入れ子になったリソースすべてを含むリソース グループを削除します。 |
|||

## <a name="next-steps"></a>次の手順

Azure PowerShell の詳細については、[Azure PowerShell のドキュメント](https://docs.microsoft.com/powershell/)を参照してください。

Azure Data Factory のその他の PowerShell サンプル スクリプトについては、[Azure Data Factory の PowerShell のサンプル](../samples-powershell.md)に関する記事をご覧ください。
