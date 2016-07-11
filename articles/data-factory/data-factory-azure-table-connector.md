<properties 
	pageTitle="Mover dados de e para a Tabela do Azure | Microsoft Azure" 
	description="Saiba como mover dados para/do Armazenamento de Tabela do Azure usando o Azure Data Factory" 
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
	ms.date="06/28/2016" 
	ms.author="spelluru"/>

# Mover dados para e da Tabela do Azure | Azure Data Factory

Este artigo descreve como você pode usar a Atividade de Cópia em um Azure Data Factory para mover dados de outro armazenamento de dados para a Tabela do Azure e mover dados da Tabela do Azure para outro armazenamento de dados. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

O(s) exemplo(s) a seguir mostra(m) como copiar dados de e para o Armazenamento de Tabelas do Azure e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a atividade de cópia no Azure Data Factory.


## Exemplo: Copiar dados da Tabela do Azure para o Blob do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usado para a tabela e o blob).
2.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureTable](#azure-table-dataset-type-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
3.	O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [AzureTableSource](#azure-table-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados pertencentes à partição padrão em uma tabela do Azure para um blob a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado de armazenamento do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

O Azure Data Factory dá suporte a dois tipos de serviços vinculados do Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-services) para obter detalhes.

**Conjunto de dados de entrada da Tabela do Azure:**

O exemplo pressupõe que você tenha criado uma tabela "MyTable" na Tabela do Azure.
 
Definir "external": "true" e especificar a política externalData informa à data factory que essa é uma tabela externa à data factory e não é produzida por uma atividade nessa data factory.

	{
	  "name": "AzureTableInput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
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
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **AzureTableSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada com a propriedade **AzureTableSourceQuery** seleciona os dados da partição padrão na última hora a serem copiados.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
		    "end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
				{
	        		"name": "AzureTabletoBlob",
	        		"description": "copy activity",
	        		"type": "Copy",
	        		"inputs": [
	          			{
		            		"name": "AzureTableInput"
		        		}
	        		],
	        		"outputs": [
	          			{
	          	  			"name": "AzureBlobOutput"
		          		}
		        	],
		        	"typeProperties": {
		          		"source": {
		            		"type": "AzureTableSource",
				            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
		          		},
		          		"sink": {
		            		"type": "BlobSink"
		          		}
    	    		},
		        	"scheduler": {
		          		"frequency": "Hour",
		          		"interval": 1
		        	},				
		        	"policy": {
		          		"concurrency": 1,
		          		"executionPriorityOrder": "OldestFirst",
		          		"retry": 0,
		          		"timeout": "01:00:00"
		        	}
				}
		     ]	
		}
	}

## Exemplo: Copiar dados do Blob do Azure para a Tabela do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (usado para ambos tabela e blob)
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureTable](#azure-table-dataset-type-properties).
4.	O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [AzureTableSink](#azure-table-copy-activity-type-properties).


O exemplo copia os dados pertencentes a uma série temporal de um blob do Azure para uma tabela no banco de dados da Tabela do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço de armazenamento vinculado do Azure (para Tabela e Blob do Azure):**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

O Azure Data Factory dá suporte a dois tipos de serviços vinculados do Armazenamento do Azure: **AzureStorage** e **AzureStorageSas**. Para o primeiro, você especifica a cadeia de conexão que inclui a chave de conta, e para o mais recente, você especifica o URI de SAS (Assinatura de Acesso Compartilhado). Confira a seção [Serviços vinculados](#linked-services) para obter detalhes.

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e nome de arquivo usa a parte de hora da hora de início. A configuração "external": "true" informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade na data factory.
	
	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
	      "fileName": "{Hour}.csv",
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
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

**Conjunto de dados de saída de Tabela do Azure:**

O exemplo copia dados para uma tabela chamada "MyTable" na Tabela do Azure. Você deve criar a tabela na Tabela do Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline com Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **AzureTableSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoTable",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureTableOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "AzureTableSink",
	            "writeBatchSize": 100,
	            "writeBatchTimeout": "01:00:00"
	          }
	        },
	        "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },						
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	      ]
	   }
	}

