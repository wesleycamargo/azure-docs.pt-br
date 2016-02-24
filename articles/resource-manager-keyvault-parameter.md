<properties
   pageTitle="Usar um segredo do Cofre da Chave com o modelo do Gerenciador de Recursos | Microsoft Azure"
   description="Mostra como transmitir um segredo de um cofre da chave como um parâmetro durante a implantação."
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
   ms.date="02/09/2016"
   ms.author="tomfitz"/>

# Transmitir valores seguros durante a implantação

Quando você precisa transmitir um valor seguro (como uma senha) como um parâmetro durante a implantação, é possível armazenar esse valor como um segredo em um [Cofre da Chave do Azure](./key-vault/key-vault-whatis.md) e fazer referência ao valor em outros modelos do Gerenciador de Recursos. Você inclui apenas uma referência ao segredo em seu modelo, para que o segredo nunca seja exposto, e você não precisa inserir manualmente o valor do segredo sempre que implantar os recursos. Você especifica quais usuários ou entidades de serviço podem acessar o segredo.

> [AZURE.NOTE] Atualmente, apenas a CLI do Azure dá suporte à capacidade de fazer referência a um segredo do cofre da chave. O Azure PowerShell adicionará essa capacidade assim que possível.

## Implantar um cofre da chave e segredo

Para criar o cofre da chave que pode ser referenciado de outros modelos do Gerenciador de Recursos, é necessário definir a propriedade **enabledForTemplateDeployment** como **true** e conceder acesso ao usuário ou à entidade de serviço que executará a implantação que faz referência ao segredo.

Para saber mais sobre a implantação de um cofre da chave e um segredo, veja [Esquema do cofre da chave](resource-manager-template-keyvault.md) e [Esquema do segredo do cofre da chave](resource-manager-template-keyvault-secret.md).

## Fazer referência a um segredo

Você faz referência ao segredo em um arquivo de parâmetros que transmite valores para seu modelo. Você faz referência ao segredo transmitindo o identificador de recurso do cofre de chave e o nome do segredo.

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




## Próximas etapas

- Para obter informações gerais sobre cofres de chave, veja [Introdução ao Cofre da Chave do Azure](./key-vault/key-vault-get-started.md).
- Para obter informações sobre como usar um cofre de chave com uma máquina virtual, consulte [Considerações de segurança para o Gerenciador de Recursos do Azure](best-practices-resource-manager-security.md).
- Para obter exemplos completos de referência de segredos de chave, veja [Exemplos do cofre da chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

<!---HONumber=AcomDC_0211_2016-->