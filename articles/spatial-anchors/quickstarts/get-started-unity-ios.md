---
title: クイック スタート - Azure Spatial Anchors を使用して Unity iOS アプリを作成する | Microsoft Docs
description: このクイック スタートでは、Spatial Anchors を使用する iOS アプリを Unity でビルドする方法について説明します。
author: craigktreasure
manager: aliemami
services: azure-spatial-anchors
ms.author: crtreasu
ms.date: 02/24/2019
ms.topic: quickstart
ms.service: azure-spatial-anchors
ms.openlocfilehash: a121cc7bcb9fba3a01c1e71c7b9e6fc67dce0572
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092157"
---
# <a name="quickstart-create-a-unity-ios-app-with-azure-spatial-anchors"></a>クイック スタート:Azure Spatial Anchors を使用して Unity iOS アプリを作成する

このクイック スタートでは、[Azure Spatial Anchors](../overview.md) を使用して Unity iOS アプリを作成する方法について説明します。 Azure Spatial Anchors は、クロスプラットフォーム対応の開発者向けサービスです。このサービスを使用すると、時間が経過した後でも複数のデバイス間で位置情報を保持するオブジェクトを使用して複合現実エクスペリエンスを作成できます。 完了すると、Unity でビルドされた、空間アンカーを保存して呼び戻すことができる ARKit iOS アプリが作成されます。

学習内容は次のとおりです。

> [!div class="checklist"]
> * Spatial Anchors アカウントを作成する
> * Unity のビルド設定を準備する
> * Spatial Anchors アカウント識別子とアカウント キーを構成する
> * Xcode プロジェクトをエクスポートする
> * iOS デバイスに展開して実行する

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、以下が必要です。

- <a href="https://unity3d.com/get-unity/download" target="_blank">Unity 2019.1 以上</a>、最新バージョンの <a href="https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12" target="_blank">Xcode</a>、および <a href="https://cocoapods.org" target="_blank">CocoaPods</a> がインストールされた macOS コンピューター。
- HomeBrew を介してインストールされた Git。 `/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"` コマンドをターミナルに 1 行で入力します。 次に、`brew install git` を実行します。
- 開発者向けの <a href="https://developer.apple.com/documentation/arkit/verifying_device_support_and_user_permission" target="_blank">ARKit 対応</a> iOS デバイス。

[!INCLUDE [Create Spatial Anchors resource](../../../includes/spatial-anchors-get-started-create-resource.md)]

## <a name="download-and-open-the-unity-sample-project"></a>Unity のサンプル プロジェクトをダウンロードして開く

[!INCLUDE [Clone Sample Repo](../../../includes/spatial-anchors-clone-sample-repository.md)]

[!INCLUDE [Open Unity Project](../../../includes/spatial-anchors-open-unity-project.md)]

[!INCLUDE [iOS Unity Build Settings](../../../includes/spatial-anchors-unity-ios-build-settings.md)]

## <a name="configure-account-identifier-and-key"></a>アカウント識別子とキーを構成する

**[Project]\(プロジェクト\)** ウィンドウで `Assets/AzureSpatialAnchors.Examples/Scenes` に移動し、シーン ファイル `AzureSpatialAnchorsBasicDemo.unity` を開きます。

[!INCLUDE [Configure Unity Scene](../../../includes/spatial-anchors-unity-configure-scene.md)]

**[File]\(ファイル\)**  ->  **[Save]\(保存\)** の順に選択してシーンを保存します。

## <a name="export-the-xcode-project"></a>Xcode プロジェクトをエクスポートする

[!INCLUDE [Export Unity Project](../../../includes/spatial-anchors-unity-export-project-snip.md)]

[!INCLUDE [Configure Xcode](../../../includes/spatial-anchors-unity-ios-xcode.md)]

アプリの指示に従って、アンカーを配置し、呼び戻します。

完了したら、Xcode で **[Stop]\(停止\)** を押してアプリを停止します。

## <a name="troubleshooting"></a>トラブルシューティング

### <a name="rendering-issues"></a>レンダリングに関する問題

アプリを実行したとき、背景としてカメラが表示されない場合は (たとえば、代わりに空白、青、または他のテクスチャが表示される場合)、Unity に資産を再インポートすることが必要な場合があります。 アプリを停止します。 Unity の上部のメニューで、 **[Assets]\(資産\) -> [Re-import all]\(すべて再インポート\)** を選択します。 その後、アプリをもう一度実行します。

### <a name="cocoapods-issues-on-macos-catalina-1015"></a>macOS Catalina (10.15) での CocoaPods に関する問題

最近 macOS Catalina (10.15) に更新し、そのとき事前に CocoaPods がインストールされていると、CocoaPods が破損した状態になり、ポッドや `.xcworkspace` プロジェクト ファイルを正しく構成できないことがあります。 この問題を解決するには、次のコマンドを実行して CocoaPods を再インストールする必要があります。

```shell
brew update
brew install cocoapods --build-from-source
brew link --overwrite cocoapods
```

[!INCLUDE [Clean-up section](../../../includes/clean-up-section-portal.md)]

[!INCLUDE [Next steps](../../../includes/spatial-anchors-quickstarts-nextsteps.md)]

> [!div class="nextstepaction"]
> [チュートリアル:デバイス間で Spatial Anchors を共有する](../tutorials/tutorial-share-anchors-across-devices.md)
