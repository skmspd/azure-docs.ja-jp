---
title: Azure Virtual WAN で ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Virtual WAN を使用して、ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を作成する方法を学習します。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 10/11/2019
ms.author: cherylmc
Customer intent: I want to connect my on-premises networks to my VNets using S2S VPN connection over my ExpressRoute private peering using Azure Virtual WAN.
ms.openlocfilehash: 6272d6fe6f8c35c06a8121e10be2dd5a2e5512a8
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73510960"
---
# <a name="create-a-site-to-site-vpn-connection-over-expressroute-private-peering-using-azure-virtual-wan"></a>Azure Virtual WAN を使用して ExpressRoute プライベート ピアリング経由のサイト間 VPN 接続を作成する

この記事では、Virtual WAN を使用して、ExpressRoute 回線のプライベート ピアリング経由で、オンプレミス ネットワークから Azure への IPsec/IKE VPN 接続を確立する方法を示します。 これにより、パブリック インターネットを経由したり、パブリック IP アドレスを使用したりすることなく、オンプレミス ネットワークと Azure 仮想ネットワークの間で暗号化されたトランジットを提供できます。

## <a name="topology-and-routing"></a>トポロジとルーティング

次の図は、ExpressRoute プライベート ピアリング接続を経由した VPN の例を示しています。

![ExpressRoute 経由の VPN](./media/vpn-over-expressroute/vwan-vpn-over-er.png)

この図は、ExpressRoute プライベート ピアリング経由で Azure ハブ VPN ゲートウェイに接続されたオンプレミス ネットワーク内のネットワークを示しています。 接続の確立は簡単です。

1. ExpressRoute 回線とプライベート ピアリングを使用して ExpressRoute 接続を確立する
2. このドキュメントで説明されているように VPN 接続を確立します

この構成の重要な側面は、オンプレミス ネットワークと Azure の間で、ExpressRoute パスと VPN パスの両方を経由してルーティングを行うことです。

### <a name="traffic-from-on-premises-networks-to-azure"></a>オンプレミス ネットワークから Azure へのトラフィック

オンプレミス ネットワークから Azure へのトラフィックに関しては、Azure プレフィックス (仮想ハブと、ハブに接続されているすべてのスポーク仮想ネットワークを含む) は、ExpressRoute プライベート ピアリング BGP と VPN BGP の両方を経由してアドバタイズされます。 この結果、オンプレミス ネットワークから Azure に向かう 2 つのネットワーク ルート (パス) が作成されます。1 つは IPsec で保護されたパスを経由し、もう 1 つは IPsec 保護**なし**で ExpressRoute を直接経由します。 通信に暗号化を確実に適用するには、図中の VPN 接続ネットワークに関して、オンプレミス VPN ゲートウェイ経由の Azure ルートが直接 ExpressRoute パスよりも優先されることを確認する必要があります。

### <a name="traffic-from-azure-to-on-premises-networks"></a>Azure からオンプレミス ネットワークへのトラフィック

Azure からオンプレミス ネットワークへのトラフィックにも同じ要件が適用されます。 IPsec パスを直接 ExpressRoute パス (IPsec なし) よりも確実に優先させるには、2 つのオプションがあります。

- VPN 接続ネットワークの VPN BGP セッションで、より具体的なプレフィックスをアドバタイズします。 "VPN 接続ネットワーク" を包含する広い範囲を ExpressRoute プライベート ピアリング経由でアドバタイズし、次に VPN BGP セッションでより具体的な範囲をアドバタイズすることができます。 たとえば、ExpressRoute 経由では 10.0.0.0/16 を、VPN 経由では 10.0.1.0/24 をアドバタイズします。

- VPN と ExpressRoute で、互いに切り離されたプレフィックスをアドバタイズします。 VPN 接続ネットワーク範囲が他の ExpressRoute 接続ネットワークから切り離されている場合、VPN と ExpressRoute の各 BGP セッションでそれぞれプレフィックスをアドバタイズできます。 たとえば、ExpressRoute 経由では 10.0.0.0/24 を、VPN 経由では 10.0.1.0/24 をアドバタイズします。

どちらの例でも Azure は、ExpressRoute 経由で直接、VPN 保護なしで送信するのではなく、VPN 接続経由で 10.0.1.0/24 にトラフィックを送信します。

