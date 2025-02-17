---
title: Azure サービス タグの概要
titlesuffix: Azure Virtual Network
description: サービス タグについて確認します。 サービス タグを使用すると、セキュリティ規則の作成の複雑さを最小限に抑えることができます。
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 0eb15d1b9b56522b9caa1bb10890eb2b485714e8
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73586852"
---
# <a name="virtual-network-service-tags"></a>仮想ネットワーク サービス タグ 
<a name="network-service-tags"></a>

サービス タグは、指定された Azure サービスからの IP アドレス プレフィックスのグループを表します。 これは、ネットワーク セキュリティ規則の頻繁な更新の複雑さを最小限に抑えるのに役立ちます。 サービス タグを使用すると、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)または [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) でのネットワーク アクセス制御を定義できます。 セキュリティ規則を作成するときは、特定の IP アドレスの代わりにサービス タグを使うことができます。 規則の適切な*ソース*または*宛先*フィールドにサービス タグ名 (**ApiManagement** など) を指定することにより、対応するサービスのトラフィックを許可または拒否できます。 サービス タグに含まれるアドレス プレフィックスの管理は Microsoft が行い、アドレスが変化するとサービス タグは自動的に更新されます。 

## <a name="available-service-tags"></a>利用可能なサービス タグ
次の表には、[ネットワーク セキュリティ グループ](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)規則で使用できるすべてのサービス タグが含まれています。

この列は、タグが次を満たすかどうかを示します。

