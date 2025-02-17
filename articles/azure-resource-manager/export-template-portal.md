---
title: Resource Manager テンプレートをエクスポートする - Azure portal
description: Azure portal を使用して、サブスクリプション内のリソースから Azure Resource Manager テンプレートをエクスポートします。
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tomfitz
ms.openlocfilehash: 0605e24590fa2d702a1385429a7808a7e1226809
ms.sourcegitcommit: 6eecb9a71f8d69851bc962e2751971fccf29557f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72532348"
---
# <a name="single-and-multi-resource-export-to-a-template-in-azure-portal"></a>Azure portal のテンプレートへの単一および複数リソースのエクスポート

Azure Resource Manager テンプレートの作成に役立てるために、既存のリソースからテンプレートをエクスポートできます。 エクスポートされたテンプレートを使用すると、リソースをデプロイする JSON の構文とプロパティを理解できます。 今後のデプロイを自動化するには、まずエクスポートされたテンプレートから始めて、シナリオに合わせて変更します。

Resource Manager では、テンプレートにエクスポートするリソースを 1 つまたは複数選択できます。 テンプレートに必要なリソースにのみ集中することができます。

この記事では、ポータルを使用してテンプレートをエクスポートする方法を示します。 [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用することもできます。

## <a name="choose-the-right-export-option"></a>適切なエクスポート オプションを選択する

テンプレートをエクスポートするには、次の 2 とおりの方法があります。

* **リソース グループまたはリソースからのエクスポート**。 このオプションでは、既存のリソースから新しいテンプレートを生成します。 エクスポートされたテンプレートは、リソース グループの現在の状態の "スナップショット" です。 リソース グループ全体、またはそのリソース グループ内の特定のリソースをエクスポートできます。

* **デプロイ前または履歴からのエクスポート**。 このオプションでは、デプロイに使用されたテンプレートのそのままのコピーを取得します。

選択したオプションに応じて、エクスポートされたテンプレートの品質は異なります。

| リソース グループまたはリソースから | デプロイ前または履歴から |
| --------------------- | ----------------- |
| テンプレートは、リソースの現在の状態のスナップショットです。 デプロイ後に手動で行った変更も含まれます。 | テンプレートにはデプロイ時点のリソースの状態のみが表示されます。 デプロイ後に手動で行った変更は含まれません。 |
| リソース グループからエクスポートするリソースを選択できます。 | 特定のデプロイに関するすべてのリソースが含まれます。 これらのリソースのサブセットを選択したり、別の時点で追加されたリソースを追加したりすることはできません。 |
| テンプレートには、通常はデプロイ中に設定しない一部のプロパティを含め、リソースのすべてのプロパティが含まれています。 テンプレートを再利用する前に、このようなプロパティを削除またはクリーンアップすることをお勧めします。 | テンプレートには、デプロイに必要なプロパティのみが含まれています。 テンプレートはすぐに使用できます。 |
| テンプレートには、再利用に必要なすべてのパラメーターが含まれていない場合もあります。 ほとんどのプロパティ値は、テンプレートにハードコーディングされています。 他の環境でテンプレートを再デプロイするには、リソースを構成する機能を高めるパラメーターを追加する必要があります。 | テンプレートには、さまざまな環境での再デプロイが簡単になるパラメーターが含まれています。 |

次の場合に、リソース グループまたはリソースからテンプレートをエクスポートします。

* 元のデプロイ後に行われたリソースへの変更をキャプチャする必要がある。
* エクスポートするリソースを選択したい。

次の場合に、以前のデプロイまたは履歴からテンプレートをエクスポートします。

* 再利用しやすいテンプレートがほしい。
* 元のデプロイ後に行った変更を含める必要はない。

## <a name="export-template-from-a-resource-group"></a>リソース グループからテンプレートをエクスポートする

リソース グループから 1 つまたは複数のリソースをエクスポートするには:

1. エクスポートするリソースを含むリソース グループを選択します。

1. 該当するチェックボックスを選択して、1 つまたは複数のリソースを選択します。  すべてを選択するには、 **[名前]** の左側にあるチェックボックスをオンにします。 **[テンプレートのエクスポート]** メニュー項目を有効にするには、少なくとも 1 つのリソースを設定している必要があります。

   ![すべてのリソースをエクスポートする](./media/export-template-portal/select-all-resources.png)

    スクリーンショットでは、ストレージ アカウントのみが選択されています。
1. **[テンプレートのエクスポート]** を選択します。

1. エクスポートされたテンプレートが表示され、ダウンロードおよびデプロイできるようになります。

   ![テンプレートの表示](./media/export-template-portal/show-template.png)

## <a name="export-template-from-a-resource"></a>リソースからテンプレートをエクスポートする

1 つのリソースをエクスポートするには:

1. エクスポートするリソースを含むリソース グループを選択します。

1. エクスポートするリソースを選択して、リソースを選択します。

1. そのリソースについて、左側のウィンドウで **[テンプレートのエクスポート]** を選択します。

   ![リソースのエクスポート](./media/export-template-portal/export-single-resource.png)

1. エクスポートされたテンプレートが表示され、ダウンロードおよびデプロイできるようになります。 このテンプレートには、1 つのリソースのみが含まれています。

## <a name="export-template-before-deployment"></a>デプロイ前にテンプレートをエクスポートする

1. デプロイする Azure サービスを選択します。

1. 新しいサービスの値を入力します。

1. 検証に合格したら、デプロイを開始する前に、 **[Automation のテンプレートをダウンロードする]** を選択します。

   ![テンプレートのダウンロード](./media/export-template-portal/download-before-deployment.png)

1. テンプレートが表示され、ダウンロードおよびデプロイできるようになります。


## <a name="export-template-after-deployment"></a>デプロイ後にテンプレートをエクスポートする

既存のリソースをデプロイするために使用されたテンプレートをエクスポートできます。 取得するテンプレートは、デプロイに使用されたものとまったく同じです。

1. エクスポートするリソース グループを選択します。

1. **[デプロイ]** の下のリンクを選択します。

   ![デプロイ履歴の選択](./media/export-template-portal/select-deployment-history.png)

1. デプロイ履歴からいずれかのデプロイを選択します。

   ![デプロイの選択](./media/export-template-portal/select-details.png)

1. **[テンプレート]** を選択します。 このデプロイに使用されているテンプレートが表示され、ダウンロードできるようになります。

   ![テンプレートの選択](./media/export-template-portal/show-template-from-history.png)

## <a name="next-steps"></a>次の手順

- [Azure CLI](manage-resource-groups-cli.md#export-resource-groups-to-templates)、[Azure PowerShell](manage-resource-groups-powershell.md#export-resource-groups-to-templates)、または [REST API](/rest/api/resources/resourcegroups/exporttemplate) を使用してテンプレートをエクスポートする方法を学びます。
- Resource Manager テンプレートの構文については、「[Azure Resource Manager テンプレートの構造と構文の詳細](./resource-group-authoring-templates.md)」を参照してください。
- テンプレートを開発する方法については、[ステップバイステップのチュートリアル](/azure/azure-resource-manager/)のページをご覧ください。
- Azure Resource Manager テンプレートのスキーマを表示するには、[テンプレート リファレンス](/azure/templates/)のページをご覧ください。
