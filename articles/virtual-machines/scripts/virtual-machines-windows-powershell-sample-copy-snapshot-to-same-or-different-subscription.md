---
title: Azure PowerShell サンプル スクリプト - マネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する
description: Azure PowerShell サンプル スクリプト - マネージド ディスクのスナップショットを同じまたは別のサブスクリプションにコピー (移動) する
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/28/2019
ms.author: ramankum
ms.openlocfilehash: 13add6614af8d3ad76e820228b748c0084a29876
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74039798"
---
# <a name="copy-snapshot-of-a-managed-disk-in-same-subscription-or-different-subscription-with-powershell"></a>マネージド ディスクのスナップショットを PowerShell で同じサブスクリプションまたは別のサブスクリプションにコピーする

このスクリプトは、同じまたは別のサブスクリプションに、マネージド ディスクのスナップショットをコピーします。 次のシナリオでこのスクリプトを使用します。

1. Premium ストレージ (Premium_LRS) のスナップショットを Standard ストレージ (Standard_LRS または Standard_ZRS) に移行してコストを削減する。
1. 信頼性が高い ZRS ストレージを利用するために、ローカル冗長ストレージ (Premium_LRS、Standard_LRS) からゾーンの冗長ストレージ (Standard_ZRS) にスナップショットを移行する。
1. 長期間の保存のために、スナップショットを同じリージョン内の別のサブスクリプションに移動する。

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-snapshot-to-same-or-different-subscription/copy-snapshot-to-same-or-different-subscription.ps1 "Copy snapshot")]

## <a name="script-explanation"></a>スクリプトの説明

このスクリプトは、コピー元スナップショットの ID を使って、コピー先のサブスクリプションにスナップショットを作成します。そのために、以下のコマンドが使われています。 表内の各コマンドは、それぞれのドキュメントにリンクされています。

| command | メモ |
|---|---|
| [New-AzSnapshotConfig](https://docs.microsoft.com/powershell/module/az.compute/New-AzSnapshotConfig) | スナップショットの作成に使用するスナップショット構成を作成します。 この構成には、親スナップショットのリソース ID および親スナップショットと同じ場所が含まれます。  |
| [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/New-AzDisk) | パラメーターとして渡されたスナップショット構成、スナップショット名、リソース グループ名を使ってスナップショットを作成します。 |

## <a name="next-steps"></a>次の手順

[スナップショットから仮想マシンを作成する](./virtual-machines-windows-powershell-sample-create-vm-from-snapshot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Windows VM のドキュメント](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)にあります。
