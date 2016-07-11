<properties
   pageTitle="Segredo do Cofre de Chaves com o modelo do Resource Manager | Microsoft Azure"
   description="Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação."
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/23/2016"
   ms.author="tomfitz"/>

# Transmitir valores seguros durante a implantação

Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um [Cofre da Chave do Azure](./key-vault/key-vault-whatis.md) e fazer referência ao valor em outros modelos do Gerenciador de Recursos. Você inclui apenas uma referência ao segredo em seu modelo, para que o segredo nunca seja exposto, e você não precisa inserir manualmente o valor do segredo sempre que implantar os recursos. Você especifica quais usuários ou entidades de serviço podem acessar o segredo.

## Implantar um cofre da chave e segredo

Para criar o cofre da chave que pode ser referenciado de outros modelos do Gerenciador de Recursos, é necessário definir a propriedade **enabledForTemplateDeployment** como **true** e conceder acesso ao usuário ou à entidade de serviço que executará a implantação que faz referência ao segredo.

Para saber mais sobre a implantação de um cofre da chave e um segredo, veja [Esquema do cofre da chave](resource-manager-template-keyvault.md) e [Esquema do segredo do cofre da chave](resource-manager-template-keyvault-secret.md).

## Fazer referência a um segredo com ID estática

Você faz referência ao segredo em um arquivo de parâmetros que transmite valores para seu modelo. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chave e o nome do segredo. Neste exemplo, o segredo do cofre da chave já deve existir, e você já deve estar usando um valor estático para sua id de recurso.

    "parameters": {
      "adminPassword": {
        "reference": {
          "keyVault": {
            "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
          }, 
          "secretName": "sqlAdminPassword" 
        } 
      }
    }

Um arquivo de parâmetro inteiro pode ter essa aparência:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "sqlsvrAdminLogin": {
          "value": ""
        },
        "sqlsvrAdminLoginPassword": {
          "reference": {
            "keyVault": {
              "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
            },
            "secretName": "adminPassword"
          }
        }
      }
    }

O parâmetro que aceita o segredo deve ser um **securestring**. O exemplo a seguir mostra as seções relevantes de um modelo que implanta um SQL Server que exige uma senha de administrador.

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
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }

## Fazer referência a um segredo com ID dinâmica

A seção anterior mostrou como passar uma id de recurso estático para o segredo do cofre da chave. No entanto, em alguns cenários, você precisa fazer referência a um segredo de cofre da chave que varia com base na implantação atual. Nesse caso, não é possível codificar a id de recurso no arquivo de parâmetros. Infelizmente, você não pode gerar dinamicamente a id do recurso no arquivo de parâmetros, pois não há permissão para expressões de modelo no arquivo de parâmetros.

Para gerar dinamicamente a id de recurso para um segredo de cofre de chave, você deve mover o recurso que precisa do segredo em um modelo aninhado. No modelo mestre, adicione o modelo aninhado e passe um parâmetro que contém a id de recurso gerada dinamicamente.

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
        }
      ],
      "outputs": {}
    }


## Próximas etapas

- Para obter informações gerais sobre cofres de chave, veja [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).
- Para obter informações sobre como usar um cofre de chave com uma máquina virtual, consulte [Considerações de segurança para o Azure Resource Manager](best-practices-resource-manager-security.md).
- Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0629_2016-->