---
title: Azure AD アプリケーション プロキシでのカスタム ドメイン | Microsoft Docs
description: Azure AD アプリケーション プロキシでカスタム ドメインを構成して管理します。
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6aa42c63809472e1681a820031e48fe4f86fb584
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756473"
---
# <a name="configure-custom-domains-with-azure-ad-application-proxy"></a>Azure AD アプリケーション プロキシでカスタム ドメインを構成する

Azure Active Directory アプリケーション プロキシ経由でアプリケーションを公開する場合は、ユーザーのための外部 URL を作成します。 この URL には、既定のドメインである *yourtenant-msappproxy.net* が与えられます。 たとえば、*Contoso* という名前のテナントの *Expenses* という名前のアプリを公開する場合、外部 URL は *https:\//expenses-contoso.msappproxy.net* です。 *msappproxy.net* の代わりに独自のドメイン名を使用する場合は、アプリケーションのカスタム ドメインを構成できます。 

## <a name="benefits-of-custom-domains"></a>カスタム ドメインの利点

可能な場合は常に、アプリのカスタム ドメインを設定することをお勧めします。 カスタム ドメインを使用するいくつかの理由として、次のものがあります。

- アプリ間のリンクが企業ネットワークの外部でも機能します。 カスタム ドメインがないと、アプリにアプリケーション プロキシの外部のターゲットへのハードコーディングされた内部リンクが含まれており、リンクを外部から解決できない場合、それらのリンクは中断されます。 内部 URL と外部 URL が同じであれば、この問題は回避されます。 カスタム ドメインを使用できない場合は、この問題に対処するための他の方法について「[Azure AD アプリケーション プロキシで公開されているアプリのハードコードされたリンクをリダイレクトする](../application-proxy-link-translation.md)」を参照してください。 
  
- ユーザーはネットワークの内部と外部の両方から同じ URL でアプリにアクセスできるため、ユーザーのエクスペリエンスが簡単になります。 異なる内部 URL と外部 URL を覚えたり、自分の現在の場所を追跡したりする必要はありません。 

- ブランドを制御し、必要な URL を作成することができます。 ユーザーは *msappproxy.net* の代わりに、なじみのある名前を参照または使用できるため、カスタム ドメインはユーザーの信頼を構築するために役立ちます。

- 一部の構成は、カスタム ドメインでのみ機能します。 たとえば、Security Assertion Markup Language (SAML) を使用するアプリのカスタム ドメインが必要です (Active Directory フェデレーション サービス (AD FS) を使用しているが、WS-Federation を使用できない場合など)。 詳細については、[アプリケーション プロキシでの要求に対応するアプリの使用](application-proxy-configure-for-claims-aware-applications.md)に関するページを参照してください。 

内部 URL と外部 URL を一致させることができない場合、カスタム ドメインの使用はそれほど重要ではありませんが、引き続き他の利点を利用できます。 

## <a name="dns-configuration-options"></a>DNS の構成オプション

要件に応じて、DNS の構成を設定するためのいくつかのオプションがあります。

### <a name="same-internal-and-external-url-different-internal-and-external-behavior"></a>同じ内部 URL と外部 URL、異なる内部の動作と外部の動作 

内部ユーザーがアプリケーション プロキシ経由で転送されないようにしたい場合は、*スプリット ブレイン DNS* を設定できます。 分割 DNS のインフラストラクチャでは、名前解決のために内部ホストを内部ドメイン ネーム サーバーに、外部ホストを外部ドメイン ネーム サーバーに転送します。 

![スプリット ブレイン DNS](./media/application-proxy-configure-custom-domain/split-brain-dns.png)

### <a name="different-internal-and-external-urls"></a>異なる内部 URL と外部 URL 

内部 URL と外部 URL が異なる場合は、URL によってユーザー ルーティングが決定されるため、スプリット ブレイン動作を構成する必要はありまません。 この場合は、外部 DNS のみを変更し、外部 URL をアプリケーション プロキシ エンドポイントにルーティングします。 

外部 URL のカスタム ドメインを選択すると、外部 DNS プロバイダーに追加する必要のある CNAME エントリが情報バーに表示されます。 この情報は、アプリの **[アプリケーション プロキシ]** ページに移動することによっていつでも表示できます。

