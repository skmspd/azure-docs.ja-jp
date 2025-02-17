---
title: Azure PowerShell スクリプト サンプル - 別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポート/コピーする | Microsoft Docs
description: Azure PowerShell スクリプト サンプル - 同じまたは別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポート/コピーする
services: virtual-machines-linux
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/17/2018
ms.author: ramankum
ms.openlocfilehash: abf32d857285de841fb12fb6f9d281afba5b2539
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750000"
---
# <a name="exportcopy-the-vhd-of-a-managed-disk-to-a-storage-account-in-different-region-with-powershell"></a>別の地域のストレージ アカウントに、マネージド ディスクの VHD を PowerShell でエクスポート/コピーする

このスクリプトでは、別の地域のストレージ アカウントに、マネージド ディスクの VHD をエクスポートします。 最初にマネージド ディスクの SAS URI を生成し、続いてそれを使用して、基盤となる VHD を別の地域のストレージ アカウントにコピーします。 このスクリプトを使用して、地域拡張のために、マネージド ディスクを別の地域にコピーします。  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 

## <a name="sample-script"></a>サンプル スクリプト

[!code-powershell[main](../../../powershell_scripts/virtual-machine/copy-managed-disks-vhd-to-storage-account/copy-managed-disks-vhd-to-storage-account.ps1 "Copy the VHD of a managed disk")]


## <a name="script-explanation"></a>スクリプトの説明

このスクリプトでは、次のコマンドを使用してマネージド ディスクの SAS URI を生成し、SAS URI を使用して基盤となる VHD をストレージ アカウントにコピーします。 表内の各コマンドは、コマンド特定のドキュメントにリンクされています。

| command | メモ |
|---|---|
| [Grant-AzDiskAccess](https://docs.microsoft.com/powershell/module/az.compute/grant-azdiskaccess) | 基盤となる VHD をストレージ アカウントにコピーするために使用される、マネージド ディスク用の SAS URI を生成します。 |
| [New-AzureStorageContext](https://docs.microsoft.com/powershell/module/azure.storage/New-AzureStorageContext) | アカウント名とキーを使ってストレージ アカウントのコンテキストを作成します。 このコンテキストを使って、ストレージ アカウントに対する読み取り/書き込み操作を実行できます。 |
| [Start-AzureStorageBlobCopy](https://docs.microsoft.com/powershell/module/azure.storage/Start-AzureStorageBlobCopy) | スナップショットの基になる VHD をストレージ アカウントにコピーします。 |

## <a name="next-steps"></a>次の手順

[VHD からマネージド ディスクを作成する](virtual-machines-linux-powershell-sample-create-managed-disk-from-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[マネージド ディスクから仮想マシンを作成する](./virtual-machines-linux-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Azure PowerShell モジュールの詳細については、[Azure PowerShell のドキュメント](/powershell/azure/overview)を参照してください。

その他の仮想マシン用の PowerShell サンプル スクリプトは、[Azure Linux VM のドキュメント](../linux/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)にあります。