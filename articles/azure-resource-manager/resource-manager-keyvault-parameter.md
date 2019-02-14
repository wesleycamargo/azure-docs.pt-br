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
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: cd45384bfd548cb73c10306dfee942cbcf7c8ca1
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55491902"
---
# <a name="use-azure-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Usar o Azure Key Vault para passar um valor de parâmetro seguro durante a implantação

Em vez de colocar um valor seguro (como uma senha) diretamente no arquivo de parâmetro, você pode recuperar o valor de um [Azure Key Vault](../key-vault/key-vault-whatis.md) durante a implantação. Você recupera o valor fazendo referência ao cofre de chaves e ao segredo no arquivo de parâmetros. O valor nunca é exposto porque você apenas fazer referência à sua ID de cofre de chaves. O cofre de chaves pode existir em uma assinatura diferente que o grupo de recursos que está sendo implantado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-key-vaults-and-secrets"></a>Implantar Key Vaults e segredos

Para criar Key Vaults e adicionar segredos, confira:

- [Definir e recuperar um segredo usando a CLI](../key-vault/quick-create-cli.md)
- [Definir e recuperar um segredo usando o PowerShell](../key-vault/quick-create-powershell.md)
- [Definir e recuperar um segredo usando o portal](../key-vault/quick-create-portal.md)
- [Definir e recuperar um segredo usando o .NET](../key-vault/quick-create-net.md)
- [Definir e recuperar um segredo usando o Node.js](../key-vault/quick-create-node.md)

Há alguns requisitos e considerações adicionais ao integrar o Key Vault à implantação de modelo do Resource Manager:

- `enabledForTemplateDeployment`é uma propriedade do Key Vault. Para acessar os segredos dentro deste Key Vault da implantação do Resource Manager, `enabledForTemplateDeployment` deve ser `true`. 
- Se você não for o proprietário do cofre de chaves, o proprietário precisará atualizar as configurações de política de segurança do cofre de chaves para você adicionar segredos.

Os exemplos do Azure PowerShell e da CLI do Azure a seguir mostram como isso é feito:

```azurecli
# Create a Key Vault
az keyvault create \
  --name $keyVaultName \
  --resource-group $resourceGroupName \
  --location $location \
  --enabled-for-template-deployment true
az keyvault set-policy --upn $userPrincipalName --name $keyVaultName --secret-permissions set delete get list
```

```azurepowershell
New-AzKeyVault `
  -VaultName $keyVaultName `
  -resourceGroupName $resourceGroupName `
  -Location $location `
  -EnabledForTemplateDeployment
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userPrincipalName -PermissionsToSecrets set,delete,get,list
```

## <a name="grant-access-to-the-secrets"></a>Permitir acesso aos segredos

O usuário que implanta o modelo deve ter uma permissão `Microsoft.KeyVault/vaults/deploy/action` para o escopo que contém o Key Vault, incluindo o grupo de recursos e o Key Vault. Ambas as funções [Proprietário](../role-based-access-control/built-in-roles.md#owner) e [Colaborador](../role-based-access-control/built-in-roles.md#contributor) concedem esse acesso. Se você criar o Key Vault, você será o proprietário, portanto, terá a permissão. Se o Key Vault estiver sob uma assinatura diferente, o proprietário do Key Vault deverá conceder o acesso.

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
    New-AzRoleDefinition -InputFile "<PathToTheJSONFile>" 
    New-AzRoleAssignment -ResourceGroupName $resourceGroupName -RoleDefinitionName "Key Vault resource manager template deployment operator" -SignInName $userPrincipalName
    ```

    O exemplo `New-AzRoleAssignment` designa a função personalizada ao usuário no nível do grupo de recursos.  

Ao usar um Key Vault com o modelo para um [Aplicativo Gerenciado](../managed-applications/overview.md), você precisa conceder acesso à entidade de serviço **Provedor de Recursos do Dispositivo**. Para obter mais informações, confira [Segredo do Azure Key Vault durante a implantação de Aplicativos Gerenciados do Azure](../managed-applications/key-vault-access.md).

## <a name="reference-secrets-with-static-id"></a>Fazer referência segredos com ID estática

Com esta abordagem, você faz referência ao Key Vault no arquivo de parâmetro, não no modelo. A imagem a seguir mostra como o arquivo de parâmetro faz referência ao segredo e passa o valor para o modelo.

![Diagrama de ID estática de integração do Key Vault do Resource Manager](./media/resource-manager-keyvault-parameter/statickeyvault.png)

[Tutorial: Integrar o Azure Key Vault à implantação de modelo do Resource Manager](./resource-manager-tutorial-use-key-vault.md) usa este método. O tutorial implanta uma máquina virtual, o que inclui uma senha de administrador. O parâmetro de senha é definido como uma cadeia de caracteres segura:

![Arquivo de modelo de ID estática de integração do Key Vault do Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-template-file.png)

Agora, crie um arquivo de parâmetro para o modelo anterior. No arquivo de parâmetro, especifique um parâmetro que corresponda ao nome do parâmetro no modelo. Para o valor do parâmetro, referenciar o segredo do cofre de chave. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chaves e o nome do segredo:

![Arquivo de parâmetro de ID estática de integração do Key Vault do Resource Manager](./media/resource-manager-keyvault-parameter/resource-manager-key-vault-static-id-parameter-file.png)

Se você precisar usar uma versão do segredo diferente da versão atual, use a propriedade `secretVersion`.

```json
"secretName": "examplesecret",
"secretVersion": "cd91b2b7e10e492ebb870a6ee0591b68"
```

Implante o modelo e passe o arquivo de parâmetro:

Para a CLI do Azure, use:

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri <The Template File URI> \
    --parameters <The Parameter File>
```

Para o PowerShell, use:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri <The Template File URI> `
  -TemplateParameterFile <The Parameter File>
```

## <a name="reference-secrets-with-dynamic-id"></a>Fazer referência a segredos com ID dinâmica

A seção anterior mostrou como passar uma ID do recurso estático para o segredo do cofre de chaves do parâmetro. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Ou, você pode passar valores de parâmetros para o modelo em vez de criar um parâmetro de referência no arquivo de parâmetros. Em ambos os casos, é possível gerar dinamicamente a ID do recurso para um segredo do cofre de chaves usando um modelo vinculado.

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

```azurecli
az group create --name $resourceGroupName --location $location
az group deployment create \
    --name $deploymentName \
    --resource-group $resourceGroupName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json \
    --parameters vaultName=$keyVaultName vaultResourceGroupName=examplegroup secretName=examplesecret
```

Para o PowerShell, use:

```powershell
New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
  -Name $deploymentName `
  -ResourceGroupName $resourceGroupName `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-key-vault-use-dynamic-id/azuredeploy.json `
  -vaultName $keyVaultName -vaultResourceGroupName $keyVaultResourceGroupName -secretName $secretName
```

## <a name="next-steps"></a>Próximas etapas

- Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
- Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).
