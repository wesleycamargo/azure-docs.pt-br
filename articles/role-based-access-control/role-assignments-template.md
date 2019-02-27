---
title: Gerenciar o acesso a recursos do Azure usando modelos do Azure Resource Manager e RBAC | Microsoft Docs
description: Saiba como gerenciar o acesso a recursos do Azure para usuários, grupos e aplicativos usando RBAC (controle de acesso baseado em função) e modelo do Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/02/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 537ee35e96a41cd02605319e244d39c6567c3bf1
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2019
ms.locfileid: "56337196"
---
# <a name="manage-access-to-azure-resources-using-rbac-and-azure-resource-manager-templates"></a>Gerenciar o acesso a recursos do Azure usando modelos do Azure Resource Manager e RBAC

O [RBAC (controle de acesso baseado em função)](overview.md) serve para gerenciar o acesso aos recursos do Azure. Além de usar o Azure PowerShell ou a CLI do Azure, você pode gerenciar o acesso aos recursos do Azure usando RBAC e [modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Os modelos poderão ser úteis se você precisar implantar recursos de maneira consistente e repetida. Este artigo descreve como gerenciar o acesso usando modelos e RBAC.

## <a name="example-template-to-create-a-role-assignment"></a>Modelo de exemplo para criar uma atribuição de função

No RBAC, para conceder acesso, você cria uma atribuição de função. O modelo a seguir demonstra:
- Como atribuir uma função a um usuário, grupo ou aplicativo no escopo do grupo de recursos
- Como especificar as funções de Proprietário, Colaborador e Leitor como um parâmetro

Para usar o modelo, você deve especificar as seguintes entradas:
- O nome de um grupo de recursos
- O identificador exclusivo de um usuário, grupo ou aplicativo para o qual atribuir a função
- A função a atribuir
- Um identificador exclusivo que será usado para a atribuição de função

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "principalId": {
      "type": "string",
      "metadata": {
        "description": "The principal to assign the role to"
      }
    },
    "builtInRoleType": {
      "type": "string",
      "allowedValues": [
        "Owner",
        "Contributor",
        "Reader"
      ],
      "metadata": {
        "description": "Built-in role to assign"
      }
    },
    "roleNameGuid": {
      "type": "string",
      "metadata": {
        "description": "A new GUID used to identify the role assignment"
      }
    }
  },
  "variables": {
    "Owner": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '8e3af657-a8ff-443c-a75c-2fe8c4bcb635')]",
    "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]",
    "scope": "[resourceGroup().id]"
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/roleAssignments",
      "apiVersion": "2017-05-01",
      "name": "[parameters('roleNameGuid')]",
      "properties": {
        "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
        "principalId": "[parameters('principalId')]",
        "scope": "[variables('scope')]"
      }
    }
  ]
}
```

O exemplo a seguir mostra uma atribuição de função de Leitor a um usuário após a implantação do modelo.

![Atribuição de função usando um modelo](./media/role-assignments-template/role-assignment-template.png)

## <a name="deploy-template-using-azure-powershell"></a>Implantar o modelo usando o Azure PowerShell

[!INCLUDE [az-powershell-update](../../includes/updated-for-az.md)]

Para implantar o modelo anterior usando o Azure PowerShell, siga estas etapas.

1. Crie um novo arquivo chamado rbac-rg.json rbac e copie o modelo anterior.

1. Entre no [Azure PowerShell](/powershell/azure/authenticate-azureps).

1. Obtenha o identificador exclusivo de um usuário, grupo ou aplicativo. Por exemplo, você pode usar o comando [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para listar os usuários do Azure AD.

    ```azurepowershell
    Get-AzADUser
    ```

1. Use uma ferramenta de GUID para gerar um identificador exclusivo que será usado para a atribuição de função. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

1. Crie um grupo de recursos de exemplo.

    ```azurepowershell
    New-AzResourceGroup -Name ExampleGroup -Location "Central US"
    ```

1. Use o comando [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) para iniciar a implantação.

    ```azurepowershell
    New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    ```

    Você é solicitado a especificar os parâmetros necessários. A seguir, é mostrado um exemplo da saída.

    ```Output
    PS /home/user> New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile rbac-rg.json
    
    cmdlet New-AzResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    principalId: 22222222-2222-2222-2222-222222222222
    builtInRoleType: Reader
    roleNameGuid: 11111111-1111-1111-1111-111111111111
    
    DeploymentName          : rbac-rg
    ResourceGroupName       : ExampleGroup
    ProvisioningState       : Succeeded
    Timestamp               : 7/17/2018 7:46:32 PM
    Mode                    : Incremental
    TemplateLink            :
    Parameters              :
                              Name             Type                       Value
                              ===============  =========================  ==========
                              principalId      String                     22222222-2222-2222-2222-222222222222
                              builtInRoleType  String                     Reader
                              roleNameGuid     String                     11111111-1111-1111-1111-111111111111
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="deploy-template-using-the-azure-cli"></a>Implantar o modelo usando a CLI do Azure

