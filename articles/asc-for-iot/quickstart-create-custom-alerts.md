---
title: クイック スタート:Azure Security Center for IoT のカスタム アラートを作成する
description: このクイックスタートでは、Azure Security Center for IoT のカスタム デバイス アラートを作成して割り当てます。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d1757868-da3d-4453-803a-7e3a309c8ce8
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/08/2019
ms.author: mlottner
ms.openlocfilehash: eca5d69efb04cf8210b0b2aa502bcee5cd4f5264
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904138"
---
# <a name="quickstart-create-custom-alerts"></a>クイック スタート:カスタム アラートの作成


カスタムのセキュリティ グループおよびアラートを使用することで、エンド ツー エンドのセキュリティ情報とカテゴリ別のデバイス知識を最大限に活用し、IoT ソリューション全体のセキュリティを確実に向上させます。 

## <a name="why-use-custom-alerts"></a>カスタム アラートを使用する理由 

お客様の IoT デバイスについて最もよく知っているのはお客様自身です。

予想されるデバイスの動作を完全に理解しているお客様のために、Azure Security Center for IoT には、その理解をデバイスの動作ポリシーに変換し、予想される通常の動作からの逸脱に基づいてアラートする機能があります。

## <a name="security-groups"></a>セキュリティ グループ

セキュリティ グループでは、デバイスの論理グループを定義して、それらのセキュリティ状態を一元的に管理できます。

これらのグループは、特定のハードウェアを備えたデバイス、一定の場所にデプロイされたデバイス、または特定のニーズに適したその他のグループを表す場合があります。

セキュリティ グループは、**SecurityGroup** という名前のデバイス ツイン タグ プロパティによって定義されます。 既定では、IoT Hub 上の各 IoT ソリューションに、**default** という名前のセキュリティ グループが 1 つあります。 **SecurityGroup** プロパティの値を変更して、デバイスのセキュリティ グループを変更します。
 
例:

```
{
  "deviceId": "VM-Contoso12",
  "etag": "AAAAAAAAAAM=",
  "deviceEtag": "ODA1BzA5QjM2",
  "status": "enabled",
  "statusUpdateTime": "0001-01-01T00:00:00",
  "connectionState": "Disconnected",
  "lastActivityTime": "0001-01-01T00:00:00",
  "cloudToDeviceMessageCount": 0,
  "authenticationType": "sas",
  "x509Thumbprint": {
    "primaryThumbprint": null,
    "secondaryThumbprint": null
  },
  "version": 4,
  "tags": {
    "SecurityGroup": "default"
  }, 
```

セキュリティ グループを使用して、デバイスを論理カテゴリにグループ化します。 グループの作成後、それらを任意のカスタム アラートに割り当てて、最も効果的なエンド ツー エンド IoT セキュリティ ソリューションを実現します。 

## <a name="customize-an-alert"></a>アラートのカスタマイズ

1. IoT ハブを開きます。 
2. **[セキュリティ]** セクションで **[カスタム アラート]** をクリックします。 
3. カスタマイズの適用先にしたいセキュリティ グループを選択します。 
4. **[Add a custom alert]\(カスタム アラートの追加\)** をクリックします。
5. ドロップダウン リストからカスタムのアラートを選択します。 
6. 必須のプロパティを編集して、 **[OK]** をクリックします。
7. 必ず **[保存]** をクリックします。 新しいアラートは、保存しないと IoT ハブを次に閉じたときに削除されます。

 
## <a name="alerts-available-for-customization"></a>カスタマイズに使用できるアラート

次の表は、カスタマイズに使用できるアラートの概要を示します。


