---
title: Azure Application Insights を使用した Java トレース ログの探索 | Microsoft Docs
description: Application Insights を使用して Log4J または Logback のトレースを検索します
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/18/2019
ms.openlocfilehash: 23e3116a0cc3283191d00079e0926dc206e677f0
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819342"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights を使用した Java トレース ログの探索
トレース用に Logback または Log4J (v1.2 または v2.0) を使用している場合は、トレース ログを自動的に Application Insights に送信して、Application Insights でトレース ログを探索および検索できます。

> [!TIP]
> Application Insights インストルメンテーション キーは、アプリケーションに対して 1 回だけ設定する必要があります。 Java Spring のようなフレームワークを使用している場合は、アプリの構成の別の場所で既にキーを登録済みである可能性があります。

## <a name="using-the-application-insights-java-agent"></a>Application Insights Java エージェントを使用する

`AI-Agent.xml` ファイルの機能を有効にすることで、ログを自動的にキャプチャするように Application Insights Java エージェントを構成できます。

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn enabled="true">
         <Logging enabled="true" />
      </BuiltIn>
   </Instrumentation>
   <AgentLogger />
</ApplicationInsightsAgent>
```

または、以下の手順に従ってください。

## <a name="install-the-java-sdk"></a>Java SDK をインストールする

[Application Insights SDK for Java][java] をまだインストールしていない場合は、インストールする手順に従います。

## <a name="add-logging-libraries-to-your-project"></a>プロジェクトへのログ ライブラリの追加
*プロジェクトに適した方法を選択してください。*

#### <a name="if-youre-using-maven"></a>Maven を使用している場合:
プロジェクトが既に Maven を使用してビルドする設定になっている場合は、pom.xml ファイルに次のいずれかのコード スニペットをマージします。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

*Logback*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```XML

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle を使用している場合:
プロジェクトが既に Gradle を使用してビルドする設定になっている場合は、次のいずれかの行を build.gradle ファイルの `dependencies` グループに追加します。

次に、バイナリがダウンロードされるように、プロジェクトの依存関係を更新します。

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>それ以外の場合:
Application Insights Java SDK を手動でインストールするガイドラインに従って、適切なアペンダーの Jar (Maven Central ページに移動した後、ダウンロード セクションの 'jar' リンクをクリック) をダウンロードし、ダウンロードされたアペンダー Jar をプロジェクトに追加します。

| ロガー | ダウンロード | ライブラリ |
| --- | --- | --- |
| Logback |[Logback アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 アペンダー Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


## <a name="add-the-appender-to-your-logging-framework"></a>ログ フレームワークへのアペンダーの追加
トレースの取得を開始するには、適切なコード スニペットを Log4J または Logback の構成ファイルに追加します。 

*Logback*

```XML

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```XML

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```XML

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights のアペンダーは、上記のコード サンプルに示されているように、構成済みの任意のロガーによって参照でき、必ずしもルート ロガーを使用する必要はありません。

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights ポータルでのトレースの探索
これで、Application Insights にトレースを送信するようにプロジェクトが構成されました。これらのトレースは、Application Insights ポータルの [[検索]][diagnostic] ブレードで表示して検索できます。

ロガー経由で送信された例外は、例外のテレメトリとしてポータルに表示されます。

![Application Insights ポータルで [検索] を開きます](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>次の手順
[診断検索][diagnostic]

<!--Link references-->

[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[java]: java-get-started.md


