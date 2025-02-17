---
title: IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを IoT Hub (Linux) に接続する | Microsoft Docs
description: IoT ハブと接続する IoT プラグ アンド プレイ プレビューのサンプル デバイス コードを Linux 上でビルドして実行します。 Azure CLI を使用してデバイスからハブに送信される情報を表示します。
author: dominicbetts
ms.author: dobett
ms.date: 09/10/2019
ms.topic: quickstart
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 38a6deb8d021c5e6a20d765cc7aee5b86042f557
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585978"
---
# <a name="quickstart-connect-a-sample-iot-plug-and-play-preview-device-application-running-on-linux-to-iot-hub"></a>クイック スタート:Linux 上で実行されているサンプルの IoT プラグ アンド プレイ プレビュー デバイス アプリケーションを IoT Hub に接続する

このクイックスタートでは、IoT プラグ アンド プレイ デバイス アプリケーションのサンプルを Linux 上でビルドし、それをご利用の IoT ハブに接続し、デバイスからハブに送信される情報を Azure CLI を使用して表示する方法について説明します。 このサンプル アプリケーションは C で記述され、C 対応の Azure IoT device SDK に含められています。ソリューション開発者は、Azure CLI ツールを使用すれば、デバイス コードを表示しなくても IoT プラグ アンド プレイ デバイスの機能を理解することができます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>前提条件

このクイックスタートは、Ubuntu Linux を使用していることを前提としています。 このチュートリアルの手順は、Ubuntu 18.04 を使用してテストされました。

このクイックスタートを完了するには、ご利用のローカルの Linux マシンに次のソフトウェアをインストールする必要があります。

`apt-get` コマンドを使用して、**GCC**、**Git**、**cmake**、およびすべての依存関係をインストールします。

```sh
sudo apt-get update
sudo apt-get install -y git cmake build-essential curl libcurl4-openssl-dev libssl-dev uuid-dev
```

`cmake` のバージョンが **2.8.12** より大きく、**GCC** のバージョンが **4.4.7** より大きいことを確認します。

```sh
cmake --version
gcc --version
```

## <a name="prepare-an-iot-hub"></a>IoT ハブを準備する

また、このクイックスタートを完了するには、ご利用の Azure サブスクリプション内に Azure IoT ハブが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

> [!IMPORTANT]
> パブリック プレビュー中、IoT プラグ アンド プレイ機能は、**米国中部**、**北ヨーロッパ**、および**東日本**の各リージョンで作成された IoT ハブでのみご利用いただけます。

Azure CLI をローカルで使用している場合、`az` のバージョンは **2.0.73** 以降である必要があります。Azure Cloud Shell には最新バージョンが使用されています。 マシンにインストールされているバージョンを確認するには、`az --version` コマンドを使用します。

Azure CLI 用の Microsoft Azure IoT 拡張機能を追加します。

```azurecli-interactive
az extension add --name azure-cli-iot-ext
```

このクイックスタートの手順を行うには、バージョン **0.8.5** 以降の拡張機能が必要です。 インストールされているバージョンを確認するには `az extension list` コマンドを、必要に応じて更新するには `az extension update` コマンドを使用します。

CLI をローカルで使用している場合は、次のコマンドを使用して Azure サブスクリプションにサインインします。

```bash
az login
```

Azure Cloud Shell を使用している場合は、既に自動的にサインインしています。

IoT ハブがない場合は、[こちらの手順に従って作成します](../iot-hub/iot-hub-create-using-cli.md)。 パブリック プレビューの期間中、IoT プラグ アンド プレイは、北ヨーロッパ、米国中部、東日本の各リージョンでご利用いただけます。 これらのリージョンのいずれかでハブを作成するようにしてください。

次のコマンドを実行して、ご利用の IoT ハブに新しいデバイス ID を作成します。 **YourIoTHubName** のプレースホルダーは実際の IoT ハブ名に置き換えます。

```azurecli-interactive
az iot hub device-identity create --hub-name [YourIoTHubName] --device-id mydevice
```

次のコマンドを実行して、登録したばかりのデバイス用の_デバイス接続文字列_を取得します。

```azurecli-interactive
az iot hub device-identity show-connection-string --hub-name [YourIoTHubName] --device-id mydevice --output table
```

## <a name="prepare-the-development-environment"></a>開発環境の準備

このクイックスタートでは、Azure IoT C device SDK を複製してビルドするのに使用できる開発環境を準備します。

