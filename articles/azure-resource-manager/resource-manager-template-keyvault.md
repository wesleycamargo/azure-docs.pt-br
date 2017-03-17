---
title: Modelo do Resource Manager para o cofre de chaves | Microsoft Docs
description: "Mostra o esquema do Gerenciador de Recursos para a implantação de cofres da chave por meio de um modelo."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: wpickett
editor: 
ms.assetid: 96433b1a-68ee-4292-85b6-a581618b921b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f2d009477a614c3b2876ce98a355d3775abf772b
ms.openlocfilehash: 04f2d5d8e501ebf41cf95ea925d238f64b096c1d
ms.lasthandoff: 02/27/2017


---
# <a name="key-vault-template-schema"></a>Esquema de modelo do cofre da chave
Cria um cofre de chave.

## <a name="schema-format"></a>Formato de esquema
Para criar um cofre da chave, adicione o esquema a seguir à seção de recursos de seu modelo.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForDiskEncryption": bool,
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

## <a name="values"></a>Valores
As tabelas a seguir descrevem os valores necessários para definir no esquema.

| Nome | Valor |
| --- | --- |
| type |Enum<br />Obrigatório<br />**Microsoft.KeyVault/vaults**<br /><br />O tipo de recurso a ser criado. |
| apiVersion |Enum<br />Obrigatório<br />**2015-06-01** ou **2014-12-19-preview**<br /><br />A versão da API a ser usada para criar o recurso. |
| Nome |Cadeia de caracteres<br />Obrigatório<br />Um nome que seja exclusivo no Azure.<br /><br />O nome do cofre de chaves a ser criado. Considere usar a função [uniqueString](resource-group-template-functions.md#uniquestring) com sua convenção de nomenclatura para criar um nome exclusivo, como mostrado no exemplo abaixo. |
| location |Cadeia de caracteres<br />Obrigatório<br />Uma região válida para cofres de chaves. Para determinar as regiões válidas, veja [regiões com suporte](resource-manager-supported-services.md#supported-regions).<br /><br />A região que hospedará o cofre de chaves. |
| propriedades |Objeto<br />Obrigatório<br />[properties object](#properties)<br /><br />Um objeto que especifica o tipo de cofre de chaves a ser criado. |
| recursos |Matriz<br />Opcional<br />Valores permitidos: [recursos secretos do Cofre de chaves](resource-manager-template-keyvault-secret.md)<br /><br />Recursos filhos do cofre de chaves. |

<a id="properties" />

### <a name="properties-object"></a>properties object
| Nome | Valor |
| --- | --- |
| enabledForDeployment |Booliano<br />Opcional<br />**true** ou **false**<br /><br />Especifica se o cofre está habilitado para implantação da Máquina Virtual ou do Service Fabric. |
| enabledForTemplateDeployment |Booliano<br />Opcional<br />**true** ou **false**<br /><br />Especifica se o cofre está habilitado para uso em implantações de modelo do Resource Manager. Para obter mais informações, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md) |
| enabledForDiskEncryption |Booliano<br />Opcional<br />**true** ou **false**<br /><br />Especifica se o cofre está habilitado para criptografia de volume. |
| tenantId |Cadeia de caracteres<br />Obrigatório<br />**Identificador global exclusivo**<br /><br />O identificador do locatário para a assinatura. Você pode recuperá-lo com o cmdlet [Get-AzureRMSubscription](https://msdn.microsoft.com/library/azure/mt619284.aspx) do PowerShell ou o comando **azure account show** da CLI do Azure. |
| accessPolicies |Matriz<br />Obrigatório<br />[objeto accessPolicies](#accesspolicies)<br /><br />Uma matriz de até 16 objetos que especificam as permissões do usuário ou da entidade de serviço. |
| sku |Objeto<br />Obrigatório<br />[objeto do SKU](#sku)<br /><br />O SKU do cofre de chaves. |

<a id="accesspolicies" />

### <a name="propertiesaccesspolicies-object"></a>properties.accessPolicies object
| Nome | Valor |
| --- | --- |
| tenantId |Cadeia de caracteres<br />Obrigatório<br />**Identificador global exclusivo**<br /><br />O identificador do locatário do Azure Active Directory que contém o **objectId** nesta política de acesso |
| objectId |Cadeia de caracteres<br />Obrigatório<br />**Identificador global exclusivo**<br /><br />O identificador de objeto do usuário ou da entidade de serviço do Azure Active Directory que terá acesso ao cofre. Você pode recuperar o valor com os cmdlets [Get-AzureRmADUser](https://msdn.microsoft.com/library/azure/mt679001.aspx) ou [Get-AzureRmADServicePrincipal](https://msdn.microsoft.com/library/azure/mt678992.aspx) do PowerShell ou com os comandos **azure ad user** ou **azure ad sp** da CLI do Azure. |
| permissões |Objeto<br />Obrigatório<br />[objeto de permissões](#permissions)<br /><br />As permissões concedidas para este cofre ao objeto do Active Directory. |

<a id="permissions" />

### <a name="propertiesaccesspoliciespermissions-object"></a>properties.accessPolicies.permissions object
| Nome | Valor |
| --- | --- |
| chaves |Matriz<br />Obrigatório<br />**all**, **backup**, **create**, **decrypt**, **delete**, **encrypt**, **get**, **import**, **list**, **restore**, **sign**, **unwrapkey**, **update**, **verify**, **wrapkey**<br /><br />As permissões concedidas para as chaves neste cofre a este objeto do Active Directory. Esse valor deve ser especificado como uma matriz de um ou mais valores permitidos. |
| segredos |Matriz<br />Obrigatório<br />**all**, **delete**, **get**, **list**, **set**<br /><br />As permissões concedidas para segredos no cofre a esse objeto do Active Directory. Esse valor deve ser especificado como uma matriz de um ou mais valores permitidos. |

<a id="sku" />

### <a name="propertiessku-object"></a>objeto properties.sku
| Nome | Valor |
| --- | --- |
| Nome |Enum<br />Obrigatório<br />**standard** ou **premium** <br /><br />A camada de serviço do KeyVault a ser usada.  O tipo de preço Standard dá suporte a segredos e chaves protegidas por software.  O tipo de preço Premium adiciona suporte para chaves protegidas pelo HSM. |
| família |Enum<br />Obrigatório<br />**A** <br /><br />A família de SKU a ser usada. |

## <a name="examples"></a>Exemplos
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
            "enableVaultForDiskEncryption": {
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
                "enabledForDiskEncryption": "[parameters('enableVaultForDiskEncryption')]",
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

## <a name="quickstart-templates"></a>Modelos de início rápido
O modelo de início rápido a seguir implanta um cofre da chave.

* [Criar Cofre da Chave](https://azure.microsoft.com/documentation/templates/101-key-vault-create/)

## <a name="next-steps"></a>Próximas etapas
* Para obter informações gerais sobre cofres de chaves, veja [Introdução ao Cofre de Chaves do Azure](../key-vault/key-vault-get-started.md).
* Para obter um exemplo de como fazer referência a um segredo do cofre da chave durante a implantação de modelos, veja [Transmitir valores seguros durante a implantação](resource-manager-keyvault-parameter.md).


