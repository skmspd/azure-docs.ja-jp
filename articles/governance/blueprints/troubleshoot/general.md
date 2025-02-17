---
title: 一般的なエラーのトラブルシューティング
description: ブループリントの作成、割り当て、および削除で発生する問題を解決する方法について説明します
ms.date: 12/11/2018
ms.topic: troubleshooting
ms.openlocfilehash: b6f1d6c40f7268e90f09457e680a3ef33996c341
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960290"
---
# <a name="troubleshoot-errors-using-azure-blueprints"></a>Azure Blueprints でエラーを解決する

ブループリントを作成したり、割り当てたりするとき、エラーが発生することがあります。 この記事では、発生する可能性があるさまざまなエラーと、その解決方法について説明します。

## <a name="finding-error-details"></a>エラー詳細の検索

多くのエラーは、ブループリントをスコープに割り当てることから発生します。 割り当てられなかったとき、デプロイの失敗に関する詳細がブループリントにより提供されます。 この情報から問題が示されるので、問題を解決すれば、次回のデプロイは成功します。

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側のページから **[割り当てられたブループリント]** を選択し、検索ボックスを使用してブループリントの割り当てをフィルター処理し、失敗した割り当てを検索します。 **[プロビジョニング状態]** 列で割り当て表を並べ替え、失敗した割り当てをまとめて表示することもできます。

1. 状態が _[失敗]_ のブループリントを左クリックするか、 **[割り当ての詳細を表示する]** を右クリックして選択します。

1. ブループリント割り当てページの一番上に、割り当ての失敗を警告する赤いバナーが表示されます。 バナー内のどこでも良いのでクリックすると、詳細が表示されます。

全体としてのブループリントではなく、成果物がエラーの原因となることがよくあります。 成果物によってキー コンテナーが作成される場合、Azure Policy によってキー コンテナーの作成が阻止され、割り当て全体が失敗します。

## <a name="general-errors"></a>一般エラー

### <a name="policy-violation"></a>シナリオ:ポリシー違反

#### <a name="issue"></a>問題

ポリシー違反に起因してテンプレートのデプロイが失敗しました。

#### <a name="cause"></a>原因

ポリシーはさまざまな理由からデプロイと競合することがあります。

- 作成されるリソースはポリシーの制約を受けます (一般的には SKU または場所の制約)
- デプロイはポリシーによって構成される設定フィールドです (タグで一般的)

#### <a name="resolution"></a>解決策

ブルー プリントを変更して、エラーの詳細に含まれているポリシーと競合しないようにします。 変更できない場合、代替として、ブループリントがポリシーと競合しないようにポリシー割り当てのスコープを変更するという選択肢があります。

### <a name="escape-function-parameter"></a>シナリオ:ブループリント パラメーターが関数である

#### <a name="issue"></a>問題

関数であるブループリント パラメーターが、成果物に渡される前に処理されます。

#### <a name="cause"></a>原因

`[resourceGroup().tags.myTag]` などの関数を使用するブループリント パラメーターを成果物に渡すと、動的関数ではなく、成果物に対して関数が設定されます。

#### <a name="resolution"></a>解決策

関数をパラメーターとして渡すには、ブループリント パラメーターが `[[resourceGroup().tags.myTag]` のようになるように、文字列全体を `[` でエスケープします。 エスケープ文字により、Blueprints によってブループリントが処理されるときに値が文字列として扱われます。 その後、関数は Blueprints によって成果物に配置され、意図したとおりに動的になります。 詳細については、「[Azure Resource Manager テンプレートの構文と式](../../../azure-resource-manager/template-expressions.md)」を参照してください。

## <a name="next-steps"></a>次の手順

問題がわからなかった場合、または問題を解決できない場合は、次のいずれかのチャネルでサポートを受けてください。

- [Azure フォーラム](https://azure.microsoft.com/support/forums/)を通じて Azure エキスパートから回答を得ることができます。
- [@AzureSupport](https://twitter.com/azuresupport) に問い合わせる – Microsoft Azure 公式アカウントです。Azure コミュニティを適切なリソース (回答、サポート、エキスパート) に結び付けることで、カスタマー エクスペリエンスを向上します。
- さらにヘルプが必要であれば、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。