## Serviços vinculados
Existem dois tipos de serviço vinculado que você pode usar para vincular um armazenamento de blobs do Azure a um data factory do Azure. São eles: o serviço vinculado **AzureStorage** e o serviço vinculado **AzureStorageSas**. O serviço vinculado do Armazenamento do Azure fornece o data factory com acesso global ao Armazenamento do Azure. Já o serviço vinculado SAS (Assinatura de Acesso Compartilhado) do Armazenamento do Azure fornece o data factory com acesso restrito/associado ao tempo ao Armazenamento do Azure. Não há outras diferenças entre esses dois serviços vinculados. Escolha o serviço vinculado que atenda às suas necessidades. As seções a seguir fornecem mais detalhes sobre esses dois serviços vinculados.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## Propriedades de tipo do Conjunto de Dados da Tabela do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AzureTable** tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela na instância do Banco de Dados da Tabela do Azure à qual o serviço vinculado se refere. | Sim. Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino. |

### Esquema do Data Factory
Para armazenamentos de dados sem esquema, como a Tabela do Azure, o serviço do Data Factory infere o esquema usando uma das seguintes maneiras:

1.	Se você especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory respeitará essa estrutura do esquema. Nesse caso, se uma linha não contiver um valor de uma coluna, um valor nulo será fornecido para ele.
2.	Se você não especificar a estrutura dos dados usando a propriedade **structure** na definição de conjunto de dados, o serviço do Data Factory inferirá o esquema usando a primeira linha dos dados. Nesse caso, se a primeira linha não contiver o esquema completo, algumas colunas estarão ausentes no resultado da operação de cópia.

Portanto, para fontes de dados sem esquema, a prática recomendada é especificar a estrutura de dados usando a propriedade **structure**.

## Propriedades de tipo de atividade de cópia de Tabela do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

**AzureTableSource** dá suporte às seguintes propriedades na seção typeProperties:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta de tabela do Azure. Veja os exemplos abaixo. | Quando um nome de tabela é especificado sem uma azureTableSourceQuery, todos os registros da tabela são copiados para o destino. Se uma azureTableSourceQuery também for especificada, os registros da tabela que atende à consulta são copiados para o destino.  
azureTableSourceIgnoreTableNotFound | Indique se assimilar a exceção da tabela não existe. | TRUE<br/>FALSE | Não |

### Exemplos do azureTableSourceQuery

Se a coluna Tabela do Azure é do tipo cadeia de caracteres:

	azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Se a coluna Tabela do Azure é do tipo datetime:

	"azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** dá suporte às seguintes propriedades na seção typeProperties:


Propriedade | Descrição | Valores permitidos | Obrigatório  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Valor de chave de partição padrão que pode ser utilizado pelo coletor. | Um valor de cadeia de caracteres. | Não 
azureTablePartitionKeyName | Nome de coluna do usuário especificado, cujos valores de coluna são utilizados como chave de partição. Se não especificado, AzureTableDefaultPartitionKeyValue será utilizado como a chave da partição. | Um nome de coluna. | Não |
azureTableRowKeyName | Nome de coluna especificado pelo usuário, cujos valores de coluna são utilizados como chave de linha. Se não especificado, um GUID é usado para cada linha. | Um nome de coluna. | Não  
azureTableInsertType | O modo para inserir dados na tabela do Azure.<br/><br/>Essa propriedade controla se linhas existentes na tabela de saída com a partição correspondente e as chaves de linha terão seus valores substituídos ou mesclados. <br/><br/>Consulte os tópicos [Inserir ou Mesclar Entidade](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou Substituir Entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) para saber mais sobre como essas configurações (mescla e substituição) funcionam. <br/><br> Observe que essa configuração se aplica ao nível de linha e não ao nível de tabela e nenhuma das opções excluirá as linhas na tabela de saída que não existem na entrada. | mesclar (padrão)<br/>substituir | Não 
writeBatchSize | Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido. | Número inteiro | Não (padrão: 10000) 
writeBatchTimeout | Insere dados na tabela do Azure quando o writeBatchSize ou writeBatchTimeout for atingido | TimeSpan<br/><br/>Exemplo: "00:20:00" (20 minutos) | Não (padrão para 90 seg. de valor de tempo padrão de cliente de armazenamento)

