---
title: 初歩的なクエリのサンプル
description: Azure Resource Graph を使用して、リソースのカウント、リソースの並べ替え、特定のタグによるクエリなど、いくつかの初歩的なクエリを実行します。
ms.date: 10/21/2019
ms.topic: quickstart
ms.openlocfilehash: ca7fc2ba3e952f93f85c677eb8a8a7322901c7d0
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076114"
---
# <a name="starter-resource-graph-queries"></a>Resource Graph の初歩的なクエリ

Azure Resource Graph でクエリを理解する最初の手順は、[クエリ言語](../concepts/query-language.md)の基本を理解することです。 [Kusto Query Language (KQL)](/azure/kusto/query/index) にまだ慣れていない場合は、[KQL のチュートリアル](/azure/kusto/query/tutorial)を参照し、目的のリソースへの要求を作成する方法を理解することをお勧めします。

次のスターター クエリを説明します。

> [!div class="checklist"]
> - [Azure リソースの数](#count-resources)
> - [キー コンテナー リソースの数](#count-keyvaults)
> - [名前で並べ替えられたリソースの一覧表示](#list-resources)
> - [降順の名前で順序付けられたすべての仮想マシンの表示](#show-vms)
> - [名前とその OS の種類による最初の 5 つの仮想マシンの表示](#show-sorted)
> - [仮想マシンの数 (OS の種類別)](#count-os)
> - [ストレージを含むリソースの表示](#show-storage)
> - [パブリック IP アドレスの一覧表示](#list-publicip)
> - [サブスクリプションで構成されている IP アドレスを持つリソースの数](#count-resources-by-ip)
> - [特定のタグ値が付いたリソースの一覧表示](#list-tag)
> - [特定のタグ値を持つすべてのストレージ アカウントの一覧表示](#list-specific-tag)
> - [仮想マシン リソースの別名の表示](#show-aliases)
> - [特定の別名の個別の値の表示](#distinct-alias-values)
> - [関連付けられていないネットワーク セキュリティ グループの表示](#unassociated-nsgs)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="language-support"></a>言語のサポート

Azure CLI (拡張経由) および Azure PowerShell (モジュール経由) は、Azure Resource Graph をサポートします。 次のクエリを実行する前に、環境が準備できていることを確認します。 選択するシェル環境をインストールし、検証する手順については、[Azure CLI](../first-query-azurecli.md#add-the-resource-graph-extension) および [Azure PowerShell](../first-query-powershell.md#add-the-resource-graph-module) を参照してください。

## <a name="a-namecount-resources-count-azure-resources"></a><a name="count-resources" />Azure リソースの数の取得

このクエリは、あなたがアクセスしたサブスクリプションに存在する Azure リソースの数を返します。 また、選択するシェルに適切な Azure Resource Graph コンポーネントがインストールされ、正常に動作していることを検証するために使用する場合にも有効なクエリです。

```kusto
Resources
| summarize count()
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | summarize count()"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | summarize count()"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20summarize%20count()" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namecount-keyvaults-count-key-vault-resources"></a><a name="count-keyvaults" />キー コンテナー リソースの数

このクエリでは、`summarize` ではなく `count` を使用して、返されるレコードの数をカウントします。 カウントに含まれるのは、キー コンテナーだけです。

```kusto
Resources
| where type =~ 'microsoft.keyvault/vaults'
| count
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.keyvault/vaults' | count"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.keyvault%2Fvaults'%20%7C%20count" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namelist-resources-list-resources-sorted-by-name"></a><a name="list-resources" />名前で並べ替えられたリソースの一覧表示

このクエリは、あらゆる種類のリソースを返しますが、返されるプロパティは **name**、**type**、**location** に限られます。 `order by` を使用することにより、これらのプロパティが **name** プロパティの昇順 (`asc`) で並べ替えらえます。

```kusto
Resources
| project name, type, location
| order by name asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, type, location | order by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, type, location | order by name asc"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20type%2C%20location%20%7C%20order%20by%20name%20asc" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-nameshow-vms-show-all-virtual-machines-ordered-by-name-in-descending-order"></a><a name="show-vms" />降順の名前で順序付けられたすべての仮想マシンの表示

仮想マシン (`Microsoft.Compute/virtualMachines`型) のみを一覧表示したければ、結果において **type** プロパティと突き合わせます。 前のクエリと同様に、`desc` は `order by` を降順に変更します。 `=~`の種類一致により、Resource Graph では大文字と小文字が区別されないことを示します。

```kusto
Resources
| project name, location, type
| where type =~ 'Microsoft.Compute/virtualMachines'
| order by name desc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name, location, type| where type =~ 'Microsoft.Compute/virtualMachines' | order by name desc"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20project%20name%2C%20location%2C%20type%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20order%20by%20name%20desc" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-nameshow-sorted-show-first-five-virtual-machines-by-name-and-their-os-type"></a><a name="show-sorted" />名前とその OS の種類による最初の 5 つの仮想マシンの表示

このクエリは、名前別に整理された 5 つの一致するレコードを取得するためのみに `top` を使用します。 Azure リソースの種類は `Microsoft.Compute/virtualMachines` です。 `project` により、Azure Resource Graph に含めるプロパティを説明します。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| project name, properties.storageProfile.osDisk.osType
| top 5 by name desc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | project name, properties.storageProfile.osDisk.osType | top 5 by name desc"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20project%20name%2C%20properties.storageProfile.osDisk.osType%20%7C%20top%205%20by%20name%20desc" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namecount-os-count-virtual-machines-by-os-type"></a><a name="count-os" />仮想マシンの数の取得 (OS の種類別)

前のクエリに基づき、Azure リソースの種類 `Microsoft.Compute/virtualMachines` によりまだ制限していますが、返されるレコード数を制限しなくなっています。
代わりに、プロパティで値をグループ化し、集計する方法を定義するために`summarize` および `count()` を使用しました。この例では、`properties.storageProfile.osDisk.osType`です。 この文字列が完全なオブジェクトにおいてどのように見えるかという例については、[ リソースの確認‐仮想マシンの検出 ](../concepts/explore-resources.md#virtual-machine-discovery)を参照してください。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| summarize count() by tostring(properties.storageProfile.osDisk.osType)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | summarize count() by tostring(properties.storageProfile.osDisk.osType)"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20summarize%20count()%20by%20tostring(properties.storageProfile.osDisk.osType)" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

同じクエリを記述するさまざまな方法は、プロパティを `extend` し、クエリ内で使用するために一時的に名前を付けます。この場合は **os** です。 前の例に示すように、**os** が `summarize` および `count()` により使用されます。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| extend os = properties.storageProfile.osDisk.osType
| summarize count() by tostring(os)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | extend os = properties.storageProfile.osDisk.osType | summarize count() by tostring(os)"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20os%20%3D%20properties.storageProfile.osDisk.osType%20%7C%20summarize%20count()%20by%20tostring(os)" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

> [!NOTE]
> `=~` によりプロパティの大文字と小文字を区別しないことが可能である一方、クエリにおけるプロパティの使用 (**properties.storageProfile.osDisk.osType** 等) には正しいケースを必要とすることにご注意ください。 プロパティが正しくない場合、null 値または間違った値が返され、グループ化または集計が不正確になる可能性があります。

## <a name="a-nameshow-storage-show-resources-that-contain-storage"></a><a name="show-storage" />ストレージを含むリソースの表示

一致する種類を明示的に定義する代わりに、このクエリの例により`contains`単語**ストレージ**する任意の Azure リソースが見つかります。

```kusto
Resources
| where type contains 'storage' | distinct type
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'storage' | distinct type"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'storage' | distinct type"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'storage'%20%7C%20distinct%20type" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namelist-publicip-list-all-public-ip-addresses"></a><a name="list-publicip" />すべてのパブリック IP アドレスの一覧表示

前のクエリと同様に、type に **publicIPAddresses** という語を含むものをすべて検索します。
このクエリは、そのパターンを拡張して、**properties.ipAddress**
`isnotempty` の場合にのみ結果を取得し、**properties.ipAddress** のみを返し、結果を上位 100 件に制限 (`limit`) します。
100. 選択したシェルによって引用符のエスケープが必要となる場合があります。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| project properties.ipAddress
| limit 100
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | project properties.ipAddress | limit 100"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20project%20properties.ipAddress%20%7C%20limit%20100" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namecount-resources-by-ip-count-resources-that-have-ip-addresses-configured-by-subscription"></a><a name="count-resources-by-ip" />サブスクリプションで構成されている IP アドレスを持つリソースの数の取得

前例のクエリを使用し、`summarize` および `count()` を追加して、構成された IP アドレスをもつリソースのサブスクリプションにより一覧を取得できます。

```kusto
Resources
| where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress)
| summarize count () by subscriptionId
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type contains 'publicIPAddresses' and isnotempty(properties.ipAddress) | summarize count () by subscriptionId"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20contains%20'publicIPAddresses'%20and%20isnotempty(properties.ipAddress)%20%7C%20summarize%20count%20()%20by%20subscriptionId" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namelist-tag-list-resources-with-a-specific-tag-value"></a><a name="list-tag" />特定のタグ値が付いたリソースの一覧表示

タグなどの Azure リソースの種類以外のプロパティによる結果を制限することができます。 この例では、**Internal** という値を持つ、**Environment** というタグ名の Azure リソースをフィルターで抽出しています。

```kusto
Resources
| where tags.environment=~'internal'
| project name
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

そのリソースが持っているタグとその値も得る必要がある場合は、`project` キーワードに **tags** プロパティを追加します。

```kusto
Resources
| where tags.environment=~'internal'
| project name, tags
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where tags.environment=~'internal' | project name, tags"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20tags.environment%3D~'internal'%20%7C%20project%20name%2C%20tags" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namelist-specific-tag-list-all-storage-accounts-with-specific-tag-value"></a><a name="list-specific-tag" />特定のタグ値を持つすべてのストレージ アカウントの一覧表示

前の例のフィルター機能を組み合わせて、**type** プロパティで Azure リソースの種類をフィルター処理してみましょう。 このクエリでは、特定の種類の Azure リソースを抽出する検索が、さらに特定のタグ名と値で制限されています。

```kusto
Resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| where tags['tag with a space']=='Custom value'
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Storage/storageAccounts' | where tags['tag with a space']=='Custom value'"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Storage%2FstorageAccounts'%20%7C%20where%20tags%5B'tag%20with%20a%20space'%5D%3D%3D'Custom%20value'" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

> [!NOTE]
> この例では`=~`の条件付きの代わりに、`==`をマッチングに使用しています。 `==` は大文字小文字が区別します。

## <a name="a-nameshow-aliases-show-aliases-for-a-virtual-machine-resource"></a><a name="show-aliases" />仮想マシン リソースの別名の表示

[Azure ポリシーの別名](../../policy/concepts/definition-structure.md#aliases)は、リソースのコンプライアンスを管理するために Azure Policy によって使用されます。 Azure Resource Graph は、リソースの種類の "_別名_" を返すことができます。 これらの値は、カスタム ポリシー定義を作成するときに別名の現在の値を比較するのに役立ちます。 _aliases_ 配列は、既定ではクエリの結果に提供されません。 明示的に結果に追加するには、`project aliases` を使用します。

```kusto
Resources
| where type =~ 'Microsoft.Compute/virtualMachines'
| limit 1
| project aliases
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'Microsoft.Compute/virtualMachines' | limit 1 | project aliases" | ConvertTo-Json
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'Microsoft.Compute%2FvirtualMachines'%20%7C%20limit%201%20%7C%20project%20aliases" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-namedistinct-alias-values-show-distinct-values-for-a-specific-alias"></a><a name="distinct-alias-values" />特定の別名の個別の値の表示

単一のリソースの別名の値を確認することは有用ですが、それだけでは Azure Resource Graph を使用して複数のサブスクリプションにわたってクエリを実行することの真の価値は表されません。 この例では、特定の別名のすべての値を調べて、個別の値を返します。

```kusto
Resources
| where type=~'Microsoft.Compute/virtualMachines'
| extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType']
| distinct tostring(alias)
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type=~'Microsoft.Compute/virtualMachines' | extend alias = aliases['Microsoft.Compute/virtualMachines/storageProfile.osDisk.managedDisk.storageAccountType'] | distinct tostring(alias)"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%3D~'Microsoft.Compute%2FvirtualMachines'%20%7C%20extend%20alias%20%3D%20aliases%5B'Microsoft.Compute%2FvirtualMachines%2FstorageProfile.osDisk.managedDisk.storageAccountType'%5D%20%7C%20distinct%20tostring(alias)" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="a-nameunassociated-nsgs-show-unassociated-network-security-groups"></a><a name="unassociated-nsgs" />関連付けられていないネットワーク セキュリティ グループの表示

このクエリでは、ネットワーク インターフェイスまたはサブネットに関連付けられていないネットワーク セキュリティ グループ (NSG) が返されます。

```kusto
Resources
| where type =~ "microsoft.network/networksecuritygroups" and isnull(properties.networkInterfaces) and isnull(properties.subnets)
| project name, resourceGroup
| sort by name asc
```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az graph query -q "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="azure-powershelltabazure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Search-AzGraph -Query "Resources | where type =~ 'microsoft.network/networksecuritygroups' and isnull(properties.networkInterfaces) and isnull(properties.subnets) | project name, resourceGroup | sort by name asc"
```

# <a name="portaltabazure-portal"></a>[ポータル](#tab/azure-portal)

![Resource Graph エクスプローラー アイコン](../media/resource-graph-small.png) Azure Resource Graph エクスプローラーで次のクエリを試してください。

- Azure portal: <a href="https://portal.azure.com/?feature.customportal=false#blade/HubsExtension/ArgQueryBlade/query/Resources%20%7C%20where%20type%20%3D~%20'microsoft.network%2Fnetworksecuritygroups'%20and%20isnull(properties.networkInterfaces)%20and%20isnull(properties.subnets)%20%7C%20project%20name%2C%20resourceGroup%20%7C%20sort%20by%20name%20asc" target="_blank">portal.azure.com</a> ![[リンクを新しいウィンドウで開く] アイコン](../../media/new-window.png)

---

## <a name="next-steps"></a>次の手順

- [クエリ言語](../concepts/query-language.md)の詳細について学習します。
- [リソースを探索する](../concepts/explore-resources.md)方法について詳しく確認します。
- [高度なクエリ](advanced.md)のサンプルを参照します。