---
title: v3 API を使用した開発 - Azure | Microsoft Docs
description: この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/21/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 79f1bd95451709485f92050a882c790f9e281eb5
ms.sourcegitcommit: b1a8f3ab79c605684336c6e9a45ef2334200844b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/13/2019
ms.locfileid: "74049027"
---
# <a name="developing-with-media-services-v3-apis"></a>Media Services v3 API シリーズを使用した開発

開発者は、Media Services の [REST API](https://aka.ms/ams-v3-rest-ref) または REST API と対話できるクライアント ライブラリを使って、カスタム メディア ワークフローを簡単に作成、管理、メンテナンスできます。 [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API は、OpenAPI 仕様 (旧称 Swagger) に基づいています。

この記事では、Media Services v3 を使用して開発を行う際にエンティティと API に適用される規則について説明します。

## <a name="accessing-the-azure-media-services-api"></a>Azure Media Services API へのアクセス

Media Services リソースと Media Services API へのアクセスが承認されるには、まず認証を受ける必要があります。 Media Services では、[Azure Active Directory (Azure AD) ベース](../../active-directory/fundamentals/active-directory-whatis.md)の認証がサポートされています。 2 つの一般的な認証オプションがあります。
 
* **サービス プリンシパル認証**: サービスの認証に使用されます (例: Web アプリ、関数アプリ、ロジック アプリ、API、マイクロ サービス)。 この認証方法がよく使用されるアプリケーションは、デーモン サービス、中間層サービス、またはスケジュールされたジョブを実行するアプリです。 たとえば、Web アプリケーションの場合、サービス プリンシパルで Media Services に接続する中間層が常にあるはずです。
* **ユーザー認証**: Azure Media Services リソースを操作するアプリを使用しているユーザーを認証するために使用されます。 ユーザーは最初に、対話型アプリケーションからユーザー資格情報の入力を求められます。 例として、承認済みユーザーがエンコード ジョブまたはライブ ストリーミングを監視するために使用する管理コンソール アプリがあります。

Media Services API では、REST API 要求を行うユーザーまたはアプリケーションは、Media Services アカウント リソースへのアクセス権を持ち、**共同作成者**または**所有者**のロールを使用することが必要です。 **閲覧者**ロールで API にアクセスすることはできますが、使用できる操作は **Get** または **List** だけです。 詳細については、「[Media Services アカウント用のロールベースのアクセス制御](rbac-overview.md)」を参照してください。

サービス プリンシパルを作成する代わりに、Azure リソースに対するマネージド ID を使い、Azure Resource Manager で Media Services API にアクセスすることを検討してください。 Azure リソースに対するマネージド ID の詳細については、「[Azure リソースのマネージド ID とは](../../active-directory/managed-identities-azure-resources/overview.md)」を参照してください。

### <a name="azure-ad-service-principal"></a>Azure AD のサービス プリンシパル 

Azure AD アプリケーションとサービス プリンシパルを作成する場合、アプリケーションは専用のテナントに置く必要があります。 アプリケーションを作成した後、アプリの**共同作成者**または**所有者**ロールのアクセス権を、Media Services アカウントに付与します。 

Azure AD アプリケーションを作成するためのアクセス許可を自分が持っているかどうかわからない場合は、「[必要なアクセス許可](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)」を参照してください。

次の図の番号は、要求のフローを時系列で表したものです。

![中間層アプリ](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. 中間層アプリが、次のパラメーターが含まれた Azure AD アクセス トークンを要求します。  

   * Azure AD テナント エンドポイント。
   * Media Services リソース URI。
   * REST Media Services のリソース URI。
   * Azure AD アプリケーションの値 (クライアント ID と クライアント シークレット)。
   
   必要な値をすべて取得するには、「[Azure CLI で Azure Media Services API にアクセスする](access-api-cli-how-to.md)」をご覧ください

2. Azure AD アクセス トークンが中間層アプリに送信されます。
4. 中間層アプリが、Azure AD トークンを使用して要求を Azure Media REST API に送信します。
5. Media Services からデータが中間層アプリに返されます。

### <a name="samples"></a>サンプル

Azure AD サービス プリンシパルを使った接続方法を示す次のサンプルを参照してください。

* [REST を使用して接続する](media-rest-apis-with-postman.md)  
* [Java を使用して接続する](configure-connect-java-howto.md)
* [.NET を使用して接続する](configure-connect-dotnet-howto.md)
* [Node.js を使用して接続する](configure-connect-nodejs-howto.md)
* [Python を使用して接続する](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>名前付け規則

Azure Media Services v3 のリソース名 (アセット、ジョブ、変換など) には、Azure Resource Manager の名前付け規則が適用されます。 Azure Resource Manager に従って、リソース名は常に一意となります。 そのため、リソース名には一意識別子の文字列 (GUID など) を使用できます。 

Media Services リソース名には、"<",">"、"%"、"&"、":"、"&#92;"、"?"、"/"、"*"、"+"、"."、一重引用符などの制御文字を使用することができません。 それ以外の文字は使用できます。 リソース名の最大文字数は 260 文字です。 

Azure Resource Manager の名前付けの詳細については、[名前付けの要件](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource)と[名前付け規則](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging)に関するページを参照してください。

### <a name="names-of-filesblobs-within-an-asset"></a>資産内のファイルまたは BLOB の名前

資産内のファイルまたは BLOB の名前は、[BLOB 名の要件](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)と[NTFS 名の要件](https://docs.microsoft.com/windows/win32/fileio/naming-a-file)の両方に従っている必要があります。 これらの要件の理由は、ファイルが BLOB ストレージからローカルの NTFS ディスクにコピーされて処理できるようになることです。

## <a name="long-running-operations"></a>長時間にわたって実行される操作

Azure Media Services の [Swagger ファイル](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json)に `x-ms-long-running-operation` とマークされた操作は長期操作です。 

非同期の Azure 操作を追跡する方法について詳しくは、[非同期操作](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations#monitor-status-of-operation)に関するセクションを参照してください。

Media Services には、次のような長期操作があります。

* [ライブ イベントの作成](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [ライブ イベントの更新](https://docs.microsoft.com/rest/api/media/liveevents/update)
* [ライブ イベントの削除](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [ライブ イベントの開始](https://docs.microsoft.com/rest/api/media/liveevents/start)
* [LiveEvent の停止](https://docs.microsoft.com/rest/api/media/liveevents/stop)

  イベントの停止時に、関連付けられているライブ出力をすべて削除するには `removeOutputsOnStop` パラメーターを使用します。  
* [LiveEvent のリセット](https://docs.microsoft.com/rest/api/media/liveevents/reset)
* [LiveOutput の作成](https://docs.microsoft.com/rest/api/media/liveevents/create)
* [LiveOutput の削除](https://docs.microsoft.com/rest/api/media/liveevents/delete)
* [StreamingEndpoint の作成](https://docs.microsoft.com/rest/api/media/streamingendpoints/create)
* [StreamingEndpoint の更新](https://docs.microsoft.com/rest/api/media/streamingendpoints/update)
* [StreamingEndpoint の削除](https://docs.microsoft.com/rest/api/media/streamingendpoints/delete)
* [StreamingEndpoint の開始](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* [StreamingEndpoint の停止](https://docs.microsoft.com/rest/api/media/streamingendpoints/stop)
* [StreamingEndpoint のスケーリング](https://docs.microsoft.com/rest/api/media/streamingendpoints/scale)

長い操作の送信に成功すると、"202 Accepted" を受け取ります。返された操作 ID を使用して、操作の完了をポーリングする必要があります。

「[非同期 Azure 操作の追跡](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-async-operations)」の記事では、応答で返される値を通じて非同期 Azure 操作の状態を追跡する方法について説明します。

実行時間の長い操作は、特定のライブ イベントまたはそれに関連付けられているライブ出力に対して 1 つだけサポートされます。 実行時間の長い操作が開始したら、それが完了してからでないと、同じ LiveEvent または関連付けられているライブ出力に対して、実行時間の長い操作を続けて開始できません。 複数のライブ出力があるライブ イベントの場合は、あるライブ出力に対して長時間実行されている操作の完了を待ってから、別のライブ出力に対して長時間実行される操作をトリガーする必要があります。 

## <a name="sdks"></a>SDK

> [!NOTE]
> Azure Media Services v3 SDK は、スレッドセーフである保証はありません。 マルチスレッドのアプリケーションを開発するときは、独自のスレッド同期ロジックを追加してクライアントを保護するか、スレッドごとに新しい AzureMediaServicesClient オブジェクトを使用する必要があります。 .NET の HttpClient インスタンスなど、コード内の任意のオブジェクトによってクライアントにもたらされるマルチスレッドの問題にも注意を払う必要があります。

|SDK|リファレンス|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET リファレンス](https://aka.ms/ams-v3-dotnet-ref)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java リファレンス](https://aka.ms/ams-v3-java-ref)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python リファレンス](https://aka.ms/ams-v3-python-ref)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js リファレンス](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go リファレンス](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>関連項目

- [メディア サービス イベントを含む EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services イベントの定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

[Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) は、Media Services について学習したい Windows ユーザーが使用できるツールです。 AMSE は、Media Services で VOD およびライブ コンテンツをアップロード、ダウンロード、エンコード、ストリーミングする Winforms/C# アプリケーションです。 AMSE ツールは、コードを記述しないで Media Services をテストしたいクライアント用です。 AMSE コードは、Media Services による開発を希望するお客様用のリソースとして提供されています。

AMSE はオープン ソース プロジェクトであり、サポートはコミュニティによって提供されます (問題は https://github.com/Azure/Azure-Media-Services-Explorer/issues) に報告できます)。 このプロジェクトでは、[Microsoft オープン ソースの倫理規定](https://opensource.microsoft.com/codeofconduct/)を採用しています。 詳しくは、「[Code of Conduct FAQ (倫理規定についてよくある質問)](https://opensource.microsoft.com/codeofconduct/faq/)」を参照するか、opencode@microsoft.com 宛てに質問またはコメントをお送りください。

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services エンティティのフィルター処理、順序付け、ページング

[Azure Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)に関するページを参照してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの提供、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="see-also"></a>関連項目

[Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>次の手順

* [Java を使用して Media Services に接続する](configure-connect-java-howto.md)
* [.NET を使用して Media Services に接続する](configure-connect-dotnet-howto.md)
* [Node.js を使用して Media Services に接続する](configure-connect-nodejs-howto.md)
* [Python を使用して Media Services に接続する](configure-connect-python-howto.md)
