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
ms.date: 12/09/2016
ms.author: tomfitz
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 0bf872a44b8ed7cae53d2659aa7be878902130e9
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Key Vault para passar um valor de parâmetro seguro durante a implantação

Quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível recuperar o valor de um [Azure Key Vault](../key-vault/key-vault-whatis.md). Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. Você não precisa inserir manualmente o valor para o segredo toda vez que implanta os recursos. O cofre de chaves pode existir em uma assinatura diferente que o grupo de recursos que está sendo implantado. Ao fazer referência ao Key Vault, inclua a ID da assinatura.

Este tópico mostra como criar um cofre de chaves e o segredo, configurar o acesso ao segredo para um modelo do Resource Manager e passar o segredo como um parâmetro. Se você já tiver um cofre de chaves e o segredo, mas precisar verificar o acesso ao modelo e ao usuário, acesse a seção [Habilitar o acesso ao segredo](#enable-access-to-the-secret). Se você já tiver o cofre de chaves e o segredo e tiver certeza de que ele está configurado para o acesso ao modelo e ao usuário, acesse a seção [Fazer referência a um segredo com uma ID estática](#reference-a-secret-with-static-id). 

## <a name="deploy-a-key-vault-and-secret"></a>Implantar um cofre da chave e segredo

É possível implantar um cofre de chaves e o segredo por meio de um modelo do Resource Manager. Para obter um exemplo, consulte [Modelo do cofre de chaves](resource-manager-template-keyvault.md) e [Modelo do segredo do cofre de chaves](resource-manager-template-keyvault-secret.md). Ao criar o cofre de chaves, defina a propriedade **enabledForTemplateDeployment** como **true** para que ela possa ser referenciada de outros modelos do Resource Manager. 

Se preferir, você pode criar o cofre de chaves e o segredo por meio do portal do Azure. 

1. Selecione **Novo** -> **Segurança + Identidade** -> **Key Vault**.

   ![criar novo cofres de chaves](./media/resource-manager-keyvault-parameter/new-key-vault.png)

2. Forneça valores para o cofre de chaves. Por enquanto, você pode ignorar as configurações **Políticas de acesso** e **Política de acesso avançada**. Essas configurações são abordadas na seção. Selecione **Criar**.

   ![definir cofre de chaves](./media/resource-manager-keyvault-parameter/create-key-vault.png)

3. Agora você tem um cofre de chaves. Selecione esse cofre de chaves.

4. Na folha do cofre de chaves, selecione **Segredos**.

   ![selecionar segredos](./media/resource-manager-keyvault-parameter/select-secret.png)

5. Selecione **Adicionar**.

   ![escolher adicionar](./media/resource-manager-keyvault-parameter/add-secret.png)

6. Selecione **Manual** para obter as opções de upload. Forneça um nome e valor para o segredo. Selecione **Criar**.

   ![fornecer o segredo](./media/resource-manager-keyvault-parameter/provide-secret.png)

Você criou o cofre de chaves e o segredo.

## <a name="enable-access-to-the-secret"></a>Habilitar o acesso ao segredo

Se você estiver usando um cofre de chaves novo ou existente, verifique se o usuário que implanta o modelo pode acessar o segredo. O usuário que implanta um modelo que faz referência a um segredo deve ter a permissão `Microsoft.KeyVault/vaults/deploy/action` do cofre de chaves. Ambas as funções [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) e [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) concedem esse acesso. Você também pode criar uma [função personalizada](../active-directory/role-based-access-control-custom-roles.md) que concede essa permissão e adicionar o usuário a essa função. Além disso, é necessário conceder ao Resource Manager a capacidade de acessar o cofre de chaves durante a implantação.

É possível verificar ou realizar essas etapas por meio do portal.

1. Selecione **IAM (Controle de acesso)**.

   ![selecionar o controle de acesso](./media/resource-manager-keyvault-parameter/select-access-control.png)

2. Se a conta que você pretende usar para a implantação de modelos ainda não for um Proprietário ou Colaborador (nem tiver sido adicionada a uma função personalizada que concede a permissão `Microsoft.KeyVault/vaults/deploy/action`), selecione **Adicionar**

   ![adicionar usuário](./media/resource-manager-keyvault-parameter/add-user.png)

3. Selecione a função Colaborador ou Proprietário e pesquise a identidade a ser atribuída a essa função. Selecione **OK** para concluir a adição da identidade à função.

   ![adicionar usuário](./media/resource-manager-keyvault-parameter/search-user.png)

4. Para habilitar o acesso de um modelo durante a implantação, selecione **Controle de acesso avançado**. Selecione a opção **Habilitar acesso ao Azure Resource Manager para implantação de modelos**.

   ![habilitar acesso a modelos](./media/resource-manager-keyvault-parameter/select-template-access.png)

## <a name="reference-a-secret-with-static-id"></a>Fazer referência a um segredo com ID estática

Você faz referência ao segredo em um **arquivo de parâmetros (não no modelo)** que passa valores para o modelo. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chave e o nome do segredo. No exemplo a seguir, o segredo do cofre de chaves já deve existir e você fornece um valor estático para sua ID de recurso.

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

No modelo, o parâmetro que aceita o segredo deve ser uma **securestring**. O exemplo a seguir mostra as seções relevantes de um modelo que implanta um SQL Server que exige uma senha de administrador.

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
* Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).


