<properties
    pageTitle="Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager | Microsoft Azure"
    description="Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# Criar um namespace dos Hubs de Eventos com um Hub de Eventos e um grupo de consumidores usando um modelo do Azure Resource Manager

Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace do Hubs de Evento com um Hub de Eventos e um grupo de consumidores. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, confira [Criando modelos do Azure Resource Manager][].

Para ver o modelo completo, veja o [Modelo de grupo de consumo e do Hub de Eventos][] no GitHub.

>[AZURE.NOTE] Os modelos do Azure Resource Manager a seguir estão disponíveis para download e implantação.
>
>-    [Create a Service Bus namespace with queue and authorization rule (Criar um namespace de Barramento de Serviço com fila e regra de autorização)](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Criar um namespace do Barramento de Serviço com fila](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um namespace do Barramento de Serviço com tópico e assinatura](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um namespace do Barramento de Serviço](service-bus-resource-manager-namespace.md)
>
>Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][] e procure por Hubs de Eventos.

## O que você implantará?

Com este modelo, você implantará um namespace Hubs de Evento com um Hub de Evento e grupo de consumidores.

[Hubs de Eventos](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters`, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### eventHubNamespaceName

O nome do namespace Hubs de Evento a criar.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### eventHubName

O nome do Hub de Evento criado no namespace Hubs de Evento.

```
"eventHubName": {
"type": "string"
}
```

### eventHubConsumerGroupName

O nome do grupo de consumo criado para o Hub de Evento.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### apiVersion

A versão da API do modelo.

```
"apiVersion": {
"type": "string"
}
```

## Recursos a implantar

Cria um namespace do tipo **Hubs de Eventos**, com um Hub de Eventos e um grupo de consumo.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
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

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## CLI do Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

## Próximas etapas

Agora que você criou e implantou recursos usando o Azure Resource Manager, saiba como gerenciar esses recursos consultando estes artigos:

- [Gerenciar Barramento de Serviço do Azure usando a Automação do Azure](service-bus-automation-manage.md)
- [Gerenciar Hubs de Eventos com o PowerShell](service-bus-powershell-how-to-provision.md)
- [Manage Event Hubs resources with the Service Bus Explorer (Gerenciar recursos de Hubs de Eventos com o Gerenciador do Barramento de Serviço)](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modelo de grupo de consumo e do Hub de Eventos]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/

<!---HONumber=AcomDC_0831_2016-->