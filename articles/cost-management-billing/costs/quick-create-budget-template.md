---
title: クイックスタート - Azure Resource Manager テンプレートを使用して予算を作成する
description: Azure Resource Manager テンプレートを使用して予算を作成する方法を紹介するクイックスタート。
author: bandersmsft
ms.author: banders
tags: azure-resource-manager
ms.service: cost-management-billing
ms.topic: quickstart
ms.date: 06/10/2020
ms.custom: subject-armqs
ms.openlocfilehash: 5bff8e6057475701a2e78835fb5a950dcb8c8fcb
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2020
ms.locfileid: "86252438"
---
# <a name="quickstart-create-a-budget-with-an-arm-template"></a>クイック スタート:ARM テンプレートを使用して予算を作成する

Cost Management での予算は、組織のアカウンタビリティを計画および推進するのに役立ちます。 予算では、特定の期間中に消費またはサブスクライブする Azure サービスを考慮することができます。 コストを事前に管理するために支出を他のユーザーに通知したり、支出の時間変化を監視したりするのに役立ちます。 作成した予算のしきい値を超えた場合は、通知がトリガーされます。 どのリソースも影響を受けることはなく、消費が停止されることはありません。 予算を使用して、コストを分析するときに支出を比較および追跡できます。 このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して予算を作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

ARM テンプレートでサポートされるのは、マイクロソフト エンタープライズ契約 (EA) の Azure サブスクリプションのみです。 その他の種類のサブスクリプションは、このテンプレートではサポートされません。

予算を作成し、管理するには、共同作成者のアクセス許可が必要です。 EA サブスクリプションとリソース グループ用に個別の予算を作成できます。 ただし、EA の請求先アカウントの予算を作成することはできません。 Azure EA サブスクリプションの場合、予算を表示するには読み取りアクセス権が必要です。

予算が作成された後、予算を表示するには、少なくとも Azure アカウントの読み取りアクセス権が必要です。

新しいサブスクリプションをご利用の場合、予算の作成など、Cost Management の機能をすぐには使用できません。 すべての Cost Management 機能を使用できるようになるまでに、最大 48 時間かかる場合があります。

ユーザーおよびグループごとの予算については、サブスクリプションに従い、次の Azure アクセス許可 (スコープ) がサポートされています。 スコープの詳細については、「[Understand and work with scopes (スコープを理解して使用する)](understand-work-scopes.md)」を参照してください。

- 所有者 – サブスクリプションに対する予算を作成、変更、削除できます。
- 共同作成者と Cost Management 共同作成者 - 自分の予算を作成、変更、削除できます。 他のユーザーによって作成された予算の予算金額を変更できます。
- 閲覧者と Cost Management 閲覧者 - 自分がアクセス許可を持っている予算を表示できます。

Cost Management データに対するアクセス許可の割り当てについて詳しくは、「[Cost Management のデータへのアクセス許可を割り当てる](assign-access-acm-data.md)」をご覧ください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/create-budget)からのものです。

:::code language="json" source="~/quickstart-templates/create-budget/azuredeploy.json" range="1-146" highlight="110-139":::

テンプレートには、1 つの Azure リソースが定義されています。