## <a name="set-up-and-use-custom-domains"></a>カスタム ドメインを設定して使用する

カスタム ドメインを使用するようにオンプレミス アプリを構成するには、検証済みの Azure Active Directory カスタム ドメイン、カスタム ドメインの PFX 証明書、および構成するオンプレミス アプリが必要です。 

### <a name="create-and-verify-a-custom-domain"></a>カスタム ドメインを作成して検証する

カスタム ドメインを作成して検証するには:

1. Azure Active Directory で、左側のナビゲーションの **[カスタム ドメイン名]** を選択してから、 **[カスタム ドメインの追加]** を選択します。 
1. カスタム ドメイン名を入力し、 **[ドメインの追加]** を選択します。 
1. ドメイン ページで、ドメインの TXT レコード情報をコピーします。 
1. ドメイン レジストラーに移動し、コピーされた DNS 情報に基づいて、ドメインの新しい TXT レコードを作成します。
1. ドメインを登録した後、Azure Active Directory のそのドメインのページで、 **[検証]** を選択します。 ドメインの状態が **[Verified] (検証済み)** になったら、そのドメインをすべての Azure AD 構成 (アプリケーション プロキシを含む) にわたって使用できます。 

詳細な手順については、「[Azure Active Directory ポータルを使用してカスタム ドメイン名を追加する](../fundamentals/add-custom-domain.md)」を参照してください。

### <a name="configure-an-app-to-use-a-custom-domain"></a>カスタム ドメインを使用するようにアプリを構成する

カスタム ドメインを使用してアプリケーション プロキシ経由でアプリを公開するには:

1. 新しいアプリの場合は、Azure Active Directory で、左側のナビゲーションの **[エンタープライズ アプリケーション]** を選択し、 **[新規アプリケーション]** を選択してから **[オンプレミスのアプリケーション]** を選択します。 
   
   既に **[エンタープライズ アプリケーション]** にあるアプリの場合は、そのアプリを一覧から選択してから、左側のナビゲーションの **[アプリケーション プロキシ]** を選択します。 

1. **[アプリケーション プロキシ]** ページで、 **[内部 URL]** フィールドにアプリの内部 URL を入力します。 
   
1. **[External Url] (外部 URL)** フィールドで、一覧をドロップダウンし、使用するカスタム ドメインを選択します。
   
1. **[保存]** を選択します。
   
   ![カスタム ドメインを選択する](./media/application-proxy-configure-custom-domain/application-proxy.png)
   
1. ドメインに既に証明書が存在する場合は、 **[証明書]** フィールドにその証明書の情報が表示されます。 それ以外の場合は、 **[証明書]** フィールドを選択します。 
   
   ![クリックし、証明書をアップロードします。](./media/application-proxy-configure-custom-domain/certificate.png)
   
