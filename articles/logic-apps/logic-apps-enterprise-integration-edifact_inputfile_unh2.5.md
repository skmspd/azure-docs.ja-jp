---
title: EDIFACT メッセージ内の UNH 2.5 セグメント - Azure Logic Apps
description: Enterprise Integration Pack を使用して Azure Logic Apps で UNH2.5 セグメントを含む EDIFACT メッセージを解決する
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 04/27/2017
ms.openlocfilehash: c30c35375a45171c938f80e94dd7d9be4c3ee8b1
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/21/2019
ms.locfileid: "72679957"
---
# <a name="handle-edifact-documents-with-unh25-segments-in-azure-logic-apps"></a>Azure Logic Apps で UNH 2.5 セグメントを使用して EDIFACT メッセージを処理する

EDIFACT ドキュメント内に UNH2.5 セグメントが存在する場合、そのセグメントはスキーマの検索に使用されます。 たとえば、このサンプルの EDIFACT メッセージでは、UNH フィールドは `EAN008` です。

`UNH+SSDD1+ORDERS:D:03B:UN:EAN008`

このメッセージを処理するには、次に説明されている手順に従います。

1. スキーマを更新します。

1. 契約の設定を確認します。

## <a name="update-the-schema"></a>スキーマを更新する

メッセージを処理するには、UNH2.5 ルート ノード名を含むスキーマをデプロイする必要があります。 たとえば、サンプルの UNH フィールドのスキーマ ルート名は `EFACT_D03B_ORDERS_EAN008` です。 UNH2.5 セグメントが異なる `D03B_ORDERS` ごとに、個別のスキーマをデプロイする必要があります。

## <a name="add-schema-to-edifact-agreement"></a>EDIFACT 契約にスキーマを追加する

### <a name="edifact-decode"></a>EDIFACT デコード

受信メッセージをデコードするには、EDIFACT 契約の受信の設定でスキーマを設定します。

1. [Azure portal](https://portal.azure.com) で、統合アカウントを開きます。

1. 統合アカウントにスキーマを追加します。

1. EDIFACT 契約の受信の設定でスキーマを構成します。

1. EDIFACT 契約を選択し、 **[JSON として編集]** を選択します。 受信契約の `schemaReferences` セクションに UNH2.5 値を追加します。

   ![受信契約に UNH2.5 を追加する](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png)

### <a name="edifact-encode"></a>EDIFACT エンコード

受信メッセージをエンコードするには、EDIFACT 契約の送信の設定でスキーマを構成します。

1. [Azure portal](https://portal.azure.com) で、統合アカウントを開きます。

1. 統合アカウントにスキーマを追加します。

1. EDIFACT 契約の送信の設定でスキーマを構成します。

1. EDIFACT アグリーメントを選択して **[JSON として編集]** をクリックします。  送信契約の **schemaReferences** で UNH2.5 値を追加します。

1. EDIFACT 契約を選択し、 **[JSON として編集]** を選択します。 送信契約の `schemaReferences` セクションに UNH2.5 値を追加します。

   ![送信契約に UNH2.5 を追加する](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>次の手順

* [統合アカウント契約](../logic-apps/logic-apps-enterprise-integration-agreements.md)の詳細を学習します。