- 受信または送信トラフィックを扱う規則に適している
- [リージョン](https://azure.microsoft.com/regions) スコープをサポートしている 
- [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) 規則で使用可能である

既定では、サービス タグにはクラウド全体の範囲が反映されます。  サービス タグによっては、対応する IP 範囲を指定されたリージョンに制限することで、より詳細な制御を実現することもできます。  たとえば、サービス タグ **Storage** はクラウド全体の Azure Storage を表していますが、**Storage.WestUS** を使用すると、WestUS リージョンのストレージ IP アドレス範囲のみに限定されます。  以下の各サービス タグの説明は、そのような地域の範囲をサポートしているかどうかを示します。  



| タグ | 目的 | 受信または送信 | リージョン別か? | Azure Firewall と共に使用できるか |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | APIM 専用デプロイメントの管理トラフィック。 | 両方 | いいえ | はい |
| **AppService**    | App Service のサービス。 WebApps フロントエンドへの送信セキュリティ規則には、このタグをお勧めします。 | 送信 | はい | はい |
| **AppServiceManagement** | App Service Environment 専用デプロイの管理トラフィック。 | 両方 | いいえ | はい |
| **AzureActiveDirectory** | Azure Active Directory サービス。 | 送信 | いいえ | はい |
| **AzureActiveDirectoryDomainServices** | Azure Active Directory Domain Services 専用デプロイの管理トラフィック。 | 両方 | いいえ | はい |
| **AzureBackup** |Azure Backup サービス。<br/><br/>*注:* このタグは、**Storage** タグと **AzureActiveDirectory** タグに依存します。 | 送信 | いいえ | はい |
| **AzureCloud** | すべての[データセンター パブリック IP アドレス](https://www.microsoft.com/download/details.aspx?id=41653)。 | 送信 | はい | はい |
| **AzureConnectors** | プローブ/バックエンド接続用の Logic Apps コネクタ。 | 受信 | はい | はい |
| **AzureContainerRegistry** | Azure Container Registry サービス。 | 送信 | はい | はい |
| **AzureCosmosDB** | Azure Cosmos データベース サービス。 | 送信 | はい | はい |
| **AzureDataLake** | Azure Data Lake サービス。 | 送信 | いいえ | はい |
| **AzureKeyVault** | Azure KeyVault サービス。<br/><br/>*注:* このタグは、**AzureActiveDirectory** タグに依存します。 | 送信 | はい | はい |
| **AzureLoadBalancer** | Azure のインフラストラクチャ ロード バランサー このタグは、Azure の正常性プローブの送信元となる[ホストの仮想 IP アドレス](security-overview.md#azure-platform-considerations) (168.63.129.16) に変換されます。 Azure Load Balancer を使っていない場合は、この規則をオーバーライドできます。 | 両方 | いいえ | いいえ |
| **AzureMachineLearning** | Azure Machine Learning service。 | 送信 | いいえ | はい |
| **AzureMonitor** | Log Analytics、App Insights、AzMon、およびカスタム メトリック (GiG エンドポイント)。<br/><br/>*注:* Log Analytics では、このタグは **Storage** タグに依存します。 | 送信 | いいえ | はい |
| **AzurePlatformDNS** | 基本インフラストラクチャ (既定) の DNS サービス。<br/><br>このタグを使用すると、既定の DNS を無効にすることができます。 このタグを使用する場合は注意してください。 [Azure プラットフォームに関する考慮事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)を確認することをお勧めします。 このタグを使用する前に、テストをお勧めします。 | 送信 | いいえ | いいえ |
| **AzurePlatformIMDS** | 基本的なインフラストラクチャ サービスである IMDS を表します。<br/><br/>このタグを使用すると、既定の IMDS を無効にすることができます。  このタグを使用する場合は注意してください。 [Azure プラットフォームに関する考慮事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)を確認することをお勧めします。 このタグを使用する前に、テストをお勧めします。 | 送信 | いいえ | いいえ |
| **AzurePlatformLKM** | Windows のライセンスまたはキー管理サービス。<br/><br/>このタグを使用すると、ライセンスの既定値を無効にすることができます。 このタグを使用する場合は注意してください。  [Azure プラットフォームに関する考慮事項](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)を確認することをお勧めします。 このタグを使用する前に、テストをお勧めします。 | 送信 | いいえ | いいえ |
| **AzureTrafficManaged** | Azure Traffic Manager プローブ IP アドレス。<br/><br/>Traffic Manager プローブ IP アドレスについて詳しくは、[Azure Traffic Manager の FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs) に関するページをご覧ください。 | 受信 | いいえ | はい |  
| **BatchNodeManagement** | Azure Batch 専用デプロイメントの管理トラフィック。 | 両方 | いいえ | はい |
| **CognitiveServicesManagement** | Cognitive Services のトラフィックのアドレス範囲 | 送信 | いいえ | いいえ |
| **Dynamics365ForMarketingEmail** | Dynamics 365 のマーケティング電子メール サービスのアドレス範囲。 | 送信 | はい | いいえ |
| **EventHub** | Azure EventHub サービス。 | 送信 | はい | はい |
| **GatewayManager** | VPN/アプリ ゲートウェイ専用デプロイメントの管理トラフィック。 | 受信 | いいえ | いいえ |
| **Internet** | パブリック インターネットによってアクセスできる仮想ネットワークの外部の IP アドレス空間。<br/><br/>[Azure に所有されているパブリック IP アドレス空間](https://www.microsoft.com/download/details.aspx?id=41653)がこのアドレス範囲に含まれます。 | 両方 | いいえ | いいえ |
| **MicrosoftContainerRegistry** | Microsoft コンテナー レジストリ サービス。 | 送信 | はい | はい |
| **ServiceBus** | Premium サービス レベルを使用した Azure Service Bus サービス。 | 送信 | はい | はい |
| **ServiceFabric** | Service Fabric サービス。 | 送信 | いいえ | いいえ |
| **Sql** | Azure SQL Database、Azure Database for MySQL、Azure Database for PostgreSQL、および Azure SQL Data Warehouse サービス。<br/><br/>*注:* このタグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure SQL Database サービスを表しますが、特定の SQL データベースや SQL サーバーは表しません。 | 送信 | はい | はい |
| **SqlManagement** | SQL 専用デプロイメントの管理トラフィック。 | 両方 | いいえ | はい |
| **Storage** | Azure Storage サービス。 <br/><br/>*注:* タグはサービスだけを表し、サービスの特定のインスタンスは表しません。 たとえば、このタグは Azure Storage サービスを表しますが、特定の Azure Storage アカウントは表しません。 | 送信 | はい | はい |
| **VirtualNetwork** | 仮想ネットワーク アドレス空間 (仮想ネットワークに対して定義されているすべての CIDR 範囲)、すべての接続されたオンプレミスのアドレス空間、[ピアリング](virtual-network-peering-overview.md)された仮想ネットワークまたは[仮想ネットワーク ゲートウェイ](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)に接続された仮想ネットワーク、[ホストの仮想 IP アドレス](security-overview.md#azure-platform-considerations)、[ユーザーが定義したルート](virtual-networks-udr-overview.md)に使用されるアドレス プレフィックス。 このタグには既定のルートが含まれる場合もあることに注意してください。 | 両方 | いいえ | いいえ |

>[!NOTE]
>*クラシック* (Azure Resource Manager 以前) 環境で作業している場合は、上記のタグの一部のセットがサポートされます。  これらは別のスペルを使用します。

| クラシックのスペル | 同等のリソース マネージャー タグ |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | インターネット |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Azure サービスのサービス タグは、使用されている特定のクラウドからのアドレス プレフィックスを表します。 たとえば、**Sql** タグ値に対応する基になる IP 範囲は Azure Public Cloud と Azure China Cloud で異なります。

> [!NOTE]
> Azure Storage や Azure SQL Database などのサービスの[仮想ネットワーク サービス エンドポイント](virtual-network-service-endpoints-overview.md)を実装する場合、Azure はサービスの仮想ネットワーク サブネットへの[ルート](virtual-networks-udr-overview.md#optional-default-routes)を追加します。 ルートのアドレス プレフィックスは、対応するサービス タグと同じアドレス プレフィックスまたは CIDR 範囲です。



## <a name="service-tags-in-on-premises"></a>オンプレミスのサービス タグ  
現在のサービス タグと範囲情報を、オンプレミスのファイアウォール構成の一部として取得して含めることができます。  この情報は、各サービス タグに対応する現在の特定時点の IP 範囲の一覧です。  この情報は、次のようにプログラムによって取得するか、JSON ファイルのダウンロードによって取得できます。

### <a name="service-tag-discovery-api-public-preview"></a>Service Tag Discovery API (パブリック プレビュー)
IP アドレス範囲の詳細を含むサービス タグの現在の一覧をプログラムで取得できます。

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> パブリック プレビューでは、Discovery API が返す情報は、JSON ダウンロードほど最新ではない場合があります (下記参照)。


### <a name="discover-service-tags-using-downloadable-json-files"></a>ダウンロード可能な JSON ファイルを使用したサービス タグの検出 
IP アドレス範囲の詳細を含むサービス タグの現在の一覧が格納された JSON ファイルをダウンロードすることができます。 これらは毎週更新され、公開されます。  各クラウドの場所は次のとおりです。

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>[Azure Public](https://www.microsoft.com/download/details.aspx?id=41653)、[Azure China](https://www.microsoft.com/download/details.aspx?id=42064)、および [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770) については、この情報のサブセットが以前 XML ファイルで公開されていました。 これらの XML ダウンロードは、2020 年 6 月 30 日に非推奨となり、その後は使用できなくなります。 上記の Discovery API または JSON ファイルのダウンロードの使用に移行してください。

### <a name="tips"></a>ヒント 
- ある公開からその次の公開に更新されたかどうかは、JSON ファイル内の *changeNumber* の値の増加によって検出できます。 各サブセクション (たとえば **Storage.WestUS**) には、変更が発生するたびにインクリメントされる固有の *changeNumber* があります。  ファイルの *changeNumber* の最上位レベルは、サブセクションのいずれかが変更されると増加します。
- サービス タグ情報を解析する方法の例 (WestUS のストレージについてのすべてのアドレス範囲を取得する方法など) については、[Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) のドキュメントを参照してください。

## <a name="next-steps"></a>次の手順
- [ネットワーク セキュリティ グループの作成](tutorial-filter-network-traffic.md)方法を学習します。