1. **[SSL 証明書]** ページで、PFX 証明書ファイルを参照して選択します。 証明書のパスワードを入力し、 **[証明書のアップロード]** を選択します。 証明書の詳細については、「[カスタム ドメインの証明書](#certificates-for-custom-domains)」のセクションを参照してください。
   
   ![証明書のアップロード](./media/application-proxy-configure-custom-domain/ssl-certificate.png)
   
   > [!TIP] 
   > カスタム ドメインの証明書は 1 回だけアップロードする必要があります。 その後、他のアプリのカスタム ドメインを使用すると、アップロードされた証明書が自動的に適用されます。
   
1. 証明書を追加した場合は、 **[アプリケーション プロキシ]** ページで **[保存]** を選択します。 
   
1. **[アプリケーション プロキシ]** ページの情報バーで、DNS ゾーンに追加する必要のある CNAME エントリをメモします。 
   
   ![CNAME DNS エントリを追加する](./media/application-proxy-configure-custom-domain/dns-info.png)
   
1. [Azure portal を使用した DNS レコードとレコード セットの管理](../../dns/dns-operations-recordsets-portal.md)に関するページにある手順に従って、新しい外部 URL を *msappproxy.net* ドメインにリダイレクトする DNS レコードを追加します。
   
1. DNS レコードが正しく構成されていることを確認するには、[nslookup](https://social.technet.microsoft.com/wiki/contents/articles/29184.nslookup-for-beginners.aspx) コマンドを使用して、外部 URL が到達可能であり、かつ *msapproxy.net* ドメインが別名として表示されることを確認します。

これで、アプリケーションはカスタム ドメインを使用するように設定されました。 テストやリリースの前に、このアプリケーションに必ずユーザーを割り当ててください。 

アプリのドメインを変更するには、そのアプリの **[アプリケーション プロキシ]** ページの **[外部 URL]** で、ドロップダウン リストから別のドメインを選択します。 必要に応じて、更新されたドメインの証明書をアップロードし、DNS レコードを更新します。 必要なカスタム ドメインが **[外部 URL]** のドロップダウン リストに表示されない場合は、そのドメインが検証されていない可能性があります。

アプリケーション プロキシの詳細な手順については、[チュートリアル: Azure Active Directory のアプリケーション プロキシを使用してリモート アクセスするためのオンプレミス アプリケーションを追加する](application-proxy-add-on-premises-application.md)を参照してください。

## <a name="certificates-for-custom-domains"></a>カスタム ドメインの証明書

証明書により、カスタム ドメイン用のセキュリティで保護された SSL 接続が作成されます。 

### <a name="certificate-formats"></a>証明書の形式

必要なすべての中間証明書が必ず含まれるようにするには、PFX 証明書を使用する必要があります。 証明書には秘密キーが含まれている必要があります。

証明書の署名方法に制限はありません。 楕円曲線暗号 (ECC)、サブジェクトの別名 (SAN)、およびその他の一般的な証明書の種類がサポートされています。 

ワイルドカード証明書は、そのワイルドカードが外部 URL に一致する限り使用できます。 [ワイルドカード アプリケーション](application-proxy-wildcard.md)にはワイルドカード証明書を使用する必要があります。 証明書を使用してサブドメインにもアクセスする場合は、同じ証明書内にサブジェクトの別名としてサブドメイン ワイルドカードを追加する必要があります。 たとえば、サブジェクトの別名として *\*.apps.adventure-works.com* を追加しない限り、 *\*.adventure-works.com* の証明書は *\*.apps.adventure-works.com* に対して機能しません。 

クライアント デバイスに証明書チェーンがインストールされている場合は、独自の公開キー インフラストラクチャ (PKI) によって発行された証明書を使用できます。 Intune は、これらの証明書をマネージド デバイスにデプロイできます。 マネージド デバイス以外の場合は、これらの証明書を手動でインストールする必要があります。

プライベート ルート CA は使用しないことをお勧めします。 プライベート ルート CA はクライアント コンピューターにプッシュする必要もあり、これによって多くの課題が発生します。 

### <a name="certificate-management"></a>証明書の管理

すべての証明書は、個々のアプリケーションのページを通して管理されます。 **[証明書]** フィールドにアクセスするには、アプリケーションの **[アプリケーション プロキシ]** ページに移動します。

複数のアプリケーションに対して同じ証明書を使用できます。 アップロードされた証明書が別のアプリケーションで使用される場合は、その証明書が自動的に適用されます。 アプリを追加または構成しても、それを再度アップロードするよう求められることはありません。 

証明書の期限が切れると、別の証明書をアップロードするよう指示する警告が表示されます。 証明書が取り消された場合は、ユーザーがそのアプリにアクセスすると、セキュリティの警告が表示されることがあります。 アプリの証明書を更新するには、アプリの **[アプリケーション プロキシ]** ページに移動し、 **[証明書]** を選択して新しい証明書をアップロードします。 古い証明書が他のアプリによって使用されていない場合、その証明書は自動的に削除されます。 

## <a name="next-steps"></a>次の手順
* Azure AD 認証を使用して発行されたアプリに対する[シングル サインオンを有効にする](application-proxy-configure-single-sign-on-with-kcd.md)。
* 発行されたアプリに対する[条件付きアクセスを有効にする](../conditional-access/technical-reference.md#cloud-apps-assignments)。

