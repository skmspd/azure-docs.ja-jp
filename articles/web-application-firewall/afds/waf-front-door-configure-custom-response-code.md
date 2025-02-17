---
title: Azure Front Door で Web アプリケーション ファイアウォールに対するカスタム応答を構成する
description: Web アプリケーション ファイアウォール (WAF) が要求をブロックした場合のカスタム応答コードとメッセージを構成する方法について学習します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 08/21/2019
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: ae3099373bbaece57c2896eb0fb4e7d59d817f25
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511612"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall"></a>Azure Web アプリケーション ファイアウォールに対するカスタム応答を構成する

既定では、Azure Front Door を使用した Azure Web アプリケーション ファイアウォール (WAF) がルールの一致により要求をブロックした場合、403 状態コードが "**要求がブロックされています**" というメッセージと共に返されます。 この記事では、要求が WAF でブロックされた場合のカスタム応答の状態コードと応答メッセージを構成する方法について説明します。

## <a name="set-up-your-powershell-environment"></a>PowerShell 環境をセットアップする
Azure PowerShell には、Azure リソースの管理に [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) モデルを使う一連のコマンドレットが用意されています。 

[Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) をローカル コンピューターにインストールして、すべての PowerShell セッションで使用することができます。 リンク先のページの手順に従って Azure の資格情報でサインインし、Az PowerShell モジュールをインストールします。

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>サインインのための対話型ダイアログを使用して Azure に接続する
```
Connect-AzAccount
Install-Module -Name Az
```
最新バージョンの PowerShellGet がインストールされていることを確認します。 次のコマンドを実行して、PowerShell を再度開きます。

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>Az.FrontDoor モジュールをインストールする 

```
Install-Module -Name Az.FrontDoor
```

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure で、関連するリソースをリソース グループに割り当てます。 この例では、[New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup) を使用してリソース グループを作成します。

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

## <a name="create-a-new-waf-policy-with-custom-response"></a>カスタム応答が設定された新しい WAF ポリシーの作成 

カスタム応答の状態コードが 405、メッセージが "**You are blocked**" に設定されている新しい WAF ポリシーを作成する例を次に示します。 [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) を使用します。

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

[Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy) を使用して、既存の WAF ポリシーのカスタム応答コードまたは応答本文の設定を変更します。

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title> Forbidden</title></head><body></body></html>"
```

## <a name="next-steps"></a>次の手順
- [Azure Front Door での Web アプリケーション ファイアウォール](../afds/afds-overview.md)について詳しく確認します