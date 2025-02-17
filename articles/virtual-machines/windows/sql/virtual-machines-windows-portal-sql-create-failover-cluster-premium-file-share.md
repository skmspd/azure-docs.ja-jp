---
title: Premium ファイル共有を使用した SQL Server FCI - Azure Virtual Machines
description: この記事では、Azure Virtual Machines で Premium ファイル共有を使用して SQL Server フェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: cebfb5315a7a0b7cf92dbb9e3e77ff4c35851aa8
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757818"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Azure Virtual Machines で Premium ファイル共有を使用して SQL Server フェールオーバー クラスター インスタンスを構成する

この記事では、[Premium ファイル共有](../../../storage/files/storage-how-to-create-premium-fileshare.md)を使用して Azure Virtual Machines で SQL Server フェールオーバー クラスター インスタンス (FCI) を作成する方法について説明します。 

Premium ファイル共有は、SSD によってバックアップされる、継続的に待機時間の短いファイル共有であり、Windows Server (2012 以降) 上の SQL Server (2012 以降) のフェールオーバー クラスター インスタンスとの使用が完全にサポートされています。 Premium ファイル共有を使用すると、柔軟性が向上し、ダウンタイムなしでファイル共有のサイズを変更したり拡張したりすることができます。 


## <a name="before-you-begin"></a>開始する前に

知っておく必要がある点と、実行前に用意が必要なことがいくつかあります。

次のテクノロジについて、運用上の理解が必要です。

- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

1 つの重要な相違点として、Azure IaaS VM フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを使用することをお勧めしています。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートで、1 つのネットワークでしかノードに到達できないという警告が出されますが、Azure IaaS VM フェールオーバー クラスターではこの警告は無視して構いません。 

さらに、次のテクノロジの概要について理解しておくことが必要です。

