<properties 
	pageTitle="Mover dados do Teradata | Azure Data Factory" 
	description="Saiba mais sobre o conector do Teradata para o serviço do Data Factory que permite mover dados do banco de dados Teradata" 
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
	ms.date="08/26/2015" 
	ms.author="spelluru"/>

# Mover dados do Teradata usando o Azure Data Factory

Este artigo descreve como você pode usar a atividade de cópia em um Azure Data Factory para mover dados do Teradata para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

O data factory dá suporte à conexão a fontes Teradata locais por meio do Gateway de Gerenciamento de Dados. Consulte o artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Gateway de gerenciamento de dados e obter instruções passo a passo de como configurar o gateway.

**Observação:** é necessário utilizar o gateway para se conectar ao Teradata, mesmo se ele estiver hospedado em VMs IaaS do Azure. Se estiver tentando se conectar a uma instância do Teradata hospedada na nuvem, você também pode instalar a instância do gateway na VM IaaS.

O data factory dá suporte apenas para a movimentação de dados do Teradata para outros armazenamentos de dados, não de outros armazenamentos de dados para o Teradata.

## Instalação 

Para o Gateway de Gerenciamento de Dados para se conectar ao banco de dados Teradata, você precisa instalar o [Provedor de dados .NET para Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) no mesmo sistema que o Gateway de Gerenciamento de Dados.

### Exemplo: copiar dados do Teradata para Blob do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.	O [pipeline](data-factory-create-pipelines.md) com atividade de cópia que usa [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados de um resultado de consulta no banco de dados Teradata para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

Como uma primeira etapa, configure o gateway de gerenciamento de dados de acordo com as instruções no artigo [movendo dados entre pontos locais e na nuvem](data-factory-move-data-between-onprem-and-cloud.md).

**Serviço Teradata vinculado:**

	{
	    "name": "OnPremTeradataLinkedService",
	    "properties": {
	        "type": "OnPremisesTeradata",
	        "typeProperties": {
	            "server": "<server>",
	            "database": "<database>",
	            "schema": "<schema>",
	            "authenticationType": "<authentication type>",
	            "username": "<username>",
	            "password": "<password>",
	            "gatewayName": "<gatewayName>"
	        }
	    }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorageLinkedService",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
			}
	    }
	}


**Conjunto de dados de entrada do Teradata:**

O exemplo supõe que você criou uma tabela "MyTable" no Teradata e que ela contém uma coluna chamada "timestamp" para dados de série temporal.

Definir “external”: true e especificar a política externalData informa ao data factory que a tabela é externa a ele e não é produzida por uma atividade nesse data factory.

	{
	    "name": "TeradataDataSet",
	    "properties": {
	        "published": false,
	        "type": "RelationalTable",
	        "linkedServiceName": "OnPremTeradataLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
			"external": true,
	        "policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}


**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.

	{
	    "name": "AzureBlobTeradataDataSet",
	    "properties": {
	        "published": false,
	        "location": {
	            "type": "AzureBlobLocation",
	            "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ],
	            "linkedServiceName": "AzureStorageLinkedService"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **RelationalSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **query** seleciona os dados na última hora para copiar.

	{
	    "name": "CopyTeradataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "TeradataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobTeradataDataSet"
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
	                "name": "TeradataToBlob"
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z",
	        "isPaused": false
	    }
	}


## Propriedades do serviço vinculado Teradata

A tabela a seguir fornece a descrição para elementos JSON específicos para o serviço vinculado Teradata.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade do tipo deve ser definida como: **OnPremisesTeradata** | Sim
server | Nome do servidor Teradata. | Sim
database | Nome do banco de dados Teradata. | Sim 
schema | Nome do esquema no banco de dados. | Não
authenticationType | Tipo de autenticação usado para se conectar ao banco de dados Teradata. Os valores possíveis são: Anonymous, Basic e Windows. | Sim
Nome de Usuário | Especifique o nome de usuário se você estiver usando a autenticação Basic ou Windows. | Não 
Senha | Especifique a senha da conta de usuário que você especificou para o nome de usuário. | Não 
gatewayName | O nome do gateway que o serviço Data Factory deve usar para se conectar ao banco de dados Teradata local. | Sim

Consulte [Definir credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security) para obter detalhes sobre como definir credenciais para uma fonte de dados do Teradata local.

## Propriedades de tipo de conjunto de dados Teradata

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** de um conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados Teradata) tem as propriedades a seguir.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
tableName | Nome da tabela na instância do banco de dados Teradata à qual o serviço vinculado se refere. | Sim 

## Propriedades de tipo de atividade de cópia do Teradata

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades como nome, descrição, tabelas de entrada e saída, diversas políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso da atividade de cópia, quando a fonte é do tipo **RelationalSource** (que inclui o Teradata), as seguintes propriedades estão disponíveis na seção **typeProperties**:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
query | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. | Não

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## Mapeamento de tipo para Teradata

Conforme mencionado no artigo [Atividades de movimentação de dados ](data-factory-data-movement-activities.md), A atividade de cópia executa conversões automáticas de tipos de fontes para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para Teradata os seguintes mapeamentos serão usados do tipo do Teradata para o tipo do .NET.

Tipo de banco de dados Teradata | Tipo .NET Framework
----------------- | ---------------------------
Char | Cadeia de caracteres
Clob | Cadeia de caracteres
Graphic | Cadeia de caracteres
VarChar | Cadeia de caracteres
VarGraphic | Cadeia de caracteres
Blob | Byte
Byte | Byte
VarByte | Byte
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Duplo | Duplo
Número inteiro | Int32
Número | Duplo
SmallInt | Int16
Data | DateTime
Hora | TimeSpan
Hora com fuso horário | Cadeia de caracteres
Timestamp | DateTime
Timestamp With Time Zone | DateTimeOffset
Intervalo - dia | TimeSpan
Intervalo - dia para hora | TimeSpan
Intervalo - dia para minuto | TimeSpan
Interval Day To Second | TimeSpan
Intervalo - hora | TimeSpan
Intervalo - hora para minuto | TimeSpan
Interval Hour To Second | TimeSpan
Interval Minute | TimeSpan
Interval Minute To Second | TimeSpan
Interval Second | TimeSpan
Interval Year | Cadeia de caracteres
Interval Year To Month | Cadeia de caracteres
Interval Month | Cadeia de caracteres
Period(Date) | Cadeia de caracteres
Period(Time) | Cadeia de caracteres
Period(Time With Time Zone) | Cadeia de caracteres
Period(Timestamp) | Cadeia de caracteres
Period(Timestamp With Time Zone) | Cadeia de caracteres
Xml | Cadeia de caracteres

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=Oct15_HO3-->