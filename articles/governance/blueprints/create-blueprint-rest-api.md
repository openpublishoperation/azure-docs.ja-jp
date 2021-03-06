---
title: REST API で Azure Blueprint を作成する
description: Azure Blueprint を使用して、成果物を作成、定義、デプロイします。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: b873ee869b2044977ebefcfd65331567c24e7ec8
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/24/2018
ms.locfileid: "46974206"
---
# <a name="define-and-assign-an-azure-blueprint-with-rest-api"></a>REST API で Azure Blueprint を定義して割り当てる

Azure でのブループリントの作成方法と割り当て方法を理解することにより、組織は一貫性の共通パターンを定義し、Resource Manager テンプレート、ポリシー、セキュリティなどに基づいて、再利用可能であり短時間でデプロイできる構成を開発できるようになります。 このチュートリアルでは、組織内のブループリントの作成、発行、および割り当てに関連する一般的ないくつかのタスクを実行するための、Azure Blueprint の使用方法について説明します。

> [!div class="checklist"]
> - 新しいブループリントを作成し、サポートされているさまざまな成果物を追加する
> - まだ**下書き**の既存のブループリントを変更する
> - 割り当ての準備が完了しているブループリントに**発行済み**とマークを付ける
> - ブループリントを既存のサブスクリプションに割り当てる
> - 割り当てられたブループリントの状態と進行状況を確認する
> - サブスクリプションに割り当てられているブループリントを削除する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free) を作成してください。

## <a name="getting-started-with-rest-api"></a>REST API で作業を開始する

REST API に慣れていない場合は、最初に「[Azure REST API Reference](/rest/api/azure/)」(Azure REST API リファレンス) を読んで、REST API の基本を理解してください (具体的には要求 URI と要求の本文)。 この記事では、これらの概念を使用して Azure Blueprint を操作する方法を説明しており、それらの実践的な知識を前提としています。 [ARMClient](https://github.com/projectkudu/ARMClient) などのツールは認可を自動的に処理できるので、初めての方にお勧めします。

### <a name="rest-api-and-powershell"></a>REST API と PowerShell

REST API の呼び出しを行うためのツールがまだない場合は、PowerShell を使うことを検討してください。 Azure で認証を行うためのヘッダーのサンプルを次に示します。 認証ヘッダー (**ベアラー トークン**と呼ばれることもあります) を生成し、パラメーターまたは**要求本文**で接続する REST API の URI を提供します。

```powershell-interactive
# Login first with Connect-AzureRmAccount if not using Cloud Shell

$azContext = Get-AzureRmContext
$azProfile = [Microsoft.Azure.Commands.Common.Authentication.Abstractions.AzureRmProfileProvider]::Instance.Profile
$profileClient = New-Object -TypeName Microsoft.Azure.Commands.ResourceManager.Common.RMProfileClient -ArgumentList ($azProfile)
$token = $profileClient.AcquireAccessToken($azContext.Subscription.TenantId)
$authHeader = @{
    'Content-Type'='application/json'
    'Authorization'='Bearer ' + $token.AccessToken
}

# Invoke the REST API
$restUri = 'https://management.azure.com/subscriptions/{subscriptionId}?api-version=2016-06-01'
$response = Invoke-RestMethod -Uri $restUri -Method Get -Headers $authHeader
```

上の **$restUri** 変数の `{subscriptionId}` を置き換えて、サブスクリプションに関する情報を取得します。 $response 変数には `Invoke-RestMethod` コマンドレットの結果が保持されており、[ConvertFrom-json](/powershell/module/microsoft.powershell.utility/convertfrom-json) などのコマンドレットで解析できます。 REST API サービス エンドポイントで**要求本文**が必要な場合は、JSON 形式の変数を `Invoke-RestMethod` の `-Body` パラメーターに提供します。

## <a name="create-a-blueprint"></a>ブループリントを作成する

コンプライアンスの標準的なパターンを定義する最初のステップは、使用可能なリソースからブループリントを作成することです。 この例では、"MyBlueprint" という名前のブループリントを作成して、サブスクリプションのロールとポリシーの割り当てを構成し、リソース グループを追加し、リソース グループの Resource Manager テンプレートとロールの割り当てを作成します。

> [!NOTE]
> REST API を使用すると、"_ブループリント_" オブジェクトが最初に作成されます。 追加する各 "_成果物_" でパラメーターを持つものについては、最初の "_ブループリント_" でパラメーターを事前に定義する必要があります。

各 REST API URI には、独自の値で置き換える必要のある変数があります。

- `{YourMG}` - 実際の管理グループの名前に置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます。

1. 初期 "_ブループリント_" オブジェクトを作成します。 **要求本文**には、ブループリントに関するプロパティ、作成するリソース グループ、および割り当ての間に設定されて、後のステップで追加される成果物によって使用される、ブループリント レベルのパラメーターが含まれています。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "properties": {
             "description": "This blueprint sets tag policy and role assignment on the subscription, creates a ResourceGroup, and deploys a resource template and role assignment to that ResourceGroup.",
             "targetScope": "subscription",
             "parameters": {
                 "storageAccountType": {
                     "type": "string",
                     "metadata": {
                         "displayName": "storage account type.",
                         "description": null
                     }
                 },
                 "tagName": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The name of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "tagValue": {
                     "type": "string",
                     "metadata": {
                         "displayName": "The value of the tag to provide the policy assignment.",
                         "description": null
                     }
                 },
                 "contributors": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Contributor role at the subscription"
                     }
                 },
                 "owners": {
                     "type": "array",
                     "metadata": {
                         "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                     }
                 }
             },
             "resourceGroups": {
                 "storageRG": {
                     "description": "Contains the resource template deployment and a role assignment."
                 }
             }
         }
     }
     ```

1. サブスクリプションでロールの割り当てを追加します。 **要求本文**では成果物の "_種類_" が定義されていて、プロパティはロール定義の識別子と整合しており、プリンシパルの ID は値の配列として渡されます。 次の例では、指定されたロールを付与されたプリンシパル ID が、ブループリントの割り当て中に設定されるパラメーターに対して構成されます。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleContributor?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
             "principalIds": "[parameters('contributors')]"
         }
     }
     ```

