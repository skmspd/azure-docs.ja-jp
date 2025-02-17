---
title: セルフサービスによるパスワードのリセットのトラブルシューティング - Azure Active Directory
description: Azure AD のセルフサービスのパスワード リセットのトラブルシューティング
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ecb2086f15159142ea55f96b2405b464c1f23a7
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/23/2019
ms.locfileid: "72786814"
---
# <a name="troubleshoot-self-service-password-reset"></a>セルフサービスのパスワードのリセットのトラブルシューティング

Azure Active Directory (Azure AD) セルフサービスのパスワードのリセット (SSPR) に関して何か問題はありますか? サービスをもう一度正常に動作させるために、以下の情報が役立ちます。

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>ユーザーに表示される可能性のある、セルフサービスのパスワードのリセット エラーのトラブルシューティング

| Error | 詳細 | 技術的な詳細 |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | 申し訳ありません。管理者が組織でのパスワードのリセットを無効にしているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、この機能を有効にするように依頼してください。 詳細については、[Azure AD パスワードを忘れた場合](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions)に関する記事をご覧ください。 | SSPR_0009: パスワードのリセットが管理者によって有効にされていないことが検出されました。 管理者に連絡して、組織のパスワードのリセットを有効にするように依頼してください。 |
| WritebackNotEnabled = 10 |申し訳ありません。管理者が組織に必要なサービスを有効にしていないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、組織の構成を確認するように依頼してください。 この必要なサービスの詳細については、[パスワード ライトバックの構成](howto-sspr-writeback.md)に関する記事をご覧ください。 | SSPR_0010: パスワード ライトバックが有効になっていないことが検出されました。 管理者に連絡して、パスワード ライトバックを有効にするように依頼してください。 |
| SsprNotEnabledInUserPolicy = 11 | 申し訳ありません。管理者が組織のパスワード リセットを構成していないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、パスワードのリセットを構成するように依頼してください。 パスワードのリセット構成の詳細については、[Azure AD のセルフサービスによるパスワードのリセットのクイック スタート](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)に関する記事をご覧ください。 | SSPR_0011: 組織でパスワード リセット ポリシーを定義していません。 管理者に連絡して、パスワード リセット ポリシーを定義するように依頼してください。 |
| UserNotLicensed = 12 | 申し訳ありません。組織で必要なライセンスが不足しているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、ご自分のライセンスの割り当てを確認するように依頼してください。 ライセンスの詳細については、「[Azure AD のセルフ サービスによるパスワード リセットのライセンス要件](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing)」をご覧ください。 | SSPR_0012: 組織には、パスワードのリセットを実行するために必要なライセンスがありません。 管理者に連絡して、ライセンスの割り当てを見直すように依頼してください。 |
| UserNotMemberOfScopedAccessGroup = 13 | 申し訳ありません。管理者が、お使いのアカウントでパスワードのリセットを使用するように構成していないため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、お使いのアカウントでパスワード リセットを構成するように依頼してください。 パスワードのリセットのアカウント構成の詳細については、[ユーザーのパスワード リセットのロールアウト](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices)に関する記事をご覧ください。 | SSPR_0013:パスワード リセットが有効になっているグループのメンバーではありません。 管理者に連絡して、このグループに追加してもらうよう依頼してください。 |
| UserNotProperlyConfigured = 14 | 申し訳ありません。お使いのアカウントでは必要な情報が不足しているため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、パスワードをリセットしてもらうよう依頼してください。 ご自分のアカウントにもう一度アクセスできるようになったら、必要な情報を登録していただく必要があります。 情報を登録するには、「[セルフサービスによるパスワードのリセットを登録する](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register)」に記載されている手順に従ってください。 | SSPR_0014: パスワードをリセットするための追加のセキュリティ情報が必要です。 続行するには、管理者に連絡して、パスワードをリセットしてもらうよう依頼してください。 ご自分のアカウントにアクセスできるようになったら、 https://aka.ms/ssprsetup で追加のセキュリティ情報を登録することができます。 管理者は、[パスワードのリセットの認証データの設定と読み取り](howto-sspr-authenticationdata.md)に関する記事に記載された手順に従い、ご利用のアカウントに追加のセキュリティ情報を追加できます。 |
| OnPremisesAdminActionRequired = 29 | 申し訳ありません。組織のパスワード リセット構成に問題があるため、現時点ではパスワードをリセットできません。 この状況を解決するためにご自分で行える処理はありません。 管理者に連絡して、調査するように依頼してください。 潜在的な問題の詳細については、「[パスワード ライトバックのトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback)」をご覧ください。 | SSPR_0029: オンプレミス構成でのエラーのため、パスワードをリセットすることができません。 管理者に連絡して、調査するように依頼してください。 |
| OnPremisesConnectivityError = 30 | 申し訳ありません。組織への接続に問題があるため、現時点ではパスワードをリセットできません。 今すぐ実行すべきアクションはありませんが、あとでもう一度試してみて、問題が解決する場合があります。 問題が解決しない場合は、管理者に連絡して、調査するように依頼してください。 接続の問題の詳細については、[パスワード ライトバックの接続のトラブルシューティング](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity)に関する記事をご覧ください。 | SSPR_0030: オンプレミス環境との接続状況が悪いため、パスワードをリセットできません。 管理者に連絡して、調査するように依頼してください。|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Azure Portal でのパスワードのリセット構成のトラブルシューティング

