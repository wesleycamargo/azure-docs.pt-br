---
title: "Atribuir e gerenciar políticas de recursos do Azure | Microsoft Docs"
description: "Descreve como aplicar as políticas de recursos do Azure a assinaturas e grupos de recursos e como exibir políticas de recursos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/30/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: fd15cbc8f6efa788ddaeaee4ab39ce1410c4be5a
ms.lasthandoff: 03/31/2017


---
# <a name="assign-and-manage-resource-policies"></a>Atribuir e gerenciar políticas de recursos

Para implementar uma política, você deve executar três etapas:

1. Defina a regra de política com JSON.
2. Crie uma definição de política em sua assinatura no JSON que você criou na etapa anterior. Essa etapa disponibiliza a política para atribuição, mas não aplica as regras à sua assinatura.
3. Atribua a política a um escopo (como uma assinatura ou um grupo de recursos). As regras da política agora são impostas.

O Azure fornece algumas políticas predefinidas que podem reduzir o número de políticas que você precisa definir. Se uma política predefinida servir para seu cenário, pule as primeiras duas etapas e atribua a política predefinida a um escopo.

Este artigo ressalta as etapas para criação de uma definição de política e atribuição dessa definição a um escopo por meio da API REST, do PowerShell ou da CLI do Azure. Se preferir usar o portal para atribuir políticas, consulte [Usar o portal do Azure para atribuir e gerenciar políticas de recurso](resource-manager-policy-portal.md). Este artigo não tem como foco a sintaxe para criação da definição de política. Para obter informações sobre a sintaxe da política, confira [Visão geral da política de recursos](resource-manager-policy.md).

## <a name="rest-api"></a>API REST

### <a name="create-policy-definition"></a>Criar definição de política

Você pode criar uma política com a [API REST para Definições de Política](/rest/api/resources/policydefinitions). A API REST permite que você crie e exclua as definições de políticas e obtenha informações sobre as definições existentes.

Para criar uma definição de política, execute:

```HTTP
PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Inclua um corpo de solicitação semelhante ao exemplo a seguir:

```json
{
  "properties": {
    "parameters": {
      "allowedLocations": {
        "type": "array",
        "metadata": {
          "description": "The list of locations that can be specified when deploying resources",
          "strongType": "location",
          "displayName": "Allowed locations"
        }
      }
    },
    "displayName": "Allowed locations",
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
    "policyRule": {
      "if": {
        "not": {
          "field": "location",
          "in": "[parameters('allowedLocations')]"
        }
      },
      "then": {
        "effect": "deny"
      }
    }
  }
}
```

### <a name="assign-policy"></a>Atribuir política

Você pode aplicar a definição de política no escopo desejado por meio da [API REST para atribuições de política](/rest/api/resources/policyassignments). A API REST permite que você crie e exclua as atribuições de políticas e obtenha informações sobre as atribuições existentes.

Para criar uma atribuição de política, execute:

```HTTP
PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}
```

A {Atribuição da política} é o nome da atribuição da política.

Inclua um corpo de solicitação semelhante ao exemplo a seguir:

```json
{
  "properties":{
    "displayName":"West US only policy assignment on the subscription ",
    "description":"Resources can only be provisioned in West US regions",
    "parameters": {
      "allowedLocations": { "value": ["northeurope", "westus"] }
     },
    "policyDefinitionId":"/subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{definition-name}",
      "scope":"/subscriptions/{subscription-id}"
  },
}
```

### <a name="view-policy"></a>Exibir política
Para obter uma política, use a operação [Obter definição de política](https://docs.microsoft.com/rest/api/resources/policydefinitions#PolicyDefinitions_Get).

### <a name="get-aliases"></a>Obter aliases
Você pode recuperar aliases por meio da API REST:

```HTTP
GET /subscriptions/{id}/providers?$expand=resourceTypes/aliases&api-version=2015-11-01
```

O exemplo a seguir mostra uma definição de um alias. Como é possível ver, um alias define caminhos em diferentes versões de API, mesmo quando há uma alteração de nome da propriedade. 

```json
"aliases": [
    {
      "name": "Microsoft.Storage/storageAccounts/sku.name",
      "paths": [
        {
          "path": "properties.accountType",
          "apiVersions": [
            "2015-06-15",
            "2015-05-01-preview"
          ]
        },
        {
          "path": "sku.name",
          "apiVersions": [
            "2016-01-01"
          ]
        }
      ]
    }
]
```

## <a name="powershell"></a>PowerShell

### <a name="create-policy-definition"></a>Criar definição de política
Você pode criar uma definição de política usando o cmdlet `New-AzureRmPolicyDefinition`. O exemplo a seguir cria uma definição de política para permitir recursos somente na Europa Setentrional e Ocidental.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy '{
   "if": {
     "not": {
       "field": "location",
       "in": "[parameters(''allowedLocations'')]"
     }
   },
   "then": {
     "effect": "deny"
   }
 }' -Parameter '{
     "allowedLocations": {
       "type": "array",
       "metadata": {
         "description": "An array of permitted locations for resources.",
         "strongType": "location",
         "displayName": "List of locations"
       }
     }
 }'
```            