Para implantar o modelo anterior usando a CLI do Azure, siga estas etapas.

1. Crie um novo arquivo chamado rbac-rg.json rbac e copie o modelo anterior.

1. Entre na [CLI do Azure](/cli/azure/authenticate-azure-cli).

1. Obtenha o identificador exclusivo de um usuário, grupo ou aplicativo. Por exemplo, você pode usar o comando [az ad user list](/cli/azure/ad/user#az-ad-user-list) para listar os usuários do Azure AD.

    ```azurecli
    az ad user list
    ```

1. Use uma ferramenta de GUID para gerar um identificador exclusivo que será usado para a atribuição de função. O identificador tem o formato: `11111111-1111-1111-1111-111111111111`

1. Crie um grupo de recursos de exemplo.

    ```azurecli
    az group create --name ExampleGroup --location "Central US"
    ```

1. Use o comando [az group deployment create](/cli/azure/group/deployment#az-group-deployment-create) para iniciar a implantação.

    ```azurecli
    az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    ```

    Você é solicitado a especificar os parâmetros necessários. A seguir, é mostrado um exemplo da saída.

    ```Output
    C:\Azure\Templates>az group deployment create --resource-group ExampleGroup --template-file rbac-rg.json
    Please provide string value for 'principalId' (? for help): 22222222-2222-2222-2222-222222222222
    Please provide string value for 'builtInRoleType' (? for help):
     [1] Owner
     [2] Contributor
     [3] Reader
    Please enter a choice [1]: 3
    Please provide string value for 'roleNameGuid' (? for help): 11111111-1111-1111-1111-111111111111
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/rbac-rg",
      "name": "rbac-rg",
      "properties": {
        "additionalProperties": {
          "duration": "PT9.5323924S",
          "outputResources": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ExampleGroup/providers/Microsoft.Authorization/roleAssignments/11111111-1111-1111-1111-111111111111",
              "resourceGroup": "ExampleGroup"
            }
          ],
          "templateHash": "0000000000000000000"
        },
        "correlationId": "33333333-3333-3333-3333-333333333333",
        "debugSetting": null,
        "dependencies": [],
        "mode": "Incremental",
        "outputs": null,
        "parameters": {
          "builtInRoleType": {
            "type": "String",
            "value": "Reader"
          },
          "principalId": {
            "type": "String",
            "value": "22222222-2222-2222-2222-222222222222"
          },
          "roleNameGuid": {
            "type": "String",
            "value": "11111111-1111-1111-1111-111111111111"
          }
        },
        "parametersLink": null,
        "providers": [
          {
            "id": null,
            "namespace": "Microsoft.Authorization",
            "registrationState": null,
            "resourceTypes": [
              {
                "aliases": null,
                "apiVersions": null,
                "locations": [
                  null
                ],
                "properties": null,
                "resourceType": "roleAssignments"
              }
            ]
          }
        ],
        "provisioningState": "Succeeded",
        "template": null,
        "templateLink": null,
        "timestamp": "2018-07-17T19:00:31.830904+00:00"
      },
      "resourceGroup": "ExampleGroup"
    }
    ```
    
## <a name="next-steps"></a>Próximas etapas

- [Início Rápido: Criar e implantar modelos do Azure Resource Manager usando o portal do Azure](../azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal.md)
- [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)
- [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?term=rbac)
