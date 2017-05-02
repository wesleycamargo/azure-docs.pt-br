---
title: Criar um namespace de Hubs de Eventos do Azure e habilitar o Arquivo Morto usando um modelo | Microsoft Docs
description: Criar um namespace de Hubs de Eventos do Azure com um hub de eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 8bdda6a2-5ff1-45e3-b696-c553768f1090
ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/07/2017
ms.author: shvija;sethm
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 62094629d899f27f56b3afc87bc08c96371acea8
ms.lasthandoff: 04/18/2017


---
# <a name="create-an-event-hubs-namespace-with-an-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Criar um namespace de Hubs de Eventos com o hub de eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager
Este artigo mostra como usar um modelo do Azure Resource Manager que cria um namespace do tipo Hubs de Eventos, com uma instância de hub de eventos e também habilita o recurso de Arquivo Morto no hub de eventos. O artigo descreve como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para obter mais informações sobre a criação de modelos, consulte [Criação de Modelos do Azure Resource Manager][Authoring Azure Resource Manager templates].

Para obter mais informações sobre as práticas e os padrões de convenções de nomenclatura de Recursos do Azure, confira [Convenções de Nomenclatura de Recursos do Azure][Azure Resources Naming Conventions].

Para ver o modelo completo, consulte [Hub de Eventos e habilitar o modelo de Arquivo Morto][Event Hub and enable Archive template] no GitHub.

> [!NOTE]
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Azure Quickstart Templates] e procure por Hubs de Eventos.
> 
> 

## <a name="what-will-you-deploy"></a>O que você implantará?
Com esse modelo, você implanta um namespace de Hub de Eventos com um hub de eventos e também habilita o Arquivo Morto dos Hubs de Eventos.

[Hubs de Eventos](event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade. O Arquivo Morto de Hubs de Eventos permite que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para o Armazenamento de Blobs do Azure de sua escolha, dentro de um período especificado ou do intervalo de tamanho de sua preferência.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
O nome do namespace Hubs de Evento a criar.

```json
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName
O nome do hub de eventos criado no namespace Hubs de Eventos.

```json
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the event hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays
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

### <a name="partitioncount"></a>partitionCount
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

### <a name="archiveenabled"></a>archiveEnabled
Habilite o Arquivo Morto no hub de eventos.

```json
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your event hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat
O formato de codificação que você especifica para serializar os dados do evento.

```json
"archiveEncodingFormat":{
    "type":"string",
    "defaultValue":"Avro",
    "allowedValues":[
    "Avro"],
    "metadata":{
        "description":"The encoding format Archive serializes the EventData"
    }
}
```

### <a name="archivetime"></a>archiveTime
O intervalo de tempo em que o Arquivo Morto começa a arquivar os dados no Armazenamento de Blobs do Azure.

```json
"archiveTime":{
    "type":"int",
    "defaultValue":300,
    "minValue":60,
    "maxValue":900,
    "metadata":{
         "description":"the time window in seconds for the archival"
    }
}
```

### <a name="archivesize"></a>archiveSize
O intervalo de tamanho em que o Arquivo Morto começa a arquivar os dados no Armazenamento de Blobs do Azure.

```json
"archiveSize":{
    "type":"int",
    "defaultValue":314572800,
    "minValue":10485760,
    "maxValue":524288000,
    "metadata":{
        "description":"the size window in bytes for archival"
    }
}
```

### <a name="destinationstorageaccountresourceid"></a>destinationStorageAccountResourceId
O Arquivo Morto exige uma ID de recurso da conta de Armazenamento do Azure para habilitar o arquivamento na sua conta de armazenamento desejada.

```json
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName
O contêiner de blob no qual deseja arquivar os dados de evento.

```json
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion
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

## <a name="resources-to-deploy"></a>Recursos a implantar
Cria um namespace do tipo **EventHubs** com um hub de eventos e habilita o Arquivo Morto.

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
                  "ArchiveDescription":{
                        "enabled":"[parameters('archiveEnabled')]",
                        "encoding":"[parameters('archiveEncodingFormat')]",
                        "intervalInSeconds":"[parameters('archiveTime')]",
                        "sizeLimitInBytes":"[parameters('archiveSize')]",
                        "destination":{
                            "name":"EventHubArchive.AzureBlockBlob",
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

## <a name="commands-to-run-deployment"></a>Comandos para executar a implantação
[!INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell
```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI do Azure
```cli
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```
## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[Authoring Azure Resource Manager templates]: ../azure-resource-manager/resource-group-authoring-templates.md
[Azure Quickstart Templates]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Event hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Azure Resources Naming Conventions]: https://azure.microsoft.com/documentation/articles/guidance-naming-conventions/
[Event hub and enable Archive template]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive

