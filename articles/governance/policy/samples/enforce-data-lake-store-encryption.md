---
title: サンプル - Data Lake Store の暗号化が必要
description: このサンプル ポリシー定義は、Data Lake Store の暗号化が有効になっていることを要求します。
ms.date: 01/23/2019
ms.topic: sample
ms.openlocfilehash: a8ed66e9b07e1fc15c8a1a3c8c33f0e218eb62b3
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74070668"
---
# <a name="sample---require-data-lake-store-encryption"></a>サンプル - Data Lake Store 暗号化を必要とする

このビルトイン ポリシーでは、暗号化が有効になっていない Data Lake Store アカウントを拒否します。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-template"></a>サンプル テンプレート

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.DataLakeStore/accounts"
      },
      {
        "field": "Microsoft.DataLakeStore/accounts/encryptionState",
        "equals": "Disabled"
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

このテンプレートは、[Azure Portal](#deploy-with-the-portal)、[PowerShell](#deploy-with-powershell)、または [Azure CLI](#deploy-with-azure-cli) を使用してデプロイできます。 ビルトイン ポリシーを取得するには、ID `a7ff3161-0087-490a-9ad9-ad6217f4f43a` を使用します。

## <a name="deploy-with-the-portal"></a>ポータルでのデプロイ

ポリシーを割り当てるときに、利用可能なビルトイン定義から **[Enforce encryption on DataLakeStore accounts]\(DataLakeStore アカウントに暗号化を適用する\)** を選択します。

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

```azurepowershell-interactive
$definition = Get-AzPolicyDefinition -Id /providers/Microsoft.Authorization/policyDefinitions/a7ff3161-0087-490a-9ad9-ad6217f4f43a

New-AzPolicyAssignment -name "Data Lake Store encryption" -PolicyDefinition $definition -Scope <scope>
```

### <a name="clean-up-powershell-deployment"></a>PowerShell でのデプロイをクリーンアップする

ポリシーの割り当てを削除するには、次のコマンドを実行します。

```azurepowershell-interactive
Remove-AzPolicyAssignment -Name "Data Lake Store encryption" -Scope <scope>
```

## <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

```azurecli-interactive
az policy assignment create --scope <scope> --name "Data Lake Store encryption" --policy a7ff3161-0087-490a-9ad9-ad6217f4f43a
```

### <a name="clean-up-azure-cli-deployment"></a>Azure CLI でのデプロイをクリーンアップする

ポリシーの割り当てを削除するには、次のコマンドを実行します。

```azurecli-interactive
az policy assignment delete --name "Data Lake Store encryption" --resource-group myResourceGroup
```

## <a name="next-steps"></a>次の手順

- 他のサンプルについては、「[Azure Policy のサンプル](index.md)」をご確認ください。