---
title: Azure Database for PostgreSQL (単一サーバー) 用に Azure AD を構成してサインインする
description: Azure Database for PostgreSQL (単一サーバー) での認証に Azure Active Directory を設定する方法について説明します
author: lfittl
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d5abfe4cc6aa0679d8009343fa24c1059700bb79
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/04/2019
ms.locfileid: "73511268"
---
# <a name="use-azure-active-directory-for-authenticating-with-postgresql"></a>PostgreSQL での認証に Azure Active Directory を使用する

この記事では、Azure Database for PostgreSQL を使用して Azure Active Directory アクセスを構成する方法と、Azure AD トークンを使用して接続する方法について説明します。

> [!IMPORTANT]
> Azure Database for PostgreSQL の Azure AD 認証は現在、パブリック プレビュー段階にあります。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="setting-the-azure-ad-admin-user"></a>Azure AD 管理者ユーザーを設定する

Azure AD ベースの認証用にユーザーを作成/有効化できるのは、Azure AD 管理者ユーザーだけです。 Azure AD 管理者ユーザーを作成するには、次の手順に従います

1. Azure portal で、Azure AD に対して有効にする Azure Database for PostgreSQL のインスタンスを選択します。
2. [設定] で、[Active Directory 管理者] を選択します。

![Azure AD 管理者の設定][2]

3. 顧客テナントで Azure AD 管理者にする有効な Azure AD ユーザーを選択します。

> [!IMPORTANT]
> 管理者を設定すると、管理者の完全なアクセス許可を持つ新しいユーザーが Azure Database for PostgreSQL サーバーに追加されます。 Azure Database for PostgreSQL の Azure AD 管理者ユーザーは、ロール `azure_ad_admin` を持ちます。

作成できる Azure AD 管理者は、PostgreSQL サーバーあたり 1 人だけです。別の管理者を選択すると、そのサーバーに構成されている既存の Azure AD 管理者が上書きされます。 個々のユーザーではなく、Azure AD グループを指定して、複数の管理者を持つことができます。 その後、管理目的には、グループ名を使用してサインインすることに注意してください。

## <a name="creating-azure-ad-users-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL で Azure AD ユーザーを作成する

Azure Database for PostgreSQL データベースに Azure AD ユーザーを追加するには、接続後に次の手順を実行します (接続方法については、後述のセクションを参照してください)。

1. まず、Azure AD ユーザー `<user>@yourtenant.onmicrosoft.com` が Azure AD テナントの有効なユーザーであることを確認します。
2. Azure AD 管理者ユーザーとして Azure Database for PostgreSQL インスタンスにサインインします。
3. Azure Database for PostgreSQL でロール `<user>@yourtenant.onmicrosoft.com` を作成します。
4. `<user>@yourtenant.onmicrosoft.com` をロール azure_ad_user のメンバーにします。 これは、Azure AD ユーザーにのみ付与する必要があります。

**例:**

```sql
CREATE ROLE "user1@yourtenant.onmicrosoft.com" WITH LOGIN IN ROLE azure_ad_user;
```

> [!NOTE]
> Azure AD を通じてユーザーを認証しても、Azure Database for PostgreSQL データベース内のオブジェクトにアクセスするためのアクセス許可はユーザーに付与されません。 必要なアクセス許可をユーザーに手動で付与する必要があります。

## <a name="creating-azure-ad-groups-in-azure-database-for-postgresql"></a>Azure Database for PostgreSQL で Azure AD グループを作成する

Azure AD グループがデータベースにアクセスできるようにするには、ユーザーの場合と同じメカニズムを使用しますが、代わりにグループ名を指定します。

**例:**

```sql
CREATE ROLE "Prod DB Readonly" WITH LOGIN IN ROLE azure_ad_user;
```

ログインするときに、グループのメンバーは自分の個人用アクセス トークンを使用しますが、ユーザー名として指定されたグループ名でサインインします。

## <a name="connecting-to-azure-database-for-postgresql-using-azure-ad"></a>Azure AD を使用して Azure Database for PostgreSQL に接続する

次の概要図は、Azure Database for PostgreSQL で Azure AD 認証を使用するワークフローをまとめたものです。

![認証フロー][1]

Azure AD 統合は、psql などの一般的な PostgreSQL ツールと連携するように設計されています。これらのツールは Azure AD 対応ではなく、PostgreSQL に接続するときにユーザー名とパスワードを指定することのみをサポートしています。 上の図に示されているように、Azure AD トークンをパスワードとして渡します。

現在、次のクライアントがテストされています。

- psql commandline (PGPASSWORD 変数を使用してトークンを渡す。以下を参照)
- Azure Data Studio (PostgreSQL 拡張機能を使用)
- その他の libpq ベースのクライアント (例: 一般的なアプリケーション フレームワークと ORM)

> [!NOTE]
> pgAdmin には、パスワードに対して 256 文字というハードコーディングの制限があり、Azure AD トークンはこれを超えているため、pgAdmin でこのトークンを使用することは現在サポートされていないことに注意してください。

ユーザー/アプリケーションで Azure AD を使用して認証を行う必要がある手順を次に示します。

### <a name="step-1-authenticate-with-azure-ad"></a>手順 1:Azure AD による認証

[Azure CLI がインストールされている](/cli/azure/install-azure-cli)ことを確認します。

Azure CLI ツールを呼び出して、Azure AD で認証します。 ご自分の Azure AD ユーザー ID とパスワードを指定する必要があります。

```
az login
```

このコマンドを実行すると、ブラウザー ウィンドウが起動して Azure AD 認証ページが表示されます。

