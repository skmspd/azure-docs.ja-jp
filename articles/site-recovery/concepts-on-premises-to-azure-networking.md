---
title: Azure Site Recovery を使用したオンプレミスから Azure へのフェールオーバー後の Azure VM への接続
description: Azure Site Recovery を使用してオンプレミスから Azure へのフェールオーバー後に Azure VM に接続する方法について説明します
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/13/2019
ms.author: mayg
ms.openlocfilehash: f535a681ac3508aafc2823bcc9b9ae7f22cc2d8e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333040"
---
# <a name="connect-to-azure-vms-after-failover-from-on-premises"></a>オンプレミスからフェールオーバー後に Azure VM に接続する 


この記事では、フェールオーバー後に Azure VM に正常に接続できるように接続を設定する方法について説明します。

オンプレミスの仮想マシン (VM) と物理サーバーのディザスター リカバリーを Azure に対して設定すると、[Azure Site Recovery](site-recovery-overview.md) が Azure へのマシンのレプリケートを開始します。 その後、障害が発生したときに、オンプレミスのサイトから Azure にフェールオーバーできます。 フェールオーバーが発生すると、Site Recovery は、レプリケートされたオンプレミス データを使用して Azure VM を作成します。 ディザスター リカバリー計画の一環として、フェールオーバー後にこれらの Azure VM で実行されているアプリに接続する方法を理解する必要があります。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * フェールオーバー前にオンプレミスのマシンを準備する。
> * フェールオーバー後に Azure VM を準備する。 
> * フェールオーバー後に Azure VM で IP アドレスを維持する。
> * フェールオーバー後に Azure VM に新しい IP アドレスを割り当てる。

## <a name="prepare-on-premises-machines"></a>オンプレミスのマシンの準備

Azure VM への接続を確保するには、フェールオーバー前にオンプレミスのマシンを準備します。

### <a name="prepare-windows-machines"></a>Windows マシンの準備

オンプレミスの Windows マシンで、次の手順を実行します。

