---
title: ユーザーをエンド ツー エンドで認証および承認する - Azure App Service | Microsoft Docs
description: App Service の認証と承認を使用して、リモート API へのアクセスを含む App Service アプリをセキュリティで保護する方法について説明します。
keywords: App Service, Azure App Service, authN, authZ, 保護, セキュリティ, 多層, Azure Active Directory, Azure Ad
services: app-service\web
documentationcenter: dotnet
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 55ba5a193e15ac4e8bd83ac046deeac1f12d61d0
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961953"
---
# <a name="tutorial-authenticate-and-authorize-users-end-to-end-in-azure-app-service"></a>チュートリアル:Azure App Service でユーザーをエンド ツー エンドで認証および承認する

[Azure App Service](overview.md) では、高度にスケーラブルな自己適用型の Web ホスティング サービスを提供しています。 さらに、App Service には、[ユーザーの認証と承認](overview-authentication-authorization.md)のためのサポートが組み込まれています。 このチュートリアルでは、App Service の認証と承認を使用してアプリケーションをセキュリティで保護する方法を示します。 ここでは ASP.NET Core アプリと Angular.js フロントエンドが使用されていますが、これはほんの一例です。 App Service の認証と承認では、すべての言語のランタイムがサポートされています。このチュートリアルに沿って、お好みの言語に適用する方法を学習することができます。

