---
title: クラシック仮想ネットワークから Azure AD Domain Services を移行する | Microsoft Docs
description: 既存の Azure AD Domain Services マネージド ドメイン インスタンスをクラシック仮想ネットワーク モデルから Resource Manager ベースの仮想ネットワークに移行する方法について説明します。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: iainfou
ms.openlocfilehash: 8cba2cbf8fcbad1acae8c36892308c3249fc4181
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674917"
---
# <a name="preview---migrate-azure-ad-domain-services-from-the-classic-virtual-network-model-to-resource-manager"></a>プレビュー - クラシック仮想ネットワーク モデルから Resource Manager への Azure AD Domain Services の移行

Azure Active Directory Domain Services (AD DS) では、現在クラシック仮想ネットワーク モデルを使用しているお客様に対して、Resource Manager 仮想ネットワーク モデルへの 1 回限りの移動がサポートされています。

この記事では、移行の利点および考慮事項と、既存の Azure AD DS インスタンスの移行を成功させるために必要な手順について説明します。 現在、この機能はプレビュー段階にあります。

## <a name="overview-of-the-migration-process"></a>移行プロセスの概要

移行プロセスでは、クラシック仮想ネットワークで実行されている既存の Azure AD DS インスタンスを、既存の Resource Manager 仮想ネットワークに移動します。 移行は PowerShell を使用して実行し、2 つの主な実行の段階があります。"*準備*" と "*移行*" です。

![Azure AD DS の移行プロセスの概要](media/migrate-from-classic-vnet/migration-overview.png)

"*準備*" 段階では、マネージド ドメインに同期されたユーザー、グループ、およびパスワードの最新のスナップショットを取得するために、Azure AD DS によってドメインのバックアップが実行されます。 その後、同期が無効になり、Azure AD DS のマネージド ドメインをホストするクラウド サービスが削除されます。 準備段階の間は、Azure AD DS マネージド ドメインでユーザーを認証することはできません。

![Azure AD DS 移行の準備段階](media/migrate-from-classic-vnet/migration-preparation.png)

"*移行*" 段階では、クラシック Azure AD DS マネージド ドメインのドメイン コントローラーの基礎となる仮想ディスクが、Resource Manager デプロイ モデルを使用して VM を作成するためにコピーされます。 次に、Azure AD DS マネージド ドメインが再作成されます。これには、LDAPS と DNS の構成が含まれます。 Azure AD との同期が再開され、LDAP 証明書が復元されます。 Azure AD DS マネージド ドメインにコンピューターを再参加させる必要はありません。コンピューターは引き続きマネージド ドメインに参加しており、変更することなく実行できます。

![Azure AD DS の移行](media/migrate-from-classic-vnet/migration-process.png)

## <a name="migration-benefits"></a>移行の利点

この移行プロセスを使用して Azure AD DS マネージド ドメインを移動すると、コンピューターをマネージド ドメインに再参加させたり、Azure AD DS インスタンスを削除して最初から作成したりする必要がありません。 移行プロセスの終わりの時点で、VM は引き続き Azure AD DS マネージド ドメインに参加しています。

移行後、Azure AD DS によって、Resource Manager 仮想ネットワークを使用しているドメインでのみ使用できる多くの機能が提供されます。次に例を示します。

* 細かい設定が可能なパスワード ポリシーのサポート。
* AD アカウントのロックアウトによる保護。
* Azure AD DS マネージド ドメインでの電子メールによるアラートの通知。
* Azure Monitor を使用したログの監視。
* Azure Files の統合
* HD Insights の統合

Resource Manager 仮想ネットワークを使用する Azure AD DS マネージド ドメインは、最新の新機能を使用して最新の状態を維持するのに役立ちます。 クラシック仮想ネットワークを使用した Azure AD DS のサポートは、今後非推奨となる予定です。

## <a name="example-scenarios-for-migration"></a>移行に関するシナリオ例

Azure AD DS マネージド ドメインを移行する一般的なシナリオには、次の例があります。

> [!NOTE]
> 移行が正常に完了したことを確認するまでは、クラシック仮想ネットワークを変換しないでください。 仮想ネットワークを変換すると、移行および検証の段階で問題が発生した場合に、Azure AD DS マネージド ドメインをロールバックまたは復元するためのオプションが排除されます。