1. Windows 設定を構成します。 これには、すべての静的な固定ルートまたは WinHTTP プロキシを削除することと、ディスク SAN ポリシーを **OnlineAll** に設定することが含まれます。 [こちらの手順](../virtual-machines/windows/prepare-for-upload-vhd-image.md#set-windows-configurations-for-azure)に従います。

2. [こちらのサービス](../virtual-machines/windows/prepare-for-upload-vhd-image.md#check-the-windows-services) が実行されていることを確認します。

3. オンプレミスのマシンへのリモート接続を許可するために、リモート デスクトップ (RDP) を有効にします。 PowerShell を使用して RDP を有効にする[方法を確認してください](../virtual-machines/windows/prepare-for-upload-vhd-image.md#update-remote-desktop-registry-settings)。

4. フェールオーバー後にインターネット経由で Azure VM にアクセスするには、オンプレミスのマシン上の Windows ファイアウォールにおいて、パブリック プロファイルで TCP と UDP を許可し、すべてのプロファイルに対して許可されたアプリとして RDP を設定します。

5. フェールオーバー後にサイト間 VPN 経由で Azure VM にアクセスする場合は、オンプレミスのマシン上の Windows ファイアウォールで、ドメイン プロファイルとプライベート プロファイルに対して RDP を許可します。 RDP トラフィックを許可する[方法を確認してください](../virtual-machines/windows/prepare-for-upload-vhd-image.md#configure-windows-firewall-rules)。
6. フェールオーバーを開始するときに、実行待ちの Windows 更新プログラムがオンプレミス VM にないことを確認してください。 存在する場合、フェールオーバー後に Azure VM で更新プログラムのインストールが開始されることがあり、更新が完了するまで VM にサインインできません。

### <a name="prepare-linux-machines"></a>Linux マシンの準備

オンプレミスの Linux マシンで、次の手順を実行します。

1. システム起動時に Secure Shell サービスが自動的に開始される設定になっていることを確認します。
2. ファイアウォール規則で SSH 接続が許可されていることを確認します。


## <a name="configure-azure-vms-after-failover"></a>フェールオーバー後に Azure VM を構成する

フェールオーバー後に、作成された Azure VM で次の手順を実行します。

1. インターネット経由で VM に接続するには、パブリック IP アドレスを VM に割り当てます。 オンプレミスのマシンに使用したパブリック IP アドレスと同じものを Azure VM に対して使用することはできません。 [詳細情報](../virtual-network/virtual-network-public-ip-address.md)
2. VM 上のネットワーク セキュリティ グループ (NSG) 規則で RDP または SSH ポートへの着信接続が許可されていることを確認します。
3. [[ブート診断]](../virtual-machines/troubleshooting/boot-diagnostics.md#enable-boot-diagnostics-on-existing-virtual-machine) をオンにして VM を表示します。


> [!NOTE]
> Azure Bastion サービスでは、Azure VM へのプライベート RDP および SSH アクセスが提供されます。 このサービスに関する[詳細情報](../bastion/bastion-overview.md)を参照してください。

## <a name="set-a-public-ip-address"></a>パブリック IP アドレスの設定

Azure VM にパブリック IP アドレスを手動で割り当てる代わりに、Site Recovery [復旧計画](site-recovery-create-recovery-plans.md)のスクリプトまたは Azure Automation Runbook を使用して、フェールオーバー時にアドレスを割り当てることができます。または、Azure Traffic Manager を使用して DNS レベルのルーティングを設定することもできます。 パブリック アドレスの設定に関する[詳細情報](concepts-public-ip-address-with-site-recovery.md)を参照してください。


## <a name="assign-an-internal-address"></a>内部アドレスの割り当て

フェールオーバー後に Azure VM の内部 IP アドレスを設定するには、いくつかのオプションがあります。

- **同じ IP アドレスを保持する**:オンプレミスのマシンに割り当てられている IP アドレスと同じものを Azure VM で使用できます。
- **異なる IP アドレスを使用する**:別の IP アドレスを Azure VM に使用できます。


## <a name="retain-ip-addresses"></a>IP アドレスを維持する

Site Recovery では、Azure へのフェールオーバー時に同じ IP アドレスを維持できます。 同じ IP アドレスを維持すると、フェールオーバー後の潜在的なネットワークの問題を回避できますが、若干複雑になります。

- ターゲットの Azure VM がオンプレミスのサイトと同じ IP アドレス/サブネットを使用している場合は、アドレスが重複しているため、サイト間 VPN 接続または ExpressRoute を使用してそれらを接続することはできません。 サブネットは一意である必要があります。
- Azure VM でアプリを利用できるように、フェールオーバー後にオンプレミスから Azure への接続が必要です。 Azure では、拡張された VLAN はサポートされないため、IP アドレスを維持する場合は、オンプレミスのマシンに加えて、サブネット全体をフェールオーバーすることで、IP アドレス空間を Azure に引き継ぐ必要があります。
- サブネットのフェールオーバーにより、特定のサブネットがオンプレミスと Azure で同時に使用できないことが保証されます。

IP アドレスを維持するには、次の手順を実行する必要があります。

- レプリケートされたアイテムの [コンピューティングとネットワーク] プロパティで、ターゲット Azure VM のネットワークと IP アドレス指定を設定して、オンプレミスの設定をミラーリングします。
- サブネットは、ディザスター リカバリー プロセスの一部として管理される必要があります。 オンプレミスのネットワークと一致する Azure VNet が必要です。フェールオーバー後のネットワーク ルートは、サブネットの Azure への移動と新しい IP アドレスの場所を反映するように変更される必要があります。  

### <a name="failover-example"></a>フェールオーバーの例

1 つ例を見てみましょう。

- 架空の会社である Woodgrove Bank は、オンプレミスでビジネス アプリをホストし、Azure でそれらのモバイル アプリをホストしています。
- サイト間 VPN 経由でオンプレミスから Azure に接続します。 
- Woodgrove では、オンプレミスのマシンを Azure にレプリケートするために、Site Recovery を使用しています。
- オンプレミスのアプリではハードコーディングされた IP アドレスが使用されるため、Azure で同じ IP アドレスを維持する必要があります。
- オンプレミスでアプリを実行しているマシンは、次の 3 つのサブネットで実行されています。
    - 192.168.1.0/24
    - 192.168.2.0/24
    - 192.168.3.0/24
- Azure で実行されているアプリは、次の 2 つのサブネット内の Azure VNet **Azure ネットワーク**にあります。
- 172.16.1.0/24
- 172.16.2.0/24

これらのアドレスを維持するために、同社では次の操作を実行します。

1. レプリケーションを有効にするときに、マシンが **Azure ネットワーク**にレプリケートするように指定します。
2. Azure で **復旧ネットワーク**を作成します。 この VNet は、オンプレミス ネットワーク内の 192.168.1.0/24 サブネットをミラーリングします。
3. Woodgrove は、2 つのネットワークをつなぐ [VNet 間接続](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)を設定します。 

    > [!NOTE]
    > アプリケーションの要件に応じて、フェールオーバー前に VNet 間接続を Site Recovery [復旧計画](site-recovery-create-recovery-plans.md)の手動の手順/スクリプト化した手順/Azure Automation Runbook として設定できます。または、フェールオーバーが完了した後に設定します。

4. フェールオーバー前に、次の手順で説明するように、Site Recovery のマシン プロパティで、ターゲット IP アドレスをオンプレミスのマシンのアドレスに設定します。
5. フェールオーバー後、Azure VM は同じ IP アドレスを使用して作成されます。 Woodgrove は、VNet ピアリングを使用して (トランジット接続を有効にして) **Azure ネットワーク**から**復旧ネットワーク** VNet に接続します。
6. オンプレミスで、Woodgrove はネットワークの変更を行う必要があります (192.168.1.0/24 が Azure に移動したことを反映するためのルートの変更など)。  

**フェールオーバー前のインフラストラクチャ**

![サブネットのフェールオーバー前](./media/site-recovery-network-design/network-design7.png)


**フェールオーバー後のインフラストラクチャ**

![サブネットのフェールオーバー後](./media/site-recovery-network-design/network-design9.png)


### <a name="set-target-network-settings"></a>ターゲット ネットワークの設定

フェールオーバー前に、ターゲット Azure VM のネットワーク設定と IP アドレスを指定します。

1.  [Recovery Services コンテナー]-> **[レプリケートされたアイテム]** で、オンプレミス マシンを選択します。
2. そのマシンの **[コンピューティングとネットワーク]** ページで **[編集]** をクリックし、ターゲット Azure VM のネットワークおよびアダプターの設定を構成します。
3. **[ネットワークのプロパティ]** で、フェールオーバー後の作成時に Azure VM が配置されるターゲット ネットワークを選択します。
4. **[ネットワーク インターフェイス]** で、ターゲット ネットワークのネットワーク アダプターを構成します。 既定では、そのオンプレミス マシンで検出されたすべての NIC が Site Recovery に表示されます。
    - **[ターゲット ネットワーク インターフェイスの種類]** では、各 NIC を **[プライマリ]** 、 **[セカンダリ]** 、または **[作成しない]** (ターゲット ネットワーク内で特定の NIC が不要な場合) のいずれかとして設定できます。 1 つのネットワーク アダプターをフェールオーバーのプライマリとして設定する必要があります。 ターゲット ネットワークを変更すると、Azure VM のすべての NIC に影響を与えることに注意してください。
    - NIC 名をクリックして、Azure VM がデプロイされるサブネットを指定します。
    - ターゲット Azure VM に割り当てるプライベート IP アドレスで **[動的]** を上書きします。 IP アドレスが指定されていない場合、Site Recovery ではフェールオーバー時にサブネット内の次に使用可能な IP アドレスを NIC に割り当てます。
    - Azure へのオンプレミスのフェールオーバーのための NIC の管理に関する[詳細情報](site-recovery-manage-network-interfaces-on-premises-to-azure.md)を参照してください。


## <a name="get-new-ip-addresses"></a>新しい IP アドレスの取得

このシナリオでは、Azure VM はフェールオーバー後に新しい IP アドレスを取得します。 DNS の更新により、フェールオーバーされたマシンのレコードが Azure VM の IP アドレスを指すように更新されます。



## <a name="next-steps"></a>次の手順
オンプレミスの Active Directory と DNS の Azure へのレプリケートについて[確認します](site-recovery-active-directory.md)。


