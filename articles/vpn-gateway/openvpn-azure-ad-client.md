---
title: VPN クライアントを P2S VPN 接続用に構成する:Azure AD 認証 | Microsoft Docs
description: P2S VPN を使用して VNet に接続する場合には、Azure AD 認証を使用できます
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 10/08/2019
ms.author: cherylmc
ms.openlocfilehash: 704dcd6335766a6058de4e520b8dcbca0d304c9d
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721473"
---
# <a name="configure-a-vpn-client-for-p2s-vpn-connections-azure-ad-authentication-preview"></a>VPN クライアントを P2S VPN 接続用に構成する:Azure AD 認証 (プレビュー)

この記事では、VPN クライアントからポイント対サイト VPN と Azure Active Directory 認証を使用して仮想ネットワークに接続する際の構成について説明します。 Azure AD を使用して接続と認証を行うには、まず Azure AD テナントを構成する必要があります。 詳細については、「[Azure AD テナントの構成](openvpn-azure-ad-tenant.md)」を参照してください。

## <a name="profile"></a>クライアント プロファイルの操作

接続するには、VNet への接続を必要とするすべてのコンピューターで Azure VPN クライアントを[ダウンロード](https://www.microsoft.com/p/azure-vpn-client-preview/9np355qt2sqb?rtc=1&activetab=pivot:overviewtab)したうえで、それぞれに VPN クライアント プロファイルを構成する必要があります。 1 つのコンピューターでクライアント プロファイルを作成した後、それをエクスポートして、別のコンピューターにインポートすることもできます。

### <a name="cert"></a>証明書ベースのクライアント プロファイルを作成するには

証明書ベースのプロファイルを使用する場合は、クライアント コンピューターに適切な証明書がインストールされていることを確認してください。 証明書の詳細については、「[クライアント証明書のインストール](point-to-site-how-to-vpn-client-install-azure-cert.md)」を参照してください。

  ![cert](./media/openvpn-azure-ad-client/create/create-cert1.jpg)

### <a name="radius"></a>RADIUS クライアント プロファイルを作成するには

  ![radius](./media/openvpn-azure-ad-client/create/create-radius1.jpg)

### <a name="export"></a>クライアント プロファイルをエクスポートして配布するには

使用するプロファイルを作成した後、それを他のユーザーに配布する必要がある場合は、次の手順に従ってプロファイルをエクスポートできます。

1. エクスポートする VPN クライアント プロファイルを強調表示し、 **[...]** を選択して、 **[エクスポート]** を選択します。

    ![エクスポート](./media/openvpn-azure-ad-client/export/export1.jpg)

2. このプロファイルを保存する場所を選択し、ファイル名はそのままで、 **[保存]** を選択して xml ファイルを保存します。

    ![エクスポート](./media/openvpn-azure-ad-client/export/export2.jpg)

### <a name="import"></a>クライアント プロファイルをインポートするには

1. ページ上で、 **[インポート]** を選択します。

    ![import](./media/openvpn-azure-ad-client/import/import1.jpg)

2. プロファイル xml ファイルを参照し、選択します。 ファイルが選択された状態で、 **[開く]** を選択します。

    ![import](./media/openvpn-azure-ad-client/import/import2.jpg)

3. プロファイルの名前を指定し、 **[保存]** を選択します。

    ![import](./media/openvpn-azure-ad-client/import/import3.jpg)

4. **[接続]** を選択して VPN に接続します。

    ![import](./media/openvpn-azure-ad-client/import/import4.jpg)

5. 接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![import](./media/openvpn-azure-ad-client/import/import5.jpg)

### <a name="delete"></a>クライアント プロファイルを削除するには

1. 削除するクライアント プロファイルの横にある省略記号を選択します。 **[削除]** を選択します。

    ![delete](./media/openvpn-azure-ad-client/delete/delete1.jpg)

2. **[削除]** を選択して削除します。

    ![delete](./media/openvpn-azure-ad-client/delete/delete2.jpg)

## <a name="connection"></a>接続を作成する

1. ページ上で、 **[+]** を選択し、 **[追加 +]** を選択します。

    ![connection](./media/openvpn-azure-ad-client/create/create1.jpg)

2. 接続情報を入力します。 値が不明な場合は、管理者に問い合わせてください。 値を入力したら、 **[保存]** を選択します。

    ![connection](./media/openvpn-azure-ad-client/create/create2.jpg)

3. **[接続]** を選択して VPN に接続します。

    ![connection](./media/openvpn-azure-ad-client/create/create3.jpg)

4. 適切な資格情報を選択し、 **[続行]** を選択します。

    ![connection](./media/openvpn-azure-ad-client/create/create4.jpg)

5. 正常に接続されると、アイコンが緑色に変わり、 **[接続済み]** と表示されます。

    ![connection](./media/openvpn-azure-ad-client/create/create5.jpg)

### <a name="autoconnect"></a>自動的に接続するには

Always-on を使用して自動的に接続するように構成するには、次の手順に従います。

1. VPN クライアントのホームページで、 **[VPN 設定]** を選択します。

    ![自動](./media/openvpn-azure-ad-client/auto/auto1.jpg)

2. アプリ切り替えのダイアログ ボックスで、 **[はい]** を選択します。

    ![自動](./media/openvpn-azure-ad-client/auto/auto2.jpg)

3. 設定する接続が既に接続済みでないことを確認してから、プロファイルを強調表示し、 **[自動的に接続する]** チェック ボックスを オンにします。

    ![自動](./media/openvpn-azure-ad-client/auto/auto3.jpg)

4. **[接続]** を選択して接続を開始します。

    ![自動](./media/openvpn-azure-ad-client/auto/auto4.jpg)

## <a name="diagnose"></a>接続の問題を診断する

1. 接続の問題を診断するには、**診断**ツールを使用します。 診断する VPN 接続の横にある **[...]** を選択して、メニューを表示します。 次に、 **[診断]** を選択します。

    ![診断](./media/openvpn-azure-ad-client/diagnose/diagnose1.jpg)

2. **[接続プロパティ]** ページで、 **[Run Diagnosis]\(診断の実行\)** を選択します。

    ![診断](./media/openvpn-azure-ad-client/diagnose/diagnose2.jpg)

3. 自分の資格情報でサインインします。

    ![診断](./media/openvpn-azure-ad-client/diagnose/diagnose3.jpg)

4. 診断結果を確認します。

    ![診断](./media/openvpn-azure-ad-client/diagnose/diagnose4.jpg)

## <a name="next-steps"></a>次の手順

詳細については、[Azure AD 認証を使用する P2S Open VPN 接続用の Azure Active Directory テナントの作成](openvpn-azure-ad-tenant.md)に関するページを参照してください。