> [!WARNING]
> ExpressRoute 接続と VPN 接続の両方を経由して**同じ**プレフィックスをアドバタイズした場合、Azure は **ExpressRoute パスを直接、VPN 保護なしで使用します**。
>

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [Before you begin](../../includes/virtual-wan-tutorial-vwan-before-include.md)]

## <a name="openvwan"></a>1.ゲートウェイを使用して仮想 WAN とハブを作成する

次に進む前に、以下の Azure リソースと、対応するオンプレミス構成が揃っている必要があります。

1. Azure 仮想 WAN
2. [ExpressRoute ゲートウェイ](virtual-wan-expressroute-portal.md)と [VPN ゲートウェイ](virtual-wan-site-to-site-portal.md)がある仮想 WAN ハブ

ExpressRoute の関連付けを使用して Azure 仮想 WAN とハブを作成する手順については、「[Azure Virtual WAN を使用して ExpressRoute の関連付けを作成する](virtual-wan-expressroute-portal.md)」を参照してください。仮想 WAN 内に VPN ゲートウェイを作成する手順については、「[Azure Virtual WAN を使用してサイト間接続を作成する](virtual-wan-site-to-site-portal.md)」を参照してください。

## <a name="site"></a>2.オンプレミス ネットワーク用のサイトを作成する

サイト リソースは、仮想 WAN 用の非 ExpressRoute VPN サイトと同じです。 重要な注意点として、この時点で、オンプレミス VPN デバイスの IP アドレスは、プライベート IP アドレスになる可能性もあれば、手順 1 で作成した、ExpressRoute プライベート ピアリング経由で到達可能なオンプレミス ネットワーク内のパブリック IP アドレスになる可能性もあります。

> [!NOTE]
> オンプレミス VPN デバイスの IP アドレスは、Azure ExpressRoute プライベート ピアリング経由で Virtual WAN ハブにアドバタイズされるアドレス プレフィックスの一部でなければなりません。
>

1. ブラウザーで Azure portal に移動します。 作成した WAN をクリックします。 [WAN] ページの **[接続]** で、 **[VPN sites]\(VPN サイト\)** をクリックして VPN サイト ページを開きます。

2. **[VPN サイト]** ページで **[+ サイトの作成]** をクリックします。

3. **[サイトの作成]** ページで、次のフィールドに入力します。

   * **[サブスクリプション]** - サブスクリプションを確認します。
   * **[リソース グループ]** - 使用するリソース グループを選択または作成します。
   * **[リージョン]** - VPN サイト リソースの Azure リージョン。
   * **[名前]** - オンプレミスのサイトの呼称。
   * **[デバイス ベンダー]** - オンプレミス VPN デバイスのベンダー。
   * **[ボーダー ゲートウェイ プロトコル]** - オンプレミス ネットワークで BGP を使用する場合は [Enable] (有効) を選択します。
   * **[プライベート アドレス空間]** - これは、オンプレミスのサイトにある IP アドレス空間です。 このアドレス空間宛てのトラフィックは、VPN ゲートウェイ経由でオンプレミス ネットワークにルーティングされます。
   * **[ハブ]** - この VPN サイトを接続する 1 つ以上のハブを選択します。 選択したハブには、既に VPN ゲートウェイが作成されている必要があります。

4. **[次へ: リンク >]** をクリックして VPN リンクを設定します。

   * **[リンク名]** - この接続の呼称。
   * **[プロバイダー名]** - このサイトのインターネット サービス プロバイダーの名前。 ExpressRoute オンプレミス ネットワークの場合、ExpressRoute サービス プロバイダーの名前です。
   * **[速度]** - インターネット サービス リンクまたは ExpressRoute 回線の速度。
   * **[IP アドレス]** - オンプレミスのサイトにある VPN デバイスのパブリック IP アドレス。 または、オンプレミスの ExpressRoute の場合は、ExpressRoute 経由の VPN デバイスのプライベート IP アドレスです。

   BGP が有効な場合、Azure でこのサイトに対して作成されたすべての接続に適用されます。 Virtual WAN 上で BGP を構成するのは、Azure VPN ゲートウェイ上で BGP を構成するのと同じです。 オンプレミスの BGP ピア アドレスをデバイス側のご使用の VPN または VPN サイトの VNet アドレス空間の IP アドレスと同じにすることは*できません*。 VPN デバイスでは BGP ピア IP に別の IP アドレスを使用してください。 デバイスのループバック インターフェイスに割り当てられたアドレスを使用できます。 ただし、APIPA (169.254.*x*.*x*) アドレスにすることは*できません*。 この場所を表している、対応するローカル ネットワーク ゲートウェイにこのアドレスを指定します。 BGP の前提条件については、「[BGP と Azure VPN Gateway について](../vpn-gateway/vpn-gateway-bgp-overview.md)」を参照してください。

