---
title: Segredo do Cofre de Chaves com o modelo do Azure Resource Manager | Microsoft Docs
description: Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: tomfitz
ms.openlocfilehash: a885fda23bb76091705ebe388f40a6eae7b56416
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/16/2019
ms.locfileid: "54351502"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação

Quando você precisa passar um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível recuperar o valor de um [Azure Key Vault](../key-vault/key-vault-whatis.md). Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. O cofre de chaves pode existir em uma assinatura diferente que o grupo de recursos que está sendo implantado.

## <a name="deploy-a-key-vault-and-secret"></a>Implantar um cofre da chave e segredo

Para criar um cofre de chaves e um segredo, use o CLI do Azure ou o PowerShell. `enabledForTemplateDeployment`é uma propriedade do Key Vault. Para acessar os segredos dentro deste Key Vault da implantação do Resource Manager, `enabledForTemplateDeployment` deve ser `true`. 

O exemplo de script a seguir do Azure PowerShell e da CLI do Azure demonstra como criar um Key Vault e um segredo.

Para a CLI do Azure, use:

```azurecli-interactive
keyVaultName='{your-unique-vault-name}'
resourceGroupName='{your-resource-group-name}'
location='centralus'
userPrincipalName='{your-email-address-associated-with-your-subscription}'

# Create a resource group
az group create --name $resourceGroupName --location $location

# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list

# Create a secret with the name, vmAdminPassword
password=$(openssl rand -base64 32)
echo $password
az keyvault secret set --vault-name $keyVaultName --name 'vmAdminPassword' --value $password
```

Para o PowerShell, use:

```azurepowershell-interactive
$keyVaultName = "{your-unique-vault-name}"
$resourceGroupName="{your-resource-group-name}"
$location='Central US'
$userPrincipalName='{your-email-address-associated-with-your-subscription}'

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

New-AzureRmKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list

$password = openssl rand -base64 32
echo $password
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $keyVaultName -Name "vmAdminPassword" -SecretValue $secretvalue
```

Se estiver executando o script do PowerShell fora do Cloud Shell, use o seguinte comando para gerar a senha:

```powershell
Add-Type -AssemblyName System.Web
[System.Web.Security.Membership]::GeneratePassword(16,3)
```

