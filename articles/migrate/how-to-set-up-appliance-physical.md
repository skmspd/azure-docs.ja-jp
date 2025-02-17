---
title: Azure Migrate Server Assessment での物理サーバーの評価用にアプライアンスを設定する
description: Azure Migrate Server Assessment を使用した物理サーバーの評価用にアプライアンスを設定する方法について説明します。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/11/2019
ms.author: raynew
ms.openlocfilehash: a3212e4dac6856a5fd032c731d877453965584ae
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/11/2019
ms.locfileid: "73907163"
---
# <a name="set-up-an-appliance-for-physical-servers"></a>物理サーバー用にアプライアンスを設定する

この記事では、Azure Migrate で物理サーバーを評価する場合に、Azure Migrate アプライアンスを設定する方法について説明します。Server Assessment ツールを追加済みであることを確認してください。

> [!NOTE]
> ここで言及されている機能がまだ Azure Migrate ポータルに表示されない場合は、しばらくお待ちください。 来週あたりに表示されるようになります。

Azure Migrate アプライアンスは、次の操作を行うために Azure Migrate Server Assessment によって使用される軽量アプライアンスです。

- オンプレミスのサーバーを検出する。
- 検出されたサーバーのメタデータとパフォーマンス データを、Azure Migrate Server Assessment に送信する。

Azure Migrate アプライアンスに関する[詳細を確認](migrate-appliance.md)します。


## <a name="appliance-deployment-steps"></a>アプライアンスのデプロイ手順

アプライアンスを設定するには、次のようにします。
- Azure portal から、Azure Migrate インストーラー スクリプトが含まれた ZIP ファイルをダウンロードします。
- ZIP ファイルの内容を抽出します。 管理特権で PowerShell コンソールを起動します。
- PowerShell スクリプトを実行して、アプライアンス Web アプリケーションを起動します。
- アプライアンスを初めて構成し、Azure Migrate プロジェクトに登録します。

## <a name="download-the-installer-script"></a>インストーラー スクリプトをダウンロードする

アプライアンスの ZIP ファイルをダウンロードします。

1. **[移行の目標]**  >  **[サーバー]**  >  **[Azure Migrate: Server Assessment]** で、 **[検出]** をクリックします。
2. **[マシンの検出]**  >  **[マシンは仮想化されていますか?]** で、 **[非仮想化/その他]** をクリックします。
3. **[ダウンロード]** をクリックして、ZIP ファイルをダウンロードします。

    ![VM をダウンロードする](./media/how-to-set-up-appliance-hyper-v/download-appliance-hyperv.png)


### <a name="verify-security"></a>セキュリティを確認する

圧縮されたファイルをデプロイする前に、それが安全であることを確認します。

1. ファイルをダウンロードしたマシンで、管理者用のコマンド ウィンドウを開きます。
2. 次のコマンドを実行して、VHD のハッシュを生成します
    - ```C:\>CertUtil -HashFile <file_location> [Hashing Algorithm]```
    - 使用例: ```C:\>CertUtil -HashFile C:\AzureMigrate\AzureMigrate.ova SHA256```
3.  アプライアンス バージョン 1.19.05.10 の場合は、生成されたハッシュがこれらの設定と一致する必要があります。

  **アルゴリズム** | **ハッシュ値**
  --- | ---
  SHA256 | 598d2e286f9c972bb7f7382885e79e768eddedfe8a3d3460d6b8a775af7d7f79


  
## <a name="run-the-azure-migrate-installer-script"></a>Azure Migrate インストーラー スクリプトを実行する
インストーラー スクリプトでは以下が実行されます。

