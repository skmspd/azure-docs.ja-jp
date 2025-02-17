---
title: Azure Time Series Insights で調整を監視し軽減する方法 | Microsoft Docs
description: この記事では、Azure Time Series Insights の待機時間と調整の原因になるパフォーマンスの問題を監視、診断、および緩和する方法について説明します。
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.devlang: csharp
ms.workload: big-data
ms.topic: troubleshooting
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 4e82cdf43f568b6415cb7cb00ce0244654559b7d
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990143"
---
# <a name="monitor-and-mitigate-throttling-to-reduce-latency-in-azure-time-series-insights"></a>Azure Time Series Insights で待機時間を削減するために調整を監視して緩和する

着信データの量が環境の構成を超えると、Azure Time Series Insights で待機時間や調整が発生する可能性があります。

分析するデータ量に合わせて環境を正しく構成することによって待機時間と調整を回避できます。

次の操作をしたときに待機時間と調整が発生する可能性が最も高くなります。

- 割り当てられた受信レートを超えるかもしれない古いデータを含むイベント ソースを追加した (Time Series Insights はキャッチアップする必要があります)。
- 環境にイベント ソースを追加した結果、追加イベントによるスパイクが発生した (環境の容量を超えた可能性があります)。
- 大量の履歴イベントをイベント ソースにプッシュした結果、ラグが発生した (Time Series Insights はキャッチアップする必要があります)。
- 参照データをテレメトリと結合した結果、イベントのサイズが大きくなった。  調整の観点から、パケット サイズが 32 KB の受信データ パケットは、それぞれ 1 KB のサイズの 32 個のイベントとして扱われます。 イベントの最大許容サイズは 32 KB です。32 KB を超えるデータ パケットは切り捨てられます。

## <a name="video"></a>ビデオ

### <a name="learn-about-time-series-insights-data-ingress-behavior-and-how-to-plan-for-itbr"></a>Time Series Insights データのイングレス動作と、それを計画する方法を説明します。</br>

> [!VIDEO https://www.youtube.com/embed/npeZLAd9lxo]

## <a name="monitor-latency-and-throttling-with-alerts"></a>アラートを使用した待機時間と調整の監視

アラートは、環境に起因する待機時間の問題の診断および緩和に役立ちます。

1. Azure portal で、 **[アラート]** を選択します。

   [![アラート](media/environment-mitigate-latency/add-alerts.png)](media/environment-mitigate-latency/add-alerts.png#lightbox)

1. **[ルールの作成]** パネルが表示されます。 **[条件]** の **[追加]** を選択します。

   [![アラートの追加](media/environment-mitigate-latency/alert-pane.png)](media/environment-mitigate-latency/alert-pane.png#lightbox)

1. 次に、シグナル ロジックの正確な条件を構成します。

   [![シグナル ロジックの構成](media/environment-mitigate-latency/configure-alert-rule.png)](media/environment-mitigate-latency/configure-alert-rule.png#lightbox)

   そこから、次のいずれかの条件を使用してアラートを構成できます。

   |メトリック  |説明  |
   |---------|---------|
   |**受信バイトの受信**     | イベント ソースから読み取られた生バイト数。 通常、生バイト数にはプロパティの名前と値が含まれます。  |  
   |**無効な受信メッセージの受信**     | すべての Azure Event Hubs または Azure IoT Hub イベント ソースから読み取られた無効なメッセージの数。      |
   |**受信メッセージの受信**   | すべての Event Hubs または IoT Hub イベント ソースから読み取られたメッセージの数。        |
   |**保存済みバイトの受信**     | クエリ用に保存済みで利用できるイベントの合計サイズ。 サイズはプロパティ値のみに基づいて計算されます。        |
   |**Ingress Stored Events (保存済みイベントの受信)**     |   クエリ用に保存済みで利用できるフラット化されたイベントの数。      |
   |**Ingress Received Message Time Lag (受信メッセージの受信のタイム ラグ)**    |  メッセージがイベント ソースでエンキューされた時刻とそれがイングレスで処理された時刻の間の差 (秒単位)。      |
   |**Ingress Received Message Count Lag (受信メッセージの受信のカウント ラグ)**    |  イベント ソース パーティションで待ち行列の最後に入っているメッセージのシーケンス番号とイングレスで処理されているメッセージのシーケンス番号の間の差。      |

   **[完了]** を選択します。

1. 目的のシグナル ロジックを構成したら、選択したアラート規則を目視で確認します。

   [![イングレス](media/environment-mitigate-latency/ingress.png)](media/environment-mitigate-latency/ingress.png#lightbox)

## <a name="throttling-and-ingress-management"></a>調整とイングレスの管理

* 調整中、 *[Ingress Received Message Time Lag]\(受信メッセージの受信のタイム ラグ\)* の値が表示され、メッセージがイベント ソースに届く実際の時間から Time Series Insights 環境が何秒遅れているのかが通知されます (約 30 ～ 60 秒のインデックス作成時間を除きます)。  

  *[Ingress Received Message Count Lag]\(受信メッセージの受信のカウント ラグ\)* にも値が含まれるはずです。その値で何通のメッセージが送れているのか判断できます。  遅れを取り戻す最も簡単な方法は、差を埋めるだけのサイズまで環境の容量を増やすことです。  

  たとえば、S1 環境が 5,000,000 メッセージの遅れを示している場合、環境のサイズを 6 ユニットまで増やせば 1 日がかりで追いつける可能性があります。  さらに増やせば、それだけ短時間で追いつくことができます。 このキャッチアップ期間は、環境に初めてプロビジョニングするときに一般的に発生します。特に、イベントが既に入っているイベント ソースに接続するときや、大量の履歴データを一括アップロードするときに発生します。

* 別の方法としては、2 時間の環境の合計容量より若干低いしきい値以上に**保存済みイベントの受信**アラートを設定することです。  このアラートは、頻繁に容量に達しているかどうかを理解するのに役立ちます。そのような状況は、待機時間が発生する可能性が高いことを示しています。 

  たとえば、3 つの S1 ユニットをプロビジョニング済み (または 1 分間の受信容量あたり 2100 イベント) の場合、**保存済みイベントの受信**アラートを 2 時間に対して 1900 イベント以上に設定できます。 このしきい値を頻繁に超えてアラートがトリガーされる場合、プロビジョニング不足である可能性が高いです。  

* 調整されている疑いがある場合、**受信メッセージの受信**をイベント ソースの送信メッセージと比較できます。  Event Hub への受信が**受信メッセージの受信**より大きい場合、Time Series Insights は調整されている可能性が高いです。

## <a name="improving-performance"></a>パフォーマンスの向上

調整や待機時間を軽減するための最善の修正方法は、環境の容量を増やすことです。

分析するデータ量に合わせて環境を正しく構成することによって待機時間と調整を回避できます。 環境内に容量を追加する方法の詳細については、「[環境のスケーリング](time-series-insights-how-to-scale-your-environment.md)」を参照してください。

## <a name="next-steps"></a>次の手順

- [Time Series Insights 環境の問題を診断して解決する](time-series-insights-diagnose-and-solve-problems.md)を確認します。

- [Time Series Insights 環境をスケーリングする方法](time-series-insights-how-to-scale-your-environment.md)を確認します。