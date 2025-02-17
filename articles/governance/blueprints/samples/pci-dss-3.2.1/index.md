---
title: PCI-DSS v3.2.1 のブループリント サンプル - 概要
description: Payment Card Industry Data Security Standard v3.2.1 のブループリント サンプルの概要。 このブループリント サンプルは、お客様が特定のコントロールを評価するのに役立ちます。
ms.date: 06/24/2019
ms.topic: sample
ms.openlocfilehash: 68623769764af658533571e171981fd77f5b3c02
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/22/2019
ms.locfileid: "74404026"
---
# <a name="overview-of-the-pci-dss-v321-blueprint-sample"></a>PCI-DSS v3.2.1 のブループリント サンプルの概要

PCI-DSS v3.2.1 のブループリント サンプルは、PCI-DSS v3.2.1 へのコンプライアンスを実現するのに役立つ一連のポリシーです。 このブループリントは、PCI-DSS ワークロードがあるクラウドベースの環境をお客様が制御する助けになります。
PCI-DSS のブループリントでは、この認定が必要とされる、Azure でデプロイされたアーキテクチャのために、一連の主要なポリシーをデプロイします。

## <a name="control-mapping"></a>コントロール マッピング

コントロール マッピングのセクションでは、このイニシアチブ内に含まれるポリシーについてと、それらのポリシーが、PCI-DSS v3.2.1 によって定義されたさまざまなコントロールに対応するうえでどのように役立つかについて詳しく説明します。 リソースはアーキテクチャに割り当てられると、割り当て済みのポリシーに違反していないかどうかを Azure Policy によって評価されます。

このブループリントを割り当てた後、Azure Policy のコンプライアンス ダッシュボードで Azure 環境のコンプライアンス レベルを確認します。

## <a name="next-steps"></a>次の手順

PCI-DSS v3.2.1 のブループリント サンプルの概要を確認しました。 次に、コントロール マッピングと、このサンプルをデプロイする方法を確認するには、次の記事を参照してください。

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 ブループリント - コントロール マッピング](./control-mapping.md)
> [PCI-DSS v3.2.1 ブループリント - デプロイ手順](./deploy.md)

ブループリントとその使用方法に関するその他の記事:

- [ブループリントのライフサイクル](../../concepts/lifecycle.md)を参照する。
- [静的および動的パラメーター](../../concepts/parameters.md)の使用方法を理解する。
- [ブループリントの優先順位](../../concepts/sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](../../concepts/resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../../how-to/update-existing-assignments.md)方法を参照する。