- エージェントと、物理サーバーの検出と評価のための Web アプリケーションをインストールする。
- Windows の役割 (Windows Activation Service、IIS、PowerShell ISE など) をインストールする。
- IIS 書き込み可能モジュールをダウンロードしてインストールする。 [詳細情報](https://www.microsoft.com/download/details.aspx?id=7435)。
- Azure Migrate の永続的な設定の詳細でレジストリ キー (HKLM) を更新する。
- パスに次のファイルを作成する。
    - **構成ファイル**: %Programdata%\Microsoft Azure\Config
    - **ログ ファイル**: %Programdata%\Microsoft Azure\Logs

次のようにスクリプトを実行します。

1. アプライアンスをホストするサーバー上のフォルダーに ZIP ファイルを抽出します。
2. 管理 (昇格された) 特権を使用して上記のサーバーで PowerShell を起動します。
3. PowerShell ディレクトリを、ダウンロードした ZIP ファイルの内容が抽出されたフォルダーに変更します。
4. 次のコマンドを実行してスクリプトを実行します。
    ```
    PS C:\Users\Administrators\Desktop> AzureMigrateInstaller-physical.ps1
    ```
スクリプトが正常に終了すると、アプライアンス Web アプリケーションが起動します。



### <a name="verify-appliance-access-to-azure"></a>アプライアンスによる Azure へのアクセスを確認する

アプライアンス VM が必要な [Azure URL](migrate-support-matrix-hyper-v.md#assessment-appliance-url-access) に接続できることを確認します。

## <a name="configure-the-appliance"></a>アプライアンスを構成する

アプライアンスを初めて設定します。

1. VM に接続できる任意のマシン上でブラウザーを開き、アプライアンス Web アプリの URL を開きます (**https://*アプライアンス名または IP アドレス*:44368**)。

   または、アプリのショートカットをクリックして、デスクトップからアプリを開くこともできます。
2. Web アプリの **[前提条件のセットアップ]** で、以下を実行します。
    - **ライセンス**:ライセンス条項に同意し、サード パーティの情報を確認します。
    - **接続**:VM でインターネットにアクセスできることが、アプリによって確認されます。 VM でプロキシを使用する場合:
        - **[プロキシの設定]** をクリックし、 http://ProxyIPAddress または http://ProxyFQDN の形式で、プロキシ アドレスとリスニング ポートを指定します。
        - プロキシで認証が必要な場合は、資格情報を指定します。
        - サポートされるのは HTTP プロキシのみです。
    - **時刻同期**:時刻が確認されます。 VM の検出を正常に機能させるには、アプライアンス上の時刻がインターネットの時刻と同期している必要があります。
    - **更新プログラムのインストール**:Azure Migrate Server Assessment によって、アプライアンスに最新の更新プログラムがインストールされていることが確認されます。

### <a name="register-the-appliance-with-azure-migrate"></a>Azure Migrate にアプライアンスを登録する

1. **[ログイン]** をクリックします。 表示されない場合は、ブラウザーでポップアップ ブロックを無効にしてあることを確認します。
2. 新しいタブで、自分の Azure 資格情報を使用してサインインします。 
    - 自分のユーザー名とパスワードを使用してサインインします。
    - PIN を使用したサインインはサポートされていません。
3. 正常にサインインした後、Web アプリに戻ります。
4. Azure Migrate プロジェクトが作成されたサブスクリプションを選択します。 その後、プロジェクトを選択します。
5. アプライアンスの名前を指定します。 名前は、14 文字以下の英数字にする必要があります。
6. **[登録]** をクリックします。


## <a name="start-continuous-discovery"></a>継続的な検出を開始する

アプライアンスから物理サーバーに接続し、検出を開始します。

1. **[資格情報の追加]** をクリックして、アプライアンスがサーバーの検出に使用するアカウント資格情報を指定します。  
2. **オペレーティング システム**、資格情報のフレンドリ名、**ユーザー名**、**パスワード**を指定し、 **[追加]** をクリックします。
Windows および Linux サーバーごとに 1 セットの資格情報を追加できます。
4. **[サーバーの追加]** をクリックし、サーバーの詳細 (FQDN/IP アドレスと資格情報のフレンドリ名 (行ごとに 1 つのエントリ)) を指定してサーバーに接続します。
3. **[Validate (検証)]** をクリックします。 検証後、検出可能なサーバーの一覧が表示されます。
    - サーバーの検証が失敗した場合は、 **[状態]** 列のアイコンをポイントしてエラーを確認します。 問題を修正し、もう一度検証します。
    - サーバーを削除するには、 **[削除]** を選択します。
4. 検証後、 **[保存して検出を開始]** をクリックして、検出プロセスを開始します。

これで検出が開始されます。 検出された VM のメタデータが Azure portal に表示されるまでに、約 15 分かかります。 

## <a name="verify-servers-in-the-portal"></a>ポータルでサーバーを確認する

検出の完了後、サーバーがポータルに表示されることを確認できます。

1. Azure Migrate ダッシュボードを開きます。
2. **[Azure Migrate - サーバー]**  >  **[Azure Migrate: Server Assessment]** ページで、 **[検出済みサーバー]** の数を表示するアイコンをクリックします。 


## <a name="next-steps"></a>次の手順

Azure Migrate Server Assessment で[物理サーバーの評価](tutorial-assess-physical.md)を試します。
