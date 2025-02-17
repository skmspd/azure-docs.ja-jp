---
title: Azure Lab Services の概要 | Microsoft Docs
description: Lab Services を利用すると、開発者、テスト担当者、教育者、学生、その他のユーザーが使用できるラボを仮想マシンで簡単に作成、管理、セキュリティ保護できることを説明します。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/13/2018
ms.author: spelluru
ms.openlocfilehash: 115320a8b4ee7afc6e420dcfa96612b91ea6d1a0
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790769"
---
# <a name="an-introduction-to-azure-lab-services"></a>Azure Lab Services の概要
Azure には、クラウドにラボ環境を設定することができるサービスが 2 つあります。 

- **Azure DevTest Labs** - このサービスでは、チーム用の環境 (クラウドでの開発環境、テスト環境など) を短時間で設定することができます。 ラボの所有者は、ラボを作成し、Windows または Linux の仮想マシンをプロビジョニングし、必要なソフトウェアとツールをインストールして、ラボのユーザーがそれらを利用できるようにします。 ラボのユーザーは、ラボ内の仮想マシン (VM) に接続して、日常の作業、短期的なプロジェクトなどに利用します。 ユーザーがラボ内のリソースの利用を始めたら、ラボの管理者は、複数のラボについてコストと使用状況を分析し、全体的なポリシーを設定して、組織やチームのコストを最適化できます。
- **Azure Lab Services**  - このサービスを使うと、マネージド ラボの種類を作成できます。 現在、Azure Lab Services でサポートされるマネージド ラボの種類はクラスルーム ラボだけです。 VM の作成から、エラーの処理やインフラストラクチャのスケーリングまで、マネージド ラボの種類用のインフラストラクチャの管理はすべて、サービス自体によって行われます。 IT 管理者が Azure Lab Services にラボ アカウントを作成した後、インストラクターはすぐに自分が担当するクラスのラボを設定し、クラスでの演習に必要な VM の数と種類を指定して、そのクラスにユーザーを追加できます。 ユーザーは、クラスへの登録後、VM にアクセスしてクラスの演習を行うことができます。  

## <a name="key-capabilities"></a>主な機能

これらのサービス (Azure DevTest Labs と Azure Lab Services) がサポートする主要な機能は次のとおりです。

- **高速かつ柔軟なラボのセットアップ**。 Azure Lab Services を使うと、ラボ所有者はニーズに合ったラボをすばやく設定できます。 サービスでは、マネージド ラボの種類で Azure インフラストラクチャに関するすべての作業を行うオプション、またはラボ所有者が自分のサブスクリプション内でインフラストラクチャを自己管理およびカスタマイズできるようにするオプションが提供されます。 サービスによって提供されるラボ用インフラストラクチャの組み込みのスケーリングと回復性は、サービスが自動的に管理します。
- **簡素化されたラボ ユーザー用のエクスペリエンス**。 クラスルーム ラボなどのマネージド ラボの種類では、ラボ ユーザーは登録コードでラボに登録し、いつでもラボにアクセスしてラボのリソースを使用できます。 DevTest Labs で作成されるラボでは、ラボの所有者が、ラボ ユーザーに対し、仮想マシンの作成とアクセス、データ ディスクの管理と再利用、および再利用可能なシークレットの設定を行うためのアクセス許可を付与できます。  
- **コストの最適化と分析**。 ラボの所有者は、ラボのスケジュールを設定して、仮想マシンのシャットダウンと起動を自動化できます。 ラボの所有者は、スケジュールを設定してユーザーがラボの仮想マシンにアクセスできる時間帯を指定し、ユーザーまたはラボごとに使用ポリシーを設定してコストを最適化し、ラボの使用状況とアクティビティの傾向を分析することができます。 クラスルーム ラボなどのマネージド ラボの種類の場合は、現在は、コストの最適化と分析のオプションの小さなサブセットを利用できます。
- **埋め込まれたセキュリティ**。 ラボ所有者は、ラボに対してプライベート仮想ネットワークとサブネットを設定し、共有パブリック IP アドレスを有効にすることができます。 ラボ ユーザーは、ExpressRoute またはサイト間 VPN で構成された仮想ネットワークを使用して、リソースに安全にアクセスできます。 (現在は、DevTest Labs でのみ使用可能)
- **ワークフローおよびツールへの統合**。 Azure Lab Services では、組織の Web サイトと管理システムにラボを統合することができます。 継続的インテグレーション/継続的配置 (CI/CD) ツール内から、環境を自動的にプロビジョニングできます。 (現在は、DevTest Labs でのみ使用可能)

