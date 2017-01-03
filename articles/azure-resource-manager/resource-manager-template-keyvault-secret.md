---
title: Modelo do Resource Manager para um segredo em um cofre de chaves | Microsoft Docs
description: "Mostra o esquema do Gerenciador de Recursos para a implantação de segredos de cofre da chave por meio de um modelo."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: 
ms.assetid: fd12bc81-b609-4ca8-b7e0-ee1049d70ab3
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: e841c21a15c47108cbea356172bffe766003a145
ms.openlocfilehash: 885a9b09685982e9bae6b50f0dbc3edd21819859


---
# <a name="key-vault-secret-template-schema"></a>Esquema de modelo do segredo do cofre da chave
Cria um segredo que é armazenado em um cofre da chave. Esse tipo de recurso é implantado com frequência como um recurso filho do [cofre da chave](resource-manager-template-keyvault.md).

## <a name="schema-format"></a>Formato de esquema
Para criar um segredo do cofre da chave, adicione o seguinte esquema ao modelo. O segredo pode ser definido como um recurso filho de um cofre da chave ou como um recurso de nível superior. Você pode defini-lo como um recurso filho quando o cofre de chaves é implantado no mesmo modelo. Você precisará definir o segredo como um recurso de nível superior quando a chave do cofre não estiver implantada no mesmo modelo, ou quando você precisar criar vários segredos fazendo um loop no tipo de recurso. 

    {
        "type": enum,
        "apiVersion": "2015-06-01",
        "name": string,
        "properties": {
            "value": string
        },
        "dependsOn": [ array values ]
    }

## <a name="values"></a>Valores
As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Valor |
| --- | --- |
| type |Enum<br />Obrigatório<br />**secrets** (quando implantado como um recurso filho do Cofre de Chaves) ou<br /> **Microsoft.KeyVault/vaults/secrets** (quando implantado como um recurso de nível superior)<br /><br />O tipo de recurso a ser criado. |
| apiVersion |Enum<br />Obrigatório<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />A versão da API a ser usada para criar o recurso. |
| Nome |Cadeia de caracteres<br />Obrigatório<br />Uma única palavra quando implantado como um recurso filho de um cofre de chaves ou no formato **{nome-do-cofre-da-chave}/{nome-do-segredo}** quando implantado como um recurso de nível superior a ser adicionado a uma chave existente.<br /><br />O nome do segredo a ser criado. |
| propriedades |Objeto<br />Obrigatório<br />[properties object](#properties)<br /><br />Um objeto que especifica o valor do segredo a ser criado. |
| dependsOn |Matriz<br />Opcional<br />Uma lista separada por vírgulas de nomes de recursos ou identificadores exclusivos de recursos.<br /><br />A coleção de recursos do qual este vínculo depende. Se a chave do cofre do segredo for implantada no mesmo modelo, inclua o nome do cofre da chave neste elemento para garantir que ele é implantado primeiro. |

<a id="properties" />

### <a name="properties-object"></a>properties object
| Nome | Valor |
| --- | --- |
| Valor |Cadeia de caracteres<br />Obrigatório<br /><br />O valor do segredo a ser armazenado no cofre da chave. Ao transmitir um valor para essa propriedade, use um parâmetro do tipo **securestring**. |

## <a name="examples"></a>Exemplos
O primeiro exemplo implanta um segredo como um recurso filho de um cofre da chave.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant ID for the subscription and use assigned access to the vault. Available from the Get-AzureRmSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object ID of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRmADUser or the Get-AzureRmADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }

O segundo exemplo implanta o segredo como um recurso de nível superior que é armazenado em um cofre da chave existente.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the existing vault"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.KeyVault/vaults/secrets",
                "apiVersion": "2015-06-01",
                "name": "[concat(parameters('keyVaultName'), '/', parameters('secretName'))]",
                "properties": {
                    "value": "[parameters('secretValue')]"
                }
            }
        ],
        "outputs": {}
    }


## <a name="next-steps"></a>Próximas etapas
* Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter um exemplo de como fazer referência a um segredo do cofre da chave durante a implantação de modelos, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).




<!--HONumber=Nov16_HO3-->


