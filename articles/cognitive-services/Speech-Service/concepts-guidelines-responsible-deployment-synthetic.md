---
title: 合成音声テクノロジの責任あるデプロイのためのガイドライン
titleSuffix: Azure Cognitive Services
description: 合成音声テクノロジを使用するための Microsoft の一般的な設計ガイドライン。 これらは、合成音声の責任ある開発を導くために、Microsoft がボイス タレント、消費者、および発話障害のある人々と共に行った研究で開発されました。
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: fe8730b0d97ae3783282cdd401fd5889ed08ad9a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73506562"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>合成音声テクノロジの責任あるデプロイのためのガイドライン
ここでは、合成音声テクノロジを使用するための Microsoft の一般的な設計ガイドラインを示します。 これらは、合成音声の責任ある開発を導くために、Microsoft がボイス タレント、消費者、および発話障害のある人々と共に行った研究で開発されました。

## <a name="general-considerations"></a>一般的な考慮事項
合成音声テクノロジのデプロイでは、ほとんどのシナリオで次のガイドラインが適用されます。

### <a name="disclose-when-the-voice-is-synthetic"></a>音声が合成音声の場合に開示する
音声がコンピューターによって生成されたものであることを開示することにより、偽装による有害な結果を招くリスクを最小限に抑えられるだけでなく、音声を発信する組織への信頼も高まります。 [開示する方法](concepts-disclosure-guidelines.md)の詳細をご覧ください。

### <a name="select-appropriate-voice-types-for-your-scenario"></a>シナリオに適した音声の種類を選択する
使用状況と、合成音声の使用に関連する潜在的な有害性について慎重に検討してください。 たとえば、忠実度の高い合成音声は、個人的なメッセージ、金融取引、人間の適応性や共感を必要とする複雑な状況など、リスクの高いシナリオには適していない場合があります。 また、音声の種類に対するユーザーの期待も異なる場合があります。 たとえば、センシティブなニュースが合成音声で読み上げられるのを聞く場合、より共感的で人間的なニュースの読み上げ方を好むユーザーもいれば、より単調で偏見のない音声を好むユーザーもいます。 ユーザーの好みをよりよく理解するために、アプリケーションのテストを検討してください。

### <a name="be-transparent-about-capabilities-and-limitations"></a>機能と制限について透明性を保つ
ユーザーは忠実度の高い合成音声エージェントと対話するときに、より高い期待を持つ傾向があります。 その結果、システムの機能がこのような期待に応えられない場合、信頼が損なわれ、不快な経験となったり、有害な経験をもたらす可能性があります。

### <a name="provide-optional-human-support"></a>人的サポートをオプションで提供する
あいまいなトランザクションのシナリオ (たとえばコール サポート センター) では、ユーザーはコンピューター エージェントが自分の要求に適切に応答することを常に信頼するわけではありません。 このような状況では、システムの音声や機能の現実的な品質に関係なく、人的サポートが必要になる場合があります。

## <a name="considerations-for-voice-talent"></a>ボイス タレントに関する考慮事項
合成音声を作成するために声優などのボイス タレントを起用する場合、次のガイドラインが適用されます。

### <a name="obtain-meaningful-consent-from-voice-talent"></a>ボイス タレントから有意な同意を得る
ボイス タレントは、自分の音声フォント (その使用方法と場所) を管理し、それが使用されるたびに報酬を受け取ることを期待しています。 したがって、システム所有者は、ボイス タレントから書面による明示的な許可を得て、使用ケース、使用期間、報酬などについて明確な契約仕様書を持つ必要があります。 一部のボイス タレントは、このテクノロジが悪用される可能性があることを認識しておらず、システム所有者からこのテクノロジの機能について教育を受ける必要があります。 ボイス タレントと同意の詳細については、Microsoft の[透明性に関するメモ](https://aka.ms/neural-tts-transparency-note)を参照してください。


## <a name="considerations-for-those-with-speech-disorders"></a>発話障害のある個人についての考慮事項
発話障害のある個人と連携して合成音声テクノロジを作成またはデプロイするには、次のガイドラインが適用されます。

### <a name="provide-guidelines-to-establish-contracts"></a>契約を確立するためのガイドラインを提供する
発話の支援として合成音声を使用する個人との契約を確立するためのガイドラインを提供します。 この契約では、音声を所有する当事者、使用期間、所有権の譲渡条件、音声フォントを削除する手順、および不正アクセスを防ぐ方法を指定することを検討する必要があります。 さらに、その個人が許可した場合、死後の音声フォントの所有権を家族に契約上譲渡できるようにします。

### <a name="account-for-inconsistencies-in-speech-patterns"></a>音声パターンの一貫性の欠如について考慮する
発話障害のある個人が自分の音声フォントを記録する場合、音声パターンの一貫性の欠如 (特定の単語が不明瞭な発音になる、または発音できないこと) が原因で、録音プロセスが複雑になる可能性があります。 このような場合は、合成音声テクノロジと録音セッションで、それらに対応する必要があります (つまり、休憩を入れて録音セッションの回数を増やします)。

### <a name="allow-modification-over-time"></a>時間の経過に伴う変化を考慮に入れる
発話障害のある個人は、加齢を反映するために自分の合成音声を更新したいと考えることがあります (たとえば、子供が思春期を迎えた場合など)。 また、様式的な好みが時間の経過と共に変化することもあり、ピッチやアクセントなどの音声特性に変更を加えたい場合もあります。


## <a name="reference-docs"></a>リファレンス ドキュメント

* [透明性に関するメモ](https://aka.ms/neural-tts-transparency-note)
* [規制の概要](concepts-gating-overview.md)
* [開示する方法](concepts-disclosure-guidelines.md)
* [設計パターンを開示する](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>次の手順

* [透明性に関するメモ](https://aka.ms/neural-tts-transparency-note)
* [開示する方法](concepts-disclosure-guidelines.md)
* [設計パターンを開示する](concepts-disclosure-patterns.md)