### azureTablePartitionKeyName
Será necessário mapear uma coluna de origem para uma coluna de destino usando a propriedade JSON do conversor antes de poder usar a coluna de destino como o azureTablePartitionKeyName.

No exemplo a seguir, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.

	"translator": {
		"type": "TabularTranslator",
		"columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
	} 

EmpID é especificada como a chave da partição.

	"sink": {
		"type": "AzureTableSink",
		"azureTablePartitionKeyName": "DivisionID",
		"writeBatchSize": 100,
		"writeBatchTimeout": "01:00:00"
	}


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo de Tabela do Azure

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em 2 etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados para e da Tabela do Azure, os seguintes [mapeamentos definidos pelo serviço Tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) serão usados nos tipos OData da Tabela do Azure para o tipo .NET e vice-versa.

| Tipo de dados OData | Tipo .NET | Detalhes |
| --------------- | --------- | ------- |
| Edm.Binary | byte | Uma matriz de bytes de até 64 KB de tamanho. |
| Edm.Boolean | bool | Um valor booliano. |
| Edm.DateTime | DateTime | Um valor de 64 bits expressado como Tempo Universal Coordenado (UTC). O intervalo de data e hora com suporte começa à 00:00 de 1º de janeiro de 1601 D.C. (C.E.), UTC. O intervalo termina em 31 de dezembro de 9999. |
| Edm.Double | double | Um valor de ponto flutuante de 64 bits. |
| Edm.Guid | Guid | Um identificador global exclusivo de 128 bits. |
| Edm.Int32 | Int32 ou int | Um inteiro de 32 bits. |
| Edm.Int64 | Int64 ou longo | Um inteiro de 64 bits. |
| Edm.String | Cadeia de caracteres | Um valor codificado em UTF-16. Valores de cadeia de caracteres podem ter até 64 KB em tamanho. |

### Exemplo de conversão de tipo

O exemplo a seguir é para copiar dados de um Blob do Azure para Tabela do Azure com conversões de tipo.

Suponha que o conjunto de dados de Blob está no formato CSV e contém 3 colunas. Uma delas é uma coluna de data e hora com um formato de data e hora personalizado usando nomes abreviados em francês para o dia da semana.

Você definirá o conjunto de dados de origem de Blob como a seguir, juntamente com definições de tipo para as colunas.
	
	{
	    "name": " AzureBlobInput",
	    "properties":
	    {
	         "structure": 
	          [
	                { "name": "userid", "type": "Int64"},
	                { "name": "name", "type": "String"},
	                { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
	          ],
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/myfolder",
	            "fileName":"myfile.csv",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "external": true,
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	        },
			"policy": {
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
			}
	    }
	}

Dado o mapeamento de tipo OData da Tabela do Azure para o tipo .NET acima, você definiria a tabela na Tabela do Azure com o esquema a seguir.

**Esquema da tabela do Azure:**

Nome da coluna | Tipo
----------- | --------
userid | Edm.Int64
name | Edm.String 
lastlogindate | Edm.DateTime

Em seguida, você definirá o conjunto de dados de Tabela do Azure da seguinte maneira. Você não precisa especificar a seção "estrutura" com as informações de tipo, pois o tipo de informação já está especificado no armazenamento de dados subjacente.

	{
	  "name": "AzureTableOutput",
	  "properties": {
	    "type": "AzureTable",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Nesse caso, a fábrica dados fará automaticamente as conversões de tipo, inclusive o campo de data e hora com o formato de data e hora personalizado usando a cultura fr-fr ao mover dados de Blob para a Tabela do Azure.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Desempenho e Ajuste  
Confira o [Guia de desempenho e ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0629_2016-->