---
title: サンプル - vNet エンドポイントがないキー コンテナー
description: このサンプル ポリシー定義では、仮想ネットワーク サービス エンドポイントがないインスタンスを検出するために、Key Vault のコンテナーを監査します。
author: DCtheGeek
ms.service: azure-policy
ms.topic: sample
ms.date: 01/26/2019
ms.author: dacoulte
ms.openlocfilehash: 7bcbcdf68b3c8f882a1e0fbb9953fea575f96556
ms.sourcegitcommit: 1c2659ab26619658799442a6e7604f3c66307a89
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72255731"
---
# <a name="sample---key-vault-vaults-with-no-virtual-network-endpoints"></a>サンプル - 仮想ネットワーク エンドポイントがない Key Vault コンテナー

このポリシーでは、仮想ネットワーク エンドポイントがない Key Vault コンテナーを監査します。 これは、セキュリティ要件を適用するために使用します。 詳細については、[Key Vault の仮想ネットワーク サービス エンドポイント](../../../key-vault/key-vault-overview-vnet-service-endpoints.md)に関するページを参照してください

このサンプル ポリシーは、次の方法でデプロイすることができます。

- [Azure Portal](#azure-portal)
- [Azure PowerShell](#azure-powershell)
- [Azure CLI](#azure-cli)
- [REST API](#rest-api)

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-policy"></a>サンプル ポリシー

### <a name="policy-definition"></a>ポリシー定義

次に示したのは、作成済みの完全な JSON ポリシー定義です。REST API や [Azure へのデプロイ] ボタンのほか、ポータルから手動で使用します。

[!code-json[full](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.json "KeyVault vnet rules")]

> [!NOTE]
> ポリシーをポータルから手動で作成する場合は、上記の **properties.parameters** 部分と **properties.policyRule** 部分を使用してください。 有効な JSON とするために、中かっこ `{}` で 2 つのセクションをラップしてください。

### <a name="policy-rules"></a>ポリシー ルール

ポリシーのルールを定義する JSON は次のとおりです。Azure CLI と Azure PowerShell から使用します。

[!code-json[rule](../../../../policy-templates/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json "Policy rules (JSON)")]

### <a name="policy-parameters"></a>ポリシー パラメーター

このサンプル ポリシー定義には、パラメーターが定義されていません。

## <a name="azure-portal"></a>Azure ポータル

[![ポリシーのサンプルを Azure にデプロイする](https://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)
[![ポリシーのサンプルを Azure Gov にデプロイする](https://docs.microsoft.com/azure/governance/policy/media/deploy/deployGovbutton.png)](https://portal.azure.us/#blade/Microsoft_Azure_Policy/CreatePolicyDefinitionBlade/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-policy%2Fmaster%2Fsamples%2FKeyVault%2Faudit-keyvault-vnet-rules%2Fazurepolicy.json)

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [sample-powershell-install](../../../../includes/sample-powershell-install-no-ssh-az.md)]

### <a name="deploy-with-azure-powershell"></a>Azure PowerShell でのデプロイ

```azurepowershell-interactive
# Create the Policy Definition (Subscription scope)
$definition = New-AzPolicyDefinition -Name "audit-keyvault-vnet-rules" -DisplayName "Audit if Key Vault has no virtual network rules" -description "Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints" -Policy 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' -Mode Indexed

# Set the scope to a resource group; may also be a subscription or management group
$scope = Get-AzResourceGroup -Name 'YourResourceGroup'

# Create the Policy Assignment
$assignment = New-AzPolicyAssignment -Name 'audit-keyvault-vnet-rules-assignment' -DisplayName 'Audit Key Vault Assignment' -Scope $scope.ResourceId -PolicyDefinition $definition
```

### <a name="remove-with-azure-powershell"></a>Azure PowerShell での削除

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurepowershell-interactive
# Remove the Policy Assignment
Remove-AzPolicyAssignment -Id $assignment.ResourceId

# Remove the Policy Definition
Remove-AzPolicyDefinition -Id $definition.ResourceId
```

### <a name="azure-powershell-explanation"></a>Azure PowerShell の説明

デプロイおよび削除のスクリプトには、次のコマンドが使用されています。 以下の表の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzPolicyDefinition](/powershell/module/az.resources/New-Azpolicydefinition) | 新しい Azure Policy の定義を作成します。 |
| [Get-AzResourceGroup](/powershell/module/az.resources/Get-Azresourcegroup) | 単一のリソース グループを取得します。 |
| [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| [Remove-AzPolicyAssignment](/powershell/module/az.resources/Remove-Azpolicyassignment) | 既存の Azure Policy の割り当てを削除します。 |
| [Remove-AzPolicyDefinition](/powershell/module/az.resources/Remove-Azpolicydefinition) | 既存の Azure Policy の定義を削除します。 |

## <a name="azure-cli"></a>Azure CLI

[!INCLUDE [sample-cli-install](../../../../includes/sample-cli-install.md)]

### <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

```azurecli-interactive
# Create the Policy Definition (Subscription scope)
definition=$(az policy definition create --name 'audit-keyvault-vnet-rules' --display-name 'Audit if Key Vault has no virtual network rules' --description 'Audits Key Vault vaults if they do not have virtual network service endpoints set up. More information on virtual network service endpoints in Key Vault is available here: https://docs.microsoft.com/azure/key-vault/key-vault-overview-vnet-service-endpoints' --rules 'https://raw.githubusercontent.com/Azure/azure-policy/master/samples/KeyVault/audit-keyvault-vnet-rules/azurepolicy.rules.json' --mode Indexed)

# Set the scope to a resource group; may also be a subscription or management group
scope=$(az group show --name 'YourResourceGroup')

# Create the Policy Assignment
assignment=$(az policy assignment create --name 'audit-keyvault-vnet-rules-assignment' --display-name 'Audit Key Vault Assignment' --scope `echo $scope | jq '.id' -r` --policy `echo $definition | jq '.name' -r`)
```

### <a name="remove-with-azure-cli"></a>Azure CLI での削除

以前の割り当てと定義を削除するには、次のコマンドを実行します。

```azurecli-interactive
# Remove the Policy Assignment
az policy assignment delete --name `echo $assignment | jq '.name' -r`

# Remove the Policy Definition
az policy definition delete --name `echo $definition | jq '.name' -r`
```

### <a name="azure-cli-explanation"></a>Azure CLI の説明

| command | メモ |
|---|---|
| [az policy definition create](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-create) | 新しい Azure Policy の定義を作成します。 |
| [az group show](/cli/azure/group?view=azure-cli-latest#az-group-show) | 単一のリソース グループを取得します。 |
| [az policy assignment create](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| [az policy assignment delete](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-delete) | 既存の Azure Policy の割り当てを削除します。 |
| [az policy definition delete](/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-delete) | 既存の Azure Policy の定義を削除します。 |

## <a name="rest-api"></a>REST API

Resource Manager REST API の対話操作には、[ARMClient](https://github.com/projectkudu/ARMClient) や PowerShell などいくつかのツールを使用できます。

### <a name="deploy-with-rest-api"></a>REST API でのデプロイ

- ポリシーの定義を作成します (サブスクリプション スコープ)。 要求本文には、[ポリシー定義](#policy-definition)の JSON を使用します。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

- ポリシーの割り当てを作成します (リソース グループ スコープ)。

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/YourResourceGroup/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

  要求本文には、次の JSON の例を使用してください。

  ```json
  {
      "properties": {
          "displayName": "Audit Key Vault Assignment",
          "policyDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules"
      }
  }
  ```

### <a name="remove-with-rest-api"></a>REST API での削除

- ポリシーの割り当てを削除する

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/audit-keyvault-vnet-rules-assignment?api-version=2018-05-01
  ```

- ポリシーの定義を削除する

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/audit-keyvault-vnet-rules?api-version=2018-05-01
  ```

### <a name="rest-api-explanation"></a>REST API の説明

| Service | Group | Operation | メモ |
|---|---|---|---|
| リソース管理 | ポリシーの定義 | [作成](/rest/api/resources/policydefinitions/createorupdate) | 新しい Azure Policy 定義をサブスクリプションで作成します。 代替手段:[管理グループで作成する](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup) |
| リソース管理 | ポリシーの割り当て | [作成](/rest/api/resources/policyassignments/create) | 新しい Azure Policy の割り当てを作成します。 この例では定義を渡していますが、イニシアチブを渡すこともできます。 |
| リソース管理 | ポリシーの割り当て | [削除](/rest/api/resources/policyassignments/delete) | 既存の Azure Policy の割り当てを削除します。 |
| リソース管理 | ポリシーの定義 | [削除](/rest/api/resources/policydefinitions/delete) | 既存の Azure Policy の定義を削除します。 代替手段:[管理グループで削除する](/rest/api/resources/policydefinitions/deleteatmanagementgroup) |

## <a name="next-steps"></a>次の手順

- その他の [Azure Policy サンプル](index.md)を確認する
- [Azure Policy の定義の構造](../concepts/definition-structure.md)を確認する