---
title: Azure Lighthouse のシナリオにおけるテナント、ロール、ユーザー
description: Azure Active Directory のテナント、ユーザー、およびロールの概念と、それらを Azure Lighthouse のシナリオで使用する方法について説明します。
ms.date: 11/05/2019
ms.topic: overview
ms.openlocfilehash: 73c5cd592f07a23edaad23796e498ea9243c5d26
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/16/2019
ms.locfileid: "74131345"
---
# <a name="tenants-roles-and-users-in-azure-lighthouse-scenarios"></a>Azure Lighthouse のシナリオにおけるテナント、ロール、ユーザー

顧客を [Azure の委任されたリソース管理](azure-delegated-resource-management.md)にオンボードする前に、Azure Active Directory (Azure AD) のテナント、ユーザー、およびロールがどのように機能するか、およびそれらを Azure Lighthouse シナリオでどのように使用するかを理解しておくことが重要です。

*テナント*とは、Azure AD の信頼された専用インスタンスです。 通常、各 Azure テナントは 1 つの組織を表します。 Azure の委任されたリソース管理を使用すると、あるテナントから別のテナントにリソースを論理的に投影できます。 これにより、(サービス プロバイダーに属するユーザーなどの) 管理テナントのユーザーは、顧客のテナント内の委任されたリソースにアクセスできるようになります。また、[複数のテナントを持つ企業が、管理操作を一元的に実行できるようになります。](enterprise.md)

この論理的な投影を実現するには、顧客テナントのサブスクリプション (またはサブスクリプション内の 1 つ以上のリソース グループ) を、Azure の委任されたリソース管理に*オンボード*する必要があります。 オンボードは、[Azure Resource Manager のテンプレート](../how-to/onboard-customer.md)を使用して行うか、[Azure Marketplace にパブリックまたはプライベート サービスを発行して行います](../how-to/publish-managed-services-offers.md)。

いずれのオンボード方法を選択しても、*承認*を定義する必要があります。 各承認では、委任されたリソースへのアクセス権を持つ管理テナントのユーザーアカウント、およびこれらの各ユーザーがこれらのリソースに対して持つアクセス許可を設定する組み込みのロールを指定します。

## <a name="role-support-for-azure-delegated-resource-management"></a>Azure の委任されたリソース管理でのロールのサポート

承認を定義する場合、各ユーザー アカウントには、[ロールベースのアクセス制御 (RBAC) の組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)を 1 つ割り当てる必要があります。 カスタム ロールと[従来のサブスクリプション管理者ロール](https://docs.microsoft.com/azure/role-based-access-control/classic-administrators)はサポートされていません。

現在、Azure の委任されたリソース管理では、以下を除く、すべての[組み込みロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles)がサポートされています。

- [所有者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#owner)ロールはサポートされていません。
- [DataActions](https://docs.microsoft.com/azure/role-based-access-control/role-definitions#dataactions) 権限を持つすべての組み込みロールはサポートされていません。
- 組み込みの[ユーザー アクセス管理者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#user-access-administrator)ロールは、[顧客のテナント](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)のマネージド ID にロールを割り当てる目的限定でサポートされています。 このロールで通常付与されるその他の権限は適用されません。 ユーザーにこのロールを定義する場合は、このユーザーがマネージド ID に割り当てることができる組み込みロールも指定する必要があります。

## <a name="best-practices-for-defining-users-and-roles"></a>ユーザーとロールを定義するためのベスト プラクティス

ご自分の承認を作成するときは、次のベスト プラクティスに従うことをお勧めします。

- ほとんどの場合、一連の個々のユーザー アカウントではなく、Azure AD のユーザー グループまたはサービス プリンシパルにアクセス許可を割り当てます。 これにより、実際のアクセス要件が変更されたときに、プランを更新して再発行することなく、個々のユーザーのアクセス権を追加または削除できます。
- ユーザーがジョブの完了に必要なアクセス許可のみを持ち、不注意によるエラーの可能性が低くなるように、必ず最小限の特権の原則に従ってください。 詳細については、「[推奨セキュリティ プラクティス](../concepts/recommended-security-practices.md)」を参照してください。
- 必要に応じて後で[委任へのアクセスを削除](../how-to/onboard-customer.md#remove-access-to-a-delegation)できるように、[マネージド サービスの登録割り当ての削除ロール](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#managed-services-registration-assignment-delete-role)を持つユーザーを含めます。 このロールが割り当てられていない場合、委任されたリソースは顧客のテナントのユーザーのみが削除できます。
- [Azure portal の [マイ カスタマー] ページを表示](../how-to/view-manage-customers.md)する必要があるすべてのユーザーには、必ず[閲覧者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader)ロール (または閲覧者アクセスを含む別の組み込みロール) を付与します。

## <a name="next-steps"></a>次の手順

- [Azure の委任されたリソース管理で推奨されるセキュリティの運用方法](recommended-security-practices.md)を学習します。
- [Azure Resource Manager テンプレートを使用する](../how-to/onboard-customer.md)か[プライベートまたはパブリックのマネージド サービスのオファーを Azure Marketplace に公開する](../how-to/publish-managed-services-offers.md)ことで、Azure の委任されたリソース管理に顧客をオンボードします。
