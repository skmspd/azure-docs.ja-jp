---
title: コンテンツの翻訳を防止する - Translator Text API
titleSuffix: Azure Cognitive Services
description: Translator Text API によるコンテンツの翻訳を防止します。 Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: f3bf784898f7f51beea890d8d2a8401af1403fbc
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888116"
---
# <a name="how-to-prevent-translation-of-content-with-the-translator-text-api"></a>Translator Text API によるコンテンツの翻訳を防止する方法

Translator Text API では、翻訳されないようコンテンツにタグを付けることができます。 たとえば、コードやブランド名、ローカライズしても意味がない語句にタグを付けることが考えられます。

## <a name="methods-for-preventing-translation"></a>翻訳を防止する方法
1. Twitter タグ (@somethingtopassthrough または #somethingtopassthrough) でエスケープする。 翻訳後にエスケープを解除します。

2. コンテンツに `notranslate` でタグ付けします。

   例:

   ```html
   <div class="notranslate">This will not be translated.</div>
   <div>This will be translated. </div>
   ```

3. [動的ディクショナリ](dynamic-dictionary.md)を使用して決まった翻訳を指示する。

4. Translator Text API に翻訳対象の文字列を渡さない。

5. カスタム トランスレーター: 語句に対して 100% の確率で決まる翻訳は、[カスタム トランスレーターのディクショナリ](custom-translator/what-is-dictionary.md)を使用して指示します。


## <a name="next-steps"></a>次の手順
> [!div class="nextstepaction"]
> [Translator API 呼び出しでの翻訳を防止する](reference/v3-0-translate.md)
