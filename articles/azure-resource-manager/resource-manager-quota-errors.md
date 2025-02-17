---
title: Azure クォータ エラー | Microsoft Docs
description: Azure Resource Manager でのリソースのデプロイ時のリソース クォータ エラーを解決する方法について説明します。
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 03/09/2018
ms.author: tomfitz
ms.openlocfilehash: 201ddf69f9c28b5b3a4197f91768f749152094de
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72390304"
---
# <a name="resolve-errors-for-resource-quotas"></a>リソース クォータのエラーを解決する

この記事では、リソースをデプロイするときに発生する可能性のあるクォータ エラーについて説明します。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

Azure クォータを超えるリソースを作成するテンプレートをデプロイすると、次のようなデプロイ エラーが発生します。

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

または、次のエラーが発生することがあります。

```
Code=ResourceQuotaExceeded
Message=Creating the resource of type <resource-type> would exceed the quota of <number>
resources of type <resource-type> per resource group. The current resource count is <number>,
please delete some resources of this type before creating a new one.
```

## <a name="cause"></a>原因

クォータは、リソース グループ、サブスクリプション、アカウント、および他のスコープごとに適用されます。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 上限を超えたコア数の仮想マシンをデプロイしようとすると、クォータを超過したというエラーが発生します。
詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="azure-cli"></a>Azure CLI

Azure CLI で、`az vm list-usage` コマンドを使用して仮想マシン クォータを検索します。

```azurecli
az vm list-usage --location "South Central US"
```

次のような結果が返されます。

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

### <a name="powershell"></a>PowerShell

PowerShell で、**Get-AzVMUsage** コマンドを使用して仮想マシン クォータを検索します。

```powershell
Get-AzVMUsage -Location "South Central US"
```

次のような結果が返されます。

```powershell
Name                             Current Value Limit  Unit
----                             ------------- -----  ----
Availability Sets                            0  2000 Count
Total Regional Cores                         0   100 Count
Virtual Machines                             0 10000 Count
```

## <a name="solution"></a>解決策

クォータの引き上げを依頼するには、ポータルに移動し、サポート案件を提出します。 サポート案件で、デプロイするリージョンのクォータの引き上げを依頼します。

> [!NOTE]
> リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。 米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。 アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 の Resource Manager コアを要求する必要があります。
>
>

1. **[サブスクリプション]** を選択します。

   ![Subscriptions](./media/resource-manager-quota-errors/subscriptions.png)

2. クォータの追加が必要なサブスクリプションを選択します。

   ![サブスクリプションを選択します。](./media/resource-manager-quota-errors/select-subscription.png)

3. **[使用量 + クォータ]** を選択します。

   ![使用量とクォータを選択します。](./media/resource-manager-quota-errors/select-usage-quotas.png)

4. 右上の **[引き上げを依頼する]** を選択します。

   ![引き上げを依頼する](./media/resource-manager-quota-errors/request-increase.png)

5. フォームに引き上げが必要なクォータの種類を入力します。

   ![フォームに入力する](./media/resource-manager-quota-errors/forms.png)