1. サブスクリプションでポリシーの割り当てを追加します。 **要求本文**では成果物の "_種類_" が定義されていて、プロパティはポリシーまたはイニシアティブの定義と整合しており、ブループリントの割り当て中に構成される定義済みのブループリント パラメーターを使用するようにポリシー割り当てを構成します。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyTags?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply tag and its default value to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValue": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. サブスクリプションでストレージ タグ (_storageAccountType_ パラメーターを再利用) に対してもう 1 つのポリシー割り当てを追加します。 この追加のポリシー割り当て成果物は、ブループリントで定義されたパラメーターを複数の成果物で使用できることを示します。 この例の **storageAccountType** は、次のステップで作成されるストレージ アカウントに関する情報を提供するリソース グループでタグを設定するために使用されます。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "policyAssignment",
         "properties": {
             "description": "Apply storage tag and the parameter also used by the template to resource groups",
             "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
             "parameters": {
                 "tagName": {
                     "value": "StorageType"
                 },
                 "tagValue": {
                     "value": "[parameters('storageAccountType')]"
                 }
             }
         }
     }
     ```

1. リソース グループにテンプレートを追加します。 Resource Manager テンプレートの**要求本文**では、テンプレートの通常の JSON コンポーネントが含まれ、**properties.resourceGroup** でターゲット リソース グループが定義され、テンプレートに提供することにより **storageAccountType**、**tagName**、および **tagValue** ブループリント パラメーターが再利用されています。 ブループリント パラメーターは **properties.parameters** を定義するとテンプレートで利用することができ、テンプレート JSON 内ではそのキー/値を使用して値を挿入します。 ブループリントとテンプレート パラメーターの名前は同じでもかまいませんが、それぞれがブループリントからテンプレート成果物に渡される方法を示すために別のものにしてあります。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/templateStorage?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "template",
         "properties": {
             "template": {
                 "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
                 "contentVersion": "1.0.0.0",
                 "parameters": {
                     "storageAccountTypeFromBP": {
                         "type": "string",
                         "defaultValue": "Standard_LRS",
                         "allowedValues": [
                             "Standard_LRS",
                             "Standard_GRS",
                             "Standard_ZRS",
                             "Premium_LRS"
                         ],
                         "metadata": {
                             "description": "Storage Account type"
                         }
                     },
                     "tagNameFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag name from blueprint"
                         }
                     },
                     "tagValueFromBP": {
                         "type": "string",
                         "defaultValue": "NotSet",
                         "metadata": {
                             "description": "Tag value from blueprint"
                         }
                     }
                 },
                 "variables": {
                     "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
                 },
                 "resources": [{
                     "type": "Microsoft.Storage/storageAccounts",
                     "name": "[variables('storageAccountName')]",
                     "apiVersion": "2016-01-01",
                     "tags": {
                        "[parameters('tagNameFromBP')]": "[parameters('tagValueFromBP')]"
                     },
                     "location": "[resourceGroup().location]",
                     "sku": {
                         "name": "[parameters('storageAccountTypeFromBP')]"
                     },
                     "kind": "Storage",
                     "properties": {}
                 }],
                 "outputs": {
                     "storageAccountSku": {
                         "type": "string",
                         "value": "[variables('storageAccountName')]"
                     }
                 }
             },
             "resourceGroup": "storageRG",
             "parameters": {
                 "storageAccountTypeFromBP": {
                     "value": "[parameters('storageAccountType')]"
                 },
                 "tagNameFromBP": {
                     "value": "[parameters('tagName')]"
                 },
                 "tagValueFromBP": {
                     "value": "[parameters('tagValue')]"
                 }
             }
         }
     }
     ```

