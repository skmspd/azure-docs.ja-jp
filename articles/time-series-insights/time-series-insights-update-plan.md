---
title: Azure Time Series Insights プレビューの環境を計画する | Microsoft Docs
description: Azure Time Series Insights プレビューの環境を計画します。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/24/2019
ms.custom: seodec18
ms.openlocfilehash: f5a12ca2bdccee1d2f738aa3c810577caf3d8eac
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73491954"
---
# <a name="plan-your-azure-time-series-insights-preview-environment"></a>Azure Time Series Insights プレビューの環境を計画する

この記事では、Azure Time Series Insights プレビューを使用して、迅速に計画し、開始するためのベスト プラクティスについて説明します。

> [!NOTE]
> 一般公開 Time Series Insights インスタンスを計画するためのベスト プラクティスについては、[Azure Time Series Insights 一般公開環境の計画](time-series-insights-environment-planning.md)に関するページを参照してください。

## <a name="best-practices-for-planning-and-preparation"></a>計画と準備のベスト プラクティス

環境の計画と準備に関するベストプラクティスについては、次の記事で詳しく説明しています。

* [Time Series Insights プレビュー環境をプロビジョニングする](#the-preview-environment)際に取得するもの。
* [タイム シリーズ ID と Timestamp プロパティ](#configure-time-series-ids-and-timestamp-properties)。
* [新しいタイム シリーズ モデルと独自のモデルの構築方法](#understand-the-time-series-model)。
* [JSON で効率的にイベントを送信する](#shape-your-events)方法。
* Time Series Insights の[ビジネス ディザスター リカバリーのオプション](#business-disaster-recovery)。

Azure Time Series Insights では、従量課金制のビジネス モデルが採用されています。 料金と容量の詳細については、「[Time Series Insights の価格](https://azure.microsoft.com/pricing/details/time-series-insights/)」を参照してください。

## <a name="the-preview-environment"></a>プレビュー環境

Time Series Insights プレビュー環境をプロビジョニングする際に、次の 2 つの Azure リソースを作成します。

* Azure Time Series Insights Preview 環境
* Azure Storage 汎用 V1 アカウント

プロビジョニング プロセスの一環として、ウォーム ストアを有効にするかどうかを指定します。 ウォーム ストアは、階層化クエリ エクスペリエンスを提供します。 有効にする場合は、7 日から 30 日の保持期間を指定する必要があります。 ウォーム ストアの保持期間内に実行されるクエリは、一般に応答時間が短縮されます。 クエリがウォーム ストアの保持期間を超える場合は、コールド ストアから提供されます。

ウォーム ストアのクエリは無料ですが、コールド ストアのクエリにはコストが発生します。 クエリ パターンを理解し、それに従ってウォーム ストア構成を計画することが重要です。 最新データに対する対話型分析はウォーム ストアに配置し、パターン分析と長期的傾向はコールド ストアに配置することをお勧めします。

> [!NOTE]
> 現在、ウォーム ストアでは最大 1000 のプロパティがサポートされています。

始めに、3 つの追加項目が必要です。

* [タイム シリーズ モデル](./time-series-insights-update-tsm.md)
* [Time Series Insights に接続されているイベント ソース](./time-series-insights-how-to-add-an-event-source-iothub.md)
* どちらもモデルにマップされ、有効な JSON 形式である[イベント ソースに送られるイベント](./time-series-insights-send-events.md)

## <a name="review-preview-limits"></a>プレビューの制限の確認

[!INCLUDE [Review Time Series Insights Preview limits](../../includes/time-series-insights-preview-limits.md)]

## <a name="configure-time-series-ids-and-timestamp-properties"></a>タイム シリーズ ID と Timestamp プロパティの構成

新しい Time Series Insights 環境を作成するには、タイム シリーズ ID を選択します。 そうすることで、データの論理パーティションとして機能します。 前述のように、タイム シリーズ ID の準備ができていることを確認します。

> [!IMPORTANT]
> タイム シリーズ ID *は、後で変更できません*。 最終的な選択と初めて使用する前に、それぞれを確認してください。

リソースを一意に区別するために、最大 3 つのキーを選択できます。 詳細については、「[Best practices for choosing a Time Series ID](./time-series-insights-update-how-to-id.md)」(タイム シリーズ ID の選択のベスト プラクティス) および[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)に関するページを参照してください。

**Timestamp** プロパティも重要です。 イベント ソースを追加する際に、このプロパティを指定できます。 各イベント ソースには、時間の経過と共にイベント ソースを追跡するために使用されるオプションの Timestamp プロパティがあります。 Timestamp の値は大文字と小文字が区別され、各イベント ソースの個々の仕様に書式設定される必要があります。

> [!TIP]
> イベント ソースの書式設定と解析の要件を確認します。

空白のままにすると、イベント ソースのイベント エンキュー時間が、イベント タイムスタンプとして使用されます。 履歴データまたはバッチ処理されたイベントを送信する場合は、既定のイベント エンキュー時間より、Timestamp プロパティをカスタマイズすることが役立ちます。 詳細については、[Azure IoT Hub にイベント ソースを追加する](./time-series-insights-how-to-add-an-event-source-iothub.md)方法に関するページを参照してください。

## <a name="understand-the-time-series-model"></a>タイム シリーズ モデルについて

Time Series Insights 環境のタイム シリーズ モデルを構成できるようになりました。 新しいモデルにより、IoT データの検索と分析が簡単になります。 時系列データのキュレーション、メンテナンス、およびエンリッチメントが可能になり、コンシューマー対応データ セットの準備に役立ちます。 モデルでは、タイム シリーズ ID が使われ、これは、型と呼ばれる変数と階層に一意のリソースを関連付けるインスタンスにマップされます。 新しい[タイム シリーズ モデル](./time-series-insights-update-tsm.md)について参照してください。

モデルは動的であるため、いつでも構築できます。 すばやく開始するため、データを Time Series Insights にプッシュする前に、モデルを構築してアップロードしてください。 モデルを構築するには、[タイム シリーズ モデルの使用](./time-series-insights-update-how-to-tsm.md)に関するページを参照してください。

多くのお客様で、タイム シリーズ モデルは既存の資産モデルや既に設置されている ERP システムにマップされます。 既存のモデルがない場合は、迅速に開始できるように、事前構築済みのユーザー エクスペリエンスが[提供されて](https://github.com/Microsoft/tsiclient)います。 モデルがどのように役立つかを考えるため、[サンプル デモ環境](https://insights.timeseries.azure.com/preview/demo)を参照してください。

## <a name="shape-your-events"></a>イベントの調整

Time Series Insights にイベントを送信する方法を確認できます。 理想的には、イベントは適切かつ効率的に非正規化されます。

お勧めの方法:

* タイム シリーズ モデルにメタデータを格納します。
* タイム シリーズ モード、インスタンス フィールド、およびイベントに、必要な情報 (タイム シリーズ ID や Timestamp プロパティなど) のみが含まれるようにします。

詳細については、[イベントの調整](./time-series-insights-send-events.md#supported-json-shapes)に関するページを参照してください。

[!INCLUDE [business-disaster-recover](../../includes/time-series-insights-business-recovery.md)]

## <a name="next-steps"></a>次の手順

- [Azure Advisor](../advisor/advisor-overview.md) を確認して、ビジネス復旧の構成オプションを計画します。
- Time Series Insights プレビューの[ストレージとイングレス](./time-series-insights-update-storage-ingress.md)の詳細を確認する。
- Time Series Insights プレビューの[データ モデリング](./time-series-insights-update-tsm.md)について学習する。