5. **[次へ: 確認および作成 >]** をクリックして設定値を確認し、VPN サイトを作成します。 接続する **[ハブ]** を選択した場合、接続はオンプレミス ネットワークとハブ VPN ゲートウェイの間で確立されます。

## <a name="hub"></a>3.ExpressRoute を使用するように VPN 接続設定を更新する

VPN サイトを作成してハブに接続したら、次の手順を使用して、ExpressRoute プライベート ピアリングを使用するように接続を構成します。

1. 仮想 WAN リソース ページに戻り、ハブ リソースをクリックします。 または、VPN サイトから、接続されたハブに移動します。

2. **[接続]** で、 **[VPN (Site-to-Site)]\(VPN (サイト間)\)** をクリックします。

3. ExpressRoute 経由の VPN サイトで [...] をクリックし、 **[Edit VPN connection to this hub]\(このハブへの VPN 接続の編集\)** を選択します。

4. **[Use Azure Private IP address]\(Azure プライベート IP アドレスを使用する\)** で [はい] を選択します。 この設定は、パブリック IP アドレスの代わりに、この接続用のゲートウェイ上のハブ アドレス範囲内のプライベート IP アドレスを使用するようにハブ VPN ゲートウェイを構成します。 これにより、オンプレミス ネットワークからのトラフィックは、この VPN 接続にパブリック インターネットを使用するのではなく、ExpressRoute プライベート ピアリングのパスを通過することが保証されます。 次のスクリーンショットは設定ウィンドウを示しています。

   ![VPN 接続設定](./media/vpn-over-expressroute/vpn-link-configuration.png)
   
5. **[Save]** をクリックします。

保存されると、ハブ VPN ゲートウェイは、VPN ゲートウェイ上のプライベート IP アドレスを使用して、ExpressRoute 経由でオンプレミス VPN デバイスとの IPsec/IKE 接続を確立します。

## <a name="associate"></a>4.ハブ VPN ゲートウェイのプライベート IP アドレスを取得する

VPN デバイス構成をダウンロードして、ハブ VPN ゲートウェイのプライベート IP アドレスを取得します。 これらは、オンプレミスの VPN デバイスを構成するために必要です。

1. ハブのページで、 **[接続]** の下にある **[VPN (Site-to-Site)]\(VPN (サイト間)\)** をクリックします

2. [概要] ページの上部にある **[Download VPN Config]\(VPN 構成のダウンロード\)** をクリックします。Azure により、リソース グループ "microsoft-network-[location]" にストレージ アカウントが作成されます。ここで、location は WAN の場所です。 VPN デバイスに構成を適用した後は、このストレージ アカウントを削除できます。

3. ファイルの作成が完了したら、リンクをクリックしてファイルをダウンロードできます。

4. VPN デバイスに構成を適用します。

### <a name="understanding-the-vpn-device-configuration-file"></a>VPN デバイス構成ファイルについて

デバイス構成ファイルには、オンプレミスの VPN デバイスを構成するときに使用する構成が含まれています。 このファイルを表示すると、次の情報を確認できます。

* **vpnSiteConfiguration -** このセクションは、仮想 WAN に接続するサイトとして設定されたデバイスの詳細を示します。 ブランチ デバイスの名前とパブリック IP アドレスが含まれています。
* **vpnSiteConnections -** このセクションには、次の設定に関する情報が含まれています。

    * 仮想ハブ VNet の**アドレス空間**<br/>例:
           ```
           "AddressSpace":"10.51.230.0/24"
           ```
    * ハブに接続されている VNet の**アドレス空間**<br>例:
           ```
           "ConnectedSubnets":["10.51.231.0/24"]
            ```
    * 仮想ハブ vpngateway の **IP アドレス**。 vpngateway の各接続はアクティブ/アクティブ構成の 2 つのトンネルで構成されているため、このファイルには両方の IP アドレスが示されています。 この例では、各サイトに "Instance0" と "Instance1" があり、これらはパブリック IP アドレスではなくプライベート IP アドレスです。<br>例:
           ``` 
           "Instance0":"10.51.230.4"
           "Instance1":"10.51.230.5"
           ```
    * BGP、事前共有キーなどの、**vpngateway 接続構成の詳細**。PSK は、自動的に生成される事前共有キーです。 カスタム PSK の [概要] ページで接続をいつでも編集できます。
  
