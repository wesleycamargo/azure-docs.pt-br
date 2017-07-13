---
title: Criar um namespace de Hubs de Eventos do Azure e habilitar a Captura usando um modelo | Microsoft Docs
description: Criar um namespace de Hubs de Eventos do Azure com um hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: get-started-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/28/2017
ms.author: shvija;sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: f19a3d9b323d75ae23480d0699d55b79bb7d2e84
ms.contentlocale: pt-br
ms.lasthandoff: 06/28/2017


---
# Criar um namespace de Hubs de Eventos com o hub de eventos e habilitar a Captura usando um modelo do Azure Resource Manager
<a id="create-an-event-hubs-namespace-with-an-event-hub-and-enable-capture-using-an-azure-resource-manager-template" class="xliff"></a>
Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace de Hubs de Eventos, com uma instância de hub de eventos e também habilita o recurso Captura no hub de eventos. O artigo descreve como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter mais informações sobre as práticas e os padrões de convenções de nomenclatura de Recursos do Azure, confira [Convenções de Nomenclatura de Recursos do Azure][Azure Resources Naming Conventions].

Para ver o modelo completo, consulte [Hub de Eventos e habilitar o modelo de Captura][Event Hub and enable Capture template] no GitHub.

> [!NOTE]
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure por Hubs de Eventos.
> 
> 

## O que você implantará?
<a id="what-will-you-deploy" class="xliff"></a>
Com esse modelo, você implanta um namespace de Hub de Eventos com um hub de eventos e também habilita a [Captura dos Hubs de Eventos](event-hubs-capture-overview.md).

[Hubs de Eventos](event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade. A Captura dos Hubs de Eventos permite que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para o Armazenamento de Blobs do Azure de sua escolha, dentro de um período especificado ou do intervalo de tamanho de sua preferência.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-capture%2Fazuredeploy.json)

## Parâmetros
<a id="parameters" class="xliff"></a>
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### eventHubNamespaceName
<a id="eventhubnamespacename" class="xliff"></a>
O nome do namespace Hubs de Evento a criar.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName
<a id="eventhubname" class="xliff"></a>
O nome do hub de eventos criado no namespace Hubs de Eventos.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### messageRetentionInDays
<a id="messageretentionindays" class="xliff"></a>
O número de dias para manter as mensagens no hub de eventos. 

```json
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in event hub"
     }
 }
```

### partitionCount
<a id="partitioncount" class="xliff"></a>
O número de partições a serem criadas no hub de eventos.

```json
"partitionCount":{
    "type":"int",
    "defaultValue":2,
    "minValue":2,
    "maxValue":32,
    "metadata":{
        "description":"Number of partitions chosen"
    }
 }
```

### captureEnabled
<a id="captureenabled" class="xliff"></a>
Habilite a Captura no hub de eventos.

```json
"captureEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Capture for your event hub"
    }
 }
```
### captureEncodingFormat
<a id="captureencodingformat" class="xliff"></a>
O formato de codificação que você especifica para serializar os dados do evento.

```json
"captureEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format in which Capture serializes the EventData"
    }
}
```

### captureTime
<a id="capturetime" class="xliff"></a>
O intervalo de tempo no qual a Captura de Hubs de Eventos inicia a captura de dados para o Armazenamento de Blobs do Azure.

```json
"captureTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the capture"
    }
}
```

### captureSize
<a id="capturesize" class="xliff"></a>
O intervalo de tamanho no qual a Captura inicia a captura de dados para o Armazenamento de Blobs do Azure.

```json
"captureSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"The size window in bytes for capture"
    }
}
```

### destinationStorageAccountResourceId
<a id="destinationstorageaccountresourceid" class="xliff"></a>
A Captura exige uma ID de recurso da conta de Armazenamento do Azure para habilitar a captura na sua conta de armazenamento desejada.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing Storage account resource id where you want the blobs be captured"
    }
 }
```

### blobContainerName
<a id="blobcontainername" class="xliff"></a>
O contêiner de blob no qual deseja capturar os dados de evento.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container in which you want the blobs captured"
    }
}
```


### apiVersion
<a id="apiversion" class="xliff"></a>
A versão da API do modelo.

```json
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## Recursos a implantar
<a id="resources-to-deploy" class="xliff"></a>
Cria um namespace do tipo **EventHubs** com um hub de eventos, e também habilita a Captura.

```json
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('eventHubNamespaceName')]",
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
                  "[concat('Microsoft.EventHub/namespaces/', parameters('eventHubNamespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]",
                  "MessageRetentionInDays":"[parameters('messageRetentionInDays')]",
                  "PartitionCount":"[parameters('partitionCount')]",
                  "CaptureDescription":{
                        "enabled":"[parameters('captureEnabled')]",
                        "encoding":"[parameters('captureEncodingFormat')]",
                        "intervalInSeconds":"[parameters('captureTime')]",
                        "sizeLimitInBytes":"[parameters('captureSize')]",
                        "destination":{
                            "name":"EventHubCapture.AzureBlockBlob",
                            "properties":{
                                "StorageAccountResourceId":"[parameters('destinationStorageAccountResourceId')]",
                                "BlobContainer":"[parameters('blobContainerName')]"
                            }
                        } 
                  }

               }

            }
         ]
      }
   ]
```

## Comandos para executar a implantação
<a id="commands-to-run-deployment" class="xliff"></a>
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell
<a id="powershell" class="xliff"></a>
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json
```

## CLI do Azure
<a id="azure-cli" class="xliff"></a>
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-capture/azuredeploy.json][]
```
## Próximas etapas
<a id="next-steps" class="xliff"></a>

Você também pode configurar a Captura de Hubs de Eventos por meio do [portal do Azure](https://portal.azure.com). Para saber mais, confira [Habilitar a Captura de Hubs de Eventos usando o portal do Azure](event-hubs-capture-enable-through-portal.md).

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Capture template]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-capture