1. リソース グループにロールの割り当てを追加します。 前のロール割り当てエントリと同様に、次の例では、**所有者**ロールに対する定義識別子を使用し、ブループリントとは異なるパラメーターを提供します。

   - REST API URI

     ```http
     PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "kind": "roleAssignment",
         "properties": {
             "resourceGroup": "storageRG",
             "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
             "principalIds": "[parameters('owners')]"
         }
     }
     ```

## <a name="publish-a-blueprint"></a>ブループリントを発行する

これで成果物がブループリントに追加されたので、発行することができます。 発行すると、サブスクリプションに割り当てられて使用できるようになります。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/versions/{BlueprintVersion}?api-version=2017-11-11-preview
  ```

`{BlueprintVersion}` の値は、文字、数字、ハイフンの (スペースまたは他の特殊文字を含まない) 文字列であり、最大長は 20 文字です。 一意で内容がわかるものを使用します (例: **v20180622-135541**)。

## <a name="assign-a-blueprint"></a>ブループリントを割り当てる

REST API を使用してブループリントを発行した後は、それをサブスクリプションに割り当てることができます。 作成したブループリントを、管理グループ階層下のいずれかのサブスクリプションに割り当てます。 **要求本文**では、割り当てるブループリントを指定し、ブループリント定義内のリソース グループに対する名前と場所を提供し、ブループリントで定義されていて 1 つ以上の接続された成果物で使用されるすべてのパラメーターを提供します。

1. Azure Blueprint のサービス プリンシパルに、ターゲット サブスクリプションでの**所有者**ロールを提供します。 AppId は静的 (`f71766dc-90d9-4b7d-bd9d-4499c4331c3f`) ですが、サービス プリンシパル ID はテナントによって異なります。 次の REST API を使用して、テナントに詳細を要求できます。 認可が異なる [Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) を使用しています。

   - REST API URI

     ```http
     GET https://graph.windows.net/{tenantId}/servicePrincipals?api-version=1.6&$filter=appId eq 'f71766dc-90d9-4b7d-bd9d-4499c4331c3f'
     ```

1. サブスクリプションに割り当てることにより、ブループリントのデプロイを実行します。 **contributors** および **owners** パラメーターにはロールの割り当てを付与するプリンシパルの objectId の配列が必要なので、[Azure Active Directory Graph API](../../active-directory/develop/active-directory-graph-api.md) を使用して、独自のユーザー、グループ、またはサービス プリンシパルに対する**要求本文**で使用するための objectId を収集します。

   - REST API URI

     ```http
     PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
     ```

   - 要求本文

     ```json
     {
         "properties": {
             "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint",
             "resourceGroups": {
                 "storageRG": {
                     "name": "StorageAccount",
                     "location": "eastus2"
                 }
             },
             "parameters": {
                 "storageAccountType": {
                     "value": "Standard_GRS"
                 },
                 "tagName": {
                     "value": "CostCenter"
                 },
                 "tagValue": {
                     "value": "ContosoIT"
                 },
                 "contributors": {
                     "value": [
                         "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                         "38833b56-194d-420b-90ce-cff578296714"
                     ]
                 },
                 "owners": {
                     "value": [
                         "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                         "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                     ]
                 }
             }
         },
         "identity": {
             "type": "systemAssigned"
         },
         "location": "westus"
     }
     ```

## <a name="unassign-a-blueprint"></a>ブループリントを割り当て解除する

ブループリントは、不要になったり、更新されたパターン、ポリシー、および設計を持つ新しいブループリントで置き換えられた場合に、サブスクリプションから削除できます。 ブループリントを削除しても、そのブループリントの一部として割り当てられている成果物は後に残されます。 ブループリントの割り当てを削除するには、次の REST API 操作を使用します。

- REST API URI

  ```http
  DELETE https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="delete-a-blueprint"></a>ブループリントを削除する

ブループリント自体を削除するには、次の REST API 操作を使用します。

- REST API URI

  ```http
  DELETE https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2017-11-11-preview
  ```

## <a name="next-steps"></a>次の手順

- [ブループリントのライフサイクル](./concepts/lifecycle.md)を参照する
- [静的および動的パラメーター](./concepts/parameters.md)の使用方法を理解する
- [ブループリントの優先順位](./concepts/sequencing-order.md)のカスタマイズを参照する
- [ブループリントのリソース ロック](./concepts/resource-locking.md)の使用方法を調べる
- [既存の割り当ての更新](./how-to/update-existing-assignments.md)方法を参照する
- ブルー プリントの割り当て時の問題を[一般的なトラブルシューティング](./troubleshoot/general.md)で解決する