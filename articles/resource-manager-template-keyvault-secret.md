<properties
   pageTitle="Modelo do Gerenciador de Recursos para um segredo em um cofre da chave | Microsoft Azure"
   description="Mostra o esquema do Gerenciador de Recursos para a implantação de segredos de cofre da chave por meio de um modelo."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/05/2016"
   ms.author="tomfitz"/>

# Esquema de modelo do segredo do cofre da chave

Cria um segredo que é armazenado em um cofre da chave. Esse tipo de recurso é implantado com frequência como um recurso filho do [cofre da chave](resource-manager-template-keyvault.md).

## Formato de esquema

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

## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Valor |
| ---- | ---- | 
| type | Enum<br />Obrigatório<br />**secrets** (quando implantado como um recurso filho do cofre da chave) ou<br /> **Microsoft.KeyVault/vaults/secrets** (quando implantado como um recurso de nível superior)<br /><br />O tipo de recurso a ser criado. |
| apiVersion | Enum<br />Obrigatório<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />A versão da API a ser usada para criar o recurso. | 
| name | String<br />Obrigatório<br />Uma única palavra quando implantado como um recurso filho de um cofre da chave ou no formato **{nome-do-cofre-da-chave}/{nome-do-segredo}** quando implantado como um recurso de nível superior a ser adicionado a uma chave existente.<br /><br />O nome do segredo a ser criado. |
| propriedades | Object<br />Obrigatório<br />[properties object](#properties)<br /><br />Um objeto que especifica o valor do segredo a ser criado. |
| dependsOn | Array<br />Opcional<br />Uma lista separada por vírgulas com os nomes ou os identificadores exclusivos de um recurso.<br /><br />A coleção de recursos de que esse link depende. Se a chave do cofre do segredo for implantada no mesmo modelo, inclua o nome do cofre da chave neste elemento para garantir que ele é implantado primeiro. |

<a id="properties" />
### properties object

| Nome | Valor |
| ---- | ---- | 
| value | String<br />Obrigatório<br /><br />O valor do segredo a ser armazenado no cofre da chave. Ao transmitir um valor para essa propriedade, use um parâmetro do tipo **securestring**. |

	
## Exemplos

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
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
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


## Próximas etapas

- Para obter informações gerais sobre cofres de chave, veja [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de como fazer referência a um segredo do cofre da chave durante a implantação de modelos, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0518_2016-->