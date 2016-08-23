<properties 
	pageTitle="Mover dados de origens do OData | Azure Data Factory" 
	description="Saiba mais sobre como mover dados de fontes OData usando o Azure Data Factory." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/25/2016" 
	ms.author="spelluru"/>

# Mover dados De uma origem de OData usando o Azure Data Factory
Este artigo descreve como você pode usar o Copiar Atividade em um Azure Data Factory para mover os dados de uma fonte do OData para outro repositório de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

> [AZURE.NOTE] Esse conector OData dá suporte à cópia de dados das fontes OData de nuvem e OData local. Para o último, você precisa instalar o Gateway de Gerenciamento de Dados. Consulte o artigo [Mover dados entre local e nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes sobre o Gateway de Gerenciamento de Dados.

## Exemplo: copiar dados da origem do OData para o Blob do Azure

Este exemplo mostra como copiar dados de uma fonte do OData para o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** para qualquer uma das fontes declaradas [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.
 
O exemplo tem as seguintes entidades de data factory:

1.	Um serviço vinculado do tipo [OData](#odata-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [ODataResource](#odata-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](#odata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de consulta em relação a uma fonte OData para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado ao OData** Esse exemplo usa a autenticação Básica. Consulte a seção [Serviço vinculado a OData](#odata-linked-service-properties) para ver os diferentes tipos de autenticação que você pode usar.

    {
		"name": "ODataLinkedService",
       	"properties": 
        {
        	"type": "OData",
           	"typeProperties": 
            {
            	"url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           	}
       	}
    }


**Serviço vinculado de armazenamento do Azure**

	{
	  	"name": "AzureStorageLinkedService",
		"properties": {
	    	"type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    	}
	  	}
	}

**Conjunto de dados de entrada do OData**

Definir “external”: “true” e especificar a política externalData informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade dessa data factory.
	
    {
    	"name": "ODataDataset",
		"properties": 
		{
    		"type": "ODataResource",
    		"typeProperties": 
			{
     			"path": "Products" 
    		},
    		"linkedServiceName": "ODataLinkedService",
    		"structure": [],
    		"availability": {
        		"frequency": "Hour",
        		"interval": 1
    		},
			"external": true,
    		"policy": {
            	"retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
	            "maximumRetry": 3				
			}
		}
	}

Observe que especificar o **caminho** na definição do conjunto de dados é opcional.


**Conjunto de dados de saída de Blob do Azure**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobODataDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "MM"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "dd"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "HH"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **consulta** seleciona os dados mais recentes (mais novos) da fonte de OData.
	
	{
	    "name": "CopyODataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "?$select=Name, Description&$top=5",
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "ODataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobODataDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "ODataToBlob"
	            }
	        ],
	        "start": "2016-02-01T18:00:00Z",
	        "end": "2016-02-03T19:00:00Z"
	    }
	}


Observe que especificar a **consulta** na definição do pipeline é opcional. A **URL** que o serviço do Data Factory usa para recuperar dados é: a URL especificada no serviço vinculado (obrigatória) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline (opcional).

## Propriedades do Serviço vinculado OData

A tabela a seguir fornece a descrição para elementos JSON específicos do serviço vinculado do OData.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| type | A propriedade de tipo deve ser definida como: **OData** | Sim |
| url| URL do serviço OData. | Sim |
| authenticationType | Tipo de autenticação usada para se conectar ao armazenamento de dados OData. <br/><br/> Para o OData de nuvem, os valores possíveis são Anônima e Básica; para o OData local, os valores possíveis são Anônima, Básica e Windows. | Sim | 
| Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Básica. | Sim (apenas se você estiver usando a autenticação Básica) | 
| Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Sim (apenas se você estiver usando a autenticação Básica) | 
| gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao serviço OData local. Só especifique se você estiver copiando dados da fonte OData local. | Não |

### Usando a autenticação Básica

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### Usando a autenticação anônima
	
    {
		"name": "ODataLinkedService",
       	"properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### Usando a autenticação do Windows para acessar uma fonte OData local

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## Propriedades do tipo de conjunto de dados OData

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção typeProperties do conjunto de dados do tipo **ODataResource** (que inclui o conjunto de dados do OData) tem as propriedades a seguir

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| path | Caminho para o recurso OData | Não | 

## Propriedades de tipo de Atividade de Cópia do OData

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso de Copiar Atividade, quando a fonte é do tipo **RelationalSource** (que inclui o OData), as seguintes propriedades estão disponíveis na seção typeProperties:

| Propriedade | Descrição | Exemplo | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| query | Utiliza a consulta personalizada para ler os dados. | "?$select=Name, Description&$top=5" | Não | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipos para OData

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de repositórios de dados OData, os tipos de dados OData são mapeados para tipos .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0810_2016-->