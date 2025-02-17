---
title: Azure AD のエンタイトルメント管理で外部ユーザーのアクセスを管理する (プレビュー) - Azure Active Directory
description: Azure Active Directory のエンタイトルメント管理で外部ユーザーのアクセスを管理するために指定できる設定について説明します。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcf4a0272e21a1fba3cf9adbd9158492e4318578
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/16/2019
ms.locfileid: "72452909"
---
# <a name="govern-access-for-external-users-in-azure-ad-entitlement-management-preview"></a>Azure AD のエンタイトルメント管理で外部ユーザーのアクセスを管理する (プレビュー)

> [!IMPORTANT]
> Azure Active Directory (Azure AD) エンタイトルメント管理は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Azure AD のエンタイトルメント管理では、[Azure AD 企業間 (B2B)](../b2b/what-is-b2b.md) を利用して、別のディレクトリにいる組織外のユーザーとの共同作業が行われます。 Azure AD B2B では、外部ユーザーはユーザー自身のホーム ディレクトリで認証を行いますが、こちらのディレクトリにユーザーの表現が存在します。 こちらのディレクトリ内のその表現により、ユーザーにこちらのリソースへのアクセスを割り当てることができます。

この記事では、外部ユーザーのアクセスを管理するために指定できる設定について説明します。

## <a name="how-entitlement-management-can-help"></a>エンタイトルメント管理の効果

[Azure AD B2B](../b2b/what-is-b2b.md) 招待状を使用する場合、こちらのリソース ディレクトリに招待して共同作業する外部ゲスト ユーザーのメール アドレスをあらかじめ知っている必要があります。 これは、小規模または短期間のプロジェクトで作業していて、すべての参加者を既に知っている場合は効果的ですが、一緒に作業したいユーザーが多い場合や、参加者の入れ替わりがある場合は管理が難しくなります。  たとえば、別の組織と仕事をしていて、その組織との連絡窓口が一本化されていたとしても、時間が経てば、その組織の新たなユーザーもアクセスを必要とするようになるでしょう。

エンタイトルメント管理を使用すれば、指定した組織のユーザーがアクセス パッケージを自分で要求できるようにするポリシーを定義できます。 承認が必要かどうか、またアクセスの有効期限を指定できます。 承認が必要な場合は、外部組織から 1 人以上のユーザーをディレクトリに招待し、承認者として指定することもできます。これらのユーザーは、自分の組織のどの外部ユーザーがアクセスを必要としているか、知っている可能性が高いからです。 アクセス パッケージを構成したら、アクセス パッケージのリンクを外部組織の連絡先担当者 (スポンサー) に送信できます。 その連絡先は外部組織の他のユーザーと共有することができ、それらのユーザーがこのリンクを使用してアクセス パッケージを要求できます。 その組織に属する、ディレクトリに招待済みのユーザーもそのリンクを使用できます。

要求が承認されると、エンタイトルメント管理は必要なアクセス権をユーザーにプロビジョニングします。これには、ユーザーがまだディレクトリに参加していない場合に、そのユーザーを招待する処理が含まれる場合があります。 Azure AD では、それらのユーザーの B2B ゲスト アカウントが自動的に作成されます。 管理者が以前に、他組織への招待を許可またはブロックする [B2B 許可/拒否リスト](../b2b/allow-deny-list.md)を設定することによって、共同作業を許可する組織を制限していた可能性があることに注意してください。  ユーザーが許可/ブロック リストによって許可されていない場合、そのユーザーは招待されません。

外部ユーザーのアクセスを恒久的にしたくない場合、180 日などの有効期限をポリシーに指定します。 180 日経ってもアクセス権が延長されない場合、エンタイトルメント管理ではそのアクセス パッケージに関連付けられているすべてのアクセス権が削除されます。 既定では、エンタイトルメント管理によって招待されたユーザーに他のアクセス パッケージが割り当てられていない場合、最後の割り当てを失った時点で、そのユーザーのゲスト アカウントは 30 日間サインインからブロックされ、その後削除されます。 これにより、不要なアカウントの拡散を防ぎます。 以下のセクションで説明するように、これらの設定は構成可能です。

## <a name="how-access-works-for-external-users"></a>外部ユーザーのアクセスのしくみ

次の図と手順では、アクセス パッケージへのアクセスを外部ユーザーに許可する方法の概要を説明します。

![外部ユーザーのライフサイクルを示す図](./media/entitlement-management-external-users/external-users-lifecycle.png)

