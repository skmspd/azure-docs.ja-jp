---
title: Azure Media Clipper を使用したクリップの作成 | Microsoft Docs
description: アセットからメディア クリップをビルドするためのツールである Azure Media Clipper の概要
services: media-services
keywords: クリップ;サブクリップ;エンコード;メディア
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 51f85dffd48e451b477018ef20491f8619a30f25
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685018"
---
# <a name="create-clips-with-azure-media-clipper"></a>Azure Media Clipper を使用したクリップの作成 

Azure Media Clipper は無料の JavaScript ライブラリで、Web 開発者はこれを使ってユーザーにメディア クリップを作成するためのインターフェイスを提供できます。 このツールを任意の Web ページに統合して、アセットを読み込み、クリッピング ジョブを送信するための API を提供できます。

Azure Media Clipper では次のことが可能です。
- ライブ アーカイブの前後のスレートをトリミングする 
- AMS ライブ イベント、ライブ アーカイブ、または fMP4 VOD ファイルのビデオのハイライトを作成する 
- 複数のソースのビデオを連結する 
- AMS メディア アセットのサマリー クリップを作成する 
- フレームの精度でビデオをクリップする 
- 画像グループ (GOP) の精度で既存のライブおよび VOD アセットの動的マニフェスト フィルターを生成する 
- お使いのメディア サービスのアカウントでアセットに対するエンコード ジョブを作成する

新機能のリクエスト、アイデアの提供、またはフィードバックは、[Azure Media Services の UserVoice](https://aka.ms/amsvoice/)に関するページにお送りください。 特定の問題や質問がある場合、またはバグを発見した場合は、Media Services チーム (amcinfo@microsoft.com) にご連絡ください。

次の図は、Clipper のインターフェイスを示しています。![Azure Media Clipper](media/media-services-azure-media-clipper-overview/media-services-azure-media-clipper-interface.PNG)

## <a name="release-notes"></a>リリース ノート
Clipper の最新リリースに関するブログ投稿、さまざまな既知の問題、変更ログについては、次の一覧をご覧ください。
- [ブログの投稿](https://azure.microsoft.com/blog/azure-media-clipper/)
- [既知の問題の一覧](https://amp.azure.net/libs/amc/latest/docs/known_issues.html)
- [変更ログ](https://amp.azure.net/libs/amc/latest/docs/changelog.html)

## <a name="browser-support"></a>ブラウザーのサポート
Azure Media Clipper は、最新の HTML5 テクノロジを使用してビルドされたもので、次のブラウザーをサポートします。

- Microsoft Edge 13 以上
- Internet Explorer 11 以上
- Chrome 54 以上
- Safari 10 以上
- Firefox 50 以上

> [!NOTE]
> 現在は Azure Media Services からのストリームの HTML5 再生のみがサポートされています。

## <a name="language-support"></a>言語のサポート
Clipper ウィジェットは、次の 18 の言語で使用できます。
- 簡体中国語
- 繁体中国語
- チェコ語
- オランダ語、フラマン語
- 英語
- フランス語
- ドイツ語
- ハンガリー語
- イタリア語
- 日本語
- 韓国語
- ポーランド語
- ポルトガル語 (ブラジル)
- ポルトガル語 (ポルトガル)
- ロシア語
- スペイン語
- スウェーデン語
- トルコ語

## <a name="next-steps"></a>次の手順
Azure Media Clipper の使用を開始するには、[作業の開始](media-services-azure-media-clipper-getting-started.md)に関する記事から、ウィジェットを展開する方法の詳細についてお読みください。