* [Microsoft.Consumption/budgets](/azure/templates/microsoft.consumption/budgets): Azure の予算を作成します。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次のイメージを選択します。 このテンプレートによって予算が作成されます。

   [![Azure へのデプロイ](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fcreate-budget%2Fazuredeploy.json)

2. 次の値を選択または入力します。

   [![Resource Manager テンプレート、予算の作成、ポータルでのデプロイ](./media/quick-create-budget-template/create-budget-using-template-portal.png)](./media/quick-create-budget-template/create-budget-using-template-portal.png#lightbox)

    * **サブスクリプション**: Azure サブスクリプションを選択します。
    * **リソース グループ**: **[新規作成]** を選択し、リソース グループの一意の名前を入力して、 **[OK]** をクリックするか、既存のリソース グループを選択します。
    * **場所**: 場所を選択します。 たとえば **[米国中部]** です。
    * **予算名**: 予算の名前を入力します。 リソース グループ内で一意であることが必要です。 使用できる文字は、英数字、アンダー スコア、ハイフンのみです。
    * **Amount (量)** : 予算で追跡するコストまたは使用量の合計を入力します。
    * **予算カテゴリ**: 予算のカテゴリ (予算で追跡するのが **[コスト]** であるか **[使用量]** であるか) を選択します。
    * **時間グレイン**: 予算の対象となる時間を入力します。 指定できる値は、毎月、四半期ごと、毎年です。 時間グレインの終了時点で予算はリセットされます。
    * **開始日**: 月の第 1 日目に対する開始日を入力します (YYYY-MM-DD 形式)。 将来の開始日は、今日から 3 か月以内でなければなりません。 時間グレイン期間に対する過去の開始日を指定することができます。
    * **終了日**: 予算の終了日を YYYY-MM-DD 形式で入力します。 指定しなかった場合は、既定で開始日から 10 年に設定されます。
    * **演算子**: 比較演算子を選択します。 指定できる値は、EqualTo、GreaterThan、GreaterThanOrEqualTo です。
    * **しきい値**: 通知のしきい値を入力します。 コストがしきい値を超えたとき、通知が送信されます。 常にパーセント単位となります。0 から 1000 の範囲で指定してください。
    * **連絡先の電子メール**: しきい値を超えたときに予算の通知が送信される一連のメール アドレスを入力します。 `["user1@domain.com","user2@domain.com"]` 形式で指定する必要があります。
    * **Contact Roles (連絡先の役割)** : しきい値を超えたときに予算の通知が送信される一連の連絡先の役割を入力します。 既定値は、所有者、共同作成者、閲覧者です。 `["Owner","Contributor","Reader"]` 形式で指定する必要があります。
    * **Contact Groups (連絡先グループ)** : しきい値を超えたときに予算の通知が送信される一連のアクション グループのリソース ID を完全なリソース URI として入力します。 文字列の配列を指定できます。 `["action group resource ID1","action group resource ID2"]` 形式で指定する必要があります。 アクション グループを使用しない場合は、「`[]`」と入力してください。
    * **Resources Filter (リソース フィルター)** : リソースに使用する一連のフィルターを入力します。 `["Resource Filter Name1","Resource Filter Name2"]` 形式で指定する必要があります。 フィルターを適用しない場合は、「`[]`」と入力してください。 リソース フィルターを入力する場合は、 **[Meters Filters]\(メーター フィルター\)** の値も入力する必要があります。
    * **[Meters Filter]\(メーター フィルター\)** : メーターに対する一連のフィルターを入力します。予算カテゴリが **[使用量]** である場合は必須です。 `["Meter Filter Name1","Meter Filter Name2"]` 形式で指定する必要があります。 **リソース フィルター**を入力しなかった場合は、「`[]`」と入力してください。
    * **上記の使用条件に同意する**: 選択。

3. **[購入]** を選択します。 予算が正常にデプロイされた後、通知が表示されます。

   ![Resource Manager テンプレートで予算をデプロイする場合のポータルの通知](./media/quick-create-budget-template/resource-manager-template-portal-deployment-notification.png)

テンプレートをデプロイするには Azure portal を使用します。 Azure portal だけでなく、Azure PowerShell、Azure CLI、REST API を使用することもできます。 他のデプロイ テンプレートについては、「[テンプレートのデプロイ](../../azure-resource-manager/templates/deploy-powershell.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

予算が作成されたことは、Azure portal から **[コストの管理と請求]** に移動してスコープを選択し、 **[予算]** を選択することによって確認できます。 または、次の Azure CLI または Azure PowerShell スクリプトを使用して予算を確認してください。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
az consumption budget list
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
Get-AzConsumptionBudget
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

予算が不要になった場合は、次のいずれかの方法を使用して削除します。

### <a name="azure-portal"></a>Azure portal

**[コストの管理と請求]** に移動して、請求スコープを選択し、 **[予算]** で予算を選択したら、 **[予算の削除]** を選択します。

### <a name="command-line"></a>コマンド ライン

Azure CLI または Azure PowerShell を使用して、予算を削除できます。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the budget name:" &&
read budgetName &&
az consumption budget delete --budget-name $budgetName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$budgetName = Read-Host -Prompt "Enter the budget name"
Remove-AzConsumptionBudget -Name $budgetName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure の予算のデプロイを作成しました。 Azure Cost Management と Billing および Azure Resource Manager の詳細については、引き続き以下の記事を参照してください。

- [Cost Management と Billing](../cost-management-billing-overview.md) の概要を読む
- Azure portal で[予算を作成](tutorial-acm-create-budgets.md)する
- [Azure Resource Manager](../../azure-resource-manager/management/overview.md) の詳細を確認する
