<properties
   pageTitle="Usando modelos vinculados com o Gerenciador de Recursos do Azure"
   description="Descreve como usar modelos vinculados em um modelo do Gerenciador de Recursos do Azure para criar uma solução de modelo modular. Mostra como passar valores de parâmetros, especificar um arquivo de parâmetro e URLs criadas dinamicamente."
   services="azure-resource-manager"
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
   ms.date="09/04/2015"
   ms.author="tomfitz"/>

# Usando modelos vinculados com o Gerenciador de Recursos do Azure

Em um modelo do Gerenciador de Recursos do Azure, você pode vincular a outro modelo, o que permite decompor sua implantação em um conjunto de modelos orientados para fins específicos. Assim como com a decomposição de um aplicativo em várias classes de código, a decomposição oferece benefícios em termos de teste, reutilização e legibilidade.

Você pode passar parâmetros de um modelo principal a um modelo vinculado e esses parâmetros podem corresponder diretamente aos parâmetros ou variáveis expostos pelo modelo de chamada. O modelo vinculado também pode passar uma variável de saída de volta para o modelo de origem, permitindo uma troca de dados bidirecional entre os modelos.

## Vinculando a um modelo

Para criar um vínculo entre dois modelos, adicione um recurso de implantação no modelo principal que aponta para o modelo vinculado. Defina a propriedade **templateLink** como o URI do modelo vinculado. Você pode fornecer valores de parâmetro para o modelo vinculado especificando os valores diretamente em seu modelo ou vinculando a um arquivo de parâmetro. O primeiro exemplo usa a propriedade **parameters** para especificar um valor de parâmetro diretamente.

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

O valor do URI para o arquivo de parâmetro vinculado não pode ser um arquivo local e deve incluir **http** ou **https**.

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

Você também pode usar [deployment()](resource-group-template-functions.md/#deployment) para obter a URL base para o modelo atual e usá-lo para obter a URL para outros modelos no mesmo local. Isso é útil se o local do modelo é alterado (talvez devido a controle de versão) ou para evitar embutir URLs no arquivo de modelo.

    "variables": {
        "sharedTemplateUrl": "[uri(deployment().properties.templateLink.uri, 'shared-resources.json')]"
    }

## Passando valores de um modelo vinculado

Se você precisar passar um valor de modelo vinculado para o modelo principal, poderá criar um valor na seção **saídas** do modelo vinculado. Para obter um exemplo, confira [Compartilhando o estado em modelos do Gerenciador de Recursos do Azure](best-practices-resource-manager-state.md).

## Próximas etapas
- [Criação de modelos](./resource-group-authoring-templates.md)
- [Implantação de modelos](resource-group-template-deploy.md)

<!---HONumber=AcomDC_1203_2015-->