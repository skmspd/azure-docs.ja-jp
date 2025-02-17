---
title: '場所と接続プロバイダー: Azure ExpressRoute | Microsoft Docs'
description: この記事では、サービスが提供されている場所と Azure リージョンに接続する方法の詳細について説明します。 場所ごとにソートされています。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: a68a5f49e82a00c4c64137169237d8d1e7d99ed7
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580339"
---
# <a name="expressroute-partners-and-peering-locations"></a>ExpressRoute パートナーとピアリングの場所

> [!div class="op_single_selector"]
> * [プロバイダー別の場所](expressroute-locations.md)
> * [場所別のプロバイダー](expressroute-locations-providers.md)


この記事の表では、ExpressRoute の地理的範囲と場所、ExpressRoute の接続プロバイダー、ExpressRoute のシステム インテグレーター (SI) に関する情報を提供します。

> [!Note]
> Azure リージョンと ExpressRoute の場所は 2 つの異なる概念であり、2 つの違いを理解することは、Azure ハイブリッド ネットワーク接続を理解するうえで非常に重要です。 
>
>

## <a name="azure-regions"></a>Azure Azure リージョン
Azure リージョンは、Azure のコンピューティング、ネットワーク、およびストレージのリソースが配置されているグローバル データセンターです。 Azure リソースを作成する場合、お客様はリソースの場所を選択する必要があります。 リソースの場所によって、リソースが作成される Azure データセンター (または可用性ゾーン) が決まります。

## <a name="expressroute-locations"></a>ExpressRoute の場所
ExpressRoute の場所 (ピアリングの場所や meet-me-locations と呼ばれることもあります) は、Microsoft エンタープライズ エッジ (MSEE) デバイスが配置されているコロケーション施設です。 ExpressRoute の場所は Microsoft のネットワークへの入り口であり、世界中に分散されているため、お客様は世界中の Microsoft のネットワークに接続できます。 これらの場所は、ExpressRoute パートナーと ExpressRoute Direct のお客様が Microsoft のネットワークへの交差接続を実行する場所です。 一般的に、ExpressRoute の場所と Azure リージョンを一致させる必要はありません。 たとえば、お客様は、リソースの場所が*米国東部*で、ピアリングの場所が*シアトル*である ExpressRoute 回線を作成できます。

地理的リージョン内の少なくとも 1 つの ExpressRoute の場所に接続している場合は、その地理的リージョン内のすべてのリージョンの Azure サービスにアクセスできます。 

## <a name="locations"></a>地理的リージョン内の Azure リージョンと ExpressRoute の場所の対応
次の表は、地理的リージョン、ExpressRoute の場所、Azure リージョンをまとめたものです。

| **地理的リージョン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- |
| **オーストラリア政府** | オーストラリア中部、オーストラリア中部 2 |キャンベラ、キャンベラ2 |
| **ヨーロッパ** | フランス中部、フランス南部、北ヨーロッパ、西ヨーロッパ、英国西部、英国南部 |アムステルダム、アムステルダム 2、コペンハーゲン、ダブリン、フランクフルト、ジュネーブ、ロンドン、ロンドン 2、マルセイユ、ミラノ、ミュンヘン、ニューポート (ウェールズ)、パリ、スタバンゲル、ストックホルム、チューリッヒ、ミュンヘン |
| **北米** | 米国東部、米国西部、米国東部 2、米国西部 2、米国中部、米国中南部、米国中北部、米国中西部、カナダ中部、カナダ東部 |アトランタ、シカゴ、ダラス、デンバー、ラスベガス、ロサンゼルス、マイアミ、ニューヨーク、サンアントニオ、シアトル、シリコン バレー、シリコン バレー 2、ワシントン DC、ワシントン DC2、モントリオール、ケベックシティ、トロント |
| **アジア** | 東アジア、東南アジア |香港特別行政区、ジャカルタ、クアラルンプール、シンガポール、シンガポール 2、台北 |
| **インド** | インド西部、インド中部、インド南部 |チェンナイ、チェンナイ 2、ムンバイ、ムンバイ 2 |
| **日本** | 西日本、東日本 |大阪、東京 |
| **オセアニア** | オーストラリア東部、オーストラリア南東部 |オークランド、メルボルン、パース、シドニー | 
| **韓国** | 韓国中部、韓国南部 |釜山、ソウル|
| **アラブ首長国連邦** | アラブ首長国連邦中部、アラブ首長国連邦北部 | ドバイ、ドバイ 2 |
| **南アフリカ** | 南アフリカ西部、南アフリカ北部 |ケープタウン、ヨハネスブルグ |
| **南アメリカ** | ブラジル南部 |サンパウロ |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>各国のクラウドの Azure リージョンと地理的境界
次の表は、国内のクラウドのリージョンと地政学的境界に関する情報をまとめたものです。

