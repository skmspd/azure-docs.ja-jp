---
title: Azure VPN Gateway パケット キャプチャ |Microsoft Docs
description: VPN ゲートウェイで使用できるパケット キャプチャ機能について説明します。
services: vpn-gateway
author: radwiv
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: radwiv
ms.openlocfilehash: 0957fabf58a68efe9e215b390d28dbf160574963
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511700"
---
# <a name="configure-packet-captures-for-vpn-gateways"></a>VPN ゲートウェイのパケット キャプチャを構成する

接続とパフォーマンスに関連する問題は多くの場合複雑で、問題の原因を絞り込むのにはかなりの時間と労力を要します。 パケット キャプチャ機能を使用すれば、問題の範囲をネットワークの特定の部分 (ネットワークの顧客側、ネットワークの Azure 側、またはその間など) に絞り込むための時間を短縮できます。 問題を絞り込めば、デバッグと修復の作業がはるかに効率的になります。

パケット キャプチャのツールには、一般的に入手できるものもいくつかあります。 ただし多くの場合、これらのツールを使用して関連するパケット キャプチャを取得する作業は、大量のトラフィックが存在する場合には非常に面倒です。 VPN ゲートウェイ パケット キャプチャで提供されるフィルター機能は、大きな差別化要因になります。 VPN ゲートウェイ パケット キャプチャは、一般的に入手可能なパケット キャプチャ ツールに加えて使用することもできます。

## <a name="vpn-gateway-packet-capture-filtering-capabilities"></a>VPN ゲートウェイ パケット キャプチャのフィルター処理機能

VPN ゲートウェイ パケット キャプチャは、お客様のニーズに応じて、ゲートウェイまたは特定の接続に対して実行できます。 また、複数のトンネルに対するパケット キャプチャを同時に実行することもできます。 VPN ゲートウェイでフィルター処理を使ってキャプチャできるのは、単一方向または双方向のトラフィック、IKE と ESP トラフィック、および内部パケットです。

5 タプルのフィルター (ソース サブネット、宛先サブネット、発信元ポート、接続先ポート、プロトコル) と TCP フラグ (SYN、ACK、FIN、URG、PSH、RST) を使用することで、大量のトラフィックを対象に、問題を効果的に特定することができます。

## <a name="setup-packet-capture-using-powershell"></a>PowerShell を使用してパケッ トキャプチャを設定する

パケット キャプチャを開始および停止する PowerShell コマンドについては、次の例を参照してください。 パラメーター オプション (フィルターの作成方法など) の詳細については、こちらの PowerShell [ドキュメント](https://docs.microsoft.com/powershell/module/az.network/start-azvirtualnetworkgatewaypacketcapture)を参照してください。

### <a name="start-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualnetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName"
```

省略可能なパラメーター **-FilterData** を使用してフィルターを適用できます。

### <a name="stop-packet-capture-for-a-vpn-gateway"></a>VPN ゲートウェイのパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayName" -SasUrl "YourSASURL"
```

### <a name="start-packet-capture-for-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを開始する

```azurepowershell-interactive
Start-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName"
```

省略可能なパラメーター **-FilterData** を使用してフィルターを適用できます。

### <a name="stop-packet-capture-on-a-vpn-gateway-connection"></a>VPN ゲートウェイ接続のパケット キャプチャを停止する

```azurepowershell-interactive
Stop-AzVirtualNetworkGatewayConnectionPacketCapture -ResourceGroupName "YourResourceGroupName" -Name "YourVPNGatewayConnectionName" -SasUrl "YourSASURL"
```

## <a name="key-considerations"></a>重要な考慮事項

- パケット キャプチャの実行は、パフォーマンスに影響を与える可能性があります。 不要な場合は、パケット キャプチャを停止してください。
- 推奨される最小パケット キャプチャ期間は 600 秒です。 パケット キャプチャ期間を短くすると、パス上の複数のコンポーネント間で同期の問題が発生し、完全なデータを取得できない場合があります。
- パケット キャプチャ データ ファイルは、PCAP または ETL 形式で生成されます。 データの内容を理解するには、Netmon パーサーが必要になることがあります。

## <a name="next-steps"></a>次の手順

VPN Gateway の詳細については、「[VPN Gateway について](vpn-gateway-about-vpngateways.md)」をご覧ください