### <a name="example-device-configuration-file"></a>デバイス構成ファイルの例

```
[{
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"5b096293-edc3-42f1-8f73-68c14a7c4db3"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-ER-site",
        "IPAddress":"172.24.127.211",
        "LinkName":"VPN-over-ER"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"10.51.230.4",
            "Instance1":"10.51.230.5"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
    },
    {
      "configurationVersion":{
        "LastUpdatedTime":"2019-10-11T05:57:35.1803187Z",
        "Version":"fbdb34ea-45f8-425b-9bc2-4751c2c4fee0"
      },
      "vpnSiteConfiguration":{
        "Name":"VPN-over-INet-site",
        "IPAddress":"13.75.195.234",
        "LinkName":"VPN-over-INet"
      },
      "vpnSiteConnections":[{
        "hubConfiguration":{
          "AddressSpace":"10.51.230.0/24",
          "Region":"West US 2",
          "ConnectedSubnets":["10.51.231.0/24"]
        },
        "gatewayConfiguration":{
          "IpAddresses":{
            "Instance0":"51.143.63.104",
            "Instance1":"52.137.90.89"
          }
        },
        "connectionConfiguration":{
          "IsBgpEnabled":false,
          "PSK":"abc123",
          "IPsecParameters":{"SADataSizeInKilobytes":102400000,"SALifeTimeInSeconds":3600}
        }
      }]
}]
```

### <a name="configuring-your-vpn-device"></a>VPN デバイスの構成

デバイスを構成する手順が必要な場合は、[VPN デバイス構成スクリプトのページ](~/articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#configscripts)の手順を使用できます。このとき、次の点に注意してください。

* VPN デバイスのページに書かれている手順は Virtual WAN 用ではありませんが、構成ファイルの Virtual WAN の値を使用して VPN デバイスを手動で構成することができます。 
* VPN Gateway 用のダウンロード可能なデバイス構成スクリプトは、構成が異なるため、Virtual WAN では機能しません。
* 新しい Virtual WAN は、IKEv1 と IKEv2 の両方をサポートできます。
* Virtual WAN では、ルートベースの VPN デバイスとデバイスの手順のみを使用できます。

## <a name="viewwan"></a>5.仮想 WAN を表示する

1. 仮想 WAN に移動します。

2. [概要] ページのマップ上の各ポイントは、ハブを表します。 任意のポイントにカーソルを置くと、ハブの正常性の概要が表示されます。

3. ハブと接続のセクションでは、ハブの状態、サイト、リージョン、VPN 接続の状態、および入出力バイト数を表示できます。

## <a name="viewhealth"></a>6.リソースの正常性を表示する

1. WAN に移動します。

2. WAN のページの **[サポート + トラブルシューティング]** セクションで、 **[正常性]** をクリックしてリソースを表示します。

## <a name="connectmon"></a>7.接続を監視する

Azure VM とリモート サイト間の通信を監視するための接続を作成します。 接続モニターを設定する方法については、[ネットワーク通信の監視](~/articles/network-watcher/connection-monitor.md)に関するページを参照してください。 ソース フィールドは Azure の VM IP で、宛先 IP はサイト IP です。

## <a name="cleanup"></a>8.リソースのクリーンアップ

これらのリソースが不要になったら、[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) を使用して、リソース グループとその中のすべてのリソースを削除できます。 "myResourceGroup" をリソース グループの名前に置き換えて、次の PowerShell コマンドを実行します。

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>次の手順

この記事では、Virtual WAN を使用して ExpressRoute プライベート ピアリング経由の VPN 接続を作成する方法を説明しました。 Virtual WAN とその他の関連機能の詳細については、[Virtual WAN の概要](virtual-wan-about.md)に関するページを参照してください。
