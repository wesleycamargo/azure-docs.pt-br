---
title: Segredo do Cofre de Chaves com o modelo do Resource Manager | Microsoft Docs
description: "Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/11/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: fa688748be96aa614b46a218e19a0e771b908baf


---
# <a name="pass-secure-values-during-deployment"></a>Transmitir valores seguros durante a implantação

Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um [Cofre de Chaves do Azure](../key-vault/key-vault-whatis.md) e fazer referência ao segredo em seu arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. Você não precisa inserir manualmente o valor para o segredo toda vez que implanta os recursos.

## <a name="deploy-a-key-vault-and-secret"></a>Implantar um cofre da chave e segredo

Para saber mais sobre a implantação de um cofre de chaves e um segredo, veja [Esquema do cofre de chaves](resource-manager-template-keyvault.md) e [Esquema do segredo do cofre de chaves](resource-manager-template-keyvault-secret.md). Ao criar o cofre de chaves, defina a propriedade **enabledForTemplateDeployment** como **true** para que ela possa ser referenciada de outros modelos do Resource Manager. 

## <a name="reference-a-secret-with-static-id"></a>Fazer referência a um segredo com ID estática

Você faz referência ao segredo em um arquivo de parâmetros que transmite valores para seu modelo. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chave e o nome do segredo. No exemplo a seguir, o segredo do cofre de chaves já deve existir e você fornece um valor estático para sua ID de recurso.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "sqlsvrAdminLoginPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          },
          "secretName": "adminPassword"
        }
      },
      "sqlsvrAdminLogin": {
        "value": "exampleadmin"
      }
    }
}
```

O parâmetro que aceita o segredo deve ser um **securestring**. O exemplo a seguir mostra as seções relevantes de um modelo que implanta um SQL Server que exige uma senha de administrador.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "sqlsvrAdminLogin": {
            "type": "string",
            "minLength": 4
        },
        "sqlsvrAdminLoginPassword": {
            "type": "securestring"
        },
        ...
    },
    "resources": [
        {
          "name": "[variables('sqlsvrName')]",
          "type": "Microsoft.Sql/servers",
          "location": "[resourceGroup().location]",
          "apiVersion": "2014-04-01-preview",
          "properties": {
              "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
              "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
          },
          ...
        }
    ],
    "variables": {
        "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
    },
    "outputs": { }
}
```

O usuário implantando um modelo que faz referência a um segredo deve ter a permissão **Microsoft.KeyVault/vaults/deploy/action** para o cofre de chaves. Ambas as funções [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) e [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) concedem esse acesso. Você também pode criar uma [função personalizada](../active-directory/role-based-access-control-custom-roles.md) que concede essa permissão e adicionar o usuário a essa função.

## <a name="reference-a-secret-with-dynamic-id"></a>Fazer referência a um segredo com ID dinâmica

A seção anterior mostrou como passar uma ID de recurso estático para o segredo do cofre de chaves. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Nesse caso, não é possível codificar a ID de recurso no arquivo de parâmetros. Infelizmente, você não pode gerar dinamicamente a ID do recurso no arquivo de parâmetros, pois não há permissão para expressões de modelo no arquivo de parâmetros.

Para gerar dinamicamente a ID de recurso para um segredo de cofre de chaves, você deve mover o recurso que precisa do segredo em um modelo aninhado. No modelo mestre, adicione o modelo aninhado e passe um parâmetro que contém a ID de recurso gerada dinamicamente.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter informações sobre como usar um cofre de chave com uma máquina virtual, consulte [Considerações de segurança para o Azure Resource Manager](best-practices-resource-manager-security.md).
* Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).




<!--HONumber=Nov16_HO3-->