> [!NOTE]
> 現在、Azure Lab Services は、Azure Marketplace イメージから作成された VM のみをサポートしています。 ラボ環境でカスタム イメージを使用する場合や他の PaaS リソースを作成する場合は、DevTest Labs を使用してください。 詳細については、[DevTest Labs でのカスタム イメージの作成](devtest-lab-create-custom-image-from-vm-using-portal.md)に関するページおよび [Resource Manager テンプレートを使用したラボ環境の作成](devtest-lab-create-environment-from-arm.md)に関するページを参照してください。

## <a name="scenarios"></a>シナリオ

Azure DevTest Labs と Azure Lab Services がサポートするシナリオの一部を次に示します。

### <a name="set-up-a-resizable-computer-lab-in-the-cloud-for-your-classroom"></a>クラスルーム用のクラウドにサイズ変更可能なコンピューター ラボを設定する  

- マネージド クラスルーム ラボを作成します。 必要なものをサービスに対して正確に指示するだけで、サービスがラボのインフラストラクチャを自動的に作成して管理するので、ユーザーはラボの技術的な詳細ではなく、クラスの指導に集中できます。
- クラスに必要なものだけで構成された仮想マシンのラボを学生に提供します。 各学生が授業のために限られた時間数だけ VM を使用できるようにします。  
- 学校の物理コンピューター ラボをクラウドに移動します。 ユーザーがラボに対して設定した最大使用量とコストになるように、VM の数を自動的にスケーリングします。
- 終了したらシングル クリックでラボを削除します。

### <a name="use-devtest-labs-for-development-environments"></a>開発環境用に DevTest Labs を使用する

Azure DevTest Labs を使うと多くの重要なシナリオを実装できますが、主要なシナリオの 1 つは、DevTest Labs を使って開発者のために開発用コンピューターをホストすることです。 このシナリオでは、DevTest Labs には次のような利点があります。

- 開発者は、必要に応じて開発用コンピューターをすばやくプロビジョニングできます。
- 再利用可能なテンプレートとアーティファクトを使用して、Windows と Linux の環境をプロビジョニングします。
- 開発者は、必要に応じていつでも、開発用コンピューターを簡単にカスタマイズできます。
- 管理者は、開発者が開発に必要な量より多くの VM を利用できないようにし、使用されていないときは VM がシャットダウンされるようにすることで、コストを制御できます。

詳しくは、[開発への DevTest Labs の使用](devtest-lab-developer-lab.md)に関するページをご覧ください。

### <a name="use-devtest-labs-for-test-environments"></a>テスト環境に DevTest Labs を使用する

Azure DevTest Labs を使うと多くの重要なシナリオを実装できますが、主要なシナリオは、DevTest Labs を使ってテスト担当者用のコンピューターをホストすることです。 このシナリオでは、DevTest Labs には次のような利点があります。

- テスト担当者は、再利用可能なテンプレートとアーティファクトを使って Windows と Linux の環境を迅速にプロビジョニングすることで、アプリケーションの最新バージョンをテストできます。
- テスト担当者は、複数のテスト エージェントをプロビジョニングすることで、ロード テストをスケールアップできます。
- 管理者は、テスト担当者がテストに必要な量より多くの VM を利用できないようにし、使用されていないときは VM がシャットダウンされるようにすることで、コストを制御できます。

詳しくは、[テストへの DevTest Labs の使用](devtest-lab-test-env.md)に関するページをご覧ください。

## <a name="types-of-labs"></a>ラボの種類
Azure Lab Services での**マネージド ラボの種類**と Azure Lab Services での**ラボ**の、2 種類のラボを作成できます。 ラボで必要な情報だけを入力し、ラボに必要なインフラストラクチャの設定と管理はサービスに任せる場合は、いずれかの**マネージド ラボの種類**を選びます。 現在、Azure Lab Services で作成できるマネージド ラボの種類は**クラスルーム ラボ**だけです。 独自のインフラストラクチャを管理する場合は、**Azure DevTest Labs** を使ってラボを作成します。

以下のセクションでは、これらのラボについて詳しく説明します。 

## <a name="managed-lab-types"></a>マネージド ラボの種類
Azure Lab Services では、Azure によって管理されるインフラストラクチャのラボを作成することができます。 この記事では、これらのラボをマネージド ラボの種類と呼びます。 マネージド ラボの種類では、特定のニーズに合わせたさまざまな種類のラボが提供されます。 現在サポートされているマネージド ラボの種類は、**クラスルーム ラボ**だけです。 