1. [[自分のディレクトリ内以外のユーザーの場合]](entitlement-management-access-package-create.md#for-users-not-in-your-directory) ポリシーが含まれるディレクトリに、アクセス パッケージを作成します。

1. 外部組織の連絡先に[マイ アクセス ポータルのリンク](entitlement-management-access-package-settings.md)を送信し、アクセス パッケージを要求するためにユーザーと共有できるようにします。

1. 外部ユーザー (この例では**要求者 A**) は、マイ アクセス ポータルのリンクを使用して、アクセス パッケージへの[アクセスを要求](entitlement-management-request-access.md)します。

1. 承認者が[要求を承認します](entitlement-management-request-approve.md) (または、要求が自動承認されます)。

1. 要求は[配信中状態](entitlement-management-process.md)になります。

1. B2B 招待プロセスを使用して、ゲスト ユーザー アカウントがディレクトリに作成されます (この例では**要求者 A (ゲスト)** )。 [許可リストまたは拒否リスト](../b2b/allow-deny-list.md)が定義されている場合、リストの設定が適用されます。

1. ゲスト ユーザーに、アクセス パッケージ内のすべてのリソースへのアクセスが割り当てられます。 Azure AD および他の Microsoft Online Services や接続された SaaS アプリケーションに対して変更が行われるまで、しばらく時間がかかることがあります。 詳細については、「[変更が適用されるタイミング](entitlement-management-access-package-resources.md#when-changes-are-applied)」を参照してください。

1. 外部ユーザーは、アクセスが[配信された](entitlement-management-process.md)ことを示すメールを受信します。

1. 外部ユーザーは、メールのリンクをクリックするか、ディレクトリ リソースのいずれかに直接アクセスを試みて招待プロセスを完了することにより、リソースにアクセスできます。

1. ポリシーの設定によっては、時間が経過すると、外部ユーザーに対するアクセス パッケージの割り当てが期限切れになり、外部ユーザーのアクセスが削除されます。

1. 外部ユーザーの設定のライフサイクルによっては、外部ユーザーにアクセス パッケージの割り当てがなくなった場合、外部ユーザーはサインインをブロックされ、ゲスト ユーザー アカウントはディレクトリから削除されます。

## <a name="manage-the-lifecycle-of-external-users"></a>外部ユーザーのライフサイクルを管理する

アクセス パッケージ要求が承認されることでディレクトリに招待された外部ユーザーが、アクセス パッケージの割り当てを失ったときに行われる処理を選択できます。 これは、ユーザーがアクセス パッケージの割り当てをすべて放棄した場合、または最後のアクセス パッケージの割り当てが期限切れになった場合に、行われる可能性があります。 既定では、アクセス パッケージの割り当てをすべて失った外部ユーザーは、ディレクトリへのサインインをブロックされます。 30 日後に、ゲスト ユーザー アカウントがディレクトリから削除されます。

**事前に必要なロール:** グローバル管理者またはユーザー管理者

1. Azure portal で **[Azure Active Directory]** をクリックし、 **[Identity Governance]** をクリックします。

1. 左側のメニューの **[エンタイトルメント管理]** セクションで、 **[設定]** をクリックします。

1. **[編集]** をクリックします。

    ![外部ユーザーのライフサイクルを管理するための設定](./media/entitlement-management-external-users/settings-external-users.png)

1. **[外部ユーザーのライフサイクルを管理する]** セクションで、外部ユーザーに対するさまざまな設定を選択します。

1. 外部ユーザーがアクセス パッケージへの最後の割り当てを失ったときに、そのユーザーによるこのディレクトリへのサインインをブロックする場合は、 **[外部ユーザーによるこのディレクトリへのサインインをブロックする]** を **[はい]** に設定します。

1. 外部ユーザーがアクセス パッケージへの最後の割り当てを失ったときに、ディレクトリ内のゲスト ユーザー アカウントを削除する場合は、 **[外部ユーザーを削除]** を **[はい]** に設定します。

    > [!NOTE]
    > エンタイトルメント管理では、エンタイトルメント管理によって招待されたアカウントのみが削除されます。 また、ユーザーがディレクトリ内のアクセス パッケージの割り当てではないリソースに追加されていた場合でも、そのユーザーはサインインをブロックされ、ディレクトリから削除されることに注意してください。 アクセス パッケージの割り当てを受け取る前に、ゲストがディレクトリ内に存在していた場合は、そのまま残ります。 ただし、ゲストがアクセス パッケージの割り当てによって招待され、招待された後で OneDrive for Business または SharePoint Online サイトにも割り当てられた場合でも、そのゲストは削除されます。

1. ディレクトリのゲスト ユーザー アカウントを削除する場合は、削除するまでの日数を設定できます。 アクセス パッケージへの最後の割り当てが失われた直後にゲスト ユーザー アカウントを削除する場合は、 **[Number of days before removing external user from this directory]\(このディレクトリから外部ユーザーを削除するまでの日数\)** を **0** に設定します。

1. **[Save]** をクリックします。

## <a name="enable-a-catalog-for-external-users"></a>外部ユーザーに対するカタログを有効にする

[新しいカタログ](entitlement-management-catalog-create.md)を作成するときは、外部ディレクトリのユーザーがカタログのアクセス パッケージを要求できるようにする設定があります。 外部ユーザーにカタログ内のアクセス パッケージを要求する権限を与えない場合は、 **[外部ユーザーに有効]** を **[いいえ]** に設定します。

**事前に必要なロール:** 全体管理者、ユーザー管理者、またはカタログ所有者

![新しいカタログ ペイン](./media/entitlement-management-shared/new-catalog.png)

この設定は、カタログを作成した後で変更することもできます。

![カタログの設定を編集する](./media/entitlement-management-shared/catalog-edit.png)

## <a name="next-steps"></a>次の手順

- [自分のディレクトリ内以外のユーザーの場合](entitlement-management-access-package-create.md#for-users-not-in-your-directory)
- [リソースのカタログを作成および管理する](entitlement-management-catalog-create.md)
- [委任とロール](entitlement-management-delegate.md)