Para usar o modelo do Resource Manager: Consulte [Tutorial: Integrar o Azure Key Vault na implantação de Modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md#prepare-the-key-vault).

> [!NOTE]
> Cada serviço do Azure tem requisitos de senha específicos. Por exemplo, os requisitos da máquina virtual do Azure podem ser encontrados em [Quais são os requisitos de senha ao criar uma VM?](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm).

## <a name="enable-access-to-the-secret"></a>Habilitar o acesso ao segredo

Além de configurar `enabledForTemplateDeployment` para `true`, o usuário que implanta o modelo deverá ter a `Microsoft.KeyVault/vaults/deploy/action` permissão para o escopo que contém o Key Vault, incluindo o grupo de recursos e Key Vault. Ambas as funções [Proprietário](../role-based-access-control/built-in-roles.md#owner) e [Colaborador](../role-based-access-control/built-in-roles.md#contributor) concedem esse acesso. Se você criar o Key Vault, você será o proprietário, portanto, terá a permissão. Se o Key Vault estiver sob uma assinatura diferente, o proprietário do Key Vault deverá permitir o acesso.

O procedimento a seguir mostra como criar uma função com a permissão mínima e como atribuir o usuário
1. Crie um arquivo JSON de definição de função personalizada:

    ```json
    {
      "Name": "Key Vault resource manager template deployment operator",
      "IsCustom": true,
      "Description": "Lets you deploy a resource manager template with the access to the secrets in the Key Vault.",
      "Actions": [
        "Microsoft.KeyVault/vaults/deploy/action"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/00000000-0000-0000-0000-000000000000"
      ]
    }
    ```
    Substitua "00000000-0000-0000-0000-000000000000" pela ID de assinatura do usuário que precisa implantar os modelos.

2. Crie a nova função usando o arquivo JSON:

    ```azurepowershell
    $resourceGroupName= "<Resource Group Name>" # the resource group which contains the Key Vault
    $userPrincipalName = "<Email Address of the deployment operator>"
    New-AzureRmRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzureRmRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    O exemplo `New-AzureRmRoleAssignment` designa a função personalizada ao usuário no nível do grupo de recursos.  

Ao usar um Key Vault com o modelo para um [Aplicativo Gerenciado](../managed-applications/overview.md), você precisa conceder acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. Para obter mais informações, confira [Segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure](../managed-applications/key-vault-access.md).

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

Se você precisar usar uma versão do segredo diferente da versão atual, use a propriedade `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Agora, implante o modelo e passe o arquivo de parâmetro. É possível usar o modelo de exemplo do GitHub, mas é necessário usar um arquivo de parâmetro local com os valores definidos para o seu ambiente.

Para a CLI do Azure, use:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json \
    --parameters @sqlserver.parameters.json
```

Para o PowerShell, use:

```powershell-interactive
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/keyvaultparameter/sqlserver.json `
  -TemplateParameterFile sqlserver.parameters.json
```

## <a name="reference-a-secret-with-dynamic-id"></a>Fazer referência a um segredo com ID dinâmica

A seção anterior mostrou como passar uma ID do recurso estático para o segredo do cofre de chaves do parâmetro. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Ou, você pode simplesmente passar valores de parâmetros para o modelo em vez de criar um parâmetro de referência no arquivo de parâmetros. Em ambos os casos, é possível gerar dinamicamente a ID do recurso para um segredo do cofre de chaves usando um modelo vinculado.

Não é possível gerar dinamicamente a ID do recurso no arquivo de parâmetros porque as expressões de modelo não são permitidas no arquivo de parâmetros. 

No modelo pai, adicione o modelo vinculado e passe um parâmetro que contém a ID de recurso gerada dinamicamente. A imagem a seguir mostra como um parâmetro no modelo vinculado faz referência ao segredo.

![ID dinâmica](./media/resource-manager-keyvault-parameter/dynamickeyvault.png)

O [seguinte modelo](https://github.com/Azure/azure-quickstart-templates/tree/master/201-key-vault-use-dynamic-id) cria dinamicamente a ID do cofre de chaves e a passa como um parâmetro.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "The location where the resources will be deployed."
            }
        },
        "vaultName": {
            "type": "string",
            "metadata": {
                "description": "The name of the keyvault that contains the secret."
            }
        },
        "secretName": {
            "type": "string",
            "metadata": {
                "description": "The name of the secret."
            }
        },
        "vaultResourceGroupName": {
            "type": "string",
            "metadata": {
                "description": "The name of the resource group that contains the keyvault."
            }
        },
        "vaultSubscription": {
            "type": "string",
            "defaultValue": "[subscription().subscriptionId]",
            "metadata": {
                "description": "The name of the subscription that contains the keyvault."
            }
        },
        "_artifactsLocation": {
            "type": "string",
            "metadata": {
                "description": "The base URI where artifacts required by this template are located. When the template is deployed using the accompanying scripts, a private location in the subscription will be used and this value will be automatically generated."
            },
            "defaultValue": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/"
        },
        "_artifactsLocationSasToken": {
            "type": "securestring",
            "metadata": {
                "description": "The sasToken required to access _artifactsLocation.  When the template is deployed using the accompanying scripts, a sasToken will be automatically generated."
            },
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2018-05-01",
            "name": "dynamicSecret",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "contentVersion": "1.0.0.0",
                    "uri": "[uri(parameters('_artifactsLocation'), concat('./nested/sqlserver.json', parameters('_artifactsLocationSasToken')))]"
                },
                "parameters": {
                    "location": {
                        "value": "[parameters('location')]"
                    },
                    "adminLogin": {
                        "value": "ghuser"
                    },
                    "adminPassword": {
                        "reference": {
                            "keyVault": {
                                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
                            },
                            "secretName": "[parameters('secretName')]"
                        }
                    }
                }
            }
        }
    ],
    "outputs": {
        "sqlFQDN": {
            "type": "string",
            "value": "[reference('dynamicSecret').outputs.sqlFQDN.value]"
        }
    }
}
```

Implante o modelo anterior e forneça valores para os parâmetros. É possível usar o modelo de exemplo do GitHub, mas é necessário fornecer os valores de parâmetro para o seu ambiente.

Para a CLI do Azure, use:

```azurecli-interactive
az group create --name datagroup --location $location
az group deployment create \
    --name exampledeployment \
    --resource-group datagroup \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=<your-vault> vaultResourceGroupName=examplegroup secretName=examplesecret
```

Para o PowerShell, use:

```powershell
New-AzureRmResourceGroup -Name datagroup -Location $location
New-AzureRmResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName datagroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName <your-vault> -vaultResourceGroupName examplegroup -secretName examplesecret
```

## <a name="next-steps"></a>Próximas etapas
* Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
