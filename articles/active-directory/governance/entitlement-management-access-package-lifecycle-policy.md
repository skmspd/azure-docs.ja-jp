---
title: Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージのライフサイクル設定を変更する - Azure Active Directory
description: Azure Active Directory エンタイトルメント管理 (プレビュー) でアクセス パッケージのライフサイクル設定を変更する方法を説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46d63a12443edf04db3570fa43fbd8f20619122e
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72391974"
---
# <a name="change-lifecycle-settings-for-an-access-package-in-azure-ad-entitlement-management-preview"></a>Azure AD エンタイトルメント管理 (プレビュー) でアクセス パッケージのライフサイクル設定を変更する

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

アクセス パッケージ管理者は、既存のポリシーを編集することで、アクセス パッケージのライフサイクル設定をいつでも変更できます。 ポリシーの有効期限を変更する場合、既に承認待ちまたは承認済みの状態になっている要求の有効期限は変更されません。

この記事では、既存のアクセス パッケージのライフサイクル設定を変更する方法について説明します。

## <a name="open-lifecycle-settings"></a>ライフサイクル設定を開く

アクセス パッケージのライフサイクル設定を変更するには、対応するポリシーを開く必要があります。 次の手順を実行して、アクセス パッケージのライフサイクル設定を開きます。

**事前に必要なロール:** グローバル管理者、ユーザー管理者、カタログ所有者、またはアクセス パッケージ マネージャー

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューで **[アクセス パッケージ]** をクリックして、アクセス パッケージを開きます。

1. **[ポリシー]** をクリックして、編集するライフサイクル設定があるポリシーをクリックします。

    ページの下部で [ポリシーの詳細] ウィンドウが開きます。

    ![アクセス パッケージ - [ポリシーの詳細] ウィンドウ](./media/entitlement-management-shared/policy-details.png)

1. **[編集]** をクリックしてポリシーを編集します。

    ![アクセス パッケージ - ポリシーの編集](./media/entitlement-management-shared/policy-edit.png)

1. **[ライフサイクル]** タブをクリックして、ライフサイクル設定を開きます。

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="next-steps"></a>次の手順

- [Change request and approval settings for an access package](entitlement-management-access-package-request-policy.md) (アクセス パッケージの要求と承認の設定を変更する)