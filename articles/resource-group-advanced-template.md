<properties
   pageTitle="Operações avançadas de modelo do Gerenciador de Recursos do Azure"
   description="Descreve como usar modelos aninhados e a operação de cópia em um modelo do Gerenciador de Recursos do Azure ao implantar aplicativos no Azure."
   services="na"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="na"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="AzurePortal"
   ms.workload="na"
   ms.date="06/29/2015"
   ms.author="tomfitz"/>

# Operações avançadas de modelo

Este tópico descreve a operação de cópia e modelos aninhados que você pode usar para executar tarefas mais avançadas ao implantar recursos para o Azure.

## copiar

Permite que você itere um número específico de vezes durante a implantação de um recurso.
   
A operação de cópia é especialmente útil ao trabalhar com matrizes porque você pode percorrer cada elemento da matriz. A função **copyIndex()** retorna o valor atual para a iteração. Você pode implantar os três sites da Web chamados:

- examplecopy-Contoso
- examplecopy-Fabrikam
- examplecopy-Coho

com o modelo a seguir.

    "parameters": { 
      "org": { 
         "type": "array", 
             "defaultValue": [ 
             "Contoso", 
             "Fabrikam", 
             "Coho" 
          ] 
      },
      "count": { 
         "type": "int", 
         "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', parameters('org')[copyIndex()])]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

Você também pode usar a operação de cópia sem uma matriz. Por exemplo, pode ser útil adicionar um número incrementado no final de cada nome de recurso que é implantado. Você pode implantar três sites da Web chamados:

- examplecopy-0
- examplecopy-1
- examplecopy-2.

com o modelo a seguir.

    "parameters": { 
      "count": { 
        "type": "int", 
        "defaultValue": 3 
      } 
    }, 
    "resources": [ 
      { 
          "name": "[concat('examplecopy-', copyIndex())]", 
          "type": "Microsoft.Web/sites", 
          "location": "East US", 
          "apiVersion": "2014-06-01",
          "copy": { 
             "name": "websitescopy", 
             "count": "[parameters('count')]" 
          }, 
          "properties": {} 
      } 
    ]

Você pode observar no exemplo anterior que o valor de índice vai de zero a 2. Para deslocar o valor do índice, você pode passar um valor da função **copyIndex()**, como **copyIndex(1)**. O número de iterações a ser executado ainda é especificado no elemento de cópia, mas o valor de copyIndex é compensado pelo valor especificado. Desta maneira, usar o mesmo modelo do exemplo anterior, porém especificando **copyIndex(1)**, implantaria três sites da Web chamados:

- examplecopy-1
- examplecopy-2
- examplecopy-3

## Modelo aninhado

Talvez seja necessário mesclar dois modelos ou criar um modelo filho de um pai. Isso pode ser feito com o uso de um recurso de implantação dentro do modelo mestre que aponta para o modelo aninhado. Defina a propriedade **templateLink** para o URI do modelo aninhado. Você pode fornecer valores de parâmetro para o modelo aninhado especificando os valores diretamente em seu modelo ou vinculando a um arquivo de parâmetro. O primeiro exemplo usa a propriedade **parameters** para especificar um valor de parâmetro diretamente.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri": "https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion": "1.0.0.0"
           }, 
           "parameters": { 
              "StorageAccountName":{"value": "[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

O exemplo a seguir usa a propriedade **parametersLink** para vincular a um arquivo de parâmetro.

    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {
              "uri":"https://www.contoso.com/ArmTemplates/newStorageAccount.json",
              "contentVersion":"1.0.0.0"
           }, 
           "parametersLink": { 
              "uri":"https://www.contoso.com/ArmTemplates/parameters.json",
              "contentVersion":"1.0.0.0"
           } 
         } 
      } 
    ] 

## Próximas etapas
- [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md)
- [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md)
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](azure-portal/resource-group-template-deploy.md)
- [Visão Geral do Gerenciador de Recursos do Azure](./resource-group-overview.md)

<!---HONumber=July15_HO3-->