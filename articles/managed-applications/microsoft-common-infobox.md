---
title: Azure の InfoBox UI 要素 | Microsoft Docs
description: Azure Portal の Microsoft.Common.InfoBox UI 要素について説明します。 管理対象アプリケーションをデプロイするとき、テキストまたは警告を追加するために使用します。
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 67ae05c2ec7b36d76e49f26d5765bbc68b952292
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331685"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox UI 要素
情報ボックスを追加するコントロール。 ボックスには、提供する値の特定に役立つ重要なテキストや警告が含まれています。 詳細についての URI にリンクされている場合もあります。

## <a name="ui-sample"></a>UI サンプル
![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>スキーマ
```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="remarks"></a>解説

* `icon`では、**None** **Info** **Warning**または **Error**を使用します。
* `uri` プロパティは省略可能です。

## <a name="sample-output"></a>サンプル出力

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="next-steps"></a>次の手順
* UI 定義の作成の概要については、「[CreateUiDefinition の基本概念](create-uidefinition-overview.md)」を参照してください。
* UI 要素の共通プロパティの説明については、「[CreateUiDefinition の要素](create-uidefinition-elements.md)」を参照してください。