| Error | 解決策 |
| --- | --- |
| Azure Portal で Azure AD の下に **[パスワードのリセット]** セクションが表示されません。 | これは、この操作を実行する管理者に割り当てられる Azure AD ライセンスを持っていない場合に発生します。 <br> <br> 該当の管理者アカウントにライセンスを割り当てます。 [ライセンスの割り当て、確認、問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事に記載された手順に従ってください。|
| 特定の構成オプションが表示されません。 | UI の多くの要素は、必要になるまで表示されません。 表示する必要のあるすべてのオプションを有効にしてください。 |
| **[オンプレミスの統合]** タブが表示されません。 | このオプションは、Azure AD Connect をダウンロードし、パスワード ライトバックを構成した場合にのみ表示されます。 詳細については、[簡単設定を使用した Azure AD Connect の開始](../hybrid/how-to-connect-install-express.md)に関する記事をご覧ください。 |

## <a name="troubleshoot-password-reset-reporting"></a>パスワード リセット レポートのトラブルシューティング

| Error | 解決策 |
| --- | --- |
| **[Self-Service Password Management]\(セルフサービスのパスワード管理\)** 監査イベント カテゴリに、パスワード管理アクティビティの種類が表示されません。 | これは、この操作を実行する管理者に割り当てられる Azure AD ライセンスを持っていない場合に発生します。 <br> <br> 該当の管理者アカウントにライセンスを割り当てることで、この問題を解決できます。 [ライセンスの割り当て、確認、問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事に記載された手順に従ってください。 |
| ユーザー登録が何回も表示されます。 | 現時点では、ユーザーが登録を行うと、登録される各データを個別のイベントとして記録しています。 <br> <br> このデータを集計してデータの表示方法をより柔軟なものにするには、レポートをダウンロードして Excel のピボット テーブルでデータを開きます。

## <a name="troubleshoot-the-password-reset-registration-portal"></a>パスワード リセット登録ポータルのトラブルシューティング

| Error | 解決策 |
| --- | --- |
| このディレクトリでは、パスワードのリセットが有効になっていません。 **管理者が、この機能を使用できるようにしていません。** | **[セルフ サービスによるパスワードのリセットが有効]** フラグを **[選択]** または **[すべて]** に切り替えて、 **[保存]** を選択します。 |
| ユーザーに Azure AD のライセンスが割り当てられていません。 **管理者が、この機能を使用できるようにしていません。** | これは、この操作を実行する管理者に割り当てられる Azure AD ライセンスを持っていない場合に発生します。 <br> <br> 該当の管理者アカウントにライセンスを割り当てることで、この問題を解決できます。 [ライセンスの割り当て、確認、問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事に記載された手順に従ってください。|
| 要求の処理中に発生したエラーがあります。 | このエラーはさまざまな問題が原因で発生することがありますが、一般的にサービスの停止や構成の問題が原因で発生します。 このエラーが表示されてビジネスに影響がある場合は、Microsoft サポートにお問い合わせください。 |

## <a name="troubleshoot-the-password-reset-portal"></a>パスワード リセット ポータルのトラブルシューティング

| Error | 解決策 |
| --- | --- |
| このディレクトリでは、パスワードのリセットが有効になっていません。 | **[セルフ サービスによるパスワードのリセットが有効]** フラグを **[選択]** または **[すべて]** に切り替えて、 **[保存]** を選択します。 |
| ユーザーに Azure AD のライセンスが割り当てられていません。 | これは、この操作を実行する管理者に割り当てられる Azure AD ライセンスを持っていない場合に発生します。 <br> <br> 該当の管理者アカウントにライセンスを割り当てれば、この問題を解決できます。 [ライセンスの割り当て、確認、問題の解決](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses)に関する記事に記載された手順に従ってください。 |
| ディレクトリでパスワードのリセットが有効になっていますが、ユーザーの認証情報が見つからない、または認証情報の形式が正しくありません。 | 続行する前に、ユーザーがディレクトリ内のファイルに正しい形式の連絡先データを指定していることを確認します。 詳しくは、[Azure AD のセルフサービスによるパスワードのリセットで使われるデータ](howto-sspr-authenticationdata.md)に関するページをご覧ください。 |
| ディレクトリでパスワードのリセットが有効になっていますが、ポリシーが 2 つの検証方法を要求するように設定されている場合に、ユーザーがファイルに 1 つの連絡先データしか指定していません。 | 続行する前に、ユーザーが少なくとも 2 つの連絡方法を正しく構成していることを確認します。 たとえば、携帯電話番号*と*会社の電話番号などです。 |
| ディレクトリでパスワードのリセットが有効になっており、ユーザーが正しく構成されていますが、ユーザーに連絡できません。 | 一時的なサービス エラーが発生したか、連絡先データが正しくない可能性があるため、正しく検出できませんでした。 <br> <br> 10 秒待つと、[もう一度お試しください] と [管理者に連絡してください] のリンクが表示されます。 [もう一度お試しください] を選択した場合、呼び出しをもう一度試します。 ユーザーが [管理者に連絡してください] を選択すると、そのユーザーのアカウントに対してパスワードのリセットを実行するよう管理者に要求するフォーム メールが送信されます。 |
| ユーザーがパスワードのリセットの SMS または電話を受け取っていません。 | ディレクトリ内の電話番号の形式が正しくない可能性があります。 電話番号の形式が “+ccc xxxyyyzzzzXeeee” となっていることをご確認ください。 <br> <br> ディレクトリ内の電話番号を指定した場合でも、パスワードのリセットは内線番号をサポートしていません。 内線番号は呼び出しがディスパッチされる前に削除されます。 内線番号のない電話番号を使用するか、構内交換機 (PBX) で電話番号と内線番号を統合してください。 |
| ユーザーがパスワードのリセットのメールを受け取っていません。 | この問題の主な原因は、メッセージがスパム フィルターによって拒否されたことである可能性があります。 スパム、迷惑メール、削除済みアイテムのフォルダーをご確認ください。 <br> <br> そのメッセージに対して正しいメール アカウントを見ているかどうかもご確認ください。 |
| パスワードのリセット ポリシーを設定しましたが、管理者アカウントでパスワードのリセットを使用してもポリシーが適用されません。 | マイクロソフトは、最高レベルのセキュリティを維持するために、管理者パスワード リセット ポリシーを管理、制御しています。 |
| ユーザーが 1 日に何回もパスワードの　リセットを試みることはできません。 | ユーザーが短時間に何回もパスワードをリセットできないように、自動調整メカニズムが実装されています。 次の場合に調整が行われます。 <br><ul><li>ユーザーが 1 時間に 5 回、電話番号の検証を試みる場合。</li><li>ユーザーが 1 時間に 5 回、セキュリティの質問ゲートの使用を試みる場合。</li><li>ユーザーが 1 時間に 5 回、同じユーザー アカウントのパスワードのリセットを試みる場合。</li></ul>この問題を解決するには、前回の試行から 24 時間待つようユーザーに指示します。 そのあと、ユーザーはパスワードをリセットできるようになります。 |
| ユーザーが自分の電話番号を検証するときにエラーが表示されます。 | このエラーは、入力した電話番号がファイルの電話番号と一致しない場合に発生します。 電話ベースの方法でパスワードのリセットを試みる場合は、ユーザーが国番号と市外局番を含む完全な電話番号を入力していることを確認します。 |
| 要求の処理中に発生したエラーがあります。 | このエラーはさまざまな問題が原因で発生することがありますが、一般的にサービスの停止や構成の問題が原因で発生します。 このエラーが表示されてビジネスに影響がある場合は、Microsoft サポートにお問い合わせください。 |
| オンプレミスのポリシーの違反 | パスワードは、オンプレミス Active Directory のパスワード ポリシーを満たしていません。 |
| パスワードは、あいまいポリシーに準拠していません | 使用されたパスワードは [禁止パスワード リスト](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated)に表示され、使用できない可能性があります。 |

## <a name="troubleshoot-password-writeback"></a>パスワード ライトバックのトラブルシューティング

| Error | 解決策 |
| --- | --- |
| オンプレミスでパスワードのリセット サービスが開始しません。 Azure AD Connect マシンのアプリケーション イベント ログに、エラー 6800 が表示されます。 <br> <br> フェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーは、オンボード後に自分のパスワードをリセットできません。 | パスワード ライトバックを有効にすると、同期エンジンはライトバック ライブラリを呼び出し、クラウド オンボード サービスと通信して構成 (オンボード) を実行します。 オンボード中またはパスワード ライトバックの Windows Communication Foundation (WCF) エンドポイントの起動中に発生したエラーは、Azure AD Connect マシンのイベント ログのエラーになります。 <br> <br> Azure AD Sync (ADSync) サービスの再起動時にライトバックが構成された場合は、WCF エンドポイントが起動します。 ただし、エンドポイントの起動に失敗した場合は、イベント 6800 をログに記録して同期サービスを起動します。 このイベントが存在するということは、パスワード ライトバックのエンドポイントが起動しなかったことを意味します。 このイベント 6800 のイベント ログ詳細では、PasswordResetService コンポーネントで生成されたイベント ログ エントリとともに、エンドポイントが起動できなかった理由が示されます。 パスワード ライトバックがまだ機能していない場合は、これらのイベント ログのエラーを再確認して、Azure AD Connect の再起動を試してください。 問題が解決しない場合は、パスワード ライトバックを無効にしてから再び有効にしてみてください。
| パスワード ライトバックを有効にした状態でユーザーがアカウントのロック解除またはパスワードのリセットを試みると操作に失敗します。 <br> <br> また、Azure AD Connect のイベント ログを見ると、ロック解除操作の後に "Synchronization Engine returned an error hr=800700CE, message=The filename or extension is too long (同期エンジンから hr=800700CE エラーと、ファイル名または拡張子が長すぎるというメッセージが返されました)" というイベントが記録されています。 | Azure AD Connect の Active Directory アカウントを探し、そのパスワードを 256 文字以内に収めてリセットします。 次に、 **[開始]** メニューから **[同期サービス]** を開きます。 **[コネクタ]** を表示し、 **[Active Directory Connector]\(Active Directory コネクタ\)** を探します。 これを選択してから、 **[プロパティ]** を選択します。 **[資格情報]** ページを表示して、新しいパスワードを入力します。 **[OK]** を選択してページを閉じます。 |
| Azure AD Connect インストール プロセスの最後の手順で、パスワード ライトバックを構成できなかったことを示すエラーが表示されます。 <br> <br> Azure AD Connect アプリケーションのイベント ログには、エラー 32009 とテキスト “Error getting auth token” (認証トークンの取得エラー) が記録されています。 | このエラーは、次の 2 つの場合に発生します。 <br><ul><li>Azure AD Connect インストール プロセスの開始時に指定されたグローバル管理者アカウントに不正なパスワードを指定しています。</li><li>Azure AD Connect インストール プロセスの開始時に指定されたグローバル管理者アカウントにフェデレーション ユーザーを使用しようとしています。</li></ul> この問題を解決するには、このインストール プロセスの開始時に指定したグローバル管理者用のフェデレーション アカウントを使用していないことを確認します。 また、指定したパスワードが正しいことも確認します。 |
| Azure AD Connect マシンのイベント ログに、PasswordResetService の実行によってスローされたエラー 32002 が記録されています。 <br> <br> エラーの内容は次のとおりです。“Error Connecting to ServiceBus. The token provider was unable to provide a security token” (ServiceBus への接続エラー。トークン プロバイダーがセキュリティ トークンを提供できませんでした) | ご利用のオンプレミス環境では、クラウドの Azure Service Bus エンドポイントに接続できません。 このエラーは、通常、特定のポートまたは Web のアドレスへの発信接続をブロックするファイアウォール規則が原因で発生します。 詳細については、[接続の前提条件](../hybrid/how-to-connect-install-prerequisites.md)に関する記事をご覧ください。 これらのルールを更新したあと、Azure AD Connect マシンを再起動してパスワード ライトバックの動作を再開する必要があります。 |
| フェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーは、しばらく操作した後に自分のパスワードをリセットできません。 | Azure AD Connect を再起動したときに、まれにパスワード ライトバック サービスの再起動に失敗することがあります。 このような場合は、まずパスワード ライトバックがオンプレミス環境で有効と表示されているかどうかを確認します。 Azure AD Connect ウィザードまたは PowerShell を使用して確認できます ("方法" に関する前のセクションをご覧ください)。 この機能が有効と表示されている場合は、UI または PowerShell を使用して、この機能をもう一度有効または無効にしてみます。 これでうまくいかない場合は、Azure AD Connect を完全にアンインストールして再インストールしてください。 |
| 自分のパスワードのリセットを試みるフェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーには、パスワード送信後にエラーが表示されます。 このエラーは、サービスに問題があったことを示しています。 <br ><br> この問題に加えて、パスワードのリセット操作中に、管理エージェントがオンプレミスのイベント ログでアクセスを拒否されたというエラーが表示されることがあります。 | イベント ログにこのようなエラーが表示された場合は、構成時にウィザードで指定した Active Directory 管理エージェント (ADMA) アカウントに、パスワード ライトバックのために必要なアクセス許可があることを確認します。 <br> <br> このアクセス許可が付与された後、ドメイン コントローラー (DC) の `sdprop` バックグラウンド タスクを介して、アクセス許可が適用されるのに最大 1 時間かかることがあります。 <br> <br> パスワードのリセットが機能するには、パスワードがリセットされるユーザー オブジェクトのセキュリティ記述子に権限を設定する必要があります。 このアクセス許可がユーザー オブジェクトに表示されるまでは、引き続きアクセス拒否メッセージが表示されてパスワードのリセットが失敗します。 |
| 自分のパスワードのリセットを試みるフェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーには、パスワード送信後にエラーが表示されます。 このエラーは、サービスに問題があったことを示しています。 <br> <br> この問題に加えて、パスワードのリセット操作中に、Azure AD Connect サービスのイベント ログに “Object could not be found” (オブジェクトが見つかりませんでした) というエラーが表示されることがあります。 | このエラーは通常、Azure AD コネクタ スペース内またはリンクされたメタバース (MV) 内のユーザー オブジェクトか、Azure AD コネクタ スペース オブジェクトのいずれかを、同期エンジンが検索できないことを示しています。 <br> <br> この問題をトラブルシューティングするには、Azure AD Connect の現在のインスタンスを介してオンプレミスから Azure AD にユーザーが実際に同期されていることを確認し、コネクタ スペースと MV 内のオブジェクトの状態を検査します。 Active Directory 証明書サービス (AD CS) オブジェクトが、"Microsoft.InfromADUserAccountEnabled.xxx" ルールによって MV オブジェクトに接続されていることを確認します。|
| 自分のパスワードのリセットを試みるフェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーには、パスワード送信後にエラーが表示されます。 このエラーは、サービスに問題があったことを示しています。 <br> <br> この問題に加えて、パスワード リセットの操作中に、Azure AD Connect サービスのイベント ログに “Multiple matches found” (複数の一致が見つかりました) というエラーが表示されることがあります。 | これは、MV オブジェクトが "Microsoft.InfromADUserAccountEnabled.xxx" によって複数の AD CS オブジェクトに接続されていることを同期エンジンが検出したことを示しています。 これは、ユーザーが複数のフォレストに有効なアカウントを持っていることを意味します。 このシナリオでは、パスワード ライトバックがサポートされません。 |
| パスワード操作は構成エラーにより失敗しました アプリケーション イベント ログには、Azure AD Connect エラー 6329 と、テキスト "0x8023061f (この管理エージェントではパスワード同期が無効になっているため操作に失敗しました)" が記録されています。 | このエラーは、パスワード ライトバック機能を有効にした後、新しい Active Directory フォレストを追加する (または既存のフォレストを削除して再度追加する) ために、 Azure AD Connect の構成が変更された場合に発生します。 このような最近追加されたフォレスト内のユーザーのパスワード操作は失敗します。 問題を解決するには、フォレスト構成の変更が完了した後に、パスワード ライトバック機能を無効にしてから再び有効にします。 |

## <a name="password-writeback-event-log-error-codes"></a>パスワード ライトバックのイベント ログのエラー コード

パスワード ライトバックに関する問題をトラブルシューティングする場合のベスト プラクティスは、Azure AD Connect マシンでそのアプリケーション イベント ログを検査することです。 このイベント ログには、パスワード ライトバックの 2 つの対象ソースのイベントが格納されます。 PasswordResetService ソースは、パスワード ライトバックの運用に関連する操作と問題について説明します。 ADSync ソースは、Active Directory 環境でのパスワード設定に関連する操作と問題について説明します。

### <a name="if-the-source-of-the-event-is-adsync"></a>イベントのソースが ADSync の場合

| コード | 名前またはメッセージ | 説明 |
| --- | --- | --- |
| 6329 | BAIL: MMS(4924) 0x80230619: "A restriction prevents the password from being changed to the current one specified." (制限によりパスワードを現在指定されているパスワードに変更することができません) | このイベントは、パスワード ライトバック サービスが、パスワードの有効期間、履歴、複雑さ、またはフィルタリングに関するドメインの要件を満たしていないローカル ディレクトリにパスワードを設定しようとすると発生します。 <br> <br> パスワードの最小有効期間が残っていて、最近その期間内にパスワードを変更した場合は、そのドメインで指定された期限に達するまで、もう一度パスワードを変更することはできません。 テストのために、最小有効期間は 0 に設定する必要があります。 <br> <br> パスワードの履歴の要件が有効になっている場合は、最後の *N* 回で使用されていないパスワードを選択する必要があります。*N* はパスワードの履歴で設定します。 最後の *N* 回で使用されているパスワードを選択した場合は、エラーが表示されます。 テストのために、パスワードの履歴は 0 に設定する必要があります。 <br> <br> パスワードの複雑さの要件を指定する場合は、ユーザーがパスワードを変更またはリセットしようとすると、すべての要件が適用されます。 <br> <br> パスワード フィルターが有効になっている場合に、ユーザーがフィルター条件を満たしていないパスワードを選択すると、リセットまたは変更の操作に失敗します。 |
| 6329 | MMS(3040): admaexport.cpp(2837): サーバーに LDAP のパスワード ポリシー コントロールが含まれていません。 | この問題は、DC で LDAP_SERVER_POLICY_HINTS_OID コントロール (1.2.840.113556.1.4.2066) が有効になっていない場合に発生します。 パスワード ライトバック機能を使用するのには、コントロールを有効にする必要があります。 これを行うには、DC が Windows Server 2008R2 以降にインストールされている必要があります。 |
| HR 8023042 | 同期エンジンから hr=80230402 エラーと、"同じアンカーに重複するエントリがあるためオブジェクトの取得に失敗しました" というメッセージが返されました。 | このエラーは、複数のドメインで同じユーザー ID を有効にした場合に発生します。 たとえば、アカウントとリソース フォレストを同期したときに、各フォレスト内に同じユーザー ID が存在し、どちらも有効な場合です。 <br> <br> また、一意ではないアンカー属性 (エイリアスや UPN) を使用し、2 人のユーザーがその同じアンカー属性を共有している場合にも、このエラーが発生します。 <br> <br> この問題を解決するには、ドメイン内に重複するユーザーがいないようにして、各ユーザーに一意のアンカー属性を使用します。 |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>イベントのソースが PasswordResetService の場合

| コード | 名前またはメッセージ | 説明 |
| --- | --- | --- |
| 31001 | PasswordResetStart | このイベントは、オンプレミスのサービスが、クラウドから送信されたフェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーへのパスワードのリセット要求を検出したことを示します。 このイベントは、すべてのパスワード リセットのライトバック操作における最初のイベントです。 |
| 31002 | PasswordResetSuccess | このイベントは、パスワードのリセット操作中に、ユーザーが新しいパスワードを選択したことを示します。 このパスワードが企業のパスワード要件を満たしていると判断されました。 パスワードがローカルの Active Directory 環境に正常にライトバックされました。 |
| 31003 | PasswordResetFail | このイベントは、ユーザーがパスワードを選択し、そのパスワードが正しくオンプレミス環境に届いたことを示します。 しかし、ローカルの Active Directory 環境でパスワードの設定を試みたときに、エラーが発生しました。 このエラーは次のことが原因で発生する場合があります。 <br><ul><li>ユーザーのパスワードは、有効期間、履歴、複雑さ、ドメインのフィルター要件を満たしていません。 この問題を解決するには、新しいパスワードを作成します。</li><li>ADMA サービス アカウントが、対象のユーザー アカウントに新しいパスワードを設定するための適切なアクセス許可を持っていません。</li><li>このユーザーのアカウントは、ドメイン管理者やエンタープライズ管理者グループなどの保護グループ内にあるため、パスワードの設定操作が許可されていません。</li></ul>|
| 31004 | OnboardingEventStart | このイベントは、Azure AD Connect でパスワード ライトバックが有効になっており、組織のパスワード ライトバック Web サービスへのオンボードが開始された場合に発生します。 |
| 31005 | OnboardingEventSuccess | このイベントは、オンボード プロセスが成功し、パスワード ライトバック機能を使用する準備が整ったことを示します。 |
| 31006 | ChangePasswordStart | このイベントは、オンプレミスのサービスが、クラウドから送信されたフェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーへのパスワード変更要求を検出したことを示します。 このイベントは、すべてのパスワード変更のライトバック操作における最初のイベントです。 |
| 31007 | ChangePasswordSuccess | このイベントは、ユーザーがパスワードの変更操作中に新しいパスワードを選択したこと、このパスワードが企業のパスワード要件を満たしていると判断されたこと、およびローカルの Active Directory 環境に正常にライトバックされたことを示します。 |
| 31008 | ChangePasswordFail | このイベントは、ユーザーがパスワードを選択し、そのパスワードがオンプレミス環境に正常に届いたものの、ローカルの Active Directory 環境でパスワードを設定しようとしたところエラーが発生したことを示します。 このエラーは次のことが原因で発生する場合があります。 <br><ul><li>ユーザーのパスワードは、有効期間、履歴、複雑さ、ドメインのフィルター要件を満たしていません。 この問題を解決するには、新しいパスワードを作成します。</li><li>ADMA サービス アカウントが、対象のユーザー アカウントに新しいパスワードを設定するための適切なアクセス許可を持っていません。</li><li>ユーザーのアカウントは、ドメイン管理者やエンタープライズ管理者などの保護グループ内にあるため、パスワードの設定操作が許可されていません。</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | オンプレミスのサービスが、ユーザーに代わって管理者から送信された、フェデレーション、パススルー認証、またはパスワード ハッシュ同期されたユーザーへのパスワードのリセット要求を検出しました。 このイベントは、管理者が開始したパスワードのリセットのライトバック操作における最初のイベントです。 |
| 31010 | ResetUserPasswordByAdminSuccess | 管理者が開始したパスワードのリセット操作中に、管理者が新しいパスワードを選択しました。 このパスワードが企業のパスワード要件を満たしていると判断されました。 パスワードがローカルの Active Directory 環境に正常にライトバックされました。 |
| 31011 | ResetUserPasswordByAdminFail | 管理者がユーザーの代わりにパスワードを選択しました。 パスワードが正常にオンプレミス環境に到着しました。 しかし、ローカルの Active Directory 環境でパスワードの設定を試みたときに、エラーが発生しました。 このエラーは次のことが原因で発生する場合があります。 <br><ul><li>ユーザーのパスワードは、有効期間、履歴、複雑さ、ドメインのフィルター要件を満たしていません。 この問題を解決するには、新しいパスワードを試します。</li><li>ADMA サービス アカウントが、対象のユーザー アカウントに新しいパスワードを設定するための適切なアクセス許可を持っていません。</li><li>ユーザーのアカウントは、ドメイン管理者やエンタープライズ管理者などの保護グループ内にあるため、パスワードの設定操作が許可されていません。</li></ul>  |
| 31012 | OffboardingEventStart | このイベントは、Azure AD Connect でパスワード ライトバックが無効になっている場合に発生し、組織がパスワード ライトバック Web サービスのオフボードを開始したことを示します。 |
| 31013| OffboardingEventSuccess| このイベントは、オフボード プロセスに成功し、パスワード ライトバック機能が正常に無効になったことを示します。 |
| 31014| OffboardingEventFail| このイベントは、オフボード プロセスに失敗したことを示します。 これは、構成時に指定したクラウドまたはオンプレミスの管理者アカウント上で起こったアクセス許可のエラーが原因である可能性があります。 また、このエラーは、パスワード ライトバックを無効にしているときにフェデレーション クラウド グローバル管理者の使用を試みた場合にも、発生することがあります。 この問題を解決するには、管理者のアクセス許可を確認し、パスワード ライトバック機能を構成する場合にフェデレーション アカウントを使用していないことを確認します。|
| 31015| WriteBackServiceStarted| このイベントは、パスワード ライトバック サービスが正常に開始したことを示します。 クラウドからのパスワード管理要求を受け入れる準備ができています。|
| 31016| WriteBackServiceStopped| このイベントは、パスワード ライトバック サービスが停止したことを示します。 クラウドからのパスワード管理要求が正常に受け入れられません。|
| 31017| AuthTokenSuccess| このイベントは、オフボードまたはオンボード プロセスを開始するために、Azure AD Connect のセットアップ時に指定されたグローバル管理者の承認トークンを正常に取得したことを示します。|
| 31018| KeyPairCreationSuccess| このイベントは、パスワード暗号化キーが正常に作成されたことを示します。 このキーは、クラウドからのパスワードを暗号化してご利用のオンプレミス環境に送信するために使用されます。|
| 32000| UnknownError| このイベントは、パスワード管理操作中に発生した原因不明のエラーを示します。 詳細については、イベントの例外の説明をご覧ください。 問題が発生した場合は、パスワード ライトバックを無効にしてから再び有効にしてみてください。 これで問題が解決しない場合は、イベント ログのコピーと、内部の特定の追跡 ID をサポート エンジニアに送信します。|
| 32001| ServiceError| このイベントは、クラウド パスワード リセット サービスへの接続中にエラーが発生したことを示します。 このエラーは通常、オンプレミスのサービスがパスワード リセット Web サービスに接続できない場合に発生します。|
| 32002| ServiceBusError| このイベントは、テナントの Service Bus インスタンスへの接続中にエラーが発生したことを示します。 これは、オンプレミス環境で発信接続をブロックしている場合に発生する可能性があります。 ファイアウォールで TCP 443 経由の https://ssprdedicatedsbprodncu.servicebus.windows.net への接続が許可されていることを確認してから、やり直してください。 問題が解決しない場合は、パスワード ライトバックを無効にしてから再び有効にしてみてください。|
| 32003| InPutValidationError| このイベントは、Web サービス API に渡された入力が無効だったことを示します。 操作をやり直してください。|
| 32004| DecryptionError| このイベントは、クラウドから受信したパスワードの解読中にエラーが発生したことを示します。 これは、クラウド サービスとオンプレミス環境との間で暗号化の解除キーが一致しないことが原因である可能性があります。 この問題を解決するには、オンプレミス環境でパスワード ライトバックを無効にしてから再び有効にします。|
| 32005| ConfigurationError| オンボード中に、オンプレミスの環境内の構成ファイルにテナント固有の情報を保存します。 このイベントは、このファイルの保存中にエラーが発生した、またはサービスの開始時にファイルの読み取りエラーが発生したことを示します。 この問題を解決するには、この構成ファイルを強制的に書き換えるためにパスワード ライトバックを無効にしてから再び有効にしてみてください。|
| 32007| OnBoardingConfigUpdateError| オンボード中に、クラウドからオンプレミスのパスワードのリセット サービスにデータを送信します。 そのデータは、同期サービスに送信される前にメモリ内のファイルに書き込まれ、ディスク上に安全に保存されます。 このイベントは、メモリ内でのそのデータの書き込みや更新に問題があることを示します。 この問題を解決するには、この構成ファイルを強制的に書き換えるためにパスワード ライトバックを無効にしてから再び有効にしてみてください。|
| 32008| ValidationError| このイベントは、パスワード リセット Web サービスから無効な応答を受け取ったことを示します。 この問題を解決するには、パスワード ライトバックを無効にしてから再び有効にしてみてください。|
| 32009| AuthTokenError| このイベントは、Azure AD Connect のセットアップ時に指定されたグローバル管理者アカウントの承認トークンを取得できなかったことを示します。 このエラーは、グローバル管理者アカウントに指定された無効なユーザー名またはパスワードが原因である可能性があります。 また、指定されたグローバル管理者アカウントがフェデレーションされているために発生している可能性もあります。 この問題を解決するには、適切なユーザー名とパスワードを使用して構成を再実行し、管理者が管理 (クラウドのみまたはパスワード同期された) アカウントになっていることを確認します。|
| 32010| CryptoError| このイベントは、パスワード暗号化キーの生成中、またはクラウド サービスから受信するパスワードの暗号化の解除中にエラーが発生したことを示します。 このエラーは、ご利用の環境に問題がある可能性を示しています。 この問題の解決方法の詳細については、イベント ログの詳細を確認します。 また、パスワード ライトバック サービスを無効にしてから再び有効にすると解決できる場合もあります。|
| 32011| OnBoardingServiceError| このイベントは、オンプレミスのサービスが、オンボード プロセスを開始しようとしてパスワード リセット Web サービスと正しく通信できなかったことを示します。 これは、ファイアウォール ルールの結果として、またはご利用のテナントの認証トークンの取得中に問題があった場合に、発生することがあります。 この問題を解決するには、TCP 443 と TCP 9350-9354 経由の発信接続、または https://ssprdedicatedsbprodncu.servicebus.windows.net への接続がブロックされていないことを確認します。 また、オンボードに使用している Azure AD 管理者アカウントがフェデレーションされていないことも確認します。|
| 32013| OffBoardingError| このイベントは、オンプレミスのサービスが、オフボード プロセスを開始しようとしてパスワード リセット Web サービスと正しく通信できなかったことを示します。 これは、ファイアウォール ルールの結果として、またはご利用のテナントの認証トークンの取得中に問題があった場合に、発生することがあります。 この問題を解決するには、TCP 443 経由の発信接続、または https://ssprdedicatedsbprodncu.servicebus.windows.net への接続がブロックされていないことと、オフボードに使用している Azure Active Directory 管理者アカウントがフェデレーションされていないことを確認します。|
| 32014| ServiceBusWarning| このイベントは、テナントの Service Bus インスタンスへの接続を再試行する必要があったことを示します。 通常の状況では、これが問題になることはありませんが、このイベントが何度も表示される場合、特に、待機時間が長い接続や低帯域幅の接続の場合は、Service Bus へのネットワーク接続の確認を検討してください。|
| 32015| ReportServiceHealthError| パスワード ライトバック サービスの正常性を監視するには、パスワード リセット Web サービスに 5 分ごとにハートビート データを送信します。 このイベントは、この正常性情報をクラウド Web サービスに送信するときにエラーが発生したことを示します。 この正常性情報は、クラウド内のサービスの状態情報を提供できるようにするため、オブジェクトを特定できる情報 (OII) または個人を特定できる情報 (PII) のデータを含まない、純粋なハートビートと基本サービスの統計情報になっています。|
| 33001| ADUnKnownError| このイベントは、Active Directory から不明なエラーが返されたことを示します。 Azure AD Connect サーバーのイベント ログで、ADSync ソースからのイベントの詳細を確認します。|
| 33002| ADUserNotFoundError| このイベントは、パスワードをリセットまたは変更しようとするユーザーがオンプレミスのディレクトリに見つからなかったことを示します。 このエラーは、ユーザーがクラウドではなくオンプレミスで削除された場合に発生する可能性があります。 また、このエラーは同期に問題がある場合にも発生することがあります。同期ログと、最近のいくつかの同期実行に関する詳細情報を確認します。|
| 33003| ADMutliMatchError| パスワードのリセットまたは変更要求がクラウドから送信されると、Azure AD Connect のセットアップ プロセス中に指定されたクラウドのアンカーを使用して、その要求をオンプレミスの環境内のユーザーにリンクする方法を決定します。 このイベントは、オンプレミスのディレクトリ内に同じクラウドのアンカー属性を持つユーザーが 2 人見つかったことを示します。 同期ログと、最近のいくつかの同期実行に関する詳細情報を確認します。|
| 33004| ADPermissionsError| このイベントは、Active Directory 管理エージェント (ADMA) のサービス アカウントが新しいパスワードを設定する対象のアカウントで適切なアクセス許可を持っていないことを示します。 ユーザーのフォレストの ADMA アカウントに、フォレスト内のすべてのオブジェクトに対するパスワードのリセットと変更のアクセス許可があることを確認します。 アクセス許可を設定する方法の詳細については、「Step 4: Set up the appropriate Active Directory permissions (手順 4: Active Directory の適切なアクセス許可を設定する)」をご覧ください。|
| 33005| ADUserAccountDisabled| このイベントは、オンプレミスで無効になっていたアカウントのパスワードをリセットまたは変更しようとしたことを示します。 アカウントを有効にして、操作をやり直してください。|
| 33006| ADUserAccountLockedOut| このイベントは、オンプレミスでロックアウトされたアカウントのパスワードをリセットまたは変更しようとしたことを示します。 ロックアウトは、ユーザーが短時間に何回もパスワードを変更またはリセットしようとした場合に発生します。 アカウントのロックを解除して、操作をやり直してください。|
| 33007| ADUserIncorrectPassword| このイベントは、ユーザーがパスワードの変更操作を行うときに、現在のパスワードを正しく指定しなかったことを示します。 現在の正しいパスワードを指定して、やり直してください。|
| 33008| ADPasswordPolicyError| このイベントは、パスワード ライトバック サービスが、パスワードの有効期間、履歴、複雑さ、またはフィルタリングに関するドメインの要件を満たしていないローカル ディレクトリにパスワードを設定しようとすると発生します。 <br> <br> パスワードの最小有効期間が残っていて、最近その期間内にパスワードを変更した場合は、そのドメインで指定された期限に達するまで、もう一度パスワードを変更することはできません。 テストのために、最小有効期間は 0 に設定する必要があります。 <br> <br> パスワードの履歴の要件が有効になっている場合は、最後の *N* 回で使用されていないパスワードを選択する必要があります。*N* はパスワードの履歴で設定します。 最後の *N* 回で使用されているパスワードを選択した場合は、エラーが表示されます。 テストのために、パスワードの履歴は 0 に設定する必要があります。 <br> <br> パスワードの複雑さの要件を指定する場合は、ユーザーがパスワードを変更またはリセットしようとすると、すべての要件が適用されます。 <br> <br> パスワード フィルターが有効になっている場合に、ユーザーがフィルター条件を満たしていないパスワードを選択すると、リセットまたは変更の操作に失敗します。|
| 33009| ADConfigurationError| このイベントは、Active Directory の構成に問題があるため、オンプレミスのディレクトリへのパスワード書き込みエラーが発生したことを示します。 発生したエラーの詳細については、Azure AD Connect マシンのアプリケーション イベント ログで、ADSync サービスからのメッセージを確認します。|

## <a name="troubleshoot-password-writeback-connectivity"></a>パスワード ライトバックの接続のトラブルシューティング

Azure AD Connect のパスワード ライトバック コンポーネントでサービスの中断が発生した場合は、いくつかの簡単な手順を使用してこの問題を解決できます。

* [ネットワーク接続を確認する](#confirm-network-connectivity)
* [Azure AD Connect 同期サービスを再起動する](#restart-the-azure-ad-connect-sync-service)
* [パスワード ライトバック機能を無効にしてから再び有効にする](#disable-and-re-enable-the-password-writeback-feature)
* [最新の Azure AD Connect リリースをインストールする](#install-the-latest-azure-ad-connect-release)
* [パスワード ライトバックのトラブルシューティング](#troubleshoot-password-writeback)

通常、最も早くサービスを復旧するには、上記の順序でこれらの手順を実行することをお勧めします。

### <a name="confirm-network-connectivity"></a>ネットワーク接続を確認する

最も一般的な障害点は、ファイアウォール、プロキシ ポート、アイドル タイムアウトなどが正しく構成されていないことです。 

Azure AD Connect バージョン 1.1.443.0 以上の場合は、次の URL への送信 HTTPS アクセスが必要です。

* \*.passwordreset.microsoftonline.com
* \*.servicebus.windows.net

アクセスをより細分化するために、[Microsoft Azure データセンターの IP 範囲 ](https://www.microsoft.com/download/details.aspx?id=41653) の更新された一覧を参照することができます。この一覧は、毎週水曜日に更新され、次の月曜日に有効になります。

詳細については、「[Azure AD Connect の前提条件](../hybrid/how-to-connect-install-prerequisites.md)」で接続の前提条件をご確認ください。

> [!NOTE]
> SSPR は、オンプレミスの AD DS のアカウントで [パスワードを無期限にする] または [ユーザーはパスワードを変更できない] の設定が構成されている場合にも、失敗することがあります。 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Azure AD Connect 同期サービスを再起動する

サービスに関する接続の問題や他の一時的な問題を解決するには、Azure AD Connect 同期サービスを再起動します。

1. 管理者として、Azure AD Connect を実行しているサーバーで **[開始]** を選択します。
1. 検索フィールドに「**services.msc**」を入力して、 **[入力]** を選択します。
1. **[Microsoft Azure AD Sync]** エントリを検索します。
1. このサービス エントリを右クリックして **[再起動]** を選択し、処理が完了するまで待機します。

   ![GUI を使用して Azure AD Sync サービスを再起動する][Service restart]

これらの手順によって、クラウド サービスとの接続が再確立され、発生する可能性のある中断が解決されます。 ADSync サービスを再起動しても問題が解決しない場合は、パスワード ライトバック機能を無効にしてから再び有効にすることをお勧めします。

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>パスワード ライトバック機能を無効にしてから再び有効にする

接続の問題を解決するには、パスワード ライトバック機能を無効にしてから再び有効にします。

   1. 管理者として Azure AD Connect 構成ウィザードを開きます。
   1. **[Azure AD に接続]** で、Azure AD のグローバル管理者の資格情報を入力します。
   1. **[AD DS に接続]** で、AD Domain Services の管理者の資格情報を入力します。
   1. **[ユーザーを一意に識別]** で、 **[次へ]** を選択します。
   1. **[オプション機能]** で、 **[パスワードの書き戻し]** チェック ボックスをオフにします。
   1. 他のダイアログ ページは何も変更せずに、 **[構成の準備完了]** ページが表示されるまで **[次へ]** を選択します。
   1. **[構成の準備完了]** ページで **[パスワードの書き戻し]** オプションが **[無効]** になっていることを確認したら、緑色の **[構成]** ボタンを選択して変更をコミットします。
   1. **[完了]** で、 **[今すぐ同期]** オプションをオフにし、 **[完了]** を選択してウィザードを閉じます。
   1. Azure AD Connect 構成ウィザードをもう一度開きます。
   1. 手順 2-8 を繰り返します。ただし、サービスをもう一度有効にするために **[オプション機能]** ページで **[パスワードの書き戻し]** オプションがオンになっていることを確認します。

これらの手順によって、クラウド サービスとの接続が再確立され、発生する可能性のある中断が解決されます。

パスワード ライトバック機能を無効にしてから再び有効にしても問題が解決しない場合は、Azure AD Connect を再インストールすることをお勧めします。

### <a name="install-the-latest-azure-ad-connect-release"></a>最新の Azure AD Connect リリースをインストールする

Azure AD Connect を再インストールすることで、クラウド サービスとご利用のローカルの Active Directory 環境との間で発生する構成と接続の問題を解決できます。

既に説明した最初の 2 つの手順を試したあとに、この手順を実行することをお勧めします。

> [!WARNING]
> 既定の同期ルールをカスタマイズしている場合は、*アップグレードを開始する前にこれらをバックアップし、終了後に手動で再デプロイする*必要があります。

1. [Microsoft ダウンロード センター](https://go.microsoft.com/fwlink/?LinkId=615771)から最新バージョンの Azure AD Connect をダウンロードします。
1. Azure AD Connect が既にインストールされているため、インプレース アップグレードを実行して Azure AD Connect を最新バージョンに更新する必要があります。
1. ダウンロードしたパッケージを実行し、画面の指示に従って Azure AD Connect コンピューターを更新します。

上記の手順によって、クラウド サービスとの接続が再確立され、発生する可能性のある中断が解決されるはずです。

最新バージョンの Azure AD Connect サーバーをインストールしても問題が解決しない場合は、最終手段として、最新のリリースをインストールしたあと、パスワード ライトバックを無効にしてから再び有効にすることをお勧めします。

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>パスワード ライトバックに必要なアクセス許可が Azure AD Connect にあることを確認する

Azure AD Connect には、パスワード ライトバックを実行するための Active Directory の **[パスワードのリセット]** アクセス許可が必要です。 特定のオンプレミス Active Directory ユーザー アカウントに必要なアクセス許可が Azure AD Connect にあるかどうかを確認するには、Windows の有効なアクセス許可機能を使用します。

1. Azure AD Connect サーバーにサインインし、 **[開始]**  >  **[同期サービス]** の順に選択して、**Synchronization Service Manager** を開始します。
1. **[コネクタ]** タブで、オンプレミスの **[Active Directory Domain Services]** コネクタを選択してから、 **[プロパティ]** を選択します。  
   ![プロパティの編集方法を示す Synchronization Service Manager](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. ポップアップ ウィンドウで **[Connect to Active Directory Forest]\(Active Directory フォレストに接続\)** を選択し、 **[ユーザー名]** のプロパティをメモします。 このプロパティは、ディレクトリ同期を実行するために、Azure AD Connect によって使用される AD DS アカウントです。 Azure AD Connect でパスワード ライトバックを実行するために、AD DS アカウントにはパスワードのリセットのアクセス許可が必要です。  

   ![同期サービスの Active Directory ユーザー アカウントの検索](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. オンプレミスのドメイン コントローラーにサインインし、 **[Active Directory ユーザーとコンピューター]** アプリケーションを起動します。
1. **[表示]** を選択し、 **[高度な機能]** オプションが有効であることを確認します。  

   ![[Active Directory ユーザーとコンピューター] に [高度な機能] が表示される](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. 確認する Active Directory ユーザー アカウントを探します。 アカウント名を右クリックし、 **[プロパティ]** を選択します。  
1. ポップアップ ウィンドウで、 **[セキュリティ]** タブに移動し、 **[詳細設定]** を選択します。  
1. **[Advanced Security Settings for Administrator]\(管理者のセキュリティの詳細設定\)** ポップアップ ウィンドウで、 **[有効なアクセス]** タブに移動します。
1. **[ユーザーの選択]** を選択し、Azure AD Connect (手順 3 参照) が使用する AD DS アカウントを選択してから、 **[有効なアクセス許可の表示]** を選択します。

   ![同期アカウントを示す [有効なアクセス] タブ](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. 下にスクロールし、 **[パスワードのリセット]** を探します。 エントリにチェック マークがついている場合は、選択した Active Directory ユーザー アカウントのパスワードをリセットするアクセス許可が AD DS アカウントにあることを意味します。  

   ![同期アカウントにパスワードのリセット権限があることの検証](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Azure AD フォーラム

Azure AD やセルフサービスのパスワード リセットに関する一般的な質問がある場合は、コミュニティに質問を [Azure AD フォーラム](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD)で投稿できます。 コミュニティのメンバーには、エンジニア、製品マネージャー、MVP、IT プロフェッショナルなどが含まれます。

## <a name="contact-microsoft-support"></a>Microsoft サポートに問い合わせる

問題に対する回答が見つからない場合は、Microsoft のサポート チームが随時、問題の解決をお手伝いします。

適切なサポートを提供するため、ケースをオープンする際はできるだけ詳しい情報のご提供をお願いいたします。 詳細情報は次のとおりです。

* **エラーの一般的な説明**: どのようなエラーですか。 どのような動作が見られましたか。 エラーを再現することはできますか。 できるだけ詳しくお知らせください。
* **ページ**: エラーが表示されたときに、どのページを表示していましたか。 可能な場合はそのページの URL とスクリーンショットをお送りください。
* **サポート コード**: エラーが表示されたときに生成されたサポート コードをお知らせください。
   * このコードを見つけるには、エラーを再現してから、画面の下部にある**サポート コード**のリンクを選択し、生成された GUID をサポート エンジニアに送信します。

   ![画面の下部にあるサポート コードを見つける][Support code]

  * ページの下部にサポート コードが表示されない場合は、F12 キーを押して SID と CID を検索し、この 2 つの結果をサポート エンジニアに送信します。
* **日付、時刻、タイム ゾーン**: エラーが発生した正確な日付、時刻、"*タイム ゾーン*" をお知らせください。
* **ユーザー ID**: エラーが表示されたユーザーをお知らせください。 たとえば、*user\@contoso.com* です。
   * このユーザーは、フェデレーション ユーザーですか。
   * このユーザーは、パススルー認証ユーザーですか。
   * このユーザーは、パスワード ハッシュ同期ユーザーですか。
   * このユーザーは、クラウド限定ユーザーですか。
* **ライセンス**: ユーザーに Azure AD のライセンスが割り当てられていますか。
* **アプリケーション イベント ログ**: パスワード ライトバックの使用中に、ご利用のオンプレミスのインフラストラクチャでエラーが発生した場合は、Azure AD Connect サーバーからのアプリケーション イベント ログのコピーを圧縮してお送りください。

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Azure AD Sync サービスを再起動する"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "サポート コードはウィンドウの右下にあります"

## <a name="next-steps"></a>次の手順

次の記事では、Azure AD によるパスワードのリセットに関する追加情報が得られます。

* [SSPR のロールアウトを正常に完了する方法](howto-sspr-deployment.md)
* [パスワードのリセットまたは変更](../user-help/active-directory-passwords-update-your-own-password.md)
* [セルフサービスのパスワード リセットのための登録](../user-help/active-directory-passwords-reset-register.md)
* [ライセンスに関する質問](concept-sspr-licensing.md)
* [SSPR が使用するデータと、ユーザー用に設定するデータ。](howto-sspr-authenticationdata.md)
* [ユーザーが使用できる認証方法](concept-sspr-howitworks.md#authentication-methods)
* [SSPR のポリシー オプション](concept-sspr-policy.md)
* [パスワード ライトバックの概要とその必要性](howto-sspr-writeback.md)
* [SSPR でアクティビティをレポートする方法](howto-sspr-reporting.md)
* [SSPR のすべてのオプションとその意味](concept-sspr-howitworks.md)
* [質問したい内容に関する説明がどこにもない。](active-directory-passwords-faq.md)