チュートリアルでは、サンプル アプリを使用して、自己完結型アプリをセキュリティで保護する方法を示します (「[バックエンド アプリの認証と承認を有効にする](#enable-authentication-and-authorization-for-back-end-app)」を参照)。

![単純な認証と承認](./media/app-service-web-tutorial-auth-aad/simple-auth.png)

また、認証されたユーザーの代わりに、[サーバー コード](#call-api-securely-from-server-code)と[ブラウザー コード](#call-api-securely-from-browser-code)の両方からセキュリティで保護されたバックエンド API にアクセスすることによって、多層アプリをセキュリティで保護する方法も示します。

![高度な認証と承認](./media/app-service-web-tutorial-auth-aad/advanced-auth.png)

これらは、App Service で実現可能な認証および承認のシナリオのごく一部です。 

以下に、チュートリアルで学習する事項のより包括的な一覧を示します。

> [!div class="checklist"]
> * 組み込みの認証および承認を有効にする
> * 未認証の要求に対してアプリをセキュリティで保護する
> * Azure Active Directory を ID プロバイダーとして使用する
> * サインインしたユーザーの代わりにリモート アプリにアクセスする
> * トークン認証を使用して、サービス間呼び出しをセキュリティで保護する
> * サーバー コードのアクセス トークンを使用する
> * クライアント (ブラウザー) コードのアクセス トークンを使用する

このチュートリアルの手順は、macOS、Linux、Windows で実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

* [Git をインストールします](https://git-scm.com/)。
* [.NET Core のインストール](https://www.microsoft.com/net/core/)。

## <a name="create-local-net-core-app"></a>ローカル .NET Core アプリを作成する

この手順では、ローカル .NET Core プロジェクトを設定します。 バックエンド API アプリとフロントエンド Web アプリのデプロイには、同じプロジェクトを使用します。

### <a name="clone-and-run-the-sample-application"></a>サンプル アプリケーションを複製して実行する

次のコマンドを実行して、サンプル リポジトリを複製し、実行します。

```bash
git clone https://github.com/Azure-Samples/dotnet-core-api
cd dotnet-core-api
dotnet run
```

`http://localhost:5000` に移動し、ToDo 項目の追加、編集、および削除を試みます。 

![ローカルで実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/local-run.png)

任意のタイミングで ASP.NET Core を停止するには、ターミナルで `Ctrl+C` キーを押します。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-apps-to-azure"></a>アプリを Azure にデプロイする

この手順では、2 つの App Service アプリにプロジェクトをデプロイします。 1 つはフロントエンド アプリで、もう 1 つはバックエンド アプリです。

### <a name="configure-a-deployment-user"></a>デプロイ ユーザーを構成する

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="create-azure-resources"></a>Azure リソースを作成する

Cloud Shell で次のコマンドを実行して、2 つの Web アプリを作成します。 _\<front-end-app-name>_ と _\<back-end-app-name>_ を、グローバルに一意の 2 つのアプリ名に置き換えます (有効な文字は `a-z`、`0-9`、`-` です)。 各コマンドの詳細については、[Azure App Service での RESTful API と CORS](app-service-web-tutorial-rest-api.md) に関する記事を参照してください。

```azurecli-interactive
az group create --name myAuthResourceGroup --location "West Europe"
az appservice plan create --name myAuthAppServicePlan --resource-group myAuthResourceGroup --sku FREE
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <front-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
az webapp create --resource-group myAuthResourceGroup --plan myAuthAppServicePlan --name <back-end-app-name> --deployment-local-git --query deploymentLocalGitUrl
```

> [!NOTE]
> `az webapp create` の出力に表示されている、フロントエンド アプリとバックエンド アプリの Git リモートの URL を保存します。
>

### <a name="push-to-azure-from-git"></a>Git から Azure へのプッシュ

"_ローカル ターミナル ウィンドウ_" に戻り、以下の Git コマンドを実行して、バックエンド アプリにデプロイします。 _\<deploymentLocalGitUrl-of-back-end-app>_ を、「[Azure リソースを作成する](#create-azure-resources)」で保存した Git リモートの URL に置き換えます。 Git Credential Manager によって資格情報の入力を求めるメッセージが表示されたら、Azure portal へのサインインに使用する資格情報ではなく、[デプロイ資格情報](deploy-configure-credentials.md)を入力してください。

```bash
git remote add backend <deploymentLocalGitUrl-of-back-end-app>
git push backend master
```

ローカル ターミナル ウィンドウで、以下の Git コマンドを実行して、同じコードをフロントエンド アプリにデプロイします。 _\<deploymentLocalGitUrl-of-front-end-app>_ を、「[Azure リソースを作成する](#create-azure-resources)」で保存した Git リモートの URL に置き換えます。

```bash
git remote add frontend <deploymentLocalGitUrl-of-front-end-app>
git push frontend master
```

### <a name="browse-to-the-apps"></a>アプリの参照

ブラウザーで次の URL に移動し、2 つのアプリが動作していることを確認します。

```
http://<back-end-app-name>.azurewebsites.net
http://<front-end-app-name>.azurewebsites.net
```

![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/azure-run.png)

> [!NOTE]
> アプリが再起動されると、新しいデータが消去されていることに気付く場合があります。 サンプル ASP.NET Core アプリではメモリ内データベースが使用されているため、この動作は仕様です。
>
>

## <a name="call-back-end-api-from-front-end"></a>フロントエンドからバックエンド API を呼び出す

この手順では、フロントエンド アプリのサーバー コードを、バックエンド API へのアクセスに合わせて設定します。 後で、フロントエンドからバックエンドへの認証済みアクセスを有効にします。

### <a name="modify-front-end-code"></a>フロントエンド コードを変更する

ローカル リポジトリで、_Controllers/TodoController.cs_ を開きます。 `TodoController` クラスの先頭に次の行を追加し、 _\<back-end-app-name>_ を実際のバックエンド アプリの名前に置き換えます。

```cs
private static readonly HttpClient _client = new HttpClient();
private static readonly string _remoteUrl = "https://<back-end-app-name>.azurewebsites.net";
```

`GetAll()` メソッドを見つけ、中かっこ内のコードを次のように置き換えます。

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo").Result;
return JsonConvert.DeserializeObject<List<TodoItem>>(data);
```

最初の行は、バックエンド API アプリへの `GET /api/Todo` 呼び出しを行います。

次に、`GetById(long id)` メソッドを見つけ、中かっこ内のコードを次のように置き換えます。

```cs
var data = _client.GetStringAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return Content(data, "application/json");
```

最初の行は、バックエンド API アプリへの `GET /api/Todo/{id}` 呼び出しを行います。

次に、`Create([FromBody] TodoItem item)` メソッドを見つけ、中かっこ内のコードを次のように置き換えます。

```cs
var response = _client.PostAsJsonAsync($"{_remoteUrl}/api/Todo", item).Result;
var data = response.Content.ReadAsStringAsync().Result;
return Content(data, "application/json");
```

最初の行は、バックエンド API アプリへの `POST /api/Todo` 呼び出しを行います。

次に、`Update(long id, [FromBody] TodoItem item)` メソッドを見つけ、中かっこ内のコードを次のように置き換えます。

```cs
var res = _client.PutAsJsonAsync($"{_remoteUrl}/api/Todo/{id}", item).Result;
return new NoContentResult();
```

最初の行は、バックエンド API アプリへの `PUT /api/Todo/{id}` 呼び出しを行います。

次に、`Delete(long id)` メソッドを見つけ、中かっこ内のコードを次のように置き換えます。

```cs
var res = _client.DeleteAsync($"{_remoteUrl}/api/Todo/{id}").Result;
return new NoContentResult();
```

最初の行は、バックエンド API アプリへの `DELETE /api/Todo/{id}` 呼び出しを行います。

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "call back-end API"
git push frontend master
```

### <a name="check-your-changes"></a>変更を確認する

`http://<front-end-app-name>.azurewebsites.net` に移動し、`from front end 1` や `from front end 2` などのいくつかの項目を追加します。

`http://<back-end-app-name>.azurewebsites.net` に移動して、フロントエンド アプリから追加された項目を確認します。 また、`from back end 1` や `from back end 2` などのいくつかの項目を追加し、フロントエンド アプリを更新して、変更が反映されているかどうかを確認します。

![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/remote-api-call-run.png)

## <a name="configure-auth"></a>auth を構成する

この手順では、2 つのアプリの認証と承認を有効にします。 また、フロントエンド アプリも構成して、バックエンド アプリへの認証済みの呼び出しを行うために使用できるアクセス トークンが生成されるようにします。

ID プロバイダーとして Azure Active Directory を使用します。 詳細については、[App Services アプリケーション用の Azure Active Directory 認証の構成](configure-authentication-provider-aad.md)に関するページを参照してください。

### <a name="enable-authentication-and-authorization-for-back-end-app"></a>バックエンド アプリの認証と承認を有効にする

1. [[Azure portal]](https://portal.azure.com) メニューで **[リソース グループ]** を選択するか、または任意のページから *[リソース グループ]* を検索して選択します。

1. **[リソース グループ]** でリソース グループを検索して選択します。 **[概要]** でバックエンド アプリの管理ページを選択します。

   ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/portal-navigate-back-end.png)

1. バックエンド アプリの左側のメニューで **[認証/承認]** を選択し、 **[オン]** を選択して App Service 認証を有効にします。

1. **[要求が認証されない場合に実行するアクション]** で、 **[Azure Active Directory でのログイン]** を選択します。

1. **[認証プロバイダー]** で **[Azure Active Directory]** を選択します。 

   ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/configure-auth-back-end.png)

1. **[簡易]** を選択し、既定の設定のままで新しい AD アプリを作成して、 **[OK]** を選択します。

1. **[認証/承認]** ページで、 **[保存]** を選択します。

   `Successfully saved the Auth Settings for <back-end-app-name> App` というメッセージの通知が表示されたら、ページを更新します。

1. **[Azure Active Directory]** を再度選択し、 **[Azure AD アプリ]** を選択します。

1. Azure AD アプリケーションの**クライアント ID** をメモ帳にコピーします。 この値は、後で必要になります。

   ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/get-application-id-back-end.png)

### <a name="enable-authentication-and-authorization-for-front-end-app"></a>フロントエンド アプリの認証と承認を有効にする

フロントエンド アプリに対して同じ手順を行いますが、最後の手順はスキップします。 フロントエンド アプリでは、クライアント ID は必要ありません。

必要に応じて、`http://<front-end-app-name>.azurewebsites.net` に移動します。 セキュリティで保護されたサインイン ページにリダイレクトされるようになったはずです。 サインインした後でも、まだバックエンド アプリのデータにアクセスすることはできません。次の 3 つの作業を行う必要があります。

- バックエンドへのフロントエンド アクセスを許可する
- 使用可能なトークンを返すように App Service を構成する
- コードでトークンを使用する

> [!TIP]
> エラーが発生してアプリの認証/承認設定を再構成すると、トークン ストア内のトークンが新しい設定で再生成されないことがあります。 トークンが再生成されるようにするには、サインアウトしてからアプリにサインインし直す必要があります。 そのための簡単な方法は、ブラウザーをプライベート モードで使用し、アプリの設定を変更した後、ブラウザーを閉じてからプライベート モードでもう一度開くことです。

### <a name="grant-front-end-app-access-to-back-end"></a>バックエンドへのフロントエンド アプリのアクセスを許可する

両方のアプリに対する認証と承認を有効にしたので、それぞれのアプリは AD アプリケーションによってサポートされています。 この手順では、ユーザーの代わりにバックエンドにアクセスするアクセス許可をフロントエンド アプリに付与します (技術的には、ユーザーの代わりにバックエンドの "_AD アプリケーション_" にアクセスするためのアクセス許可をフロントエンドの "_AD アプリケーション_" に付与します)。

1. [[Azure portal]](https://portal.azure.com) メニューで **[Azure Active Directory]** を選択するか、任意のページから *[Azure Active Directory]* を検索して選択します。

1. **[アプリの登録]**  >  **[所有しているアプリケーション]** を選択します。 フロントエンド アプリ名を選択し、 **[API のアクセス許可]** を選択します。

   ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/add-api-access-front-end.png)

1. **[アクセス許可の追加]** を選択し、 **[自分の API]**  >  **\<back-end-app-name>** を選択します。

1. バックエンド アプリの **[API アクセス許可の要求]** ページで、 **[委任されたアクセス許可]** と **[user_impersonation]** を選択し、次に **[アクセス許可の追加]** を選択します。

   ![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/select-permission-front-end.png)

### <a name="configure-app-service-to-return-a-usable-access-token"></a>使用可能なアクセス トークンを返すように App Service を構成する

これで、フロントエンド アプリに、サインインしたユーザーとしてバックエンド アプリにアクセスするために必要なアクセス許可が与えられました。 この手順では、バックエンドにアクセスするための使用可能なアクセス トークンを提供するように、App Service の認証および承認を構成します。 この手順では、バックエンドのクライアント ID が必要です。この ID は、「[バックエンド アプリの認証と承認を有効にする](#enable-authentication-and-authorization-for-back-end-app)」でコピーしたものです。

[Azure Resource Explorer](https://resources.azure.com) にサインインします。 ページの上部にある **[読み取り/書き込み]** をクリックして、Azure リソースの編集を有効にします。

![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/resources-enable-write.png)

左側のブラウザーで、 **[subscriptions]**  >  **_\<your-subscription>_**  >  **[resourceGroups]**  >  **[myAuthResourceGroup]**  >  **[providers]**  > **Microsoft.Web** >  **[sites]**  >  **_\<front-end-app-name>_**  >  **[config]**  >  **[authsettings]** の順にクリックします。

**[authsettings]** ビューで、 **[編集]** をクリックします。 コピーしたクライアント ID を使用して、`additionalLoginParams` を次の JSON 文字列に設定します。 

```json
"additionalLoginParams": ["response_type=code id_token","resource=<back-end-client-id>"],
```

![Azure App Service で実行される ASP.NET Core API](./media/app-service-web-tutorial-auth-aad/additional-login-params-front-end.png)

**[PUT]** をクリックして、設定を保存します。

これでアプリの構成は完了です。 フロントエンドが適切なアクセス トークンを使用してバックエンドにアクセスする準備ができました。

他のプロバイダー用にアクセス トークンを構成する方法については、「[ID プロバイダー トークンの更新](app-service-authentication-how-to.md#refresh-identity-provider-tokens)」を参照してください。

## <a name="call-api-securely-from-server-code"></a>サーバー コードから API を安全に呼び出す

この手順では、前に変更したサーバー コードを有効にして、バックエンド API への認証済み呼び出しを行います。

フロントエンド アプリに必要なアクセス許可が付与されていて、バックエンドのクライアント ID もログイン パラメーターに追加されます。 そのため、バックエンド アプリでの認証用のアクセス トークンを取得することができます。 App Service は、認証された各要求に `X-MS-TOKEN-AAD-ACCESS-TOKEN` ヘッダーを挿入することで、このトークンをサーバー コードに提供します (「[Retrieve tokens in app code (アプリ コードでトークンを取得する)](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)」を参照)。

> [!NOTE]
> これらのヘッダーは、サポートされているすべての言語用に挿入されます。 言語ごとの標準パターンを使用して、これらにアクセスします。

ローカル リポジトリで、_Controllers/TodoController.cs_ を再度開きます。 `TodoController(TodoContext context)` コンストラクターの下に、次のコードを追加します。

```cs
public override void OnActionExecuting(ActionExecutingContext context)
{
    base.OnActionExecuting(context);

    _client.DefaultRequestHeaders.Accept.Clear();
    _client.DefaultRequestHeaders.Authorization =
        new AuthenticationHeaderValue("Bearer", Request.Headers["X-MS-TOKEN-AAD-ACCESS-TOKEN"]);
}
```

このコードは、すべてのリモート API 呼び出しに標準の HTTP ヘッダー `Authorization: Bearer <access-token>` を追加します。 ASP.NET Core MVC 要求実行パイプラインでは、`OnActionExecuting` が各アクション メソッド (`GetAll()` など) の直前に実行されるため、各発信 API 呼び出しにアクセス トークンが提供されます。

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "add authorization header for server code"
git push frontend master
```

`https://<front-end-app-name>.azurewebsites.net` に再度サインインします。 ユーザー データの使用の同意のページで、 **[同意する]** をクリックします。

以前と同様に、バックエンド アプリのデータを作成、読み取り、更新、および削除できるようになったはずです。 唯一の違いは、App Service の認証と承認によって、サービス間呼び出しも含めて、両方のアプリがセキュリティで保護されていることです。

お疲れさまでした。 サーバー コードは、認証されたユーザーの代わりにバックエンドのデータにアクセスするようになりました。

## <a name="call-api-securely-from-browser-code"></a>ブラウザー コードから API を安全に呼び出す

この手順では、フロントエンドの Angular.js アプリをバックエンド API に合わせて設定します。 そのようにして、アクセス トークンを取得し、それを使用してバックエンド アプリへの API 呼び出しを行う方法を学習します。

サーバー コードは要求ヘッダーにアクセスできますが、クライアント コードは `GET /.auth/me` にアクセスして同じアクセス トークンを取得することができます (「[Retrieve tokens in app code (アプリ コードでトークンを取得する)](app-service-authentication-how-to.md#retrieve-tokens-in-app-code)」を参照)。

> [!TIP]
> このセクションでは、標準の HTTP メソッドを使用して、セキュリティで保護された HTTP 呼び出しを行います。 ただし、[Active Directory Authentication Library (ADAL) for JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js) を使用すると、Angular.js アプリケーション パターンの簡略化に役立ちます。
>

### <a name="configure-cors"></a>CORS を構成する

Cloud Shell で [`az resource update`](/cli/azure/resource#az-resource-update) コマンドを使用して、クライアントの URL に対して CORS を有効にします。 _\<back-end-app-name>_ と _\<front-end-app-name>_ のプレースホルダーを置き換えます。

```azurecli-interactive
az resource update --name web --resource-group myAuthResourceGroup --namespace Microsoft.Web --resource-type config --parent sites/<back-end-app-name> --set properties.cors.allowedOrigins="['https://<front-end-app-name>.azurewebsites.net']" --api-version 2015-06-01
```

この手順は、認証と承認には無関係です。 ただし、ブラウザーで Angular.js アプリからのクロスドメイン API 呼び出しが許可されるようにするには、この手順が必要です。 詳細については、「[CORS 機能の追加](app-service-web-tutorial-rest-api.md#add-cors-functionality)」を参照してください。

### <a name="point-angularjs-app-to-back-end-api"></a>Angular.js アプリをバックエンド API に合わせて設定する

ローカル リポジトリで、_wwwroot/index.html_ を開きます。

行 51 の `apiEndpoint` 変数を、バックエンド アプリの URL (`https://<back-end-app-name>.azurewebsites.net`) に設定します。 _\<back-end-app-name>_ を、App Service の実際のアプリ名に置き換えます。

ローカル リポジトリで、_wwwroot/app/scripts/todoListSvc.js_ を開き、すべての API 呼び出しの前に `apiEndpoint` が付加されていることを確認します。 Angular.js アプリが、バックエンド API を呼び出すようになりました。 

### <a name="add-access-token-to-api-calls"></a>API 呼び出しにアクセス トークンを追加する

_wwwroot/app/scripts/todoListSvc.js_ の API 呼び出しの一覧の上 (`getItems : function(){` の行の上) で、次の関数を一覧に追加します。

```javascript
setAuth: function (token) {
    $http.defaults.headers.common['Authorization'] = 'Bearer ' + token;
},
```

この関数は、既定の `Authorization` ヘッダーにアクセス トークンを設定するために呼び出されます。 すぐ後の手順で、それを呼び出します。

ローカル リポジトリで _wwwroot/app/scripts/app.js_ を開き、次のコードを見つけます。

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
}).otherwise({ redirectTo: "/Home" });
```

コード ブロック全体を次のコードに置き換えます。

```javascript
$routeProvider.when("/Home", {
    controller: "todoListCtrl",
    templateUrl: "/App/Views/TodoList.html",
    resolve: {
        token: ['$http', 'todoListSvc', function ($http, todoListSvc) {
            return $http.get('/.auth/me').then(function (response) {
                todoListSvc.setAuth(response.data[0].access_token);
                return response.data[0].access_token;
            });
        }]
    },
}).otherwise({ redirectTo: "/Home" });
```

新しい変更では、`/.auth/me` を呼び出し、アクセス トークンを設定する、`resolve` マッピングを追加します。 こうすることで、`todoListCtrl` コントローラーをインスタンス化する前に、アクセス トークンを入手することができます。 この方法では、コントローラーによるすべての API 呼び出しにトークンが含まれます。

### <a name="deploy-updates-and-test"></a>更新をデプロイし、テストする

すべての変更を保存します。 ローカル ターミナル ウィンドウで、以下の Git コマンドを使用して、変更をフロントエンド アプリにデプロイします。

```bash
git add .
git commit -m "add authorization header for Angular"
git push frontend master
```

再度 `https://<front-end-app-name>.azurewebsites.net` に移動します。 バックエンド アプリのデータを Angular.js で直接作成、読み取り、更新、および削除できるようになったはずです。

お疲れさまでした。 クライアント コードは、認証されたユーザーの代わりにバックエンドのデータにアクセスするようになりました。

## <a name="when-access-tokens-expire"></a>アクセス トークンの有効期限が切れたら

アクセス トークンは、しばらくすると有効期限が切れます。 アプリに対する再認証をユーザーに強制することなくアクセス トークンを更新する方法については、「[Refresh identity provider tokens (ID プロバイダー トークンの更新)](app-service-authentication-how-to.md#refresh-identity-provider-tokens)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

前の手順では、リソース グループ内に Azure リソースを作成しました。 これらのリソースが将来必要になると想定していない場合、Cloud Shell で次のコマンドを実行して、リソース グループを削除します。

```azurecli-interactive
az group delete --name myAuthResourceGroup
```

このコマンドの実行には、少し時間がかかる場合があります。

<a name="next"></a>
## <a name="next-steps"></a>次の手順

ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * 組み込みの認証および承認を有効にする
> * 未認証の要求に対してアプリをセキュリティで保護する
> * Azure Active Directory を ID プロバイダーとして使用する
> * サインインしたユーザーの代わりにリモート アプリにアクセスする
> * トークン認証を使用して、サービス間呼び出しをセキュリティで保護する
> * サーバー コードのアクセス トークンを使用する
> * クライアント (ブラウザー) コードのアクセス トークンを使用する

次のチュートリアルに進み、カスタム DNS 名を Web アプリにマップする方法を学習してください。

> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure App Service にマップする](app-service-web-tutorial-custom-domain.md)
