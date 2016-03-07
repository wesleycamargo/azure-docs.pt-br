<properties
   pageTitle="Modelo do Gerenciador de Recursos para o cofre da chave | Microsoft Azure"
   description="Mostra o esquema do Gerenciador de Recursos para a implantação de cofres da chave por meio de um modelo."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/23/2016"
   ms.author="tomfitz"/>

# Esquema de modelo do cofre da chave

Cria um cofre de chave.

## Formato de esquema

Para criar um cofre da chave, adicione o esquema a seguir à seção de recursos de seu modelo.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## Valores

As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| type | enum | Sim | **Microsoft.KeyVault/vaults** | O tipo de recurso a ser criado. |
| apiVersion | enum | Sim | **2015-06-01** <br /> **2014-12-19-preview** | A versão da API a ser usada para criar o recurso. | 
| name | cadeia de caracteres | Sim | | O nome do cofre da chave a ser criado. O nome deve ser exclusivo em todo o Azure. Considere o uso da função [uniqueString](resource-group-template-functions.md#uniquestring) com a convenção de nomenclatura, como mostrado no exemplo abaixo. |
| location | cadeia de caracteres | Sim | Para determinar as regiões válidas, veja [regiões com suporte](resource-manager-supported-services.md#supported-regions). | A região para hospedar o cofre da chave. |
| propriedades | objeto | Sim | ([mostrado abaixo](#properties)) | Um objeto que especifica o tipo de chave de cofre a ser criada. |
| recursos | array | Não | [Segredos do cofre da chave](resource-manager-template-keyvault-secret.md) | Recursos filho do cofre da chave. |

<a id="properties" />
### properties object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | Booleano | Não | **true** ou **false** | Especifica se o cofre está habilitado para a implantação de Máquina Virtual ou do Service Fabric. |
| enabledForTemplateDeployment | Booleano | Não | **true** ou **false** | Especifica se o cofre está habilitado para uso em implantações de modelo do Gerenciador de Recursos. Para obter mais informações, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md) |
| enabledForVolumeEncryption | Booleano | Não | **true** ou **false** | Especifica se o cofre está habilitado para criptografia de volume. |
| tenantId | cadeia de caracteres | Sim | Identificador global exclusivo | O identificador do locatário para a assinatura. Você pode recuperá-lo com o cmdlet **Get-AzureRMSubscription** do PowerShell. |
| accessPolicies | array | Sim | ([mostrado abaixo](#accesspolicies)) | Uma matriz de até 16 objetos que especificam as permissões do usuário ou da entidade de serviço. |
| sku | objeto | Sim | ([mostrado abaixo](#sku)) | O SKU do cofre da chave. |

<a id="accesspolicies" />
### properties.accessPolicies object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | cadeia de caracteres | Sim | Identificador global exclusivo | O identificador do locatário do locatário do Active Directory do Azure que contém o **objectId** nesta política de acesso |
| ID do objeto | cadeia de caracteres | Sim | Identificador global exclusivo | O identificador de objeto do usuário do AAD ou a entidade de serviço que terá acesso ao cofre. Você pode recuperar o valor dos cmdlets **Get-AzureRMADUser** ou **Get-AzureRMADServicePrincipal**. |
| permissões | objeto | Sim | ([mostrado abaixo](#permissions)) | As permissões concedidas para este cofre ao objeto do Active Directory. |

<a id="permissions" />
### properties.accessPolicies.permissions object

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| chaves | array | Sim | Uma lista separada por vírgulas dos seguintes valores:<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | As permissões concedidas para as chaves neste cofre a esse objeto do Active Directory. Esse valor deve ser especificado como uma matriz de valores permitidos. |
| segredos | array | Sim | Uma lista separada por vírgulas dos seguintes valores:<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | As permissões concedidas para segredos no cofre a esse objeto do Active Directory. Esse valor deve ser especificado como uma matriz de valores permitidos. |

<a id="sku" />
### objeto properties.sku

| Nome | Tipo | Obrigatório | Valores permitidos | Descrição |
| ---- | ---- | -------- | ---------------- | ----------- |
| name | enum | Sim | **standard**<br />**premium** | A camada de serviço do KeyVault a ser usado. O tipo de preço Standard dá suporte a segredos e chaves protegidas por software. O tipo de preço Premium adiciona suporte para chaves protegidas pelo HSM. |
| família | enum | Sim | **UMA** | A família de SKU a ser usada. 
 
	
## Exemplos

O exemplo a seguir implanta um cofre da chave e um segredo.

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

## Modelos de início rápido

O modelo de início rápido a seguir implanta um cofre da chave.

- [Criar Cofre da Chave](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)


## Próximas etapas

- Para obter informações gerais sobre cofres de chave, veja [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).
- Para obter um exemplo de como fazer referência a um segredo do cofre da chave durante a implantação de modelos, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_0224_2016-->