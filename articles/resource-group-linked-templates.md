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
   ms.date="06/08/2016"
   ms.author="tomfitz"/>

# Usando modelos vinculados com o Gerenciador de Recursos do Azure

Em um modelo do Gerenciador de Recursos do Azure, você pode vincular a outro modelo, o que permite decompor sua implantação em um conjunto de modelos orientados para fins específicos. Assim como com a decomposição de um aplicativo em várias classes de código, a decomposição oferece benefícios em termos de teste, reutilização e legibilidade.

Você pode passar parâmetros de um modelo principal a um modelo vinculado e esses parâmetros podem corresponder diretamente aos parâmetros ou variáveis expostos pelo modelo de chamada. O modelo vinculado também pode passar uma variável de saída de volta para o modelo de origem, permitindo uma troca de dados bidirecional entre os modelos.

## Vinculando a um modelo

Para criar um vínculo entre dois modelos, adicione um recurso de implantação no modelo principal que aponta para o modelo vinculado. Defina a propriedade **templateLink** como o URI do modelo vinculado. Você pode fornecer valores de parâmetro para o modelo vinculado especificando os valores diretamente em seu modelo ou vinculando a um arquivo de parâmetro. O exemplo a seguir usa a propriedade **parameters** para especificar um valor de parâmetro diretamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
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

O serviço do Gerenciador de Recursos deve ser capaz de acessar o modelo vinculado, o que significa que você não pode especificar um arquivo local ou um arquivo que só está disponível em sua rede local para o modelo vinculado. Você só pode fornecer um valor de URI que inclua **http** ou **https**. Uma opção é colocar o modelo vinculado em uma conta de armazenamento e usar o URI do item, conforme mostrado abaixo.

    "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0",
    }

Embora o modelo vinculado precise estar disponível externamente, ele não precisa estar disponível para o público. Você pode adicionar o modelo a uma conta de armazenamento privada, que é acessível somente para o proprietário da conta de armazenamento, e, em seguida, criar um token SAS (Assinatura de Acesso Compartilhado) para permitir o acesso durante a implantação. Você pode adicionar esse token SAS ao URI para o modelo vinculado. Para ver as etapas para configurar um modelo em uma conta de armazenamento e gerar um token SAS, consulte [Implantar recursos com modelos do Resource Manager e o Azure PowerShell](resource-group-template-deploy.md) ou [Implantar recursos com modelos do Resource Manager e a CLI do Azure](resource-group-template-deploy-cli.md).

O exemplo a seguir mostra um modelo pai vinculado a outro modelo. O modelo aninhado é acessado com um token SAS que é transmitido como um parâmetro.

    "parameters": {
        "sasToken": { "type": "securestring" }
    },
    "resources": [
        {
            "apiVersion": "2015-01-01",
            "name": "nestedTemplate",
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
         "name": "nestedTemplate", 
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

O valor do URI para o arquivo de parâmetro vinculado não pode ser um arquivo local e deve incluir **http** ou **https**. Obviamente, o arquivo de parâmetro também pode ter o acesso limitado por meio de um token SAS.

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

Você também pode usar [deployment()](resource-group-template-functions.md#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Isso é útil se o local do modelo é alterado (talvez devido a controle de versão) ou para evitar embutir URLs no arquivo de modelo.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
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
          "name": "nestedTemplate",
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
          "value": "[reference('nestedTemplate').outputs.result]"
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

Você precisará fornecer o token SAS como um parâmetro. Você precisa preceder o token com **?**.

## Próximas etapas
- Para saber mais sobre como definir a ordem de implantação para seus recursos, confira [Definição de dependências nos modelos do Azure Resource Manager](resource-group-define-dependencies.md)
- Para saber como definir um recurso, mas criando várias instâncias dele, confira [Criar várias instâncias de recursos no Azure Resource Manager](resource-group-create-multiple.md)

<!---HONumber=AcomDC_0615_2016-->