| **地理的リージョン** | **Azure リージョン** | **ExpressRoute の場所** |
| --- | --- | --- |
| **米国政府のクラウド** |米国政府アリゾナ、US Gov アイオワ、米国政府テキサス、米国政府バージニア、US DoD 中部、US DoD 東部  |シカゴ、ダラス、ニューヨーク、フェニックス、サンアントニオ、シアトル、シリコン バレー、ワシントン DC |
| **中国東部** |中国東部、中国東部 2 |上海、上海 2 |
| **中国北部** |中国北部、中国北部 2 |北京、北京 2 |
| **ドイツ** |ドイツ中部、ドイツ東部 |ベルリン、フランクフルト |

標準の ExpressRoute SKU では、地理的リージョン間の接続はサポートされていません。 グローバル接続に対応するには、ExpressRoute Premium アドオンを有効にする必要があります。 国内のクラウド環境には接続できません。 国内のクラウド環境に接続する必要がある場合、接続プロバイダーにご相談ください。

## <a name="partners"></a>ExpressRoute 接続プロバイダー

次の表に、接続する場所と、各場所のサービス プロバイダーを示します。 サービス プロバイダーと、そのサービス プロバイダーがサービスを提供できる場所を確認する場合は、[サービス プロバイダー別の場所](expressroute-locations.md)に関するページを参照してください。

* **ローカル Azure リージョン**は、それぞれのピアリングの場所で [ExpressRoute Local](expressroute-faqs.md) がアクセスできるリージョンです。 **該当なし**は、そのピアリングの場所で ExpressRoute Local が使用できないことを示します。