- [Azure Premium ファイル共有](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure リソース グループ](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> 現時点では、Azure 仮想マシン上の SQL Server フェールオーバー クラスター インスタンスは、[SQL Server IaaS エージェント拡張機能](virtual-machines-windows-sql-server-agent-extension.md)の[軽量](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)管理モードでのみサポートされています。 完全拡張機能モードから軽量に変更するには、対応する VM の **SQL 仮想マシン** リソースを削除し、それらを[軽量](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)モードの SQL VM リソース プロバイダーに登録します。 Azure portal を使用して **SQL 仮想マシン** リソースを削除するときに、正しい仮想マシンの横のチェックボックスをオフにします。 完全拡張機能では、自動バックアップ、パッチ適用、高度なポータル管理などの機能がサポートされます。 エージェントを[軽量](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider)管理モードで再インストールすると、これらの機能は SQL VM で動作しなくなります。

### <a name="workload-consideration"></a>ワークロードに関する考慮事項

Premium ファイル共有は、多くのワークロードのニーズを満たす IOPS とスループット容量を提供します。 ただし、IO 集中型ワークロードの場合は、マネージド Premium ディスクまたは Ultra Disk に基づく、[記憶域スペース ダイレクトを使用した SQL Server FCI](virtual-machines-windows-portal-sql-create-failover-cluster.md) を検討してください。  

デプロイまたは移行を開始する前に、現在の環境の IOPS アクティビティをチェックし、必要な IOPS が Premium ファイルで提供されることを確認します。 Windows パフォーマンス モニターのディスク カウンターを使用し、SQL Server のデータ、ログ、および一時 DB ファイルに必要な合計 IOPS (ディスク転送数/秒) とスループット (ディスク バイト数/秒) を監視します。 多くのワークロードにはバースティング IO があるため、使用率が高い時間帯にチェックして、最大 IOPS と平均 IOPS をメモしておくことをお勧めします。 Premium ファイル共有は、共有のサイズに基づいて IOPS を提供します。 また、Premium ファイルには、最大 1 時間、IO をベースライン量の 3 倍にバーストできる無料バースティングも用意されています。 

Premium ファイル共有のパフォーマンスの詳細については、「[ファイル共有のパフォーマンス レベル](https://docs.microsoft.com/azure/storage/files/storage-files-planning#file-share-performance-tiers)」を参照してください。 

### <a name="licensing-and-pricing"></a>ライセンスと価格

Azure Virtual Machines では、従量課金制 (PAYG) またはライセンス持ち込み (BYOL) VM イメージを使用して SQL Server の使用許可を与えることができます。 選択するイメージの種類が課金方法に影響を与えます。

PAYG ライセンスでは、Azure Virtual Machines 上の SQL Server のフェールオーバー クラスター インスタンス (FCI) により、FCI のすべてのノード (パッシブ ノードを含む) に対する課金が発生します。 詳細については、「[SQL Server Enterprise Virtual Machines の料金](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/)」を参照してください。 

ソフトウェア アシュアランスの Enterprise Agreement を締結している顧客には、アクティブ ノードごとに 1 つの無料のパッシブ FCI ノードを使用する権利があります。 Azure でこの利点を利用するには、BYOL VM イメージを使用した後、FCI のアクティブ ノードとパッシブ ノードの両方で同じライセンスを使用します。 詳細については、[Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx) に関するページを参照してください。

SQL Server on Azure Virtual Machines の PAYG ライセンスと BYOL ライセンスを比較するには、[SQL VM の概要](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)に関するページを参照してください。

SQL Server のライセンスに関する完全な情報については、[価格](https://www.microsoft.com/sql-server/sql-server-2017-pricing)に関するページを参照してください。

### <a name="limitations"></a>制限事項

- Filestream は、Premium ファイル共有のフェールオーバー クラスターではサポートされていません。 Filestream を使用するには、[記憶域スペース ダイレクト](virtual-machines-windows-portal-sql-create-failover-cluster.md)を使用してクラスターをデプロイします。 

## <a name="prerequisites"></a>前提条件 

この記事の手順を実行する前に、次のものが必要です。

- Microsoft Azure サブスクリプションが必要です。
- Azure Virtual Machines 上の Windows ドメイン。
- Azure 仮想マシン内にオブジェクトを作成する権限を持つアカウント。
- 十分な IP アドレス空間を持つ、次のコンポーネント用の Azure 仮想ネットワークとサブネット。
   - 両方の仮想マシン。
   - フェールオーバー クラスターの IP アドレス。
   - 各 FCI の IP アドレス。
- Azure ネットワーク上で構成された、ドメイン コントローラーを指す DNS。
- データ ファイル用のデータベースのストレージ クォータに基づいた [Premium ファイル共有](../../../storage/files/storage-how-to-create-premium-fileshare.md)。 
- データ ファイルに使用される Premium ファイル共有とは異なるバックアップ用のファイル共有。 このファイル共有は、Standard または Premium のいずれかになります。 

これらの前提条件が整ったら、フェールオーバー クラスターの構築を開始できます。 最初の手順で、仮想マシンを作成します。

## <a name="step-1-create-virtual-machines"></a>手順 1:仮想マシンを作成する

1. お使いのサブスクリプションで、[Azure Portal](https://portal.azure.com) にログインします。

1. [Azure 可用性セットを作成します](../tutorial-availability-sets.md)。

   可用性セットを利用すると、障害ドメインと更新ドメインの仮想マシンがグループ化されます。 可用性セットがあると、アプリケーションがネットワーク スイッチ、サーバーのラックの電源装置など、単一障害点の影響を受けないようにすることができます。

   仮想マシンのリソース グループを作成していない場合、これは Azure 可用性セットの作成時に行います。 可用性セットの作成に Azure Portal を使用する場合は、次の手順を実行します。

   - Azure Portal で **[+]** をクリックし、Azure Marketplace を開きます。 "**可用性セット**" を検索します。
   - **[可用性セット]** をクリックします。
   - **Create** をクリックしてください。
   - **[可用性セットの作成]** ブレードで、次の値を設定します。
      - **Name**:可用性セットの名前。
      - **サブスクリプション**:Azure サブスクリプション。
      - **[リソース グループ]** :既存のグループを使用する場合は、 **[既存のものを使用]** をクリックし、ドロップダウン リストからグループを選択します。 または、 **[新規作成]** を選択し、グループの名前を入力します。
      - **[場所]** :仮想マシンを作成する予定の場所を設定します。
      - **[障害ドメイン]** : 既定値 (3) を使用します。
      - **[更新ドメイン]** : 既定値 (5) を使用します。
   - **[作成]** をクリックして可用性セットを作成します。

1. 可用性セット内に仮想マシンを作成します。

   Azure 可用性セット内に 2 つの SQL Server 仮想マシンをプロビジョニングします。 手順については、「[Azure Portal での SQL Server 仮想マシンのプロビジョニング](virtual-machines-windows-portal-sql-server-provision.md)」を参照してください。

   両方の仮想マシンを配置します。

   - 可用性セットと同じ Azure リソース グループ内。
   - ドメイン コントローラーと同じネットワーク内。
   - 両方の仮想マシン、およびこのクラスター上で最終的に使用するすべての FCI 用に十分な IP アドレス空間を持つサブネット内。
   - Azure 可用性セット内。   

      >[!IMPORTANT]
      >仮想マシンを作成した後に可用性セットを設定または変更することはできません。

   Azure Marketplace からイメージを選択します。 Windows Server と SQL Server、または Windows Server だけを含む Marketplace イメージを使用できます。 詳細については、「[Azure Virtual Machines における SQL Server の概要](virtual-machines-windows-sql-server-iaas-overview.md)」を参照してください。

   Azure ギャラリーの SQL Server の公式イメージには、インストール済みの SQL Server インスタンス、SQL Server のインストール ソフトウェア、必要なキーが含まれます。

   >[!IMPORTANT]
   > 仮想マシンを作成した後、事前にインストールされていたスタンドアロンの SQL Server インスタンスを削除します。 フェールオーバー クラスターと Premium ファイル共有を記憶域として構成したら、プレインストールされた SQL Server メディアを使用して SQL Server FCI を作成します。 

   または、オペレーティング システムだけを含む Azure Marketplace イメージを使用することができます。 フェールオーバー クラスターと Premium ファイル共有を記憶域として構成した後、**Windows Server 2016 Datacenter** イメージを選択し、SQL Server FCI をインストールします。 このイメージには、SQL Server インストール メディアは含まれません。 各サーバーへの SQL Server のインストールを実行できる場所に、インストール メディアを配置します。 

1. Azure によって仮想マシンが作成されたら、RDP で各仮想マシンに接続します。

   RDP で最初に仮想マシンに接続する際、コンピューターによって、この PC をネットワーク上で検出可能にするかどうかが確認されます。 **[はい]** をクリックします。

1. SQL Server ベースの仮想マシン イメージの 1 つを使用している場合は、SQL Server インスタンスを削除します。

   - **[プログラムと機能]** で **[Microsoft SQL Server 201_ (64 ビット)]** を右クリックし、 **[アンインストールと変更]** をクリックします。
   - **[削除]** をクリックします。
   - 既定のインスタンスを選択します。
   - **[データベース エンジン サービス]** のすべての機能を削除します。 **[共有機能]** は削除しないでください。 次の図を参照してください。

      ![機能を削除する](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - **[次へ]** をクリックし、 **[削除]** をクリックします。

1. <a name="ports"></a>ファイアウォール ポートを開きます。

   各仮想マシンで、Windows ファイアウォールの次のポートを開きます。

   | 目的 | TCP ポート | メモ
   | ------ | ------ | ------
   | SQL Server | 1433 | SQL Server の既定のインスタンスの通常のポートです。 ギャラリーからイメージを使用した場合、このポートが自動的に開きます。
   | 正常性プローブ | 59999 | 開いている任意の TCP ポートです。 後の手順で、このポートを使用するようにロード バランサーの[正常性プローブ](#probe)とクラスターを構成します。   
   | ファイル共有 | 445 | ファイル共有サービスによって使用されるポート。 

1. [既存のドメインに仮想マシンを追加します](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain)。

仮想マシンを作成して構成したら、Premium ファイル共有を構成できます。

## <a name="step-2-mount-premium-file-share"></a>手順 2:Premium ファイル共有をマウントする

1. [Azure portal](https://portal.azure.com) にサインインし、お使いのストレージ アカウントに移動します。
1. **[ファイル サービス]** の **[ファイル共有]** に移動し、SQL ストレージに使用する Premium ファイル共有を選択します。 
1. **[接続]** を選択して、ファイル共有の接続文字列を表示します。 
1. 使用するドライブ文字をドロップダウンから選択し、両方のコード ブロックをメモ帳にコピーします。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="ファイル共有接続ポータルから両方の PowerShell コマンドをコピーする":::

1. SQL Server FCI がサービス アカウントに使用するアカウントを使用して、SQL Server VM に RDP 接続します。 
1. 管理 PowerShell コマンド コンソールを起動します。 
1. 前の手順で保存したコマンドをポータルから実行します。 
1. ファイル エクスプローラーで、またはネットワーク パス `\\storageaccountname.file.core.windows.net\filesharename` を使って **[Run]\(実行\)** ダイアログ ボックス (Windows キー + r) で共有に移動します。 例: `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. 新しく接続されたファイル共有に、SQL データ ファイルを配置するフォルダーを少なくとも 1 つ作成します。 
1. クラスターに参加する各 SQL Server VM で、この手順を繰り返します。 

  > [!IMPORTANT]
  > バックアップ ファイル用に別のファイル共有を使用して、この共有の IOPS とサイズ容量をデータとログ ファイル用に確保することを検討してください。 バックアップ ファイルには、Premium または Standard のいずれのファイル共有も使用できます

## <a name="step-3-configure-failover-cluster-with-file-share"></a>手順 3:ファイル共有を使用してフェールオーバー クラスターを構成する 

次の手順では、フェールオーバー クラスターを構成します。 この手順では、次のサブ手順を実行します。

1. Windows フェールオーバー クラスタリング機能を追加する
1. クラスターを検証する
1. フェールオーバー クラスターを作成する
1. クラウド監視を作成する


### <a name="add-windows-failover-clustering-feature"></a>Windows フェールオーバー クラスタリング機能を追加する

1. これを開始するには、ローカルの Administrators のメンバーであり、かつ Active Directory でオブジェクトを作成するアクセス許可を持つドメイン アカウントを使用して、RDP で最初の仮想マシンに接続します。 このアカウントを使用して、構成を進めます。

1. [各仮想マシンにフェールオーバー クラスタリング機能を追加します](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms)。

   UI からフェールオーバー クラスタリング機能をインストールするには、両方の仮想マシンで次の手順を実行します。
   - **[サーバー マネージャー]** で、 **[管理]** 、 **[役割と機能の追加]** の順にクリックします。
   - **役割と機能の追加ウィザード**で、 **[機能の選択]** ページが表示されるまで **[次へ]** をクリックします。
   - **[機能の選択]** で **[フェールオーバー クラスタリング]** をクリックします。 必要なすべての機能と管理ツールを含めます。 **[機能の追加]** をクリックします。
   - **[次へ]** をクリックし、 **[完了]** をクリックして、機能をインストールします。

   PowerShell を使用したフェールオーバー クラスタリング機能をインストールするには、いずれかの仮想マシン上の管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

### <a name="validate-the-cluster"></a>クラスターを検証する

このガイドは、[クラスターの検証](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation)に関するセクションの手順に沿っています。

UI または PowerShell を使用して、クラスターを検証します。

UI を使用してクラスターを検証するには、いずれかの仮想マシンから次の手順を実行します。

1. **[サーバー マネージャー]** で、 **[ツール]** 、 **[フェールオーバー クラスター マネージャー]** の順にクリックします。
1. **[フェールオーバー クラスター マネージャー]** で、 **[操作]** をクリックし、 **[構成の検証...]** をクリックします。
1. **[次へ]** をクリックします。
1. **[サーバーまたはクラスターの選択]** で、両方の仮想マシンの名前を入力します。
1. **[テスト オプション]** で、 **[選択するテストのみを実行する]** を選択します。 **[次へ]** をクリックします。
1. **[テストの選択]** では、 **[ストレージ]** と **[記憶域スペース ダイレクト]** を除くすべてのテストを含めます。 次の図を参照してください。

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="クラスター検証テスト":::

1. **[次へ]** をクリックします。
1. **[確認]** で **[次へ]** をクリックします。

**構成の検証ウィザード**により、検証テストが実行されます。

PowerShell を使用してクラスターを検証するには、いずれかの仮想マシンの管理者 PowerShell セッションから次のスクリプトを実行します。

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

クラスターの検証後、フェールオーバー クラスターを作成します。

### <a name="create-the-failover-cluster"></a>フェールオーバー クラスターを作成する


フェールオーバー クラスターを作成するには、以下が必要です。
- クラスター ノードになる仮想マシンの名前。
- フェールオーバー クラスターの名前。
- フェールオーバー クラスターの IP アドレス。 クラスター ノードと同じ Azure 仮想ネットワークおよびサブネットでは使用されていない IP アドレスを使用することができます。

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

次の PowerShell は、**Windows Server 2012-2016** 用のフェールオーバー クラスターを作成します。 ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

次の PowerShell は、Windows Server 2019 用のフェールオーバー クラスターを作成します。  詳細については、「[フェールオーバー クラスター: クラスター ネットワーク オブジェクト](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97)」を確認してください。  ノード名 (仮想マシン名) と、Azure VNET の使用可能な IP アドレスでスクリプトを更新してください。

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>クラウド監視を作成する

クラウド監視とは、Azure Storage Blob に格納されている、新しい種類のクラスター クォーラム監視です。 これにより、監視共有をホストする別個の VM が不要になります。

1. [フェールオーバー クラスターのクラウド監視を作成](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness)します。

1. BLOB コンテナーを作成します。

1. アクセス キーと、コンテナーの URL を保存します。

1. フェールオーバー クラスターのクォーラム監視を構成します。 [ユーザー インターフェイスでクォーラム監視を構成する方法](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness)に関するページを参照してください。


## <a name="step-4-test-cluster-failover"></a>手順 4:クラスターのフェールオーバーをテストする

クラスターのフェールオーバーをテストします。 フェールオーバー クラスター マネージャーで、クラスターを右クリックし、 **[More Actions]\(その他のアクション\)**  >  **[Move Core Cluster Resource]\(コア クラスター リソースの移動\)**  >  **[Select node]\(ノードの選択\)** の順に選択し、クラスターの他のノードを選択します。 コア クラスター リソースをクラスターのすべてのノードに移動してから、プライマリ ノードに戻します。 クラスターを各ノードに正常に移動できる場合は、SQL Server をインストールする準備ができています。  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="コア リソースを他のノードに移動して、クラスター フェールオーバーをテストする":::

## <a name="step-5-create-sql-server-fci"></a>手順 5:SQL Server FCI を作成する

フェールオーバー クラスターを構成したら、SQL Server FCI を作成できます。

1. RDP で最初の仮想マシンに接続します。

1. **フェールオーバー クラスター マネージャー**で、すべてのクラスター コア リソースが最初の仮想マシン上にあることを確認します。 必要に応じて、すべてのリソースをこの仮想マシンに移動します。

1. インストール メディアを探します。 仮想マシンでいずれかの Azure Marketplace イメージが使用されている場合、メディアは `C:\SQLServer_<version number>_Full` にあります。 **[Setup]** をクリックします。

1. **[SQL Server インストール センター]** で、 **[インストール]** をクリックします。

1. **[SQL Server フェールオーバー クラスターの新規インストール]** をクリックします。 ウィザードの指示に従って、SQL Server FCI をインストールします。

   FCI データ ディレクトリは、Premium ファイル共有上に存在する必要があります。 共有の完全なパスを `\\storageaccountname.file.core.windows.net\filesharename\foldername` の形式で入力します。 ファイル サーバーをデータ ディレクトリとして指定したことを通知する警告が表示されます。 これは予期されることです。 ファイル共有の保持に使用したアカウントが、SQL Server サービスが潜在的なエラーを回避するために使用しているアカウントと同じであることを確認します。 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="ファイル共有を SQL データ ディレクトリとして使用する":::

1. ウィザードを完了すると、セットアップにより、SQL Server FCI が最初のノードにインストールされます。

1. セットアップで FCI が最初のノードに正常にインストールされたら、RDP で 2 番目のノードに接続します。

1. **[SQL Server インストール センター]** を開きます。 **[インストール]** をクリックします。

1. **[SQL Server フェールオーバー クラスターにノードを追加]** をクリックします。 ウィザードの指示に従って SQL Server をインストールし、このサーバーを FCI に追加します。

   >[!NOTE]
   >SQL Server で Azure Marketplace ギャラリー イメージを使用した場合、SQL Server のツールはイメージに含まれています。 このイメージを使用しなかった場合、SQL Server のツールは別途インストールしてください。 「[SQL Server Management Studio (SSMS) のダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)」を参照してください。

## <a name="step-6-create-azure-load-balancer"></a>手順 6:Azure Load Balancer を作成する

Azure 仮想マシンでは、クラスターは、一度に 1 つのクラスター ノードに存在する必要がある IP アドレスを保持するためにロード バランサーを使用します。 このソリューションでは、ロード バランサーは SQL Server FCI の IP アドレスを保持します。

[Azure のロード バランサーを作成し、構成します](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer)。

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Azure Portal でロード バランサーを作成する

ロード バランサーを作成するには、次の手順を実行します。

1. Azure Portal で、仮想マシンが含まれているリソース グループに移動します。

1. **[+ 追加]** をクリックします。 Marketplace で "**ロード バランサー**" を検索します。 **[ロード バランサー]** をクリックします。

1. **Create** をクリックしてください。

1. 次の項目を入力して、ロード バランサーを構成します。

   - **サブスクリプション**:Azure サブスクリプション。
   - **リソース グループ**:仮想マシンと同じリソース グループを使用します。
   - **Name**:ロード バランサーを識別する名前。
   - **[リージョン]** :仮想マシンと同じ Azure の場所を使用します。
   - **[タイプ]** :ロード バランサーのタイプとして、パブリックまたはプライベートのどちらかを選ぶことができます。 プライベート ロード バランサーには、同じ VNET 内からアクセスできます。 プライベート ロード バランサーは、ほとんどの Azure アプリケーションで使用できます。 アプリケーションがインターネット経由で直接 SQL Server にアクセスする必要がある場合は、パブリック ロード バランサーを使用します。
   - **SKU**:ロード バランサーの SKU は Standard である必要があります。 
   - **[仮想ネットワーク]** : 仮想マシンと同じネットワーク。
   - **[IP アドレスの割り当て]** :IP アドレスの割り当ては、静的である必要があります。 
   - **[プライベート IP アドレス]** :SQL Server FCI クラスターのネットワーク リソースに割り当てたものと同じ IP アドレス。
   次の図を参照してください。

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>ロード バランサーのバックエンド プールを構成する

1. 仮想マシンが含まれている Azure リソース グループに戻り、新しいロード バランサーを探します。 リソース グループの表示を更新する必要がある場合があります。 ロード バランサーをクリックします。

1. **[バックエンド プール]** をクリックし、 **[+ 追加]** をクリックしてバックエンド プールを追加します。

1. VM を含む可用性セットにバックエンド プールを関連付けます。

1. **[ターゲット ネットワーク IP 構成]** で、 **[仮想マシン]** をオンにして、クラスター ノードとして参加する仮想マシンを選びます。 必ず、FCI をホストするすべての仮想マシンを含めます。 

1. **[OK]** をクリックして、バックエンド プールを作成します。

### <a name="configure-a-load-balancer-health-probe"></a>ロード バランサーの正常性プローブを構成する

1. [ロード バランサー] ブレードで **[Health probes (正常性のプローブ)]** をクリックします。

1. **[+ 追加]** をクリックします。

1. **[Add health probe (正常性プローブの追加)]** ブレードで、<a name="probe"></a>正常性プローブのパラメーターを設定します。

   - **Name**:正常性プローブの名前。
   - **プロトコル**:TCP
   - **ポート**:[こちらの手順](#ports)で正常性プローブ用にファイアウォールで作成したポートに設定します。 この記事の例では、TCP ポート `59999` を使用します。
   - **間隔**: 5 秒
   - **[異常のしきい値]** : 連続エラー数 2。

1. [OK] をクリックします。

### <a name="set-load-balancing-rules"></a>負荷分散規則を設定する

1. [ロード バランサー] ブレードで、 **[負荷分散規則]** をクリックします。

1. **[+ 追加]** をクリックします。

1. 負荷分散規則のパラメーターを設定します。

   - **Name**:負荷分散規則の名前。
   - **[フロントエンド IP アドレス]** : SQL Server FCI クラスターのネットワーク リソースの IP アドレスを使用します。
   - **ポート**:SQL Server FCI の TCP ポートに設定します。 既定のインスタンス ポートは 1433 です。
   - **[バックエンド ポート]** :この値には、 **[フローティング IP (ダイレクト サーバー リターン)]** を有効にしたときの **[ポート]** の値と同じポートを使用します。
   - **[バックエンド プール]** : 先ほど構成したバックエンド プール名を使用します。
   - **[正常性プローブ]** : 先ほど構成した正常性プローブを使用します。
   - **[セッション永続化]** : なし。
   - **[アイドル タイムアウト (分)]** : 4.
   - **[フローティング IP]\(ダイレクト サーバー リターン\)** : 有効

1. Click **OK**.

## <a name="step-7-configure-cluster-for-probe"></a>手順 7:プローブのクラスターを構成する

PowerShell でクラスターのプローブ ポート パラメーターを設定します。

クラスターのプローブ ポート パラメーターを設定するには、使用環境の値を使用して、次のスクリプトで変数を更新します。 スクリプトから山かっこの `<>` を削除します。 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

前のスクリプトで、環境の値を設定します。 次の一覧では、値について説明します。

   - `<Cluster Network Name>`:ネットワークの Windows Server フェールオーバー クラスターの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[ネットワーク]** で、ネットワークを右クリックして **[プロパティ]** をクリックします。 正しい値は **[全般]** タブの **[名前]** にあります。 

   - `<SQL Server FCI IP Address Resource Name>`:SQL Server FCI IP アドレス リソースの名前。 **[フェールオーバー クラスター マネージャー]**  >  **[Roles]\(ロール\)** で、SQL Server FCI ロールの **[サーバー名]** の下にある IP アドレス リソースを右クリックして **[プロパティ]** をクリックします。 正しい値は **[全般]** タブの **[名前]** にあります。 

   - `<ILBIP>`:ILB の IP アドレス。 このアドレスは、ILB のフロント エンド アドレスとして Azure Portal で構成されます。 これは、SQL Server FCI の IP アドレスでもあります。 **[フェールオーバー クラスター マネージャー]** の `<SQL Server FCI IP Address Resource Name>` がある同じプロパティ ページで見つけることができます。  

   - `<nnnnn>`:ロード バランサーの正常性プローブで構成したプローブ ポートです。 未使用の TCP ポートが有効です。 

>[!IMPORTANT]
>クラスター パラメーターのサブネット マスクは、TCP IP ブロードキャスト アドレス (`255.255.255.255`) である必要があります。

クラスターのプローブを設定したら、PowerShell ですべてのクラスター パラメーターを確認できます。 次のスクリプトを実行します。

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>ステップ 8:FCI フェールオーバーをテストする

FCI のフェールオーバーをテストして、クラスターの機能を検証します。 次の手順を実行します。

1. SQL Server FCI クラスターのノードの 1 つに RDP で接続します。

1. **フェールオーバー クラスター マネージャー**を開きます。 **[ロール]** をクリックします。 SQL Server FCI ロールを所有しているノードを確認します。

1. SQL Server FCI ロールを右クリックします。

1. **[移動]** をクリックし、 **[最適なノード]** をクリックします。

**フェールオーバー クラスター マネージャー**で、ロールとそのリソースがオフラインになったことが示されます。 リソースは移動し、もう一方のノードでオンラインになります。

### <a name="test-connectivity"></a>接続をテストする

接続をテストするには、同じ仮想ネットワーク内の別の仮想マシンにログインします。 **SQL Server Management Studio** を開き、SQL Server FCI 名に接続します。

>[!NOTE]
>必要に応じて、[SQL Server Management Studio をダウンロード](https://msdn.microsoft.com/library/mt238290.aspx)できます。

## <a name="limitations"></a>制限事項

Azure Virtual Machines では、クラスター共有ボリューム (CSV) および [Standard Load Balancer](../../../load-balancer/load-balancer-standard-overview.md) 上のストレージを備えた Windows Server 2019 で、 Microsoft 分散トランザクション コーディネーター (MSDTC) をサポートします。

Azure Virtual Machines では、次の理由から、MSDTC はWindows Server 2016 以前ではサポートされていません。

- クラスター化された MSDTC リソースは、共有ストレージを使用するように構成することはできません。 Windows Server 2016 では、MSDTC リソースを作成した場合、ストレージがそこにあっても、使用可能な共有ストレージは 1 つも表示されません。 この問題は、Windows Server 2019 で修正済みです。
- Basic Load Balance は、RPC ポートを処理しません。

## <a name="see-also"></a>関連項目

- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
