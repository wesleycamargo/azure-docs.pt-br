<properties 
	pageTitle="Mover dados de e para o SQL Data Warehouse do Azure | Microsoft Azure" 
	description="Saiba como mover dados para/do SQL Data Warehouse do Azure usando o Azure Data Factory" 
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
	ms.date="04/14/2016" 
	ms.author="spelluru"/>

# Mover dados para e do SQL Data Warehouse do Azure usando o Azure Data Factory

Este artigo descreve como você pode usar a Atividade de cópia no Azure Data Factory para mover dados do SQL Data Warehouse do Azure para outro armazenamento de dados e mover dados de outro armazenamento de dados para o SQL Data Warehouse. Este artigo tem base no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia, e fontes e coletores de dados com suporte para o SQL Data Warehouse.

O(s) exemplo(s) a seguir mostra(m) como copiar dados de e para o Azure SQL Data Warehouse e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes para qualquer um dos coletores declarados no artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de cópia no Azure Data Factory.

> [AZURE.NOTE] 
Para obter uma visão geral do serviço Azure Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md).
> 
> Este artigo fornece exemplos de JSON, mas não fornece instruções passo a passo para criar um data factory. Confira [Tutorial: copiar dados do Blob do Azure para o Banco de Dados SQL do Azure](data-factory-get-started.md) para uma breve passo a passo com instruções sobre como usar a Atividade de cópia no Azure Data Factory.


## Exemplo: Copiar dados do SQL Data Warehouse do Azure para o Blob do Azure

O exemplo a seguir mostra:

1. Um serviço vinculado do tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2. Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties). 
3. Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties). 
4. Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [SqlDWSource](#azure-sql-data-warehouse-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia a cada hora dados pertencentes a uma série temporal de uma tabela no banco de dados do SQL Data Warehouse do Azure para um blob. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada do SQL Data Warehouse do Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Data Warehouse do Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.
 
Definir “external”: “true” e especificar a política externalData informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade dessa data factory.

	{
	  "name": "AzureSqlDWInput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
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

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **SqlDWSource** e o tipo **sink** está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora a serem copiados.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLDWtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSqlDWInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlDWSource",
	            "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

> [AZURE.NOTE] No exemplo acima, **sqlReaderQuery** é especificado para o SqlDWSource. A Atividade de Cópia executa essa consulta em relação à fonte de SQL Data Warehouse do Azure para obter os dados.
>  
> Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usa parâmetros).
>  
> Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura do conjunto de dados JSON são usadas para criar uma consulta (selecione column1, column2 de mytable) para executar o SQL Data Warehouse do Azure. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

## Exemplo: Copiar dados do Blob do Azure para o SQL Data Warehouse do Azure

O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureSqlDW](#azure-sql-data-warehouse-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlDWTable](#azure-sql-data-warehouse-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlDWSink](#azure-sql-data-warehouse-copy-activity-type-properties).


O exemplo copia os dados pertencentes a uma série temporal de um blob do Azure para uma tabela no banco de dados do SQL Data Warehouse do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Data Warehouse do Azure:**

	{
	  "name": "AzureSqlDWLinkedService",
	  "properties": {
	    "type": "AzureSqlDW",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Serviço vinculado do armazenamento de Blob do Azure:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Conjunto de dados de entrada de Blob do Azure:**

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

**Conjunto de dados de saída do SQL Data Warehouse do Azure:**

O exemplo copia os dados em uma tabela chamada "MyTable" no SQL Data Warehouse do Azure. Você deve criar a tabela no SQL Data Warehouse do Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

	{
	  "name": "AzureSqlDWOutput",
	  "properties": {
	    "type": "AzureSqlDWTable",
	    "linkedServiceName": "AzureSqlDWLinkedService",
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

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída acima e agendada para ser executada a cada hora. Na definição de JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **SqlDWSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQLDW",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlDWOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlDWSink"
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

Confira o artigo [Carregar dados com o Azure Data Factory](../sql-data-warehouse/sql-data-warehouse-get-started-load-with-azure-data-factory.md) na documentação do SQL Data Warehouse do Azure para ver um passo a passo.

## Propriedades do serviço vinculado do SQL Data Warehouse do Azure

A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço vinculado do SQL Data Warehouse do Azure.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
type | A propriedade type deve ser definida como: **AzureSqlDW** | Sim
**connectionString** | Especifique as informações necessárias para se conectar à instância do SQL Data Warehouse do Azure para a propriedade connectionString. | Sim

Observação: você precisa configurar o [Firewall do Banco de Dados SQL do Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Você precisa configurar o servidor de banco de dados para [permitir que os Serviços do Azure acessem o servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Além disso, se você estiver copiando dados para o SQL Data Warehouse do Azure, de fora do Azure, incluindo fontes de dados locais com o gateway de fábrica de dados, você precisa configurar o intervalo de endereços IP apropriado para o computador que está enviando dados para o SQL Data Warehouse do Azure.

## Propriedades de tipo do conjunto de dados do SQL Data Warehouse do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre o local dos dados no armazenamento de dados. A seção **typeProperties** para o conjunto de dados do tipo **AzureSqlDWTable** tem as propriedades a seguir.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tableName | Nome da tabela no banco de dados SQL Data Warehouse do Azure ao qual o serviço vinculado se refere. | Sim |

## Propriedades de tipo de atividade de cópia do SQL Data Warehouse do Azure

Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

**Observação:** a Atividade de Cópia usa apenas uma entrada e produz apenas uma saída.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de Atividade de cópia, variam dependendo dos tipos de fontes e coletores.

### SqlDWSource

No caso de Atividade de cópia, quando a fonte é do tipo **SqlDWSource**, as seguintes propriedades estão disponíveis na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utiliza a consulta personalizada para ler os dados. | Cadeia de caracteres de consulta SQL. Por exemplo: select * from MyTable. | Não |
| sqlReaderStoredProcedureName | Nome do procedimento armazenado que lê os dados da tabela de origem. | Nome do procedimento armazenado. | Não |
| storedProcedureParameters | Parâmetros para o procedimento armazenado. | Pares de nome/valor. Nomes e uso de maiúsculas e minúsculas de parâmetros devem corresponder aos nomes e o uso de maiúsculas e minúsculas dos parâmetros do procedimento armazenado. | Não |

Se **sqlReaderQuery** for especificado para SqlDWSource, a Atividade de Cópia executará essa consulta na fonte do SQL Data Warehouse do Azure para obter os dados.

Como alternativa, você pode especificar um procedimento armazenado especificando o **sqlReaderStoredProcedureName** e o **storedProcedureParameters** (se o procedimento armazenado usar parâmetros).

Se você não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, as colunas definidas na seção de estrutura do conjunto de dados JSON são usadas para criar uma consulta (selecione column1, column2 de mytable) para executar o SQL Data Warehouse do Azure. Se a definição de conjunto de dados não tem a estrutura, todas as colunas serão selecionadas da tabela.

#### Exemplo de SqlDWSource

    "source": {
        "type": "SqlDWSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**A definição do procedimento armazenado:**

	CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
	(
		@stringData varchar(20),
		@id int
	)
	AS
	SET NOCOUNT ON;
	BEGIN
	     select *
	     from dbo.UnitTestSrcTable
	     where dbo.UnitTestSrcTable.stringData != stringData
	    and dbo.UnitTestSrcTable.id != id
	END
	GO
 

### SqlDWSink
O **SqlDWSink** dá suporte às seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| writeBatchSize | Insere dados na tabela SQL quando o tamanho do buffer atinge writeBatchSize | Inteiro. (unidade = Contagem de Linhas) | Não (Padrão = 10.000) |
| writeBatchTimeout | Tempo de espera para a operação de inserção em lotes ser concluída antes de atingir o tempo limite. | (Unidade = timespan) Exemplo: "00:30:00" (30 minutos). | Não | 
| sqlWriterCleanupScript | A consulta especificada pelo usuário para a Atividade de cópia ser executada para assegurar que os dados de uma fatia específica serão limpos. Consulte a seção de repetição abaixo para obter mais detalhes. | Uma instrução de consulta. | Não |
| allowPolyBase | Indica se deve usar o PolyBase (quando aplicável) em vez de mecanismo BULKINSERT para carregar dados no Azure SQL Data Warehouse. <br/><br/>Observe que apenas um conjunto de dados de **blobs do Azure** com o **formato** definido como **TextFormat** como uma fonte de conjunto de dados tem suporte no momento e o suporte a outros tipos de fontes será disponibilizado em breve. <br/><br/>Consulte a seção [Usar o PolyBase para carregar dados no Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse), para obter detalhes e restrições. | True <br/>False (padrão) | Não |  
| polyBaseSettings | Um grupo de propriedades que pode ser especificado quando a propriedade **allowPolybase** estiver definida como **true**. | &nbsp; | Não |  
| rejectValue | Especifica o número ou o percentual de linhas que podem ser rejeitadas antes de a consulta falhar. <br/><br/>Saiba mais sobre as opções de rejeição do PolyBase na seção **Argumentos** do tópico [CRIAR TABELA EXTERNA (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). | 0 (padrão), 1, 2, … | Não |  
| rejectType | Especifica se a opção rejectValue é especificada como um valor literal ou um percentual. | Valor (padrão), Percentual | Não |   
| rejectSampleValue | Determina o número de linhas a serem recuperadas antes de o PolyBase recalcular o percentual de linhas rejeitadas. | 1, 2, … | Sim, se **rejectType** for **percentual** |  
| useTypeDefault | Especifica como tratar valores ausentes nos arquivos de texto delimitados quando PolyBase recupera dados do arquivo de texto.<br/><br/>Saiba mais sobre essa propriedade na seção Argumentos em [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). | True, False (padrão) | Não | 


#### Exemplo de SqlDWSink


    "sink": {
        "type": "SqlDWSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00"
    }

## Usar o PolyBase para carregar dados no Azure SQL Data Warehouse
O **PolyBase** é uma maneira eficiente de carregar grandes quantidades de dados do Armazenamento de Blobs do Azure no Azure SQL Data Warehouse com alta taxa de transferência. Você pode notar um grande ganho na taxa de transferência usando PolyBase em vez do mecanismo BULKINSERT padrão.

Se o armazenamento de dados de origem não for o Armazenamento de Blobs do Azure, você poderá considerar copiar os dados do armazenamento de dados de origem para o Armazenamento de Blobs do Azure primeiro, como um preparo e, em seguida, usar o PolyBase para carregar os dados no Azure SQL Data Warehouse do repositório de preparo. Nesse cenário, você usará duas atividades de cópia, com a primeira atividade de cópia configurada para copiar dados do armazenamento de dados de origem para o Armazenamento de Blobs do Azure e a segunda atividade de cópia para copiar dados do Armazenamento de Blobs do Azure para o Azure SQL Data Warehouse usando PolyBase.

Defina a propriedade **allowPolyBase** como **true**, conforme mostrado no exemplo a seguir para o Azure Data Factory usar o PolyBase para copiar dados do Armazenamento de Blobs do Azure para o Azure SQL Data Warehouse. Quando você definir allowPolyBase como true, poderá especificar propriedades específicas do PolyBase usando o grupo de propriedades **polyBaseSettings**. Consulte a seção [SqlDWSink](#SqlDWSink) seção acima para obter detalhes sobre as propriedades que você pode usar com polyBaseSettings.


    "sink": {
        "type": "SqlDWSink",
		"allowPolyBase": true,
		"polyBaseSettings":
		{
			"rejectType": "percentage",
			"rejectValue": 10,
			"rejectSampleValue": 100,
			"useTypeDefault": true 
		}

    }

O Azure Data Factory verifica se os dados atendem aos seguintes requisitos antes de usar o PolyBase para copiar dados para o Azure SQL Data Warehouse. Se os requisitos não forem atendidos, ele automaticamente se voltará para o mecanismo BULKINSERT para a movimentação de dados.

1.	**Serviço vinculado de origem** é do tipo: **Armazenamento do Azure** e ele não está configurado para usar a autenticação SAS (Assinatura de Acesso Compartilhado). Consulte [Serviço vinculado do Armazenamento do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter detalhes.  
2. O **conjunto de dados de entrada** é do tipo: **Blob do Azure** e as propriedades do tipo de conjunto de dados atendem aos seguintes critérios: 
	1. **Tipo** deve ser **TextFormat**. 
	2. **rowDelimiter** deve ser **\\n**. 
	3. **nullValue** é definido como **cadeia de caracteres vazia** (""). 
	4. **encodingName** é definido como **utf-8**, que é o valor **padrão**, portanto, não o defina como um valor diferente. 
	5. **escapeChar** e **quoteChar** não são especificados. 
	6. **Compactação** não é **BZIP2**.
	 
			"typeProperties": {
				"folderPath": "<blobpath>",
				"format": {
					"type": "TextFormat",     
					"columnDelimiter": "<any delimiter>", 
					"rowDelimiter": "\n",       
					"nullValue": "",           
					"encodingName": "utf-8"    
				},
            	"compression": {  
                	"type": "GZip",  
	                "level": "Optimal"  
    	        }  
			},
3.	Não há uma configuração **skipHeaderLineCount** em **BlobSource** para a atividade de Cópia no pipeline. 
4.	Não há uma configuração **sliceIdentifierColumnName** em **SqlDWSink** para a atividade de Cópia no pipeline. (O PolyBase garante que todos os dados são atualizados ou que nada é atualizado em uma execução única. Para obter a **repetição**, você pode usar **sqlWriterCleanupScript**.
5.	Não há nenhum **columnMapping** sendo usado na atividade de Cópia associada. 

### Práticas recomendadas ao usar o PolyBase

#### Limitação de tamanho de linha
O Polybase não dá suporte a linhas de tamanho maior que 32 KB. Tentar carregar uma tabela com linhas maiores que 32 KB resultaria no seguinte erro:

	Type=System.Data.SqlClient.SqlException,Message=107093;Row size exceeds the defined Maximum DMS row size: [35328 bytes] is larger than the limit of [32768 bytes],Source=.Net SqlClient

Caso você tenha dados de origem com linhas de tamanho maior que 32 KB, convém dividir as tabelas de origem verticalmente em várias pequenas, em que o maior tamanho de linha de cada um deles não excede o limite. As tabelas menores podem ser carregadas usando o PolyBase e mescladas no Azure SQL Data Warehouse.

#### tableName no Azure SQL Data Warehouse
A tabela a seguir fornece exemplos de como especificar a propriedade **tableName** no conjunto de dados JSON para várias combinações de nome de esquema e tabela.

| Esquema do BD | Nome da tabela | Propriedade JSON tableName |
| --------- | -----------| ----------------------- | 
| dbo | MyTable | MyTable ou dbo.MyTable ou [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable ou [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] ou [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1]. [My.Table] |

Se você vir um erro, como mostrado abaixo, poderá ser um problema com o valor especificado para a propriedade tableName. Consulte a tabela acima para obter a maneira correta de especificar valores para a propriedade JSON tableName.

	Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider

#### Colunas com valores padrão
Atualmente, o recurso PolyBase no Data Factory só aceita o mesmo número de colunas da tabela de destino. Digamos que você tenha uma tabela com 4 colunas e uma delas é definida com um valor padrão, os dados de entrada ainda devem conter 4 colunas. Fornecer um conjunto de dados de entrada de 3 colunas produziria um erro conforme mostrado abaixo:

	All columns of the table must be specified in the INSERT BULK statement.

O valor NULL é uma forma especial do valor padrão. Se a coluna for anulável, os dados de entrada (no blob) para essa coluna poderão estar vazios (não poderão estar ausentes no conjunto de dados de entrada). O PolyBase inserirá NULL para eles no armazém Azure SQL Data Warehouse.

#### Aproveitando a cópia de duas etapas para usar o PolyBase
O PolyBase tem limitações de armazenamentos de dados e formatos com os quais pode operar. Se seu cenário não atender os requisitos, você deverá aproveitar a Atividade de Cópia para copiar dados para um armazenamento de dados com suporte pelo PolyBase e/ou converter os dados em um formato que dá suporte a PolyBase. Aqui estão exemplos das transformações que você pode fazer:

-	Converter arquivos de origem em outras codificações para blobs do Azure em UTF-8
-	Serializar dados no SQL Server/Banco de Dados SQL do Azure nos blobs do Azure em formato CSV.
-	Altere a ordem das colunas, especificando a propriedade columnMapping.

Aqui estão algumas dicas ao realizar as transformações:

- Seleção de um delimitador apropriado ao converter dados tabulares em arquivos CSV.

	É recomendável usar caracteres que muito provavelmente não aparecem nos dados como o delimitador de coluna. Delimitadores comuns incluem a vírgula (,), til (~), barra vertical (|) e TAB (\\t). Se seus dados tiverem esses itens, você poderá definir o delimitador de coluna caracteres não imprimíveis, como "\\u0001". O Polybase aceita delimitadores de coluna de vários caracteres, o que permitiria que você construísse delimitadores de coluna mais complexos.	
- Formato de objetos datetime

	Quando objetos datetime são serializados, a Atividade de Cópia, por padrão, usa o formato: "aaaa-MM-dd HH:mm:ss.fffffff", que, por padrão, não tem suporte do PolyBase. Os formatos de datetime com suporte podem ser encontrados aqui: [CRIAR FORMATO DE ARQUIVO EXTERNO (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). Deixar de atender às expectativas do Polybase no formato de datetime resultaria em um erro conforme mostrado abaixo:

		Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 1 rows processed.
		(/AccountDimension)Column ordinal: 97, Expected data type: DATETIME NOT NULL, Offending value: 2010-12-17 00:00:00.0000000  (Column Conversion Error), Error: Conversion failed when converting the NVARCHAR value '2010-12-17 00:00:00.0000000' to data type DATETIME.

	Para resolver esse erro, especifique o formato de datetime, conforme mostrado no exemplo a seguir:
	
		"structure": [
    		{ "name" : "column", "type" : "int", "format": "yyyy-MM-dd HH:mm:ss" }
		]


[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Mapeamento de tipo do SQL Data Warehouse do Azure

Como mencionado no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), a atividade de Cópia executa conversões automáticas de tipos de fonte para tipos de coletor, com a seguinte abordagem de duas etapas:

1. Converter de tipos de fonte nativos para o tipo .NET
2. Converter do tipo .NET para o tipo de coletor nativo

Ao mover dados de e para o SQL Azure, SQL Server, Sybase e os seguintes mapeamentos serão usados do tipo SQL para o tipo .NET e vice-versa.

O mapeamento é o mesmo que o [Mapeamento de tipo de dados do SQL Server para o ADO.NET](https://msdn.microsoft.com/library/cc716729.aspx).

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

## Desempenho e Ajuste  
Confira o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para aprender sobre os principais fatores que afetam o desempenho e o movimento de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0420_2016-->