> [!NOTE]
> Azure Cloud Shell を使用してこれらの手順を実行することもできます。
> Azure Cloud Shell で Azure AD アクセス トークンを取得する場合は、明示的に `az login` を呼び出して、(別のウィンドウでコードを使用して) もう一度サインインする必要があることに注意してください。 サインイン後、`get-access-token` コマンドは正常に動作します。

### <a name="step-2-retrieve-azure-ad-access-token"></a>手順 2:Azure AD アクセス トークンを取得する

Azure CLI ツールを起動して、手順 1 で認証された Azure AD ユーザーのアクセス トークンを取得し、Azure Database for PostgreSQL にアクセスします。

例 (パブリック クラウドの場合):

```shell
az account get-access-token --resource https://ossrdbms-aad.database.windows.net
```

上記のリソース値は、示されているとおりに正確に指定する必要があります。 他のクラウドの場合、リソース値は次を使用して検索できます。

```shell
az cloud show
```

Azure CLI バージョン 2.0.71 以降では、すべてのクラウドに対して、次のより便利なバージョンでコマンドを指定できます。

```shell
az account get-access-token --resource-type oss-rdbms
```

認証が成功すると、Azure AD によってアクセス トークンが返されます。

```json
{
  "accessToken": "TOKEN",
  "expiresOn": "...",
  "subscription": "...",
  "tenant": "...",
  "tokenType": "Bearer"
}
```

トークンは、認証されたユーザーに関するすべての情報をエンコードする Base 64 文字列であり、Azure Database for PostgreSQL サービスをターゲットとしています。

> [!NOTE]
> アクセス トークンの有効性は、5 分から 60 分の範囲内です。 アクセス トークンは、Azure Database for PostgreSQL へのログインを開始する直前に取得することをお勧めします。

### <a name="step-3-use-token-as-password-for-logging-in-with-postgresql"></a>手順 3:PostgreSQL でログインするためのパスワードとしてトークンを使用する

接続時には、PostgreSQL ユーザー パスワードとしてアクセス トークンを使用する必要があります。

`psql` コマンド ライン クライアントを使用する場合は、`PGPASSWORD` 環境変数を介してアクセス トークンを渡す必要があります。これは、アクセス トークンが、`psql` が直接受け入れることができるパスワードの長さを超えているためです。

Windows の例:

```shell
set PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

Linux/macOS の例:

```shell
export PGPASSWORD=<copy/pasted TOKEN value from step 2>
```

これで、通常行っているように、Azure Database for PostgreSQL との接続を開始できるようになりました。

```shell
psql "host=mydb.postgres... user=user@tenant.onmicrosoft.com@mydb dbname=postgres"
```

これで、Azure AD 認証を使用して PostgreSQL サーバーに対して認証されました。

## <a name="token-validation"></a>トークンの検証

Azure Database for PostgreSQL での Azure AD 認証により、ユーザーが PostgreSQL サーバーに存在することが保証され、トークンの内容を検証することによってトークンの有効性がチェックされます。 次のトークンの検証手順が実行されます。

-   トークンが Azure AD によって署名されていて、改ざんされていないこと
-   トークンがサーバーに関連付けられているテナントの Azure AD によって発行されたこと
-   トークンの有効期限が切れていないこと
-   トークンが (別の Azure リソースではなく) Azure Database for PostgreSQL リソース用であること

## <a name="migrating-existing-postgresql-users-to-azure-ad-based-authentication"></a>既存の PostgreSQL ユーザーを Azure AD ベースの認証に移行する

既存のユーザーに対して Azure AD 認証を有効にすることができます。 考慮すべきケースが 2 つあります。

### <a name="case-1-postgresql-username-matches-the-azure-ad-user-principal-name"></a>ケース 1:PostgreSQL ユーザー名が Azure AD のユーザー プリンシパル名と一致している

まれなケースで既存のユーザーが Azure AD のユーザー名と既に一致している場合は、そのユーザーに Azure AD 認証を有効にするために `azure_ad_user` ロールを付与することができます。

```sql
GRANT azure_ad_user TO "existinguser@yourtenant.onmicrosoft.com";
```

これで、以前に構成した PostgreSQL ユーザー パスワードを使用するのではなく、Azure AD の資格情報でサインインできるようになります。

### <a name="case-2-postgresql-username-is-different-than-the-azure-ad-user-principal-name"></a>ケース 2:PostgreSQL ユーザー名が Azure AD のユーザー プリンシパル名と異なっている

PostgreSQL ユーザーが Azure AD に存在しない場合、または別のユーザー名を持っている場合は、Azure AD グループを使用して、この PostgreSQL ユーザーとして認証することができます。 既存の Azure Database for PostgreSQL ユーザーを Azure AD に移行するには、PostgreSQL ユーザーと一致する名前を持つ Azure AD グループを作成し、既存の PostgreSQL ユーザーにロール azure_ad_user を付与します。

```sql
GRANT azure_ad_user TO "DBReadUser";
```

これは、Azure AD に "DBReadUser" というグループが作成されていることを前提としています。 これで、そのグループに属するユーザーが、このユーザーとしてデータベースにサインインできるようになります。

## <a name="next-steps"></a>次の手順

* [Azure Database for PostgreSQL (単一サーバー) を使用した Azure Active Directory 認証](concepts-aad-authentication.md)の全体的な概念を確認する

<!--Image references-->

[1]: ./media/concepts-aad-authentication/authentication-flow.png
[2]: ./media/concepts-aad-authentication/set-aad-admin.png