* **ゾーン**は[価格](https://azure.microsoft.com/pricing/details/expressroute/)を参照します。


### <a name="global-commercial-azure"></a>グローバルな商用 Azure
| **Location** | **アドレス** | **ゾーン** | **ローカル Azure リージョン** | **ER Direct** | **サービス プロバイダー** |
| --- | --- | --- | --- | --- | --- |
| **アムステルダム** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | 西ヨーロッパ | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、Colt、Equinix、euNetworks、GÉANT、InterCloud、Interxion、KPN、IX Reach、Level 3 Communications、Megaport、NTT Communications、Orange、Tata Communications、Telefonica、Telenor、Telia Carrier、Verizon、Zayo |
| **アムステルダム 2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | 西ヨーロッパ | 10G、100G | CenturyLink Cloud Connect、Colt、DE-CIX、euNetworks、Interxion、Vodafone |
| **アトランタ** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | 該当なし | 該当なし | Equinix、Megaport |
| **オークランド** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | 該当なし | 10G | Devoli、Kordia、Megaport、Spark NZ、Vocus Group NZ |
| **釜山** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | 韓国南部 | 該当なし | LG CNS |
| **キャンベラ** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | オーストラリア中部 | 該当なし | CDC |
| **キャンベラ2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | オーストラリア中部 2| 該当なし | CDC |
| **ケープタウン** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | 南アフリカ西部 | 10G | Internet Solutions - Cloud Connect、Liquid Telecom、Teraco |
| **チェンナイ** | Tata Communications | 2 | インド南部 | 該当なし | Global CloudXchange (GCX)、SIFY、Tata Communications |
| **チェンナイ 2** | Airtel | 2 | インド南部 | 該当なし | Airtel |
| **シカゴ** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | 米国中北部 | 10G、100G | Aryaka Networks、AT&T NetBond、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、InterCloud、Internet2、Level 3 Communications、Megaport、PacketFabric、PCCW Global Limited、Sprint、Telia Carrier、Verizon、Zayo |
| **コペンハーゲン** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | 該当なし | 10G | Interxion |
| **ダラス** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | 該当なし | 10G、100G | Aryaka Networks、AT&T NetBond、Cologix、Equinix、Internet2、Level 3 Communications、Megaport、Neutrona Networks、Telmex Uninet、Telia Carrier、Transtelco、Verizon、Zayo|
| **デンバー** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | 米国中西部 | 該当なし | CoreSite、Megaport、Zayo |
| **ドバイ** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | アラブ首長国連邦北部 | 該当なし | Etisalat UAE |
| **ドバイ 2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | アラブ首長国連邦北部 | 該当なし | du datamena、Megaport、Orixcom |
| **ダブリン** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | 北ヨーロッパ | 10G、100G | Colt、eir、Equinix、Interxion、Megaport |
| **フランクフルト** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | ドイツ中西部 | 該当なし | DE CIX、Interxion、Orange |
| **ジュネーブ** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | スイス西部 | 10G、100G | |
| **香港特別行政区** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | 東アジア | 該当なし | Aryaka Networks、British Telecom、CenturyLink Cloud Connect、Chief Telecom、China Telecom Global、Equinix、Megaport、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telia Carrier、Verizon |
| **ジャカルタ** | Telkom Indonesia | 4 | 該当なし | 10G | |
| **ヨハネスブルグ** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | 南アフリカ北部 | 該当なし | British Telecom、Internet Solutions - Cloud Connect、Liquid Telecom、Orange、Teraco |
| **クアラルンプール** | [TIME dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | 該当なし | 該当なし | TIME dotCom |
| **ラスベガス** | [Switch LV](https://www.switch.com/las-vegas) | 1 | 該当なし | 該当なし | CenturyLink Cloud Connect、Megaport |
| **ロンドン** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | 英国南部 | 10G、100G | AT&T NetBond、British Telecom、Colt、Equinix、InterCloud、Internet Solutions - Cloud Connect、Interxion、Jisc、Level 3 Communications、Megaport、MTN、NTT Communications、Orange、PCCW Global Limited、Tata Communications、Telehouse - KDDI、Telenor、Telia Carrier、Verizon、Vodafone、Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | 英国南部 | 10G、100G | IX Reach、Equinix、Telehouse - KDDI |
| **ロサンゼルス** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | 該当なし | 該当なし | CoreSite、Equinix、Megaport、Neutrona Networks、NTT、Transtelco、Zayo |
| **マルセイユ** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | フランス南部 | 該当なし | DE-CIX、Interxion、Jaguar Network |
| **メルボルン** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | オーストラリア南東部 | 10G、100G | AARNet、Devoli、Equinix、Megaport、NEXTDC、Optus、Telstra Corporation、TPG Telecom |
| **マイアミ** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | 該当なし | 10G | C3ntro+、Equinix、Megaport、Neutrona Networks |
| **ミラノ** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | 該当なし | 10G | |
| **モントリオール** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | 該当なし | 該当なし | Bell Canada、Cologix、Megaport、Telus、Zayo |
| **ムンバイ** | Tata Communications | 2 | インド西部 | 該当なし | Global CloudXchange (GCX)、Reliance Jio、Sify、Tata Communications、Verizon |
| **ムンバイ 2** | Airtel | 2 | インド西部 | 該当なし | Airtel、Sify、Vodafone Idea |
| **ミュンヘン** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | 該当なし | 10G、100G | |
| **ニューヨーク** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | 該当なし | 10G、100G | CenturyLink Cloud Connect、Colt、Coresite、Equinix、InterCloud、Megaport、Packet、Zayo |
| **ニューポート (ウェールズ)** | [Next Generation Data](https://www.nextgenerationdata.co.uk) | 1 | 英国西部 | 該当なし | British Telecom、Colt、Level 3 Communications、Next Generation Data |
| **大阪** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | 西日本 | 該当なし | Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT SmartConnect、Softbank |
| **パリ** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | フランス中部 | 該当なし | CenturyLink Cloud Connect、Colt、Equinix、Intercloud、Interxion、Orange、Telia Carrier、Zayo |
| **パース** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | 該当なし | 10G | Megaport、NextDC |
| **ケベック シティ** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | カナダ東部 | 該当なし | Bell Canada、Megaport |
| **サンアントニオ** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | 米国中南部 | 10G、100G | CenturyLink Cloud Connect、Megaport |
| **サンパウロ** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | ブラジル南部 | 該当なし | Aryaka Networks、Ascenty Data Centers、British Telecom、Equinix、Level 3 Communications、Neutrona Networks、Orange、Tata Communications、Telefonica、UOLDIVEO |
| **シアトル** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | 米国西部 2 | 10G、100G | Aryaka Networks、Equinix、Level 3 Communications、Megaport、Telus、Zayo |
| **ソウル** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | 韓国中部 | 10G、100G | KINX、LG CNS、Sejong Telecom |
| **シリコン バレー** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | 米国西部 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Comcast、Coresite、Equinix、InterCloud、Internet2、IX Reach、Packet、PacketFabric、Level 3 Communications、Megaport、Orange、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **シリコン バレー2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | 米国西部 | 10G、100G | Colt、Coresite | 
| **シンガポール** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | 東南アジア | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、Epsilon Global Communications、Equinix、InterCloud、Level 3 Communications、Megaport、NTT Communications、Orange、SingTel、Tata Communications、Telstra Corporation、Verizon、Vodafone |
| **シンガポール 2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | 東南アジア | 10G、100G | Colt、Epsilon Global Communications、Megaport、SingTel |
| **スタバンゲル** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | 該当なし | 10G、100G | |
| **ストックホルム** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | 該当なし | 10G | Equinix、Telia Carrier |
| **シドニー** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | オーストラリア東部 | 10G、100G | AARNet、AT&T NetBond、British Telecom、Devoli、Equinix、Kordia、Megaport、NEXTDC、NTT Communications、Optus、Orange、Spark NZ、Telstra Corporation、TPG Telecom、Verizon、Vocus Group NZ |
| **台北** | Chief Telecom | 2 | 該当なし | 10G | Chief Telecom、FarEasTone |
| **東京** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | 東日本 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Colt、Equinix、Internet Initiative Japan Inc. - IIJ、NTT Communications、NTT EAST、Orange、Softbank、Verizon |
| **トロント** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | カナダ中部 | 10G、100G | AT&T NetBond、Bell Canada、CenturyLink Cloud Connect、Cologix、Equinix、IX Reach Megaport、Telus、Verizon、Zayo |
| **ワシントン DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | 米国東部、米国東部 2 | 10G、100G | Aryaka Networks、AT&T NetBond、British Telecom、CenturyLink Cloud Connect、Cologix、Comcast、Coresite、Equinix、Internet2、InterCloud、Level 3 Communications、Megaport、Neutrona Networks、NTT Communications、Orange、PacketFabric、SES、Sprint、Tata Communications、Telia Carrier、Verizon、Zayo |
| **ワシントン DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | 米国東部、米国東部 2 | 10G、100G | Coresite、Viasat、Zayo | 
| **チューリッヒ** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | 該当なし | 該当なし | Intercloud、Interxion、Swisscom |

 **+** は近日対応予定を表します

### <a name="national-cloud-environments"></a>国内のクラウド環境

Azure の各国のクラウドは互いに分離され、またグローバルな商用 Azure からも分離されています。 ある Azure クラウドの ExpressRoute は、他のクラウド内の Azure リージョンには接続できません。

### <a name="us-government-cloud"></a>米国政府のクラウド
| **Location** | **アドレス** | **ローカル Azure リージョン**| **ER Direct** | **サービス プロバイダー** |
| --- | --- | --- | --- | --- |
| **シカゴ** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 該当なし | 10G、100G | AT&T NetBond、Equinix、Level 3 Communications、Verizon |
| **ダラス** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 該当なし | 10G、100G | Equinix、Megaport、Verizon |
| **ニューヨーク** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | 該当なし | 10G、100G | Equinix、CenturyLink Cloud Connect、Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | 米国政府アリゾナ | 該当なし | AT&T NetBond、CenturyLink Cloud Connect、Megaport |
| **サンアントニオ** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | 米国政府テキサス | 該当なし | CenturyLink Cloud Connect、Megaport |
| **シリコン バレー** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | 該当なし | 10G、100G | Equinix、Level 3 Communications、Verizon |
| **シアトル** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 該当なし | 該当なし | Equinix、Megaport |
| **ワシントン DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD 東、米国政府バージニア州 | 10G、100G | AT&T NetBond、CenturyLink Cloud Connect、Equinix、Level 3 Communications、Megaport、Verizon |

### <a name="china"></a>中国
| **Location** | **サービス プロバイダー** |
| --- | --- |
| **北京** |China Telecom |
| **北京 2** | China Telecom、GDS |
| **上海** |China Telecom |
| **上海 2** | China Telecom、GDS |

詳細については、 [中国の ExpressRoute](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>ドイツ
| **Location** | **サービス プロバイダー** |
| --- | --- |
| **ベルリン** |e-shelter、Megaport+、T-Systems |
| **フランクフルト** |Colt、Equinix、Interxion |

## <a name="c1partners"></a>Exchange プロバイダー経由の接続
接続プロバイダーが上記のセクションの一覧にない場合でも、接続を作成できます。

* 接続プロバイダーが上の表に記載されているいずれかの Exchange に接続されているかどうかをその接続プロバイダーに確認します。 次のリンクから、Exchange プロバイダーが提供するサービスの詳細情報を収集できます。 一部の接続プロバイダーは既にイーサネット Exchange に接続されています。
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* その接続プロバイダーに、選択したピアリングの場所までネットワークを拡張してもらいます。
  * 単一障害点がないように、接続プロバイダーが可用性の高い方法で接続を拡張していることを確認します。
* Microsoft に接続するために、接続プロバイダーとしてその Exchange で ExpressRoute 回線を要求します。
  * 「 [ExpressRoute 回線の作成](expressroute-howto-circuit-classic.md) 」の手順に従い、接続を構築します。

## <a name="connectivity-through-satellite-operators"></a>衛星通信業者経由の接続
遠隔地でファイバー接続がない場合、あるいはその他の接続方法を探してみる場合、次の衛星通信業者をお調べください。 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>その他のサービス プロバイダー経由の接続
| **Location** | **Exchange** | **接続プロバイダー** |
| --- | --- | --- |
| **アムステルダム** | Equinix、Interxion、Level 3 Communications | BICS、CloudXpress、Eurofiber、Fastweb S.p.A、Gulf Bridge International、Kalaam Telecom Bahrain B.S.C、MainOne、Nianet、POST Telecom Luxembourg、Proximus、TDC Erhverv、Telecom Italia Sparkle、Telekom Deutschland GmbH、Telia |
| **アトランタ** | Equinix| Crown Castle
| **ケープタウン** | Teraco | MTN |
| **シカゴ** | Equinix| Crown Castle、Spectrum Enterprise、Windstream |
| **ダラス** | Equinix、Megaport | Axtel、C3ntro Telecom、Cox Business、Crown Castle、Data Foundry、Spectrum Enterprise、Transtelco |
| **フランクフルト** | Interxion | BICS、Cinia、Nianet、QSC AG、Telekom Deutschland GmbH |
| **ハンブルク** | Equinix | Cinia |
| **香港特別行政区** | Equinix | Chief、Macroview Telecom |
| **ヨハネスブルグ** | Teraco | MTN |
| **ロンドン** | BICS、Equinix、euNetworks| Bezeq International Ltd.、CoreAzure、Epsilon Telecommunications Limited、Exponential E、HSO、NexGen Networks、Proximus、Tamares Telecom、Zain |
| **ロサンゼルス** | Equinix |Crown Castle、Spectrum Enterprise、Transtelco |
| **マドリッド** | Level3 | Zertia |
| **モントリオール** | Cologix、Equinix | Airgate Technologies, Inc.Aptum Technologies、Rogers、Zirro |
| **ニューヨーク** |Equinix、Megaport | Altice Business、Crown Castle、Spectrum Enterprise、Webair |
| **パリ** | Equinix | Proximus |
| **ケベック シティ** | Megaport | Fibrenoire |
| **サンパウロ** | Equinix | Venha Pra Nuvem |
| **シアトル** |Equinix | Alaska Communications |
| **シリコン バレー** |Coresite、Equinix | Cox Business、Spectrum Enterprise、Windstream、X2nsat Inc. |
| **シンガポール** |Equinix |1CLOUDSTAR、BICS、CMC Telecom、Epsilon Telecommunications Limited、LGA Telecom、United Information Highway (UIH) |
| **スラウ** | Equinix | HSO|
| **シドニー** | Megaport | Macquarie Telecom Group|
| **東京** | Equinix | アルテリア・ネットワークス株式会社、株式会社ブロードバンドタワー |
| **トロント** | Equinix、Megaport | Airgate Technologies Inc.、Beanfield Metroconnect、Aptum Technologies、IVedha Inc、Rogers、Thinktel、Zirro|
| **ワシントン DC** |Equinix | Altice Business、BICS、Cox Business、Crown Castle、Gtt Communications Inc、Epsilon Telecommunications Limited、Masergy、Windstream |

## <a name="expressroute-system-integrators"></a>ExpressRoute システム インテグレーター
ネットワークの規模によっては、ニーズに合わせてプライベート接続を有効にするのは難しい場合があります。 次の表のいずれかのシステム インテグレーターを使用すると、ExpressRoute の利用開始に役立ちます。

| **大陸** | **システム インテグレーター** |
| --- | --- |
| **アジア** |Avanade Inc.、OneAs1a |
| **オーストラリア** | Ensyst、IT Consultancy、MOQdigital、Vigilant.IT |
| **ヨーロッパ** |Avanade Inc.、Altogee、Bright Skies GmbH、Inframon、MSG Services、New Signature、Nelite、Orange Networks、sol-tec |
| **北米** |Avanade Inc.、Equinix Professional Services、FlexManage、Lightstream、Perficient、Presidio |
| **南アメリカ** |Avanade Inc.、Venha Pra Nuvem |
## <a name="next-steps"></a>次の手順
* ExpressRoute の詳細については、「 [ExpressRoute のFAQ](expressroute-faqs.md)」をご覧ください。
* すべての前提条件を満たしていることを確認します。 「 [Azure ExpressRoute の前提条件](expressroute-prerequisites.md)」を参照してください。

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "場所のマップ"
