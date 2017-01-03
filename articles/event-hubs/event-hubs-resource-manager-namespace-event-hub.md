---
title: Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager | Microsoft Docs
description: Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 28bb4591-1fd7-444f-a327-4e67e8878798
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 11/21/2016
ms.author: sethm;shvija
translationtype: Human Translation
ms.sourcegitcommit: a9e31954983fa673258917785a5bd828f1970fa1
ms.openlocfilehash: 92ec109c6cf9e3a2792ed68dfd96f86f5f9ae339


---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager
Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace do Hubs de Evento com um Hub de Eventos e um grupo de consumidores. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, consulte [Criando modelos do Azure Resource Manager][Criando modelos do Azure Resource Manager].

Para ver o modelo completo, confira o [Modelo de grupo de consumo e do Hub de Eventos][Modelo de grupo de consumo e do Hub de Eventos] (Hub de Eventos e modelo de grupo de consumo) no GitHub.

> [!NOTE]
> Para verificar os modelos mais recentes, acesse a galeria de [Modelos de Início Rápido do Azure][Modelos de Início Rápido do Azure] e procure por Hubs de Eventos.
> 
> 

## <a name="what-will-you-deploy"></a>O que você implantará?
Com este modelo, você implantará um namespace Hubs de Evento com um Hub de Evento e grupo de consumidores.

[Hubs de Eventos](event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro no modelo define os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
O nome do namespace Hubs de Evento a criar.

```json
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName
O nome do Hub de Evento criado no namespace Hubs de Evento.

```json
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName
O nome do grupo de consumo criado para o Hub de Evento.

```json
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion
A versão da API do modelo.

```json
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos a implantar
Cria um namespace do tipo **Hubs de Eventos**, com um Hub de Eventos e um grupo de consumo.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>CLI do Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modelos de Início Rápido do Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Usando o Azure PowerShell com o Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure]: ../xplat-cli-azure-resource-manager.md
[Modelo de grupo de consumo e do Hub de Eventos]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/



<!--HONumber=Nov16_HO5-->