A saída é armazenada em um objeto `$policy`, que é usado durante a atribuição da política. 

Em vez de especificar o JSON como um parâmetro, você pode fornecer o caminho para um arquivo .json que contém a regra de política.

```powershell
$policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain regions" -Policy "c:\policies\storageskupolicy.json"
```

### <a name="assign-policy"></a>Atribuir política

Aplique a política no escopo desejado usando o cmdlet `New-AzureRmPolicyAssignment`:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
$array = @("West US", "West US 2")
$param = @{"allowedLocations"=$array}
New-AzureRMPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId -PolicyDefinition $policy -PolicyParameterObject $param
```

### <a name="view-policies"></a>Exibir políticas

Para obter todas as atribuições de política, use:

```powershell
Get-AzureRmPolicyAssignment
```

Para obter uma política específica, use:

```powershell
$rg = Get-AzureRmResourceGroup -Name "ExampleGroup"
(Get-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope $rg.ResourceId
```

Para exibir a regra de política de uma definição de política, use:

```powershell
(Get-AzureRmPolicyDefinition -Name regionPolicyDefinition).Properties.policyRule | ConvertTo-Json
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, use:

```powershell
Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-20"></a>CLI do Azure 2.0

### <a name="create-policy-definition"></a>Criar definição de política

Você pode criar uma definição de política usando a CLI do Azure 2.0 com o comando de definição de política. O exemplo a seguir cria uma política para permitir recursos somente na Europa Setentrional e na Ocidental.

```azurecli
az policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --rules '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

### <a name="assign-policy"></a>Atribuir política

Você pode aplicar a política para o escopo desejado usando o comando de atribuição de política:

```azurecli
az policy assignment create --name regionPolicyAssignment --policy regionPolicyDefinition --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

### <a name="view-policy-definition"></a>Exibir definição de política
Para obter uma definição de política, use o seguinte comando:

```azurecli
az policy definition show --name regionPolicyAssignment
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, use:

```azurecli
az policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="azure-cli-10"></a>CLI 1.0 do Azure

### <a name="create-policy-definition"></a>Criar definição de política

Você pode criar uma definição de política usando a CLI do Azure com o comando de definição de política. O exemplo a seguir cria uma política para permitir recursos somente na Europa Setentrional e na Ocidental.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy-string '{    
  "if" : {
    "not" : {
      "field" : "location",
      "in" : ["northeurope" , "westeurope"]
    }
  },
  "then" : {
    "effect" : "deny"
  }
}'    
```

É possível especificar o caminho para um arquivo .json contendo a política em vez de especificar a política embutida.

```azurecli
azure policy definition create --name regionPolicyDefinition --description "Policy to allow resource creation only in certain regions" --policy "path-to-policy-json-on-disk"
```

### <a name="assign-policy"></a>Atribuir política

Você pode aplicar a política para o escopo desejado usando o comando de atribuição de política:

```azurecli
azure policy assignment create --name regionPolicyAssignment --policy-definition-id /subscriptions/{subscription-id}/providers/Microsoft.Authorization/policyDefinitions/{policy-name} --scope    /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

O escopo aqui é o nome do grupo de recursos que você especificar. Se o valor do parâmetro policy-definition-id for desconhecido, será possível obtê-lo por meio da CLI do Azure. 

```azurecli
azure policy definition show {policy-name}
```

### <a name="view-policy"></a>Exibir política
Para obter uma política, use o seguinte comando:

```azurecli
azure policy definition show {definition-name} --json
```

### <a name="remove-policy-assignment"></a>Remover atribuição de política 

Para remover uma atribuição de política, use:

```azurecli
azure policy assignment delete --name regionPolicyAssignment --scope /subscriptions/{subscription-id}/resourceGroups/{resource-group-name}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter orientação sobre como as empresas podem usar o Resource Manager para gerenciar assinaturas de forma eficaz, consulte [Azure enterprise scaffold – controle de assinatura prescritivas](resource-manager-subscription-governance.md).