マネージド ラボの種類を使うと、最小限の設定ですぐに始めることができます。 サービス自体が、VM の作成から、エラーの処理やインフラストラクチャのスケーリングまで、ラボ用のインフラストラクチャの管理をすべて処理します。 クラスルーム ラボのようなマネージド ラボの種類を作成するには、組織用のラボ アカウントを最初に作成する必要があります。 ラボ アカウントは、組織内のすべてのラボが管理される中心的なアカウントとして機能します。 

これらのマネージド ラボの種類で Azure リソースを作成して使うと、サービスは内部 Microsoft サブスクリプション内にリソースを作成して管理します。 ユーザーの Azure サブスクリプションには作成されません。 サービスは、内部 Microsoft サブスクリプション内のこれらのリソースの使用量を追跡します。 この使用量は、ラボ アカウントが含まれるユーザーの Azure サブスクリプションに課金されます。   

**マネージド ラボの種類のユース ケース**をいくつか次に示します。 

- クラスに必要なものだけで構成された仮想マシンのラボを学生に提供します。 各学生が宿題や個人プロジェクトのために限られた時間数だけ VM を使用できるようにします。
- 計算量やグラフィックスの多い研究を実行するために、高パフォーマンスの計算 VM のプールを設定します。 必要に応じて VM を実行し、完了した後でマシンをクリーンアップします。 
- 学校の物理コンピューター ラボをクラウドに移動します。 ユーザーがラボに対して設定した最大使用量とコストになるように、VM の数を自動的にスケーリングします。  
- ハッカソンをホストするための仮想マシンのラボを迅速にプロビジョニングします。 終了したらシングル クリックでラボを削除します。 


## <a name="devtest-labs"></a>DevTest Labs
ユーザーが、ユーザー自身のサブスクリプション内で、すべてのインフラストラクチャと構成を自分で管理したい場合があります。 そのためには、Azure portal で Azure DevTest Labs を使ってラボを作成できます。 このようなラボについては、ラボ アカウントを作成する必要はありません。 これらのラボは、(マネージド ラボの種類用に存在する) ラボ アカウントには表示されません。  

**DevTest ラボを使うユース ケース**をいくつか次に示します。 

- ハッカソンや、カンファレンスでのハンズオン セッションをホストするために、仮想マシンのラボを迅速にプロビジョニングします。 終了したらシングル クリックでラボを削除します。 
- ユーザーのアプリケーションで構成された VM のプールを作成し、チームがバグ潰し用の仮想マシンを簡単に使用できるようにします。  
- 開発者が必要とするすべてのツールを構成された仮想マシンを開発者に提供します。 コストを最小限に抑えるために、自動的な開始とシャットダウンをスケジュールします。 
- 展開の一部としてテスト マシンのラボを繰り返し作成します。 最新のコードをテストし、完了したらテスト マシンをクリーンアップします。 
- スケール テストとパフォーマンス テストのために、異なる構成のさまざまな仮想マシンと複数のテスト エージェントを設定します。 
- ユーザーの製品の最新バージョンで構成されたラボを使って、顧客にトレーニング セッションを提供します。 各顧客が一定の時間だけラボを使用できるようにします。 


## <a name="managed-lab-types-vs-devtest-labs"></a>マネージド ラボの種類と DevTest Labs
次の表では、Azure Lab Services によってサポートされている 2 種類のラボを比較します。 

| 機能 | マネージド ラボの種類 | DevTest Labs |
| -------- | ----------------- | ---------- |
| ラボ内の Azure インフラストラクチャの管理 |  サービスによって自動的に管理されます | ユーザーが自分で管理します  |
| インフラストラクチャの問題に対する組み込みの回復性 | サービスによって自動的に処理されます | ユーザーが自分で管理します  |
| サブスクリプション管理 | サービスが、サービスの基になっている Microsoft サブスクリプション内のリソースの割り当てを処理します。 スケーリングはサービスによって自動的に処理されます。 | ユーザー自身が自分の Azure サブスクリプション内で管理します。 サブスクリプションの自動スケーリングはありません。 |
| ラボ内の Azure Resource Manager の展開 | 使用できません。 | 使用可能 |

## <a name="next-steps"></a>次の手順

次の記事を参照してください。 

- [クラスルーム ラボについて](./classroom-labs/classroom-labs-overview.md)
- [DevTest ラボについて](devtest-lab-overview.md)
