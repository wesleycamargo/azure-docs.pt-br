<properties
    pageTitle="Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager | Microsoft Azure"
    description="Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager"
    services="event-hubs"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="09/14/2016"
    ms.author="ShubhaVijayasarathy"/>

# Criar um namespace de Hubs de Eventos com o Hub de Eventos e habilitar o Arquivo Morto usando um modelo do Azure Resource Manager

Este artigo mostra como usar um modelo do Azure Resource Manager, que cria um namespace de Hubs de Eventos com um Hub de Eventos e habilita o Arquivo Morto em seu Hub de Eventos. Você aprenderá como definir quais recursos são implantados e como definir os parâmetros que são especificados quando a implantação é executada. Você pode usar este modelo para suas próprias implantações ou personalizá-lo para atender às suas necessidades

Para saber mais sobre a criação de modelos, confira [Criando modelos do Azure Resource Manager][].

Para obter mais informações sobre as práticas e os padrões de convenções de nomenclatura de Recursos do Azure, confira [Azure Resources Naming Conventions][] (Convenções de Nomenclatura de Recursos do Azure).

Para ver o modelo completo, confira [Event Hub and enable Archive template][] (Hub de Eventos e modelo para habilitar o Arquivo Morto) no GitHub.

>[AZURE.NOTE]
Para verificar os modelos mais recentes, visite a galeria [Modelos de Início Rápido do Azure][] e procure por Hubs de Eventos.

## O que você implantará?

Com este modelo, você implantará um namespace de Hubs de Eventos com um Hub de Eventos e habilitará o Arquivo Morto.

[Hubs de Eventos](../event-hubs/event-hubs-what-is-event-hubs.md) é um serviço de processamento de eventos usado para fornecer entrada a telemetria e eventos para o Azure em grande escala, com baixa latência e alta confiabilidade. O Arquivo Morto de Hubs de Eventos permitirá que você forneça automaticamente os dados de streaming em seus Hubs de Eventos para o Armazenamento de Blobs do Azure de sua escolha, dentro de um período especificado ou do intervalo de tamanho de sua preferência.

Para executar a implantação automaticamente, clique no seguinte botão:

[![Implantar no Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-eventhubs-create-namespace-and-enable-archive%2Fazuredeploy.json)

## Parâmetros

Com o Gerenciador de Recursos do Azure, você define parâmetros para os valores que deseja especificar quando o modelo é implantado. O modelo inclui uma seção chamada `Parameters`, que contém todos os valores de parâmetro. Você deve definir um parâmetro para os valores que variam de acordo com o projeto que você está implantando ou com o ambiente em que a implantação ocorre. Não defina parâmetros para valores que permanecem sempre os mesmos. Cada valor de parâmetro é usado no modelo para definir os recursos que são implantados.

O modelo define os parâmetros a seguir.

### eventHubNamespaceName

O nome do namespace Hubs de Evento a criar.

```
"eventHubNamespaceName":{  
     "type":"string",
     "metadata":{  
         "description":"Name of the EventHub namespace"
      }
}
```

### eventHubName

O nome do Hub de Evento criado no namespace Hubs de Evento.

```
"eventHubName":{  
    "type":"string",
    "metadata":{  
        "description":"Name of the Event Hub"
    }
}
```

### messageRetentionInDays

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

### partitionCount

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

### archiveEnabled

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
### archiveEncodingFormat

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

### archiveTime

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

### archiveSize

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

### destinationStorageAccountResourceId

O Arquivo Morto exigirá uma ID de recurso da conta de armazenamento para habilitar o arquivamento no Armazenamento do Azure desejado.

```
 "destinationStorageAccountResourceId":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Account resource id where you want the blobs be archived"
	}
 }
```

### blobContainerName

O contêiner de blob no qual você deseja que seus dados de evento sejam arquivados.

```
 "blobContainerName":{
	"type":"string",
	"metadata":{
		"description":"Your existing storage Container that you want the blobs archived in"
	}
}
```


### apiVersion

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

## Recursos a implantar

Cria um namespace do tipo **EventHubs** com um Hub de Eventos e habilita o Arquivo Morto.

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

## Comandos para executar a implantação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName <resource-group-name> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json
```

## CLI do Azure

```
azure config mode arm

azure group deployment create <my-resource-group> <my-deployment-name> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-eventhubs-create-namespace-and-enable-archive/azuredeploy.json][]
```

## Próximas etapas

Agora que você criou e implantou recursos usando o Azure Resource Manager, saiba como gerenciar esses recursos consultando estes artigos:

- [Manage Event Hubs resources with the Service Bus Explorer (Gerenciar recursos de Hubs de Eventos com o Gerenciador do Barramento de Serviço)](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)

  [Criando modelos do Azure Resource Manager]: ../resource-group-authoring-templates.md
  [Modelos de Início Rápido do Azure]: https://azure.microsoft.com/documentation/templates/?term=event+hubs
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Event Hub and consumer group template]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-eventhubs-create-namespace-and-enable-archive/
  [Azure Resources Naming Conventions]: https://azure.microsoft.com/pt-BR/documentation/articles/guidance-naming-conventions/
  [Event Hub and enable Archive template]: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-archive]

<!---HONumber=AcomDC_0914_2016-->