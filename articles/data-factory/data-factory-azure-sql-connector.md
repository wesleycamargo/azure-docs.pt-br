<properties 
	pageTitle="Mover dados para e do SQL Azure | Azure Data Factory"
	description="Saiba como mover dados para/do Banco de Dados SQL do Azure usando o Azure Data Factory"
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

# Mover dados para e do SQL Azure com o Azure Data Factory

Este artigo descreve como você pode usar a Atividade de Cópia em uma Azure Data Factory para mover dados para o SQL Azure de outro armazenamento de dados e mover dados de outro armazenamento de dados para o SQL Azure. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados com suporte.

## Exemplo: Copiar dados do SQL Azure para o Blob do Azure

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties). 
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia a cada hora dados pertencentes a uma série temporal de uma tabela no banco de dados SQL do Azure para um blob. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do SQL Azure**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir “external”: ”true” e especificar a política externalData informa o serviço Azure Data Factory que essa é uma tabela externa à fábrica de dados e não é produzida por uma atividade dessa fábrica de dados.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
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

**Conjunto de dados de saída de Blob do Azure**

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

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **SqlSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Exemplo: Copiar dados do Blob do Azure para o SQL Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

O exemplo copia os dados pertencentes a uma série temporal do blob do Azure para uma tabela no banco de dados SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.


**Serviço vinculado do SQL Azure**
	
	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada de Blob do Azure**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e nome de arquivo usa a parte de hora da hora de início. A configuração “external”: ”true” informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade na data factory.

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

**Conjunto de dados de saída do SQL Azure**

O exemplo copia dados para uma tabela chamada "MyTable" no SQL Azure. Você deve criar a tabela no SQL Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline com Atividade de cópia**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **SqlSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
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

## Propriedades do serviço vinculado do SQL Server

A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de vinculado de SQL Azure.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: AzureSqlDatabase | Sim |
| connectionString | Especifique as informações necessárias para se conectar à instância do Banco de Dados SQL Azure para a propriedade connectionString. | Sim |

**Observação**: você precisa configurar o [Firewall de Banco de Dados SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Você precisa configurar o servidor de banco de dados para [permitir que os serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando dados para o SQL Azure, de fora do Azure, incluindo fontes de dados locais com o gateway de fábrica de dados, você precisa configurar o intervalo de endereços IP apropriado para o computador que está enviando dados para o SQL Azure.

## Propriedades de tipo do Conjunto de Dados SQL Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AzureSqlTable** tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela na instância do Banco de Dados SQL Azure à qual o serviço vinculado se refere. | Sim |

## Propriedades de tipo de atividade de cópia de SQL Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

> [AZURE.NOTE]A Atividade de cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

No caso de Atividade de cópia, quando a fonte é do tipo **SqlSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. Se não for especificada, a instrução SQL que é executada é: select from MyTable. | Não |

**SqlSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlWriterStoredProcedureName | Nome do procedimento armazenado especificado pelo usuário para dados de inserção (atualização/inserção) na tabela de destino. | Nome do procedimento armazenado. | Não |
| sqlWriterTableType | Nome do tipo de tabela especificado pelo usuário a ser utilizado no procedimento armazenado acima. A atividade de cópia disponibiliza aqueles dados sendo movidos em uma tabela temporária com esse tipo de tabela. O código de procedimento armazenado pode mesclar os dados sendo copiados com dados existentes. | Um nome de tipo de tabela. | Não |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. | (Unidade = timespan) Exemplo: "00:30:00" (30 minutos). | Não | 
| sqlWriterCleanupScript | A consulta especificada pelo usuário para a Atividade de cópia ser executada para assegurar que os dados de uma fatia específica serão limpos. Consulte a seção de repetição abaixo para obter mais detalhes. | Uma instrução de consulta. | Não |
| sliceIdentifierColumnName | Nome de coluna especificado pelo usuário para a Atividade de cópia preencher com o identificador de fatia gerado automaticamente, que será usado para limpar os dados de uma fatia específica quando executado novamente. Consulte a seção de repetição abaixo para obter mais detalhes. | Nome de uma coluna com tipo de dados de binário (32). | Não |

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo para SQL Server e SQL Azure

Conforme mencionado no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md), a Atividade de cópia executa conversões automáticas de tipo de fonte para tipos de coletor, com a abordagem em duas etapas descritas a seguir:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de e para o SQL Azure, SQL Server, Sybase e os seguintes mapeamentos serão usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para o ADO.NET.

| Tipo de mecanismo do Banco de Dados do SQL Server | Tipo .NET Framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| binário | Byte |
| bit | Booliano |
| char | Cadeia de caracteres, caractere |
| data | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Atributo FILESTREAM (varbinary(max)) | Byte |
| Float | Duplo |
| imagem | Byte | 
| int | Int32 | 
| money | Decimal |
| nchar | Cadeia de caracteres, caractere |
| ntext | Cadeia de caracteres, caractere |
| numérico | Decimal |
| nvarchar | Cadeia de caracteres, caractere |
| real | Single |
| rowversion | Byte |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql\_variant | Objeto * |
| texto | Cadeia de caracteres, caractere |
| tempo real | TimeSpan |
| timestamp | Byte |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte |
| varchar | Cadeia de caracteres, caractere |
| xml | Xml |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Enviar comentários
Apreciamos muito seus comentários sobre este artigo. Reserve alguns minutos para enviar seus comentários por meio de [email](mailto:adfdocfeedback@microsoft.com?subject=data-factory-azure-sql-connector.md).



	 

<!----HONumber=September15_HO1-->