コマンド プロンプトを開きます。 次のコマンドを実行して、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) の GitHub リポジトリを複製します。

```bash
git clone https://github.com/Azure/azure-iot-sdk-c --recursive -b public-preview
```

このコマンドは、完了するまでに数分かかります。

## <a name="build-the-code"></a>コードのビルド

デバイス SDK を使用して、含まれているサンプル コードをビルドします。 ビルドしたアプリケーションでは、IoT ハブに接続するデバイスのシミュレーションが行われます。 アプリケーションによりテレメトリとプロパティが送信され、コマンドが受け取られます。

1. デバイス SDK ルート フォルダーに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。

    ```bash
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    ```

1. 次のコマンドを実行して、デバイス SDK と生成されたコード スタブをビルドします。

    ```bash
    cmake ..
    cmake --build .
    ```

## <a name="update-your-model-repository"></a>モデル リポジトリを更新する

サンプルを実行する前に、デバイスの機能モデルとインターフェイスの定義を会社のモデル リポジトリに追加します。

1. Microsoft の職場または学校のアカウント、またはお持ちの場合は Microsoft パートナー ID を使用して、[Azure Certified for IoT portal](https://preview.catalog.azureiotsolutions.com) ポータルにサインインします。

1. **[Company repository]\(会社リポジトリ\)** 、 **[Capability models]\(機能モデル\)** の順に選択します。

1. **[新規]** 、 **[アップロード]** の順に選択します。

1. デバイスの SDK ルート フォルダーの `digitaltwin_client/samples` フォルダーにある `SampleDevice.capabilitymodel.json` ファイルを選択します。 **[開く]** 、 **[保存]** の順に選択し、モデル ファイルをリポジトリにアップロードします。

1. **[Company repository]\(会社リポジトリ\)** 、 **[インターフェイス]** の順に選択します。

1. **[新規]** 、 **[アップロード]** の順に選択します。

1. デバイスの SDK ルート フォルダーの `digitaltwin_client/samples/digitaltwin_sample_environmental_sensor` フォルダーにある `EnvironmentalSensor.interface.json` ファイルを選択します。 **[開く]** 、 **[保存]** の順に選択し、インターフェイス ファイルをリポジトリにアップロードします。

1. **[Company repository]\(会社リポジトリ\)** 、 **[接続文字列]** の順に選択します。 最初の会社モデル リポジトリの接続文字列をメモしておきます。このクイックスタートの後の手順で使用します。

## <a name="run-the-sample"></a>サンプルを実行する

SDK でサンプル アプリケーションを実行して、IoT ハブにテレメトリを送信する IoT プラグ アンド プレイ デバイスをシミュレートします。 サンプル アプリケーションを実行するには:

1. `cmake` フォルダーから、実行可能ファイルを含むフォルダーに移動します。

    ```bash
    cd digitaltwin_client/samples/digitaltwin_sample_device
    ```

1. 実行可能ファイルを実行します。

    ```bash
    ./digitaltwin_sample_device "{your device connection string}"
    ```

シミュレートされたデバイスで、テレメトリの送信、コマンドのリッスン、プロパティの更新のリッスンが開始されます。

### <a name="use-the-azure-iot-cli-to-validate-the-code"></a>Azure IoT CLI を使用してコードを検証する

デバイス クライアントのサンプルを起動したら、Azure CLI を使って動作していることを確認します。

次のコマンドを使用して、サンプル デバイスから送信されているテレメトリを表示します。 出力にテレメトリが表示されるまで、状況に応じて 1、2 分待つ必要があります。

```azurecli-interactive
az iot dt monitor-events --hub-name {your IoT hub} --device-id mydevice
```

デバイスから送信されたプロパティを表示するには、次のコマンドを使用します。

```azurecli-interactive
az iot dt list-properties --hub-name {your IoT hub} --device-id mydevice --interface sensor --source private --repo-login "{your company model repository connection string}"
```

## <a name="next-steps"></a>次の手順

このクイックスタートでは、IoT プラグ アンド プレイ デバイスを IoT ハブに接続する方法を学習しました。 ご利用の IoT プラグ アンド プレイ デバイスとやりとりするソリューションを構築する方法の詳細については、次を参照してください。

> [!div class="nextstepaction"]
> [方法: デバイスに接続してやりとりする](howto-develop-solution.md)
