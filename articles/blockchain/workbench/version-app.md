---
title: ブロックチェーン アプリケーションのバージョン管理 - Azure Blockchain Workbench
description: Azure Blockchain Workbench プレビューでのアプリケーション バージョンの使用方法。
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/14/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: f6edbb54962ff11b5ed1497b6a202b9010c67aae
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328841"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Blockchain Workbench プレビューでのアプリケーションのバージョン管理

Azure Blockchain Workbench プレビュー アプリの複数のバージョンを作成して使用できます。 同じアプリケーションの複数のバージョンをアップロードすると、バージョン履歴が利用可能になり、ユーザーは使用するバージョンを選択できます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

* Blockchain Workbench のデプロイ。 デプロイについて詳しくは、[Azure Blockchain Workbench のデプロイ](deploy.md)に関するページをご覧ください
* Blockchain Workbench 内にデプロイされたブロックチェーン アプリケーション。 「[Azure Blockchain Workbench でブロックチェーン アプリケーションを作成する](create-app.md)」をご覧ください

## <a name="add-an-app-version"></a>アプリ バージョンを追加する

新しいバージョンを追加するには、新しい構成ファイルとスマート コントラクト ファイルを Blockchain Workbench にアップロードします。

1. Web ブラウザーで、Blockchain Workbench の Web アドレスに移動します。 たとえば、`https://{workbench URL}.azurewebsites.net/`Blockchain Workbench の Web アドレスを調べる方法については、「[Blockchain Workbench Web URL (Blockchain Workbench の Web URL)](deploy.md#blockchain-workbench-web-url)」を参照してください
2. [Blockchain Workbench 管理者](manage-users.md#manage-blockchain-workbench-administrators)としてサインインします。
3. 別のバージョンで更新するブロック チェーン アプリケーションを選択します。
4. **[バージョンの追加]** を選択します。 **[バージョンの追加]** ペインが表示されます。
5. アップロードする新しいバージョンのコントラクト構成ファイルおよびコントラクト コード ファイルを選択します。 構成ファイルは自動的に検証されます。 アプリケーションをデプロイする前に検証エラーを修正してください。
6. **[バージョンの追加]** を選択して、新しいバージョンのブロック チェーン アプリケーションを追加します。

    ![新しいバージョンを追加する](media/version-app/add-version.png)

ブロックチェーン アプリケーションのデプロイには数分かかることがあります。 デプロイが完了したら、アプリケーション ページを更新します。 アプリケーションを選択して、 **[バージョン履歴]** ボタンを選択すると、アプリケーションのバージョン履歴が表示されます。

> [!IMPORTANT]
> 以前のバージョンのアプリケーションは無効になっています。 以前のバージョンを個別に再有効化できます。
>
> 新しいバージョンのアプリケーション ロールが変更された場合、アプリケーション ロールにメンバーを再度追加することが必要な場合もあります。

## <a name="using-app-versions"></a>アプリ バージョンの使用

既定では、最後に有効化されたアプリケーションのバージョンが Blockchain Workbench で使用されます。 アプリケーションの以前のバージョンを使用する場合は、最初にアプリケーション ページからバージョンを選択する必要があります。

1. Blockchain Workbench のアプリケーション セクションで、使用するコントラクトを含んだアプリケーション チェックボックスを選択します。 以前のバージョンが有効になっている場合、[バージョン履歴] ボタンを選択できます。
2. **[バージョン履歴]** ボタンを選択します。
3. [バージョン履歴] ペインで、 *[更新日]* 列のリンクを選択してアプリケーションのバージョンを選択します。

    ![以前のバージョンを選択する](media/version-app/use-version.png)

    新しいコントラクトを作成したり、以前のバージョンのコントラクトで操作を実行することができます。 アプリケーションのバージョンはアプリケーション名に続けて表示され、以前のバージョンに関する警告が表示されます。

## <a name="next-steps"></a>次の手順

* [Azure Blockchain Workbench のトラブルシューティング](troubleshooting.md)