### <a name="migrate-azure-ad-ds-to-an-existing-resource-manager-virtual-network-recommended"></a>Azure AD DS を既存の Resource Manager 仮想ネットワークに移行する (推奨)

1 つの一般的なシナリオは、他の既存のクラシック リソースを Resource Manager のデプロイ モデルと仮想ネットワークに既に移動している場合です。 その後、Resource Manager の仮想ネットワークから、引き続き Azure AD DS を実行するクラシック仮想ネットワークへのピアリングを使用します。 このアプローチにより、Resource Manager アプリケーションとサービスで、クラシック仮想ネットワーク内の Azure AD DS マネージド ドメインの認証および管理機能を使用できるようになります。 移行が完了すると、すべてのリソースは Resource Manager のデプロイ モデルと仮想ネットワークを使用して実行されます。

![Azure AD DS を既存の Resource Manager 仮想ネットワークに移行する](media/migrate-from-classic-vnet/migrate-to-existing-vnet.png)

この移行シナリオの例には、次のようなおおまかな手順が含まれています。

1. クラシック仮想ネットワークに構成されている既存の VPN ゲートウェイまたは仮想ネットワーク ピアリングを削除します。
1. この記事に示されている手順に従って、Azure AD DS マネージド ドメインを移行します。
1. 移行が成功したことをテストして確認してから、クラシック仮想ネットワークを削除します。

### <a name="migrate-multiple-resources-including-azure-ad-ds"></a>Azure AD DS を含む複数のリソースの移行

このシナリオ例では、Azure AD DS とその他の関連リソースをクラシック デプロイ モデルから Resource Manager デプロイ モデルに移行します。 Azure AD DS マネージド ドメインと並行してクラシック仮想ネットワークで引き続き実行するリソースがある場合は、そのようなすべてのリソースについて、Resource Manager デプロイ モデルへの移行によってメリットを得ることができます。

![複数のリソースを Resource Manager デプロイ モデルに移行する](media/migrate-from-classic-vnet/migrate-multiple-resources.png)

この移行シナリオの例には、次のようなおおまかな手順が含まれています。

1. クラシック仮想ネットワークに構成されている既存の VPN ゲートウェイまたは仮想ネットワーク ピアリングを削除します。
1. この記事に示されている手順に従って、Azure AD DS マネージド ドメインを移行します。
1. クラシック仮想ネットワークと Resource Manager ネットワークの間に仮想ネットワーク ピアリングを設定します。
1. 移行が成功したことをテストして確認します。
1. [VM のようなその他のクラシック リソースを移動します][migrate-iaas]。

### <a name="migrate-azure-ad-ds-but-keep-other-resources-on-the-classic-virtual-network"></a>Azure AD DS を移行するが、その他のリソースはクラシック仮想ネットワーク上に保持する

このシナリオ例では、1 つのセッションに最小限のダウンタイムがあります。 Azure AD DS のみを Resource Manager 仮想ネットワークに移行し、既存のリソースをクラシック デプロイ モデルとクラシック仮想ネットワーク上に保持します。 それに続くメンテナンス期間に、必要に応じてクラシック デプロイ モデルとクラシック仮想ネットワークからその他のリソースを移行できます。

![Azure AD DS のみを Resource Manager デプロイ モデルに移行する](media/migrate-from-classic-vnet/migrate-only-azure-ad-ds.png)

この移行シナリオの例には、次のようなおおまかな手順が含まれています。

1. クラシック仮想ネットワークに構成されている既存の VPN ゲートウェイまたは仮想ネットワーク ピアリングを削除します。
1. この記事に示されている手順に従って、Azure AD DS マネージド ドメインを移行します。
1. クラシック仮想ネットワークと新しい Resource Manager 仮想ネットワークの間に仮想ネットワーク ピアリングを設定します。
1. 後で、必要に応じて、クラシック仮想ネットワークから[その他のリソースを移行][migrate-iaas]します。

## <a name="before-you-begin"></a>開始する前に

