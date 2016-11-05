---
title: Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager | Microsoft Docs
description: Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
manager: timlt
editor: ''

ms.service: event-hubs
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 09/14/2016
ms.author: ShubhaVijayasarathy

---
# <a name="create-an-event-hubs-namespace-with-event-hub-and-enable-archive-using-an-azure-resource-manager-template"></a>Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager
Este artigo mostra como usar um modelo do Azure Resource Manager, que cria um namespace de Hubs de Eventos com um Hub de Eventos e habilita o Arquivo Morto em seu Hub de Eventos. Você aprende como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, consulte [Criação de modelos do Azure Resource Manager][].

Para obter mais informações sobre as práticas e os padrões de convenções de nomenclatura de Recursos do Azure, confira [Azure Resources Naming Conventions][](Convenções de Nomenclatura de Recursos do Azure.md).

Para ver o modelo completo, confira [Event Hub and enable Archive template][](Hub de Eventos e modelo para habilitar o Arquivo Morto.md) no GitHub.

> [!NOTE]
> Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][Modelos de Início Rápido do Azure] e procure por Hubs de Eventos.
> 
> 

## <a name="what-you-deploy?"></a>O que implantar?
Com este modelo, você implanta um namespace de Hubs de Eventos com um Hub de Eventos e habilita o Arquivo Morto.

[Hubs de Eventos](event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade. O Arquivo Morto de Hubs de Eventos permitirá que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para o Armazenamento de Blobs do Azure de sua escolha, dentro de um período especificado ou do intervalo de tamanho de sua preferência.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros
Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters` , que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName
O nome do namespace Hubs de Evento a criar.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### <a name="eventhubname"></a>eventHubName
O nome do Hub de Evento criado no namespace Hubs de Evento.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### <a name="messageretentionindays"></a>messageRetentionInDays
O número de dias que você quer que as mensagens sejam retidas em seu Hub de Eventos. 

```
"messageRetentionInDays":{
    "type":"int",
    "defaultValue": 1,
    "minValue":"1",
    "maxValue":"7",
    "metadata":{
       "description":"How long to retain the data in Event Hub"
     }
 }
```

### <a name="partitioncount"></a>partitionCount
O número de partições que você deseja em seu Hub de Eventos.

```
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
Habilite o Arquivo Morto em seu Hub de Eventos.

```
"archiveEnabled":{
    "type":"string",
    "defaultValue":"true",
    "allowedValues": [
    "false",
    "true"],
    "metadata":{
        "description":"Enable or disable the Archive for your Event Hub"
    }
 }
```
### <a name="archiveencodingformat"></a>archiveEncodingFormat
O formato de codificação que você especificar para serializar os Dados do Evento.

```
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

```
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

```
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
O Arquivo Morto exige uma ID de recurso da conta de armazenamento para habilitar o arquivamento no Armazenamento do Azure desejado.

```
 "destinationStorageAccountResourceId":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Account resource id where you want the blobs be archived"
    }
 }
```

### <a name="blobcontainername"></a>blobContainerName
O contêiner de blob no qual você deseja que seus dados de evento sejam arquivados.

```
 "blobContainerName":{
    "type":"string",
    "metadata":{
        "description":"Your existing storage Container that you want the blobs archived in"
    }
}
```


### <a name="apiversion"></a>apiVersion
A versão da API do modelo.

```
 "apiVersion":{  
    "type":"string",
    "defaultValue":"2015-08-01",
    "metadata":{  
        "description":"ApiVersion used by the template"
    }
 }
```

## <a name="resources-to-deploy"></a>Recursos a implantar
Cria um namespace do tipo **EventHubs**com um Hub de Eventos e habilita o Arquivo Morto.

```
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
```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## <a name="azure-cli"></a>CLI do Azure
```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

[Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
[Modelos de início rápido do Azure]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Usando o Azure PowerShell com o Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Usando a CLI do Azure para Mac, Linux e Windows com o Gerenciamento de Recursos do Azure]: ../xplat-cli-azure-resource-manager.md
[Modelo de grupo de consumo e do Hub de Eventos]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
[Convenções de nomenclatura dos Recursos do Azure]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
[Hub de Eventos e modelo para habilitar o Arquivo Morto]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive



<!--HONumber=Oct16_HO2-->


