---
title: WebJobs でバックグラウンド タスクを実行する - Azure App Service
description: Azure App Service Web アプリ、API アプリ、またはモバイル アプリで Web ジョブを使用してバックグラウンド タスクを実行する方法を説明します。
author: ggailey777
manager: gwallace
s.assetid: af01771e-54eb-4aea-af5f-f883ff39572b
ms.service: app-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: glenga
ms.reviewer: msangapu;david.ebbo;suwatch;pbatum;naren.soni
ms.custom: seodec18
ms.openlocfilehash: 293227352f27a724228136532c88e35fe877feb2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/14/2019
ms.locfileid: "72312173"
---
# <a name="run-background-tasks-with-webjobs-in-azure-app-service"></a>Azure App Service で Web ジョブを使用してバックグラウンド タスクを実行する

この記事では、実行可能ファイルまたはスクリプトをアップロードする Web ジョブを [Azure ポータル](https://portal.azure.com)を使用してデプロイする方法について説明します。 Visual Studio を使用した Web ジョブの開発とデプロイ方法については、[Visual Studio を使用した Web ジョブのデプロイ](webjobs-dotnet-deploy-vs.md)に関する記事を参照してください。

## <a name="overview"></a>概要
Web ジョブは、Web アプリ、API アプリ、またはモバイル アプリと同じコンテキストでプログラムやスクリプトを実行できる [Azure App Service](index.yml) の機能です。 Web ジョブの使用に追加コストはかかりません。

> [!IMPORTANT]
> Web ジョブは App Service on Linux ではまだサポートされていません。

Azure WebJobs SDK と Web ジョブを使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure Web ジョブ SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。

Azure Functions は、プログラムとスクリプトを実行する別の方法を提供します。 Web ジョブと Functions の比較については、「[Flow、Logic Apps、Functions、WebJobs の比較](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md)」を参照してください。

## <a name="webjob-types"></a>Web ジョブの種類

次の表で、*継続的* Web ジョブと*トリガーされる* Web ジョブの違いを説明します。


|継続的  |トリガー  |
|---------|---------|
| Web ジョブが作成されるとすぐに開始します。 ジョブが終了しないようにするため、プログラムまたはスクリプトは、その動作を無限ループ内で実行します。 ジョブが終了した場合でも、再開することができます。 | 手動またはスケジュールに従ってトリガーされたときにのみ開始します。 |
| Web アプリが実行されているすべてのインスタンスで実行されます。 必要に応じて、Web ジョブを 1 つのインスタンスに制限できます。 |Azure が負荷分散用に選択した 1 つのインスタンス上で実行されます。|
| リモート デバッグをサポートします。 | リモート デバッグをサポートしません。|

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

## <a name="acceptablefiles"></a>スクリプトまたはプログラムでサポートされるファイルの種類

次のファイルの種類がサポートされます。

* .cmd、.bat、.exe (Windows コマンドを使用)
* .ps1 (PowerShell を使用)
* .sh (Bash を使用)
* .php (PHP を使用)
* .py (Python を使用)
* .js (Node.js を使用)
* .jar (Java を使用)

## <a name="CreateContinuous"></a> 継続的 Web ジョブを作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[Web ジョブ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjcontinuous.png)

   | Setting      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myContinuousWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | 継続的 | [Web ジョブの種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **スケール** | 複数のインスタンス | 継続的 Web ジョブでのみ使用できます。 プログラムまたはスクリプトがすべてのインスタンスで実行されるか、1 つのインスタンスだけで実行されるかどうかを決定します。 複数のインスタンスで実行するオプションは、Free または Shared [価格レベル](https://azure.microsoft.com/pricing/details/app-service/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)には適用されません。 | 

4. Click **OK**.

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

2. 継続的 Web ジョブを開始または停止するには、一覧の Web ジョブを右クリックし、 **[停止]** または **[開始]** をクリックします。

    ![継続的 Web ジョブを停止する](./media/web-sites-create-web-jobs/continuousstop.png)

## <a name="CreateOnDemand"></a>手動でトリガーされる Web ジョブを作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[Web ジョブ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[Web ジョブ]** ページで、 **[追加]** を選択します。

    ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjtriggered.png)

   | Setting      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myTriggeredWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。|
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [Web ジョブの種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **トリガー** | マニュアル | |

4. Click **OK**.

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

7. Web ジョブを実行するには、一覧でその名前を右クリックし、 **[実行]** をクリックします。
   
    ![Web ジョブの実行](./media/web-sites-create-web-jobs/runondemand.png)

## <a name="CreateScheduledCRON"></a>スケジュールされた Web ジョブを作成する

<!-- 
Several steps in the three "Create..." sections are identical; 
when making changes in one don't forget the other two.
-->

1. [Azure ポータル](https://portal.azure.com)で、App Service Web アプリ、API アプリ、またはモバイル アプリの **App Service** ページに移動します。

2. **[Web ジョブ]** を選択します。

   ![Web ジョブを選択する](./media/web-sites-create-web-jobs/select-webjobs.png)

2. **[Web ジョブ]** ページで、 **[追加]** を選択します。

   ![[Web ジョブ] ページ](./media/web-sites-create-web-jobs/wjblade.png)

3. **[Web ジョブの追加]** 設定を、次の表に示されているように使用します。

   ![[Web ジョブの追加] ページ](./media/web-sites-create-web-jobs/addwjscheduled.png)

   | Setting      | 値の例   | 説明  |
   | ------------ | ----------------- | ------------ |
   | **Name** | myScheduledWebJob | App Service アプリ内で一意の名前。 名前は文字または数字で始まる必要があり、"-" と "_" 以外の特殊文字を使用することはできません。 |
   | **ファイルのアップロード** | ConsoleApp.zip | 実行可能ファイルまたはスクリプト ファイルと、プログラムまたはスクリプトを実行するために必要な関連ファイルを含む *.zip* ファイル。 サポートされている実行可能ファイルまたはスクリプト ファイルの種類については、「[サポートされるファイルの種類](#acceptablefiles)」セクションを参照してください。 |
   | **Type** | トリガー | [Web ジョブの種類](#webjob-types)については、この記事の中で既に説明しています。 |
   | **トリガー** | スケジュール | スケジュールを確実に動作させるために、Always On 機能を有効にします。 Always On 機能は、Basic、Standard、および Premium の価格レベルでのみ利用できます。|
   | **CRON 式** | 0 0/20 * * * * | [CRON 式](#ncrontab-expressions)については、次のセクションで説明します。 |

4. Click **OK**.

   **[Web ジョブ]** ページに新しい Web ジョブが表示されます。

   ![Web ジョブの一覧](./media/web-sites-create-web-jobs/listallwebjobs.png)

## <a name="ncrontab-expressions"></a>NCRONTAB 式

[NCRONTAB 式](../azure-functions/functions-bindings-timer.md#ncrontab-expressions)は、ポータルで入力するか、`settings.job` ファイルを Web ジョブの *.zip* ファイルのルートに含めることができます。例を以下に示します。

```json
{
    "schedule": "0 */15 * * * *"
}
```

詳細については、「[Scheduling a triggered WebJob (トリガーされる Web ジョブのスケジュール設定)](webjobs-dotnet-deploy-vs.md#scheduling-a-triggered-webjob)」を参照してください。

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

## <a name="ViewJobHistory"></a>ジョブ履歴を表示する

1. 履歴を表示する Web ジョブを選択し、 **[ログ]** ボタンを選択します。
   
   ![[ログ] ボタン](./media/web-sites-create-web-jobs/wjbladelogslink.png)

2. **[WebJob Details]\(Web ジョブの詳細\)** ページで、詳細を確認する実行時間を選択します。
   
   ![Web ジョブの詳細](./media/web-sites-create-web-jobs/webjobdetails.png)

3. **[WebJob Run Details]\(Web ジョブの実行の詳細\)** ページで、 **[出力の切り替え]** を選択すると、ログの内容のテキストが表示されます。
   
    ![Web ジョブ実行の詳細](./media/web-sites-create-web-jobs/webjobrundetails.png)

   出力されるテキストを別のブラウザー ウィンドウに表示するには、 **[ダウンロード]** を選択します。 テキスト自体をダウンロードするには、 **[ダウンロード]** を右クリックし、ブラウザーのオプションを使用してファイルの内容を保存します。
   
5. Web ジョブの一覧に移動するには、ページの上部にある **[Web ジョブ]** 階層リンクを選択します。

    ![[Web ジョブ] 階層リンク](./media/web-sites-create-web-jobs/breadcrumb.png)
   
    ![履歴ダッシュボードに表示された Web ジョブの一覧](./media/web-sites-create-web-jobs/webjobslist.png)
   
## <a name="NextSteps"></a> 次のステップ

Azure WebJobs SDK と Web ジョブを使用して、多くのプログラミング タスクを単純化できます。 詳細については、「 [Azure Web ジョブ SDK とは](https://github.com/Azure/azure-webjobs-sdk/wiki)」をご覧ください。