Azure AD DS マネージド ドメインを準備してから移行するときには、認証サービスと管理サービスの可用性に関する考慮事項がいくつかあります。 Azure AD DS マネージド ドメインは、移行中の一定期間、使用できません。 Azure AD DS に依存するアプリケーションとサービスには、移行中にダウンタイムが発生します。

> [!IMPORTANT]
> 移行プロセスを開始する前に、移行に関するこの記事とガイダンスをすべてお読みください。 移行プロセスでは、一定の期間、Azure AD DS ドメイン コントローラーの可用性に影響が生じます。 移行プロセス中に、ユーザー、サービス、およびアプリケーションをマネージド ドメインに対して認証することはできません。

### <a name="ip-addresses"></a>IP アドレス

移行後に、Azure AD DS マネージド ドメインのドメイン コントローラーの IP アドレスが変更されます。 これには、Secure LDAP エンドポイントのパブリック IP アドレスが含まれます。 新しい IP アドレスは、Resource Manager 仮想ネットワークの新しいサブネットのアドレス範囲内にあります。

ロールバックの場合、IP アドレスはロールバック後に変更される可能性があります。

Azure AD DS では通常、アドレス範囲内の最初の 2 つの使用可能な IP アドレスが使用されますが、これは保証されません。 現時点では、移行後に使用する IP アドレスを指定することはできません。

### <a name="downtime"></a>ダウンタイム

移行プロセスでは、ドメイン コントローラーが一定の期間オフラインになります。 Azure AD DS が Resource Manager のデプロイ モデルと仮想ネットワークに移行されている間は、ドメイン コントローラーにアクセスできません。 平均して、ダウンタイムは約 1 時間から 3 時間です。 この期間は、ドメイン コントローラーがオフラインになってから、最初のドメイン コントローラーがオンラインに戻った時点までです。 この平均には、2 番目のドメイン コントローラーがレプリケートされるまでにかかる時間や、Resource Manager デプロイ モデルへの追加リソースの移行にかかる時間は含まれていません。

### <a name="account-lockout"></a>アカウントのロックアウト

クラシック仮想ネットワークで実行される Azure AD DS マネージド ドメインには、AD アカウント ロックアウト ポリシーが設定されていません。 VM がインターネットに公開されている場合、攻撃者はパスワード スプレー方式を使用して、アカウントに対するブルートフォース攻撃を行うことができます。 このような試みを阻止するためのアカウント ロックアウト ポリシーはありません。 Resource Manager のデプロイ モデルと仮想ネットワークを使用する Azure AD DS マネージド ドメインでは、AD アカウント ロックアウト ポリシーによって、これらのパスワード スプレー攻撃から保護されます。

既定では、無効なパスワードの試行が 2 分間に 5 回あった場合、アカウントが 30 分間ロックアウトされます。

ロックアウトされたアカウントにサインインすることはできません。これは、Azure AD DS マネージド ドメインを管理する機能、またはアカウントで管理されているアプリケーションを管理する機能の妨げになる可能性があります。 Azure AD DS マネージド ドメインの移行後、アカウントでのサインインが繰り返し失敗し、永続的なロックアウトのように感じられる事態が発生する可能性があります。 移行後の 2 つの一般的なシナリオを次に示します。

* 有効期限が切れたパスワードを使用しているサービス アカウント。
    * サービス アカウントで、有効期限が切れたパスワードを使用してサインインを繰り返し試行します。これにより、アカウントがロックアウトされます。 この問題を解決するには、資格情報の有効期限が切れているアプリケーションまたは VM を見つけ、パスワードを更新します。
* 悪意のあるエンティティが、ブルートフォース攻撃によるアカウントへのサインインを試行している。
    * VM がインターネットに公開されている場合、攻撃者は一般的なユーザー名とパスワードの組み合わせを使ってサインインを試みます。 このようなサインイン試行が繰り返し失敗したことによって、アカウントがロックアウトされる場合があります。 管理者アカウントがロックアウトされる可能性を最小化するため、たとえば *admin* や *administrator* などの一般的な名前の管理者アカウントを使用することはお勧めしません。
    * インターネットに公開する VM の数を最小限に抑えます。 [Azure Bastion (現在はプレビュー段階)][azure-bastion] を使用すると、Azure portal を使用して VM に安全に接続できます。

