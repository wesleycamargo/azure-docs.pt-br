---
title: Segredo do Cofre de Chaves com o modelo do Azure Resource Manager | Microsoft Docs
description: "Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/30/2017
ms.author: tomfitz
ms.openlocfilehash: 7e02bd9c6130ef8b120282fafa9f0ee517890d0d
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação

Quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível recuperar o valor de um [Azure Key Vault](../key-vault/key-vault-whatis.md). Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. Você não precisa inserir manualmente o valor para o segredo toda vez que implanta os recursos. O cofre de chaves pode existir em uma assinatura diferente que o grupo de recursos que está sendo implantado. Ao fazer referência ao Key Vault, inclua a ID da assinatura.

Ao criar o cofre de chaves, defina a propriedade *enabledForTemplateDeployment* para *true*. Ao definir esse valor como true, é possível permitir acesso de modelos do Gerenciador de Recursos durante a implantação.

## <a name="deploy-a-key-vault-and-secret"></a>Implantar um cofre da chave e segredo

Para criar um cofre de chaves e um segredo, use o CLI do Azure ou o PowerShell. Observe que o cofre de chaves está habilitado para implantação de modelo. 

Para a CLI do Azure, use:

```azurecli-interactive
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create \
  --name $vaultname \
  --resource-group examplegroup \
  --location 'South Central US' \
  --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

Para o PowerShell, use:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault `
  -VaultName $vaultname `
  -ResourceGroupName examplegroup `
  -Location "South Central US" `
  -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>Habilitar o acesso ao segredo

Se você estiver usando um cofre de chaves novo ou existente, verifique se o usuário que implanta o modelo pode acessar o segredo. O usuário que implanta um modelo que faz referência a um segredo deve ter a permissão `Microsoft.KeyVault/vaults/deploy/action` do cofre de chaves. Ambas as funções [Proprietário](../active-directory/role-based-access-built-in-roles.md#owner) e [Colaborador](../active-directory/role-based-access-built-in-roles.md#contributor) concedem esse acesso.

## <a name="reference-a-secret-with-static-id"></a>Fazer referência a um segredo com ID estática

O modelo que recebe um segredo do cofre de chaves é como qualquer outro modelo. Isso ocorre porque **você faz referência ao Cofre de chaves no arquivo de parâmetro, não no modelo.** A imagem a seguir mostra como o arquivo de parâmetro faz referência ao segredo e passa o valor para o modelo.

![ID estática](./media/resource-manager-keyvault-parameter/statickeyvault.png)

Por exemplo, o [modelo a seguir](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) implanta um banco de dados SQL que inclui uma senha de administrador. O parâmetro de senha é definido como uma cadeia de caracteres segura. Mas, o modelo não especifica de onde vem esse valor.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adminLogin": {
      "type": "string"
    },
    "adminPassword": {
      "type": "securestring"
    },
    "sqlServerName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "name": "[parameters('sqlServerName')]",
      "type": "Microsoft.Sql/servers",
      "apiVersion": "2015-05-01-preview",
      "location": "[resourceGroup().location]",
      "tags": {},
      "properties": {
        "administratorLogin": "[parameters('adminLogin')]",
        "administratorLoginPassword": "[parameters('adminPassword')]",
        "version": "12.0"
      }
    }
  ],
  "outputs": {
  }
}
```

Agora, crie um arquivo de parâmetro para o modelo anterior. No arquivo de parâmetro, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, referenciar o segredo do cofre de chave. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chave e o nome do segredo. No [arquivo de parâmetro a seguir](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.parameters.json), o segredo do cofre de chaves já deve existir e você fornece um valor estático para sua ID de recurso. Copie esse arquivo localmente e defina a ID de assinatura, o nome do cofre e o nome do SQL Server.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminLogin": {
            "value": "exampleadmin"
        },
        "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/<subscription-id>/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/<vault-name>"
              },
              "secretName": "examplesecret"
            }
        },
        "sqlServerName": {
            "value": "<your-server-name>"
        }
    }
}
```

Agora, implante o modelo e passe o arquivo de parâmetro. É possível usar o modelo de exemplo do GitHub, mas é necessário usar um arquivo de parâmetro local com os valores definidos para o seu ambiente.

Para a CLI do Azure, use:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Fazer referência a um segredo com ID dinâmica

A seção anterior mostrou como passar uma ID de recurso estático para o segredo do cofre de chaves. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Nesse caso, não é possível codificar a ID de recurso no arquivo de parâmetros. Infelizmente, você não pode gerar dinamicamente a ID do recurso no arquivo de parâmetros, pois não há permissão para expressões de modelo no arquivo de parâmetros.

Para gerar dinamicamente a ID de recurso para um segredo do cofre de chaves, você deve mover o recurso que precisa do segredo para um modelo vinculado. No modelo pai, adicione o modelo vinculado e passe um parâmetro que contém a ID de recurso gerada dinamicamente. A imagem a seguir mostra como um parâmetro no modelo vinculado faz referência ao segredo.

![ID dinâmica](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

O modelo vinculado deve estar disponível por meio de um URI externo. Normalmente, o modelo é adicionado a uma conta de armazenamento e acessado por meio do URI, como `https://<storage-name>.blob.core.windows.net/templatecontainer/sqlserver.json`.

O [seguinte modelo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json) cria dinamicamente a ID do cofre de chaves e a passa como um parâmetro. Ela está vinculada a um [modelo de exemplo](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/keyvaultparameter/sqlserver.json) no GitHub.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "vaultResourceGroup": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      },
      "adminLogin": {
        "type": "string"
      },
      "sqlServerName": {
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
          "uri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(subscription().subscriptionId,  parameters('vaultResourceGroup'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          },
          "adminLogin": { "value": "[parameters('adminLogin')]" },
          "sqlServerName": {"value": "[parameters('sqlServerName')]"}
        }
      }
    }],
    "outputs": {}
}
```

Implante o modelo anterior e forneça valores para os parâmetros. É possível usar o modelo de exemplo do GitHub, mas é necessário fornecer os valores de parâmetro para o seu ambiente.

Para a CLI do Azure, use:

```azurecli-interactive
az group create --name datagroup --location "South Central US"
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json \
    --parameters vaultName=<your-vault> vaultResourceGroup=examplegroup secretName=examplesecret adminLogin=exampleadmin sqlServerName=<server-name>
```

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location "South Central US"
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver-dynamic-id.json `
  -vaultName <your-vault> -vaultResourceGroup examplegroup -secretName examplesecret -adminLogin exampleadmin -sqlServerName <server-name>
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
