---
title: 仮想マシン スケール セットで Azure Disk Encryption を有効にする
description: この記事では、仮想マシン スケール セットで Microsoft Azure Disk Encryption を有効にする手順について説明します
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529967"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>仮想マシン スケール セットの Azure Disk Encryption

Azure Disk Encryption は、仮想マシンの OS とデータ ディスクのボリューム暗号化を提供し、データを保護して、組織のセキュリティおよびコンプライアンス コミットメントを満たすのに役立ちます。 詳細については、[Azure Disk Encryption: Linux VM](../virtual-machines/linux/disk-encryption-overview.md) および [Azure Disk Encryption: Windows VM](../virtual-machines/windows/disk-encryption-overview.md) に関するページを参照してください。  

また、Azure Disk Encryption は、Windows および Linux の仮想マシン スケール セットに、以下のインスタンスで有効にすることもできます。
- マネージド ディスクで作成されたスケール セット。 Azure Disk encryption は、ネイティブ (アンマネージド) ディスク スケール セットの場合はサポートされません。
- Windows スケール セット内の OS とデータ ボリューム。
- Linux スケール セット内のデータ ボリューム。 OS ディスク暗号化は Linux スケール セットに対しては現在はサポートされません。

仮想マシン スケール セットでの Azure Disk Encryption の基礎については、[Azure CLI を使用した仮想マシン スケール セットの暗号化](disk-encryption-cli.md)または [Azure CLI を使用した仮想マシン スケール セットの暗号化](disk-encryption-powershell.md)に関するチュートリアルを参照してください。

## <a name="next-steps"></a>次の手順

- [Azure Resource Manager を使用して仮想マシン スケール セットを暗号化する](disk-encryption-azure-resource-manager.md)
- [Azure Disk Encryption のキー コンテナーを作成して構成する](disk-encryption-key-vault.md)
- [Azure Disk Encryption を仮想マシン スケール セットの拡張機能のシーケンス処理と共に使用する](disk-encryption-extension-sequencing.md)