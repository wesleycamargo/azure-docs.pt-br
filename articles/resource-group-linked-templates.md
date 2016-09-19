<properties
   pageTitle="Modelos vinculados com o Resource Manager | Microsoft Azure"
   description="Descreve como usar modelos vinculados em um modelo do Gerenciador de Recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um arquivo de parâmetro e URLs criadas dinamicamente."
   services="azure-resource-manager"
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
   ms.date="09/02/2016"
   ms.author="tomfitz"/>

# Usando modelos vinculados com o Gerenciador de Recursos do Azure

Em um modelo do Azure Resource Manager, você pode vincular a outro modelo, o que permite decompor sua implantação em um conjunto de modelos orientados para fins específicos. Assim como com a decomposição de um aplicativo em várias classes de código, a decomposição oferece benefícios em termos de teste, reutilização e legibilidade.

Você pode passar parâmetros de um modelo principal a um modelo vinculado e esses parâmetros podem corresponder diretamente aos parâmetros ou variáveis expostos pelo modelo de chamada. O modelo vinculado também pode passar uma variável de saída de volta para o modelo de origem, permitindo uma troca de dados bidirecional entre os modelos.

## Vinculando a um modelo

Para criar um vínculo entre dois modelos, adicione um recurso de implantação no modelo principal que aponta para o modelo vinculado. Defina a propriedade **templateLink** como o URI do modelo vinculado. Você pode fornecer valores de parâmetro para o modelo vinculado especificando os valores diretamente em seu modelo ou vinculando a um arquivo de parâmetro. O exemplo a seguir usa a propriedade **parameters** para especificar um valor de parâmetro diretamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

O serviço do Resource Manager deve ser capaz de acessar o modelo vinculado. Você não pode especificar um arquivo local ou um arquivo que esteja disponível apenas em sua rede local para o modelo vinculado. Você só pode fornecer um valor de URI que inclua **http** ou **https**. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item, conforme mostra o exemplo a seguir.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Embora o modelo vinculado precise estar disponível externamente, ele não precisa estar disponível para o público. Você pode adicionar o modelo a uma conta de armazenamento privada que pode ser acessada somente pelo proprietário da conta de armazenamento. Em seguida, você cria um token SAS (assinatura de acesso compartilhado) para permitir o acesso durante a implantação. Você pode adicionar esse token SAS ao URI para o modelo vinculado. Para ver as etapas para configurar um modelo em uma conta de armazenamento e gerar um token SAS, consulte [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md).

O exemplo a seguir mostra um modelo pai vinculado a outro modelo. O modelo vinculado é acessado com um token SAS que é transmitido como um parâmetro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
              "mode": "incremental",
              "templateLink": {
                "uri": "[concat('https://storagecontosotemplates.blob.core.windows.net/templates/helloworld.json', parameters('sasToken'))]",
                "contentVersion": "1.0.0.0"
              }
            }
        }
    ],

Embora o token seja transmitido como uma cadeia de caracteres segura, o URI do modelo vinculado, incluindo o token SAS, é registrado nas operações de implantação para esse grupo de recursos. Para limitar a exposição, defina uma expiração para o token.

## Vinculando a um arquivo de parâmetro

O exemplo a seguir usa a propriedade **parametersLink** para vincular a um arquivo de parâmetro.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "linkedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/AzureTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/AzureTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

O valor do URI para o arquivo de parâmetro vinculado não pode ser um arquivo local e deve incluir **http** ou **https**. O arquivo de parâmetro também pode ter o acesso limitado por meio de um token SAS.

## Usando variáveis para vincular modelos

Os exemplos anteriores mostraram valores codificados de URL para os vínculos de modelo. Essa abordagem pode funcionar para um modelo simples, mas não funciona bem quando ao trabalhar com um grande conjunto de modelos modulares. Em vez disso, você pode criar uma variável estática que armazena uma URL de base para o modelo principal e, em seguida, criar dinamicamente URLs para os modelos vinculados dessa URL de base. A vantagem dessa abordagem é mover ou bifurcar o modelo, pois você precisa alterar a variável estática no modelo principal. O modelo principal passa os URIs corretos em todo o modelo decomposto.

O exemplo a seguir mostra como usar uma URL base para criar duas URLs para modelos vinculados (**sharedTemplateUrl** e **vmTemplate**).

    "variables": {
        "templateBaseUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/postgresql-on-ubuntu/",
        "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
        "tshirtSizeSmall": {
            "vmSize": "Standard_A1",
            "diskSize": 1023,
            "vmTemplate": "[concat(variables('templateBaseUrl'), 'database-2disk-resources.json')]",
            "vmCount": 2,
            "slaveCount": 1,
            "storage": {
                "name": "[parameters('storageAccountNamePrefix')]",
                "count": 1,
                "pool": "db",
                "map": [0,0],
                "jumpbox": 0
            }
        }
    }