| 重大度 | 名前 | データ ソース | 説明 | 推奨される修復方法|
|---|---|---|---|---|
| 低      | カスタム アラート - AMQP プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である          | IoT Hub     | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (AMQP プロトコル) の数が、現在の構成の許容範囲外です。||
| 低      | カスタム アラート - AMQP プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲外である | IoT Hub     | 特定の時間枠内においてデバイスから拒否された、クラウドからデバイスへのメッセージ (AMQP プロトコル) の数が、現在の構成の許容範囲外です。||
| 低      | カスタム アラート - AMQP プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である      | IoT Hub     | 特定の時間枠内におけるデバイスからクラウドへのメッセージ (AMQP プロトコル) の量が、現在の構成の許容範囲外です。|   |
| 低      | カスタム アラート - ダイレクト メソッド呼び出しの回数が許容範囲外である | IoT Hub     | 特定の時間枠内におけるダイレクト メソッド呼び出しの量が、現在の構成の許容範囲外です。||
| 低      | カスタム アラート - ファイル アップロードの回数が許容範囲外である | IoT Hub     | 特定の時間枠内におけるファイル アップロードの量が、現在の構成の許容範囲外です。| |
| 低      | カスタム アラート - HTTP プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である | IoT Hub     | ある時間枠内におけるクラウドからデバイスへのメッセージ (HTTP プロトコル) の量が、構成された許容範囲内にありません                                  |
| 低      | カスタム アラート - HTTP プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲内にない | IoT Hub     | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (HTTP プロトコル) の量が、現在の構成の許容範囲外です。 |
| 低      | カスタム アラート - HTTP プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である | IoT Hub| 特定の時間枠内におけるデバイスからクラウドへのメッセージ (HTTP プロトコル) の量が、現在の構成の許容範囲外です。|    |
| 低      | カスタム アラート - MQTT プロトコルによるクラウドからデバイスへのメッセージの数が、許容範囲外である | IoT Hub     | 特定の時間枠内におけるクラウドからデバイスへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。|   |
| 低      | カスタム アラート - MQTT プロトコルによる、拒否されたクラウドからデバイスへのメッセージの数が、許容範囲外である | IoT Hub     | 特定の時間枠内においてデバイスから拒否された、クラウドからデバイスへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。 |
| 低      | カスタム アラート - MQTT プロトコルによるデバイスからクラウドへのメッセージの数が、許容範囲外である          | IoT Hub     | 特定の時間枠内におけるデバイスからクラウドへのメッセージ (MQTT プロトコル) の量が、現在の構成の許容範囲外です。|
| 低      | カスタム アラート - コマンド キューの消去の回数が許容範囲外である                               | IoT Hub     | 特定の時間枠内におけるコマンド キューの消去の量が、現在の構成の許容範囲外です。||
| 低      | カスタム アラート - モジュール ツインの更新の回数が許容範囲外である                                       | IoT Hub     | 特定の時間枠内におけるモジュール ツインの更新の量が、現在の構成の許容範囲外です。|
| 低      | カスタム アラート - 未承認の操作の回数が許容範囲外である  | IoT Hub     | 特定の時間枠内における未承認の操作の量が、現在の構成の許容範囲外です。|
| 低      | カスタム アラート - アクティブな接続の数が許容範囲外である  | エージェント       | 特定の時間枠内におけるアクティブ接続の数が、現在の構成の許容範囲外です。|  デバイス ログを調査します。 接続が発生した場所を調べ、それが無害か悪意のあるものかを判断します。 悪意のあるものである場合は、マルウェアを除去し、ソースを調べます。 無害である場合は、許可されている接続の一覧にソースを追加します。  |
| 低      | カスタム アラート - 許可されていない IP に対して作成されたアウトバウンド接続                             | エージェント       | 許可されている IP の一覧にない IP に対して、アウトバウンド接続が作成されました。 |デバイス ログを調査します。 接続が発生した場所を調べ、それが無害か悪意のあるものかを判断します。 悪意のあるものである場合は、マルウェアを除去し、ソースを調べます。 無害である場合は、許可されている IP の一覧にソースを追加します。                        |
| 低      | カスタム アラート - 失敗したローカル ログインの回数が許容範囲外である                               | エージェント       | 特定の時間枠内における失敗したローカル ログインの量が、現在の構成の許容範囲外です。 |   |
| 低      | カスタム アラート - 許可されているユーザーの一覧にないユーザーのログイン | エージェント       | 許可されているユーザーの一覧にないローカル ユーザーが、デバイスにログインしました。|  生データを保存している場合は、ログ分析アカウントに移動し、そのデータを使用してデバイスを調査し、ソースを特定してから、これらの設定の許可と禁止の一覧を修正します。 現在は生データを保存していない場合は、デバイスに移動し、それらの設定の許可と禁止の一覧を修正します。|
| 低      | カスタム アラート - 許可されていないプロセスが実行された | エージェント       | 許可されていないプロセスがデバイス上で実行されました。 |生データを保存している場合は、ログ分析アカウントに移動し、そのデータを使用してデバイスを調査し、ソースを特定してから、これらの設定の許可と禁止の一覧を修正します。 現在は生データを保存していない場合は、デバイスに移動し、それらの設定の許可と禁止の一覧を修正します。  |
|


## <a name="next-steps"></a>次の手順

次の記事に進んで、セキュリティ エージェントのデプロイ方法を確認してください。

> [!div class="nextstepaction"]
> [セキュリティ エージェントのデプロイ](how-to-deploy-agent.md)