移行後に一部のアカウントがロックアウトされる可能性があると思われる場合は、移行の最後の手順で、監査を有効にする方法や、細かい設定が可能なパスワード ポリシー設定を変更する方法について説明しています。

### <a name="roll-back-and-restore"></a>ロールバックと復元

移行が成功しなかった場合は、Azure AD DS マネージド ドメインをロールバックまたは復元するプロセスがあります。 ロールバックは、マネージド ドメインの状態を、移行を試行する前の状態にまですぐに戻すセルフサービス オプションです。 Azure サポート エンジニアは、最後の手段として、バックアップからマネージド ドメインを復元することもできます。 詳細については、[失敗した移行からロールバックまたは復元する方法](#roll-back-and-restore-from-migration)の説明を参照してください。

### <a name="restrictions-on-available-virtual-networks"></a>使用可能な仮想ネットワークに関する制限事項

Azure AD DS マネージド ドメインの移行先となる仮想ネットワークにはいくつかの制限があります。 移行先の Resource Manager 仮想ネットワークは、次の要件を満たしている必要があります。

* Resource Manager 仮想ネットワークは、Azure AD DS が現在展開されているクラシック仮想ネットワークと同じ Azure サブスクリプションに存在する必要があります。
* Resource Manager 仮想ネットワークは、Azure AD DS が現在展開されているクラシック仮想ネットワークと同じリージョンに存在する必要があります。
* Resource Manager 仮想ネットワークのサブネットには、少なくとも 3 個から 5 個の使用可能な IP アドレスが必要です。
* Resource Manager 仮想ネットワークのサブネットは Azure AD DS 専用のサブネットである必要があります。その他のワークロードはホストしないでください。

仮想ネットワークの要件の詳細については、[仮想ネットワーク設計の考慮事項と構成オプション][network-considerations]に関するページを参照してください。

## <a name="migration-steps"></a>移行の手順

Resource Manager のデプロイ モデルと仮想ネットワークへの移行は、次の 5 つの主な手順に分かれています。

| 手順    | 実行方法  | 推定時間  | ダウンタイム  | ロールバック/復元 |
|---------|--------------------|-----------------|-----------|-------------------|
| [手順 1 - 新しい仮想ネットワークを更新して検索する](#update-and-verify-virtual-network-settings) | Azure ポータル | 約 15 分 | ダウンタイムは必要ありません | 該当なし |
| [手順 2 - Azure AD DS マネージド ドメインを移行用に準備する](#prepare-the-managed-domain-for-migration) | PowerShell | 平均 15 から 30 分 | このコマンドが完了すると、Azure AD DS のダウンタイムが開始されます。 | ロールバックと復元を使用できます。 |
| [手順 3 - Azure AD DS マネージド ドメインを既存の仮想ネットワークに移動する](#migrate-the-managed-domain) | PowerShell | 平均 1 から 3 時間 | このコマンドが完了すると、1 つのドメイン コントローラーを使用できます。ダウンタイムは終了します。 | 失敗した場合は、ロールバック (セルフサービス) と復元の両方を使用できます。 |
| [手順 4 - レプリカ ドメイン コントローラーをテストして待機する](#test-and-verify-connectivity-after-the-migration)| PowerShell と Azure portal | コアの数に応じて 1 時間またはそれ以上 | 両方のドメイン コントローラーが使用可能で、正常に機能します。 | 該当なし。 最初の VM が正常に移行された後では、ロールバックまたは復元のオプションはありません。 |
| [手順 5 - オプションの構成手順](#optional-post-migration-configuration-steps) | Azure portal と VM | 該当なし | ダウンタイムは必要ありません | 該当なし |

> [!IMPORTANT]
> 追加のダウンタイムを回避するには、移行プロセスを開始する前に、移行に関するこの記事とガイダンスをすべてお読みください。 移行プロセスでは、一定の期間、Azure AD DS ドメイン コントローラーの可用性に影響が生じます。 移行プロセス中に、ユーザー、サービス、およびアプリケーションをマネージド ドメインに対して認証することはできません。

## <a name="update-and-verify-virtual-network-settings"></a>仮想ネットワークの設定を更新して確認する

移行を開始する前に、次の初期チェックと更新を完了してください。 これらの手順は、移行前にいつでも実行でき、Azure AD DS マネージド ドメインの操作には影響しません。

1. ご利用のローカル Azure PowerShell 環境を最新バージョンに更新します。 移行手順を完了するには、少なくともバージョン *2.3.2* が必要です。

    チェックと更新の方法については、[Azure PowerShell の概要][azure-powershell]に関するページを参照してください。

1. Resource Manager 仮想ネットワークを作成するか、既存の Resource Manager 仮想ネットワークを選択します。

    Azure AD DS に必要なポートがネットワーク設定によってブロックされていないことを確認してください。 クラシック仮想ネットワークと Resource Manager 仮想ネットワークの両方でポートを開く必要があります。 これらの設定には、ルート テーブル (ただし、ルート テーブルを使用することは推奨されません) とネットワーク セキュリティ グループが含まれます。

    必要なポートを確認するには、「[ネットワーク セキュリティ グループと必要なポート][network-ports]」を参照してください。 ネットワーク通信の問題を最小限に抑えるには、移行が正常に完了した後に、ネットワーク セキュリティ グループまたはルート テーブルを Resource Manager 仮想ネットワークに適用することをお勧めします。

    このターゲット リソース グループ、ターゲット仮想ネットワーク、ターゲット仮想ネットワークのサブネットをメモしておきます。 これらのリソース名を移行プロセス中に使用します。

1. Azure portal で Azure AD DS マネージド ドメインの正常性を確認します。 マネージド ドメインに関するアラートがある場合は、移行プロセスを開始する前にアラートを解決してください。
1. 必要に応じて、他のリソースを Resource Manager のデプロイ モデルと仮想ネットワークに移動する予定がある場合は、それらのリソースを移行できることを確認します。 詳細については、[プラットフォームでサポートされているクラシックから Resource Manager への IaaS リソースの移行][migrate-iaas]に関するページを参照してください。

    > [!NOTE]
    > クラシック仮想ネットワークを Resource Manager 仮想ネットワークに変換することは、しないください。 これを行った場合、Azure AD DS マネージド ドメインをロールバックまたは復元するオプションはありません。

## <a name="prepare-the-managed-domain-for-migration"></a>マネージド ドメインを移行用に準備する

Azure PowerShell を使用して、Azure AD DS マネージド ドメインを移行用に準備します。 この手順には、バックアップの実行、同期の一時停止、Azure AD DS をホストしているクラウド サービスの削除が含まれます。 この手順が完了すると Azure AD DS が一定期間オフラインになります。 準備の手順が失敗した場合は、[以前の状態にロールバックする](#roll-back)ことができます。

Azure AD DS マネージド ドメインを移行用に準備するには、次の手順を行います。

1. [PowerShell ギャラリー][powershell-script]から `Migrate-Aaads` スクリプトをインストールします。 この PowerShell 移行スクリプトは、Azure AD エンジニアリング チームによってデジタル署名されています。

    ```powershell
    Install-Script -Name Migrate-Aadds
    ```

1. [Get-Credential][get-credential] コマンドレットを使用して、移行スクリプト用の資格情報を保持する変数を作成します。

    指定するユーザー アカウントには、Azure AD DS を有効にするために Azure AD テナントの "*全体管理者*" 特権が必要であり、その後に必要な Azure AD DS リソースを作成するために、Azure サブスクリプションの "*共同作成者*" 特権が必要です。

    プロンプトが表示されたら、適切なユーザー アカウントとパスワードを入力します。

    ```powershell
    $creds = Get-Credential
    ```

1. 次に、 *-Prepare* パラメーターを使用して `Migrate-Aadds` コマンドレットを実行します。 *contoso.com* など、ご自身の Azure AD DS マネージド ドメインの *-ManagedDomainFqdn* を指定します。

    ```powershell
    Migrate-Aadds `
        -Prepare -ManagedDomainFqdn contoso.com `
        -Credentials $creds
    ```

## <a name="migrate-the-managed-domain"></a>マネージド ドメインを移行する

Azure AD DS マネージド ドメインを準備してバックアップしたら、ドメインを移行できます。 この手順では、Resource Manager デプロイ モデルを使用して Azure AD Domain Services ドメイン コントローラー VM を再作成します。 この手順を完了するには、1 時間から 3 時間かかることがあります。

*-Commit* パラメーターを使用して `Migrate-Aadds` コマンドレットを実行します。 *contoso.com* など、前のセクションで準備したご自身の Azure AD DS マネージド ドメインの *-ManagedDomainFqdn* を指定します。

Azure AD DS の移行先である仮想ネットワークを含むターゲット リソース グループを指定します (*myResourceGroup*など)。 *myVnet* などのターゲット仮想ネットワークと、*DomainServices* などのサブネットを指定します。

このコマンドを実行すると、その後にロールバックすることはできません。

```powershell
Migrate-Aadds `
    -Commit `
    -ManagedDomainFqdn contoso.com `
    -VirtualNetworkResourceGroupName myResourceGroup `
    -VirtualNetworkName myVnet `
    -VirtualSubnetName DomainServices `
    -Credentials $creds
```

マネージド ドメインの移行準備が整ったことがスクリプトで検証されたら、「*Y*」を入力して移行プロセスを開始します。

> [!IMPORTANT]
> 移行プロセス中にクラシック仮想ネットワークを Resource Manager 仮想ネットワークに変換しないでください。 仮想ネットワークを変換した場合、変換後に Azure AD DS マネージド ドメインをロールバックまたは復元することはできません。元の仮想ネットワークはもう存在しないからです。

次の出力例に示すように、移行プロセス中は 2 分ごとに、進行状況のインジケーターによって現在の状態が報告されます。

![Azure AD DS 移行の進行状況のインジケーター](media/migrate-from-classic-vnet/powershell-migration-status.png)

PowerShell スクリプトを閉じても、移行プロセスは引き続き実行されます。 Azure portal では、マネージド ドメインの状態は "*移行中*" と報告されます。

移行が正常に完了したら、Azure portal または Azure PowerShell を使用して、最初のドメイン コントローラーの IP アドレスを表示できます。 使用可能な 2 番目のドメイン コントローラーの推定時間も表示されます。

この段階で、必要に応じて、クラシック デプロイ モデルとクラシック仮想ネットワークから他の既存のリソースを移動することができます。 または、クラシック デプロイ モデルでリソースを保持し、Azure AD DS の移行が完了した後に、仮想ネットワークを相互にピアリングすることもできます。

## <a name="test-and-verify-connectivity-after-the-migration"></a>移行後に接続をテストして検証する

2 番目のドメイン コントローラーが正常にデプロイされ、Azure AD DS マネージド ドメインで使用できるようになるまで、時間がかかることがあります。

Resource Manager デプロイ モデルでは、Azure AD DS マネージド ドメインのネットワーク リソースが Azure portal または Azure PowerShell に表示されます。 これらのネットワーク リソースが何であり、何を行うのかの詳細については、「[Azure AD DS によって使用されるネットワーク リソース][network-resources]」を参照してください。

少なくとも 1 つのドメイン コントローラーが使用可能なときに、VM とのネットワーク接続について次の構成手順を行います。

* **DNS サーバー設定の更新** - Resource Manager 仮想ネットワーク上の他のリソースで Azure AD DS マネージド ドメインを解決して使用できるようにするには、新しいドメイン コントローラーの IP アドレスを使用して DNS 設定を更新します。 これらの設定は、Azure portal によって自動的に構成されます。 Resource Manager 仮想ネットワークを構成する方法の詳細については、「[Azure 仮想ネットワークの DNS 設定を更新する][update-dns]」を参照してください。
* **ドメインに参加している VM の再起動** - Azure AD DS ドメイン コントローラーの DNS サーバー IP アドレスが変わったため、ドメインに参加している VM を再起動します。これにより、それらの VM で新しい DNS サーバーの設定が使用されるようになります。 アプリケーションまたは VM の DNS 設定が手動で構成されている場合は、Azure portal に表示されるドメイン コントローラーの新しい DNS サーバー IP アドレスに手動で更新します。

次に、仮想ネットワーク接続と名前解決をテストします。 Resource Manager 仮想ネットワークに接続またはピアリングされている VM で、次のネットワーク通信テストを行います。

1. `ping 10.1.0.4` のように、いずれかのドメイン コントローラーの IP アドレスに対して ping を実行できるかどうかを確認します。
    * ドメイン コントローラーの IP アドレスは、Azure portal で Azure AD DS マネージド ドメインの **[プロパティ]** ページに表示されます。
1. `nslookup contoso.com` のように、マネージド ドメインの名前解決を確認します。
    * ご自身の Azure AD DS マネージド ドメインの DNS 名を指定して、DNS 設定が正しいことと解決されることを確認します。

2 番目のドメイン コントローラーは、移行コマンドレットの完了後 1 時間から 2 時間で使用可能になります。 2 番目のドメイン コントローラーが使用可能かどうかをチェックするには、Azure portal で Azure AD DS マネージド ドメインの **[プロパティ]** ページを確認します。 IP アドレスが 2 つ表示されている場合は、2 番目のドメイン コントローラーの準備ができています。

## <a name="optional-post-migration-configuration-steps"></a>移行後のオプションの構成手順

移行プロセスが正常に完了したら、いくつかのオプションの構成手順があります。監査ログや電子メールによる通知の有効化、細かい設定が可能なパスワード ポリシーの更新などです。

#### <a name="subscribe-to-audit-logs-using-azure-monitor"></a>Azure Monitor を使用して監査ログの受信登録を行う

Azure AD DS では、ドメイン コントローラーのイベントのトラブルシューティングと把握に役立つ監査ログが公開されます。 詳細については、[監査ログの有効化と使用][security-audits]に関するページを参照してください。

テンプレートを使用して、ログで公開される重要な情報を監視できます。 たとえば、監査ログ ブック テンプレートでは、Azure AD DS マネージド ドメインで発生する可能性があるアカウントのロックアウトを監視できます。

#### <a name="configure-azure-ad-domain-services-email-notifications"></a>Azure AD Domain Services の電子メールによる通知を構成する

Azure AD DS マネージド ドメインで問題が検出されたときに通知を受けるには、Azure portal で電子メールによる通知の設定を更新します。 詳細については、[通知設定の構成][notifications]に関するページを参照してください。

#### <a name="update-fine-grained-password-policy"></a>細かい設定が可能なパスワード ポリシーを更新する

必要に応じて、細かい設定が可能なパスワード ポリシーを更新して、既定の構成よりも制限を緩和することができます。 監査ログを使用して、制限の緩い設定に意味があるかどうかを判断し、必要に応じてポリシーを構成することができます。 移行後に繰り返しロックアウトされたアカウントのポリシー設定を確認して更新する場合、おおまかな手順は次のとおりです。

1. Azure AD DS マネージド ドメインの制限を少なくするように[パスワード ポリシーを構成][password-policy]し、監査ログ内のイベントを観察します。
1. 期限切れのパスワードを使用しているサービス アカウントが監査ログで特定された場合は、それらのアカウントを正しいパスワードに更新します。
1. VM がインターネットに公開されている場合は、*administrator*、*user*、*guest* のような一般的なアカウント名について、サインインの試行回数が多いものがないか確認します。 可能であれば、一般的でない名前のアカウントを使用するように VM を更新します。
1. VM 上のネットワーク トレースを使用して攻撃の発生元を特定し、それらの IP アドレスをブロックしてサインインを試行できないようにします。
1. ロックアウトの問題が最小限である場合は、細かい設定が可能なパスワード ポリシーを、必要に応じた厳しさの制限になるように更新します。

#### <a name="creating-a-network-security-group"></a>ネットワーク セキュリティ グループを作成する

Azure AD DS には、マネージド ドメインに必要なポートをセキュリティで保護し、その他すべての受信トラフィックをブロックするネットワーク セキュリティ グループが必要です。 このネットワーク セキュリティ グループは、マネージド ドメインへのアクセスをロックダウンするための追加の保護レイヤーとして機能し、自動的には作成されません。 ネットワーク セキュリティ グループを作成し、必要なポートを開くには、次の手順を確認します。

1. Azure portal で、お使いの Azure AD DS リソースを選択します。 Azure AD Domain Services に関連付けられたネットワーク セキュリティ グループがない場合、[概要] ページには、ネットワーク セキュリティ グループを作成するためのボタンが表示されます。
1. Secure LDAP を使用する場合は、*TCP* ポート *636* の受信トラフィックを許可する規則をネットワーク セキュリティ グループに追加します。 詳細については、[Secure LDAP の構成][secure-ldap]に関するページを参照してください。

## <a name="roll-back-and-restore-from-migration"></a>移行のロールバックと復元

### <a name="roll-back"></a>ロールバック

手順 2 で移行用に準備するため、または手順 3 で移行自体を行うために PowerShell コマンドレットを実行したときにエラーが発生した場合、Azure AD DS マネージド ドメインを元の構成にロールバックできます。 このロールバックを行うには、元のクラシック仮想ネットワークが必要です。 ロールバック後も IP アドレスが変更される可能性があることに注意してください。

*-Abort* パラメーターを使用して `Migrate-Aadds` コマンドレットを実行します。 *contoso.com* など、前述のセクションで準備したご自身の Azure AD DS マネージド ドメインの *-ManagedDomainFqdn* を指定します。

```powershell
Migrate-Aadds `
    -Abort `
    -ManagedDomainFqdn contoso.com `
    -Credentials $creds
```

### <a name="restore"></a>復元

最後の手段として、最新の利用可能なバックアップから Azure AD Domain Services を復元できます。 移行の手順 1 で、最新のバックアップを確実に使用可能にするため、バックアップが実行されます。 このバックアップは 30 日間保存されます。

Azure AD DS マネージド ドメインをバックアップから復元するには、[Azure portal を使用してサポート ケース チケットを開きます][azure-support]。 ディレクトリ ID、ドメイン名、復元の理由を入力します。 サポートと復元のプロセスが完了するまでに数日かかる場合があります。

## <a name="troubleshooting"></a>トラブルシューティング

Resource Manager デプロイ モデルへの移行後に問題が発生した場合は、次の一般的なトラブルシューティングの領域を確認してください。

* [ドメイン参加の問題のトラブルシューティング][troubleshoot-domain-join]
* [アカウントがロックアウトされる問題を解決する][troubleshoot-account-lockout]
* [アカウント サインイン問題を解決する][troubleshoot-sign-in]
* [Secure LDAP の接続に関する問題のトラブルシューティング][tshoot-ldaps]

## <a name="next-steps"></a>次の手順

Azure AD DS マネージド ドメインを Resource Manager デプロイ モデルに移行したら、[Windows VM を作成してドメインに参加][join-windows]させます。その後、[管理ツールをインストール][tutorial-create-management-vm]します。

<!-- INTERNAL LINKS -->
[azure-bastion]: ../bastion/bastion-overview.md
[network-considerations]: network-considerations.md
[azure-powershell]: /powershell/azure/overview
[network-ports]: network-considerations.md#network-security-groups-and-required-ports
[Connect-AzAccount]: /powershell/module/az.accounts/connect-azaccount
[Set-AzContext]: /powershell/module/az.accounts/set-azcontext
[Get-AzResource]: /powershell/module/az.resources/get-azresource
[Set-AzResource]: /powershell/module/az.resources/set-azresource
[network-resources]: network-considerations.md#network-resources-used-by-azure-ad-ds
[update-dns]: tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network
[azure-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
[security-audits]: security-audit-events.md
[notifications]: notifications.md
[password-policy]: password-policy.md
[secure-ldap]: tutorial-configure-ldaps.md
[migrate-iaas]: ../virtual-machines/windows/migration-classic-resource-manager-overview.md
[join-windows]: join-windows-vm.md
[tutorial-create-management-vm]: tutorial-create-management-vm.md
[troubleshoot-domain-join]: troubleshoot-domain-join.md
[troubleshoot-account-lockout]: troubleshoot-account-lockout.md
[troubleshoot-sign-in]: troubleshoot-sign-in.md
[tshoot-ldaps]: tshoot-ldaps.md
[get-credential]: /powershell/module/microsoft.powershell.security/get-credential

<!-- EXTERNAL LINKS -->
[powershell-script]: https://www.powershellgallery.com/packages/Migrate-Aadds/1.0