Você também pode usar [deployment()](resource-group-template-functions.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Essa abordagem será útil se o local do modelo é alterado (talvez devido a controle de versão) ou para evitar embutir URLs no arquivo de modelo.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Vinculação condicional a modelos

Você pode vincular a modelos diferentes passando um valor de parâmetro usado para construir o URI do modelo vinculado. Essa abordagem funciona bem quando você precisa especificar qual modelo vinculado deve ser usado durante a implantação. Por exemplo, você pode especificar um modelo a ser usado para uma conta de armazenamento existente, e outro modelo a ser usado para uma nova conta de armazenamento.

O exemplo a seguir mostra um parâmetro para um nome de conta de armazenamento, e um parâmetro para especificar se a conta de armazenamento é nova ou existente.

    "parameters": {
        "storageAccountName": {
            "type": "String"
        },
        "newOrExisting": {
            "type": "String",
            "allowedValues": [
                "new",
                "existing"
            ]
        }
    },

Crie uma variável para o URI do modelo que inclui o valor do parâmetro novo ou existente.

    "variables": {
        "templatelink": "[concat('https://raw.githubusercontent.com/exampleuser/templates/master/',parameters('newOrExisting'),'StorageAccount.json')]"
    },

Forneça esse valor variável para o recurso de implantação.

    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "linkedTemplate",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "incremental",
                "templateLink": {
                    "uri": "[variables('templatelink')]",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "StorageAccountName": {
                        "value": "[parameters('storageAccountName')]"
                    }
                }
            }
        }
    ],

O URI é resolvido para um modelo denominado **existingStorageAccount.json** ou **newStorageAccount.json**. Crie modelos para esses URIs.

A exemplo a seguir mostra o modelo **existingStorageAccount.json**.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

A próximo exemplo mostra o modelo **newStorageAccount.json**. Observe que, assim como o modelo da conta de armazenamento existente, o objeto da conta de armazenamento retorna nas saídas. O modelo mestre funciona com qualquer modelo vinculado.

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "storageAccountName": {
          "type": "string"
        }
      },
      "resources": [
        {
          "type": "Microsoft.Storage/storageAccounts",
          "name": "[parameters('StorageAccountName')]",
          "apiVersion": "2016-01-01",
          "location": "[resourceGroup().location]",
          "sku": {
            "name": "Standard_LRS"
          },
          "kind": "Storage",
          "properties": {
          }
        }
      ],
      "outputs": {
        "storageAccountInfo": {
          "value": "[reference(concat('Microsoft.Storage/storageAccounts/', parameters('StorageAccountName')),providers('Microsoft.Storage', 'storageAccounts').apiVersions[0])]",
          "type" : "object"
        }
      }
    }

## Exemplo completo

Os exemplos de modelos a seguir mostram uma disposição simplificada de modelos vinculados para ilustrar vários conceitos neste artigo. Ela pressupõe que os modelos tenham sido adicionados ao mesmo contêiner em uma conta de armazenamento com acesso público desativado. O modelo vinculado transmite um valor de volta para o modelo principal na seção **outputs**.

O arquivo **parent.json** consiste em:

    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "containerSasToken": { "type": "string" }
      },
      "resources": [
        {
          "apiVersion": "2015-01-01",
          "name": "linkedTemplate",
          "type": "Microsoft.Resources/deployments",
          "properties": {
            "mode": "incremental",
            "templateLink": {
              "uri": "[concat(uri(deployment().properties.templateLink.uri, 'helloworld.json'), parameters('containerSasToken'))]",
              "contentVersion": "1.0.0.0"
            }
          }
        }
      ],
      "outputs": {
        "result": {
          "type": "object",
          "value": "[reference('linkedTemplate').outputs.result]"
        }
      }
    }

O arquivo **helloworld.json** consiste em:

    {
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {},
	  "variables": {},
	  "resources": [],
	  "outputs": {
		"result": {
			"value": "Hello World",
			"type" : "string"
		}
	  }
    }
    
No PowerShell, você obtém um token para o contêiner e implanta os modelos com:

    Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name storagecontosotemplates
    $token = New-AzureStorageContainerSASToken -Name templates -Permission r -ExpiryTime (Get-Date).AddMinutes(30.0)
    New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri ("https://storagecontosotemplates.blob.core.windows.net/templates/parent.json" + $token) -containerSasToken $token

Na CLI do Azure, você obtém um token para o contêiner e implanta os modelos com o código a seguir. Atualmente, você precisa fornecer um nome para a implantação ao usar um URI do modelo que inclui um token SAS.

    expiretime=$(date -I'minutes' --date "+30 minutes")  
    azure storage container sas create --container templates --permissions r --expiry $expiretime --json | jq ".sas" -r
    azure group deployment create -g ExampleGroup --template-uri "https://storagecontosotemplates.blob.core.windows.net/templates/parent.json?{token}" -n tokendeploy  

Você recebe uma solicitação para fornecer o token SAS como um parâmetro. Você precisa preceder o token com **?**.

## Próximas etapas
- Para saber mais sobre como definir a ordem de implantação para seus recursos, confira [Definição de dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md)
- Para saber como definir um recurso, mas criando várias instâncias dele, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md)

<!---HONumber=AcomDC_0907_2016-->