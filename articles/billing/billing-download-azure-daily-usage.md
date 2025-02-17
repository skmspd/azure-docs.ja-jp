---
title: Azure の使用状況と料金の表示とダウンロード
description: Azure の毎日の使用量と料金をダウンロードまたは表示する方法について説明します。
keywords: 使用量の請求,利用料金,使用量のダウンロード,使用量の表示,Azure 請求書, Azure 使用量
author: bandersmsft
manager: jureid
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: e7d1947b2194c04bb5269887b73e2f4fa13df6e7
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375734"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Azure の使用量と料金の表示とダウンロード

Azure の利用状況と請求金額に対する日単位の内訳を Azure portal からダウンロードすることができます。 Azure 使用状況情報を取得するアクセス許可を持つのは、特定のロール (アカウント管理者やエンタープライズ管理者など) だけです。 課金情報へのアクセス権の取得に関する詳細については、[ロールを使用した Azure の課金へのアクセス管理](billing-manage-access.md)に関するページをご覧ください。

Microsoft 顧客契約 (MCA) を結んでいる場合、Azure の利用状況と請求金額を表示するには、課金プロファイルの所有者、共同作成者、閲覧者、または請求書管理者である必要があります。  Microsoft Partner Agreement (MPA) を結んでいる場合、Azure の利用状況と請求金額を表示してダウンロードできるのは、取引先組織である Microsoft の全体管理者ロールと管理者エージェント ロールだけです。 [Azure portal で課金アカウントの種類を確認](#check-your-billing-account-type)してください。

## <a name="download-usage-from-the-azure-portal-csv"></a>Azure portal から利用状況をダウンロードする (.csv)

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. お持ちのアクセス権によっては、課金アカウントまたは課金プロファイルを選択する必要があります。
1. 左側のメニューの **[課金]** から **[請求書]** を選択します。
1. 請求書グリッドで、ダウンロードする使用量に対応する請求期間の行を探します。
1. 右側にあるダウンロード アイコンまたは省略記号 (`...`) をクリックします。
1. ダウンロード メニューから **[Azure の利用状況と請求金額をダウンロードする]** を選択します。

## <a name="download-usage-for-ea-customers"></a>EA のお客様の使用量のダウンロード

EA のお客様として使用量データを表示およびダウンロードするには、料金表示ポリシーが有効になっているエンタープライズ管理者、アカウント所有者、または部門管理者である必要があります。

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. "*コスト管理 + 請求*" を検索します。

    ![Azure portal の検索を表示するスクリーンショット](./media/billing-download-azure-usage/portal-cm-billing-search.png)

1. **[使用量 + 請求金額]** を選択します。
1. ダウンロードしたい月で、 **[ダウンロード]** を選択します。

## <a name="download-usage-for-pending-charges"></a>保留中の料金の使用量のダウンロード

Microsoft 顧客契約を結んでいる場合は、現在の請求期間の月度累計使用量をダウンロードすることができます。 これらの料金はまだ請求されていません。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. "*コスト管理 + 請求*" を検索します。
3. 課金プロファイルを選択します。 お持ちのアクセス権によっては、最初に請求先アカウントを選択することが必要な場合があります。
4. **[概要]** 領域で、月度累計請求金額の下にあるダウンロード リンクを見つけます。
5. **[Azure の利用状況と請求金額]** を選択します。

    ![概要からのダウンロードを示すスクリーンショット](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-billing-account-type"></a>課金アカウントの種類を確認する
[!INCLUDE [billing-check-account-type](../../includes/billing-check-account-type.md)]

## <a name="need-help-contact-us"></a>お困りの際は、 お問い合わせください。

ご質問がある場合やヘルプが必要な場合は、[サポート リクエストを作成](https://go.microsoft.com/fwlink/?linkid=2083458)してください。

## <a name="next-steps"></a>次の手順

請求書および使用状況の詳細については、以下を参照してください。

- [Microsoft Azure の詳細な使用状況の用語を理解する](billing-understand-your-usage.md)
- [Microsoft Azure の課金内容の確認](billing-understand-your-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [組織の Azure の価格の表示とダウンロード](billing-ea-pricing.md)

Microsoft 顧客契約を結んでいる場合は、次のページを参照してください。

- [Microsoft 顧客契約での Azure の詳細な使用状況の用語を理解する](billing-mca-understand-your-usage.md)
- [Microsoft 顧客契約の請求書での料金を理解する](billing-mca-understand-your-bill.md)
- [Microsoft Azure の請求書の表示とダウンロード](billing-download-azure-invoice.md)
- [Microsoft 顧客契約に関する税務書類の表示とダウンロード](billing-mca-download-tax-document.md)
- [組織の Azure の価格の表示とダウンロード](billing-ea-pricing.md)
