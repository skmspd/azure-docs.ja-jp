---
title: Azure NetApp Files の NFS ボリュームを作成する | Microsoft Docs
description: Azure NetApp Files の NFS ボリュームを作成する方法について説明します。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/12/2019
ms.author: b-juche
ms.openlocfilehash: 1a479b4928631f27d5453d462a59fe7fed09a88c
ms.sourcegitcommit: bd4198a3f2a028f0ce0a63e5f479242f6a98cc04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72302763"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Azure NetApp Files の NFS ボリュームを作成する

Azure NetApp Files では、NFS ボリューム (NFSv3 と NFSv4.1) および SMBv3 ボリュームがサポートされています。 ボリュームの容量消費は、そのプールのプロビジョニング容量を前提としてカウントされます。 この記事では、NFS ボリュームを作成する方法について説明します。 SMB ボリュームを作成する場合は、「[Create an SMB volume for Azure NetApp Files](azure-netapp-files-create-volumes-smb.md)」(Azure NetApp Files の SMB ボリュームを作成する) を参照してください。 

## <a name="before-you-begin"></a>開始する前に 
あらかじめ容量プールを設定しておく必要があります。   
[容量プールを設定する](azure-netapp-files-set-up-capacity-pool.md)   
サブネットが Azure NetApp Files に委任されている必要があります。  
[サブネットを Azure NetApp Files に委任する](azure-netapp-files-delegate-subnet.md)

## <a name="considerations"></a>考慮事項 

> [!IMPORTANT] 
> NFSv4.1 機能にアクセスするには、ホワイトリストへの登録が必要です。  ホワイトリストへの登録を申請するには、申請を <anffeedback@microsoft.com> に送信してください。 

* 使用する NFS のバージョンを決定する  
  NFSv3 は、さまざまなユース ケースの処理に使用でき、通常、ほとんどのエンタープライズ アプリケーションにデプロイされます。 アプリケーションで必要なバージョン (NFSv3 または NFSv4.1) を検証し、適切なバージョンを使用してボリュームを作成する必要があります。 たとえば、[Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave) を使用する場合は、NFSv4.1 でのファイル ロックの方が、NFSv3 より推奨されます。 

* セキュリティ  
  UNIX モード ビット (読み取り、書き込み、実行) のサポートは、NFSv3 と NFSv4.1 で利用できます。 NFS ボリュームをマウントするには、NFS クライアントでのルート レベルのアクセス権が必要です。

* NFSv4.1 に対するローカル ユーザー/グループと LDAP のサポート  
  現在、NFSv4.1 ではボリュームに対するルート アクセスのみがサポートされています。 

## <a name="best-practice"></a>ベスト プラクティス

* ボリュームに対する適切なマウント方法を使用していることを確認する必要があります。  「[Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)」を参照してください。

* NFS クライアントは、Azure NetApp Files ボリュームと同じ VNet またはピアリングされた VNet に存在する必要があります。 VNet の外部からの接続はサポートされていますが、待機時間が長くなり、全体的なパフォーマンスが低下します。

* NFS クライアントが最新であり、オペレーティング システムの最新の更新プログラムが実行されていることを、確認する必要があります。

## <a name="create-an-nfs-volume"></a>NFS ボリュームを作成する

1.  [容量プール] ブレードから **[ボリューム]** ブレードをクリックします。 

    ![ボリュームに移動する](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2.  **[+ ボリュームの追加]** をクリックして、ボリュームを作成します。  
    [ボリュームの作成] ウィンドウが表示されます。

3.  [ボリュームの作成] ウィンドウの **[作成]** をクリックし、次のフィールドの情報を指定します。   
    * **ボリューム名**      
        作成するボリュームの名前を指定します。   

        ボリューム名は、各容量プール内で一意である必要があります。 3 文字以上になるようにしてください。 任意の英数字を使用できます。   

        ボリューム名として `default` を使用することはできません。

    * **容量プール**  
        ボリュームを作成する容量プールを指定します。

    * **[クォータ]**  
        ボリュームに割り当てられる論理ストレージの量を指定します。  

        **[使用可能なクォータ]** フィールドには、選択した容量プール内の未使用の領域のうち、新しいボリュームの作成に使用できる領域の量が示されます。 新しいボリュームのサイズが、使用可能なクォータを超えてはいけません。  

    * **Virtual Network**  
        ボリュームへのアクセス元となる Azure Virtual Network (Vnet) を指定します。  

        指定する Vnet には、Azure NetApp Files に委任されているサブネットがある必要があります。 Azure NetApp Files サービスにアクセスできるのは、同じ Vnet から、またはボリュームと同じリージョンにある Vnet から Vnet ピアリングを経由する場合のみです。 ボリュームには、オンプレミス ネットワークから Express Route 経由でアクセスすることもできます。   

    * **サブネット**  
        ボリュームで使用するサブネットを指定します。  
        指定するサブネットは Azure NetApp Files に委任されている必要があります。 
        
        サブネットを委任していない場合は、[Create a Volume]\(ボリュームの作成) ページで **[新規作成]** をクリックできます。 次に、[サブネットの作成] ページでサブネットの情報を指定し、 **[Microsoft.NetApp/volumes]** を選択してサブネットを Azure NetApp Files に委任します。 各 VNet で、1 つのサブネットだけを Azure NetApp Files に委任できます。   
 
        ![ボリュームを作成する](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![サブネットの作成](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

4. **[プロトコル]** をクリックし、次のアクションを実行します。  
    * ボリュームのプロトコルの種類として **[NFS]** を選択します。   
    * 新しいボリュームのエクスポート パスを作成する際に使用する**ファイル パス**を指定します。 ボリュームのマウントとアクセスには、このエクスポート パスが使用されます。

        ファイル パス名には、文字、数字、ハイフン ("-") のみを含めることができます。 長さは 16 文字から 40 文字でなければなりません。 

        ファイルのパスは、各サブスクリプションと各リージョン内で一意である必要があります。 

    * ボリュームの NFS バージョン ( **[NFSv3]** または **[NFSv4.1]** ) を選択します。  
    * 必要に応じて、[NFS ボリュームのエクスポート ポリシーを構成します](azure-netapp-files-configure-export-policy.md)。

    ![NFS プロトコルを指定する](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

5. **[確認および作成]** をクリックし、ボリュームの詳細を確認します。  **[作成]** をクリックして NFS ボリュームを作成します。

    作成したボリュームは [ボリューム] ページに表示されます。 
 
    ボリュームは、その容量プールから、サブスクリプション、リソース グループ、場所の各属性を継承します。 ボリュームのデプロイ状態を監視するには、[通知] タブを使用してください。


## <a name="next-steps"></a>次の手順  

* [Windows または Linux 仮想マシンのボリュームをマウント/マウント解除する](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [NFS ボリュームのエクスポート ポリシーを構成する](azure-netapp-files-configure-export-policy.md)
* [Azure NetApp Files のリソース制限](azure-netapp-files-resource-limits.md)
* [Azure サービスの仮想ネットワーク統合について理解する](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)
