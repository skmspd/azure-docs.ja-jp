---
title: Azure Log Analytics ワークスペースの削除と復元 | Microsoft Docs
description: 個人用の月額プランで Log Analytics ワークスペースを作成した場合や、ワークスペース モデルを再構築する場合に、Log Analytics ワークスペースを削除する方法について説明します。
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: magoedte
ms.openlocfilehash: fb6714a52a65ef5efe4725b99acb30cb67af20c3
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299272"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Azure Log Analytics ワークスペースの削除と復元

この記事では、Azure Log Analytics ワークスペースの論理的な削除の概念および削除したワークスペースを回復させる方法について説明します。 

## <a name="considerations-when-deleting-a-workspace"></a>ワークスペースを削除するときの考慮事項

Log Analytics ワークスペースを削除すると、論理的な削除操作が実行されます。削除操作が不注意によるものであれ、意図的なものであれ、14 日以内であればそのデータや接続されているエージェントを含め、ワークスペースを回復させることができます。 論理的な削除の期間が過ぎると、ワークスペースとそのデータは回復不能となります。それらはキューに格納され、30 日以外に完全に削除されます。

重要なデータや構成が含まれているとサービスの運用に悪影響が生じるおそれがあるため、ワークスペースを削除する場合は注意が必要です。 エージェントやソリューションなど、Log Analytics にデータを格納する各種 Azure サービスとソースを再確認してください。そうしたサービスとソースの例を次に示します。

* 管理ソリューション
* Azure Automation
* Windows と Linux の仮想マシンで実行されているエージェント
* 環境内の Windows および Linux のコンピューターで実行されているエージェント
* System Center Operations Manager

論理的な削除操作では、ワークスペース リソースが削除され、関連するユーザーのアクセス許可がすべて破棄されます。 ユーザーは、他のワークスペースに関連付けられていれば、それらのワークスペースを使用して Log Analytics を使い続けることができます。

## <a name="soft-delete-behavior"></a>論理的な削除の動作

ワークスペースの削除操作では、ワークスペースの Resource Manager リソースが削除されますが、ワークスペースは見かけ上、削除されたように見えるだけで、その構成とデータは 14 日間維持されます。 ワークスペースに対してレポートするよう構成されたエージェントと System Center Operations Manager の管理グループは、論理的な削除の期間、孤立した状態で維持されます。 さらに、削除されたワークスペース (そのデータと接続されているリソースを含む) を回復させる、実質的には削除を元に戻すメカニズムも用意されています。

> [!NOTE] 
> インストールされているソリューションおよびリンクされたサービス (Azure Automation アカウントなど) は、削除時点でワークスペースから完全に削除され、回復させることはできません。 ワークスペースを以前に構成した状態に戻すには、回復操作の後で再構成する必要があります。

ワークスペースは、[PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0)、[REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)、[Azure portal](https://portal.azure.com) のいずれかの方法で削除することができます。

### <a name="delete-workspace-in-azure-portal"></a>Azure portal でワークスペースを削除する

1. サインインするには、[Azure portal](https://portal.azure.com) に移動します。 
2. Azure Portal で **[すべてのサービス]** を選択します。 リソースの一覧で、「**Log Analytics**」と入力します。 入力を始めると、入力内容に基づいて、一覧がフィルター処理されます。 **[Log Analytics ワークスペース]** を選択します。
3. Log Analytics ワークスペースの一覧でワークスペースを選択して、中央のウィンドウの上部にある **[削除]** をクリックします。
   ![ワークスペースのプロパティ ウィンドウの削除オプション](media/delete-workspace/log-analytics-delete-workspace.png)
4. ワークスペースの削除を確定するよう求める確認メッセージ ウィンドウが表示されたら、 **[はい]** をクリックします。
   ![ワークスペースの削除の確定](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

## <a name="recover-workspace"></a>ワークスペースを回復させる

論理的な削除操作の前にワークスペースが関連付けられていたサブスクリプションとリソース グループに対する共同作成者のアクセス許可を持っているユーザーは、論理的な削除の期間中であれば、データと構成、接続されているエージェントを含め、ワークスペースを回復させることができます。 論理的な削除の期間が過ぎると、ワークスペースは回復不能となり、完全な削除の対象に割り当てられます。 論理的な削除期間中は、削除されたワークスペースの名前が維持されるため、新しいワークスペースを作成しようとするときにその名前を使用することはできません。  

ワークスペースは、ワークスペース作成方法 ([PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) または [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate)) を使用してワークスペースを再作成することで回復させることができます。ただし、そのためには、これらのプロパティに、次を含む削除されたワークスペースの情報が設定されている必要があります。

* サブスクリプション ID
* リソース グループ名
* ワークスペース名
* リージョン

> [!NOTE]
> * [Azure portal](https://portal.azure.com) ではワークスペースの回復はサポートされていません。 
> * 論理的な削除期間中にワークスペースを再作成すると、このワークスペース名が既に使用中であることが表示されます。 
> 
