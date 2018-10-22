---
title: Implantar recursos na assinatura do Azure | Microsoft Docs
description: Descreve como criar um modelo do Azure Resource Manager que implanta recursos no escopo da assinatura.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: 1d281ebe80c6089c559cfaa77f4875a856566092
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079371"
---
# <a name="deploy-resources-to-an-azure-subscription"></a>Implantar recursos na assinatura do Azure

Normalmente, você deve implantar recursos para um grupo de recursos em sua assinatura do Azure. No entanto, alguns recursos podem ser implantados no nível da sua assinatura do Azure. Esses recursos são aplicados em sua assinatura. [As políticas](../azure-policy/azure-policy-introduction.md), [controle de acesso baseado em função](../role-based-access-control/overview.md), e [Central de Segurança do Azure](../security-center/security-center-intro.md) são serviços que talvez você queira aplicar no nível da assinatura, em vez de nível de grupo de recursos.

Este artigo usa a CLI do Azure e o PowerShell para implantar os modelos.

## <a name="name-and-location-for-deployment"></a>Nome e local para implantação

Ao implantar em sua assinatura, você deverá fornecer um local para a implantação. Você também pode fornecer um nome para a implantação. Se você não especificar um nome para a implantação, o nome do modelo será usado como o nome da implantação. Por exemplo, implantar um modelo chamado **azuredeploy.json** cria um nome de implantação padrão de **azuredeploy**.

O local das implantações em nível de assinatura é imutável. Você não pode criar uma implantação em um local quando há uma implantação existente com o mesmo nome, mas local diferente. Se você receber o código de erro `InvalidDeploymentLocation`, use um nome diferente ou o mesmo local que a implantação anterior para esse nome.

## <a name="using-template-functions"></a>Usando funções de modelo

Para implantações em nível de assinatura, há algumas considerações importantes ao usar funções de modelo:

* A função [resourceGroup()](resource-group-template-functions-resource.md#resourcegroup) **não** é suportada.
* A função [resourceId()](resource-group-template-functions-resource.md#resourceid) é suportada. Use-a para obter a ID de recurso para recursos que são usados em implantações de nível de assinatura. Por exemplo, obtenha a ID de recurso para uma definição de política com `resourceId('Microsoft.Authorization/roleDefinitions/', parameters('roleDefinition'))`
* A funções [reference()](resource-group-template-functions-resource.md#reference) e [list()](resource-group-template-functions-resource.md#list) são suportadas.

## <a name="assign-policy"></a>Atribuir política

O exemplo a seguir atribui uma definição de política existente para a assinatura. Se a política usa parâmetros, você deve fornecê-los como um objeto. Se a política não aceita parâmetros, use o objeto vazio padrão.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "policyDefinitionID": {
            "type": "string"
        },
        "policyName": {
            "type": "string"
        },
        "policyParameters": {
            "type": "object",
            "defaultValue": {}
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "[parameters('policyName')]",
            "apiVersion": "2018-03-01",
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[parameters('policyDefinitionID')]",
                "parameters": "[parameters('policyParameters')]"
            }
        }
    ]
}
```

Para aplicar uma política interna a sua assinatura do Azure, use os seguintes comandos da CLI do Azure. Neste exemplo, a política não tem parâmetros

```azurecli-interactive
# Built-in policy that does not accept parameters
definition=$(az policy definition list --query "[?displayName=='Audit resource location matches resource group location'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=auditRGLocation
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Audit resource location matches resource group location' }

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName auditRGLocation
```

Para aplicar uma política interna a sua assinatura do Azure, use os seguintes comandos da CLI do Azure. Neste exemplo, a política tem parâmetros.

```azurecli-interactive
# Built-in policy that accepts parameters
definition=$(az policy definition list --query "[?displayName=='Allowed locations'].id" --output tsv)

az deployment create \
  -n policyassign \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json \
  --parameters policyDefinitionID=$definition policyName=setLocation policyParameters="{'listOfAllowedLocations': {'value': ['westus']} }"
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell-interactive
$definition = Get-AzureRmPolicyDefinition | Where-Object { $_.Properties.DisplayName -eq 'Allowed locations' }

$locations = @("westus", "westus2")
$policyParams =@{listOfAllowedLocations = @{ value = $locations}}

New-AzureRmDeployment `
  -Name policyassign `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policyassign.json `
  -policyDefinitionID $definition.PolicyDefinitionId `
  -policyName setLocation `
  -policyParameters $policyParams
```

## <a name="define-and-assign-policy"></a>Definir e atribuir política

Você pode [definir](../azure-policy/policy-definition.md) e atribuir uma política no mesmo modelo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/policyDefinitions",
            "name": "locationpolicy",
            "apiVersion": "2018-05-01",
            "properties": {
                "policyType": "Custom",
                "parameters": {},
                "policyRule": {
                    "if": {
                        "field": "location",
                        "equals": "northeurope"
                    },
                    "then": {
                        "effect": "deny"
                    }
                }
            }
        },
        {
            "type": "Microsoft.Authorization/policyAssignments",
            "name": "location-lock",
            "apiVersion": "2018-05-01",
            "dependsOn": [
                "locationpolicy"
            ],
            "properties": {
                "scope": "[subscription().id]",
                "policyDefinitionId": "[resourceId('Microsoft.Authorization/policyDefinitions', 'locationpolicy')]"
            }
        }
    ]
}
```

Para criar a definição de política em sua assinatura e aplicá-la à assinatura, use o seguinte comando CLI.

```azurecli-interactive
az deployment create \
  -n definePolicy \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell-interactive
New-AzureRmDeployment `
  -Name definePolicy `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/policydefineandassign.json
```

## <a name="assign-role"></a>Atribuir função

O exemplo a seguir atribui uma função a um usuário ou grupo.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string"
        },
        "roleDefinitionId": {
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "name": "[guid(parameters('principalId'), deployment().name)]",
            "apiVersion": "2017-09-01",
            "properties": {
                "roleDefinitionId": "[resourceId('Microsoft.Authorization/roleDefinitions', parameters('roleDefinitionId'))]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

Para atribuir um grupo do Active Directory a uma função para sua assinatura, use os seguintes comandos da CLI do Azure.

```azurecli-interactive
# Get ID of the role you want to assign
role=$(az role definition list --name Contributor --query [].name --output tsv)

# Get ID of the AD group to assign the role to
principalid=$(az ad group show --group demogroup --query objectId --output tsv)

az deployment create \
  -n demoRole \
  -l southcentralus \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json \
  --parameters principalId=$principalid roleDefinitionId=$role
```

Para implantar este modelo com o PowerShell, use:

```azurepowershell-interactive
$role = Get-AzureRmRoleDefinition -Name Contributor

$adgroup = Get-AzureRmADGroup -DisplayName demogroup

New-AzureRmDeployment `
  -Name demoRole `
  -Location southcentralus `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/roleassign.json `
  -roleDefinitionId $role.Id `
  -principalId $adgroup.Id
```

## <a name="next-steps"></a>Próximas etapas
* Para obter um exemplo de implantação de configurações de workspace para a Central de Segurança do Azure, consulte [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
* Para criar um grupo de recursos, consulte [Criar grupos de recurso nos modelos do Azure Resource Manager](create-resource-group-in-template.md).
* Para saber mais sobre a criação de modelos do Gerenciador de Recursos do Azure, consulte [Criando modelos](resource-group-authoring-templates.md). 
* Para obter uma lista das funções disponíveis em um modelo, consulte [Funções de modelo](resource-group-template-functions.md).

