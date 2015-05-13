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
   ms.date="04/28/2015"
   ms.author="tomfitz;ilygre"/>

# Operações avançadas de modelo

Este tópico descreve a operação de cópia e modelos aninhados que você pode usar para executar tarefas mais avançadas ao implantar recursos para o Azure.

## cópia

Permite que você use iterar por uma matriz e use cada elemento durante a implantação de um recurso.
   
O exemplo a seguir implementa três sites da Web chamados examplecopy-Contoso, examplecopy-Fabrikam e examplecopy-Coho.

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

## Modelo aninhado

Às vezes você pode precisar mesclar dois modelos, ou talvez você precise iniciar um modelo filho em um modelo pai. Isso pode ser feito com o uso de um recurso de implantação no modelo mestre para implantar um modelo filho. Você fornece o URI do modelo aninhado, conforme mostrado abaixo.

    "variables": {"templatelink":"https://www.contoso.com/ArmTemplates/newStorageAccount.json"}, 
    "resources": [ 
      { 
         "apiVersion": "2015-01-01", 
         "name": "nestedTemplate", 
         "type": "Microsoft.Resources/deployments", 
         "properties": { 
           "mode": "incremental", 
           "templateLink": {"uri":"[variables('templatelink')]","contentVersion":"1.0.0.0"}, 
           "parameters": { 
              "StorageAccountName":{"value":"[parameters('StorageAccountName')]"} 
           } 
         } 
      } 
    ] 

## Próximas etapas
- [Criação de modelos do Gerenciador de Recursos do Azure](./resource-group-authoring-templates.md)
- [Funções de modelo do Gerenciador de Recursos do Azure](./resource-group-template-functions.md)
- [Implantar um aplicativo com o modelo do Gerenciador de Recursos do Azure](./resouce-group-template-deploy.md)
- [Visão Geral do Gerenciador de Recursos do Azure](./resource-group-overview.md)

<!--HONumber=52-->
