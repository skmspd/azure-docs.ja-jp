---
title: Azure Data Factory でのタンブリング ウィンドウ トリガーの依存関係の作成
description: Azure Data Factory でタンブリング ウィンドウ トリガーの依存関係を作成する方法について説明します。
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 3780a50429b95c402459866b7e3c87e8a91207d3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/06/2019
ms.locfileid: "73683710"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>タンブリング ウィンドウ トリガーの依存関係の作成

この記事では、タンブリング ウィンドウ トリガーの依存関係を作成する手順について説明します。 タンブリング ウィンドウ トリガーに関する全般な情報については、[タンブリング ウィンドウ トリガーを作成する方法](how-to-create-tumbling-window-trigger.md)に関するページを参照してください。

依存関係チェーンを構築し、データ ファクトリ内の別のトリガーの実行が成功した後でのみトリガーが実行されるようにするには、この高度な機能を使用して、タンブリング ウィンドウの依存関係を作成します。

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Data Factory UI で依存関係を作成する

トリガーの依存関係を作成するには、 **[トリガー]、[詳細設定]、[新規]** の順に選択した後、適切なオフセットとサイズに依存するトリガーを選択します。 **[完了]** を選択して、依存関係に対するデータ ファクトリの変更を発行して有効にします。

![依存関係の作成](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "依存関係の作成")

## <a name="tumbling-window-dependency-properties"></a>タンブリング ウィンドウの依存関係プロパティ

依存関係を持つタンブリング ウィンドウ トリガーには、次のプロパティがあります。

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        }
    }
}
```

次の表に、タンブリング ウィンドウの依存関係を定義するために必要な属性の一覧を示します。

| **プロパティ名** | **説明**  | **Type** | **必須** |
|---|---|---|---|
| type  | 既存のすべてのタンブリング ウィンドウ トリガーがこのドロップダウンに表示されます。 依存関係を設定するトリガーを選択します。  | TumblingWindowTriggerDependencyReference または SelfDependencyTumblingWindowTriggerReference | はい |
| offset | 依存関係トリガーのオフセット。 時間範囲形式で値を指定します。正と負の両方のオフセットを指定できます。 このプロパティは、トリガーが自己に依存する場合は必須であり、それ以外の場合は省略可能です。 自己依存関係は、常に負のオフセットにする必要があります。 値が指定されていない場合、ウィンドウはトリガーそのものと同じになります。 | Timespan<br/>(hh:mm:ss) | 自己依存関係:はい<br/>その他:いいえ |
| size | 依存関係のタンブリング ウィンドウのサイズ。 正の timespan 値を指定します。 このプロパティは省略可能です。 | Timespan<br/>(hh:mm:ss) | いいえ  |

> [!NOTE]
> タンブリング ウィンドウ トリガーは、最大 2 つの他のトリガーに依存することができます。

## <a name="tumbling-window-self-dependency-properties"></a>タンブリング ウィンドウの自己依存関係プロパティ

前のウィンドウが正常に完了するまではトリガーが次のウィンドウに進まないシナリオでは、自己依存関係を構築します。 前の時間内のそれ自体の以前の実行が成功したことに依存する自己依存トリガーには、次のプロパティがあります。

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>使用シナリオと例

タンブリング ウィンドウの依存関係プロパティのシナリオと使用法をイラストで次に示します。

### <a name="dependency-offset"></a>依存関係のオフセット

![オフセットの例](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "オフセットの例")

### <a name="dependency-size"></a>依存関係のサイズ

![サイズの例](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "サイズの例")

### <a name="self-dependency"></a>自己依存関係

![自己依存関係](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "自己依存関係")

### <a name="dependency-on-another-tumbling-window-trigger"></a>別のタンブリング ウィンドウ トリガーに対する依存関係

過去 7 日間の出力を集計する別の日次ジョブに依存する日次テレメトリ処理ジョブで、7 日分のローリング ウィンドウ ストリームを生成するとします。

![依存関係の例](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "依存関係の例")

### <a name="dependency-on-itself"></a>自己に対する依存関係

ジョブの出力ストリームにずれがない日次ジョブ:

![自己依存関係の例](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "自己依存関係の例")

## <a name="monitor-dependencies"></a>依存関係を監視する

[トリガーの実行] 監視ページで、依存関係チェーンと該当するウィンドウを監視できます。 **[監視]、[トリガーの実行]** の順に移動します。 [アクション] 列で、トリガーを再実行したり、その依存関係を確認することができます。

![トリガーの実行を監視する](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "トリガーの実行を監視する")

[View Trigger Dependencies]\(トリガーの依存関係の表示\) をクリックすると、依存関係の状態を確認できます。 依存関係トリガーのいずれかが失敗した場合は、正常に再実行して、依存するトリガーが稼働できるようにする必要があります。 タンブリング ウィンドウ トリガーは、依存関係を 7 日間待機してからタイムアウトします。

![依存関係を監視する](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "依存関係を監視する")

トリガーの依存関係のスケジュールを視覚的に表示するには、[ガント] ビューを選択します。

![依存関係を監視する](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "依存関係を監視する")

## <a name="next-steps"></a>次の手順

* [タンブリング ウィンドウ トリガーの作成方法](how-to-create-tumbling-window-trigger.md)を確認します
