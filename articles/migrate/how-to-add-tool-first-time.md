---
title: Azure Migrate 内での評価/移行ツールの初回追加 | Microsoft Docs
description: Azure Migrate プロジェクトを作成し、評価/移行ツールを追加する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: a4e66175b20552e632702cb2ba46d0ae6c0956d4
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720252"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>評価/移行ツールの初回追加

この記事では、評価ツールまたは移行ツールを [Azure Migrate](migrate-overview.md) プロジェクトに初めて追加する方法について説明します。  
Azure Migrate は、オンプレミスのアプリとワークロード、およびプライベート/パブリック クラウド VM の検出、評価、および Azure への移行を追跡するための中央ハブとなります。 このハブには、評価および移行のための Azure Migrate ツールのほか、他のツールや独立系ソフトウェア ベンダー (ISV) の[オファリング](migrate-services-overview.md#isv-integration)が用意されています。 

## <a name="create-a-project-and-add-a-tool"></a>プロジェクトの作成とツールの追加

Azure サブスクリプション内に新しい Azure Migrate プロジェクトを設定し、ツールを追加します。

- Azure Migrate のプロジェクトは、評価または移行しようとしている環境から収集された検出、評価、移行のメタデータを格納するために使用されます。 
- プロジェクトでは、検出されたアセットを追跡し、評価と移行を調整することができます。

1. Azure portal の **[すべてのサービス]** で、**Azure Migrate** を検索します。
2. **[サービス]** で **[Azure Migrate]** を選択します。

    ![Azure Migrate の設定](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. **[概要]** で **[サーバーの評価と移行]** をクリックします。
4. **[サーバーの検出、評価、移行]** で、 **[サーバーの評価と移行]** をクリックします。

    ![サーバーの検出と評価](./media/how-to-add-tool-first-time/assess-migrate.png)

1. **[サーバーの検出、評価、移行]** で、 **[ツールの追加]** をクリックします。
2. **[移行プロジェクト]** で、自分の Azure サブスクリプションを選択し、リソース グループがない場合は作成します。
3. **[プロジェクトの詳細]** で、プロジェクト名と、プロジェクトを作成したい地理的な場所を指定します。 

    ![Azure Migrate プロジェクトの作成](./media/how-to-add-tool-first-time/migrate-project.png)

    Azure Migrate プロジェクトは、これらのいずれの地域でも作成できます。

   **地理的な場所** | **保存場所のリージョン**
    --- | ---
    アジア   | 東南アジアまたは東アジア
    ヨーロッパ | 北ヨーロッパまたは西ヨーロッパ
    日本  | 東日本または西日本
    イギリス | 英国南部または英国西部
    米国 | 米国中部または米国西部 2
    カナダ | カナダ中部
    インド  | インド中部またはインド南部
    オーストラリア | オーストラリア南東部

    プロジェクトのために指定した地理的な場所は、オンプレミスの VM から収集されたメタデータを格納するためにのみ使用されます。 実際の移行では、任意のターゲット リージョンを選択できます。

    移行プロジェクトとそれに関連付けられているリソースをデプロイするために、geo 内の特定のリージョンを指定する必要がある場合 (サブスクリプションのポリシー制限により、特定の Azure リージョンへの Azure リソースのデプロイのみが許可される場合があります)、以下の API を使用して移行プロジェクトを作成できます。 サブスクリプション ID、リソース グループ名、Migrate プロジェクト名、および場所 (Azure Migrate がデプロイされる、表に記載されているいずれかの Azure リージョン) を指定します。

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. **[次へ]** をクリックし、評価ツールまたは移行ツールを追加します。

    > [!NOTE]
    > プロジェクトを作成するときは、少なくとも 1 つの評価ツールまたは移行ツールを追加する必要があります。

5. **[評価ツールの選択]** で、評価ツールを追加します。 評価ツールが不要な場合は、 **[今は評価ツールの追加をスキップします]**  >  **[次へ]** を選択します。 
2. **[移行ツールの選択]** で、必要に応じて移行ツールを追加します。 今すぐ移行ツールが必要でない場合は、 **[今は移行ツールの追加をスキップします]**  >  **[次へ]** を選択します。
3. **[ツールの確認と追加]** で設定を確認し、 **[ツールの追加]** をクリックします。

プロジェクトを作成した後、サーバーとワークロード、データベース、Web アプリの評価と移行のための追加のツールを選択できます。

## <a name="create-additional-projects"></a>追加のプロジェクトの作成

状況によっては、追加の Azure Migrate プロジェクトの作成が必要になる場合があります。 たとえば、データセンターが地理的に異なる場所にある場合、または地理的に異なる場所にメタデータを格納する必要がある場合です。 次のように、追加のプロジェクトを作成します。

1. 現在の Azure Migrate プロジェクト内で、 **[サーバー]** または **[データベース]** をクリックします。
2. 右上隅の現在のプロジェクト名の横にある **[変更]** をクリックします。
3. **[設定]** で、[ここをクリックして**新しいプロジェクトを作成する] を選択**します。
4. 前の手順で説明したように、新しいプロジェクトを作成し、新しいツールを追加します。

## <a name="next-steps"></a>次の手順

追加の[評価](how-to-assess.md)ツールと[移行](how-to-migrate.md)ツールを追加する方法を確認します。 
