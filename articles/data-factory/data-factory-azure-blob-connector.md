<properties 
	pageTitle="Mover dados para e do blob do Azure | Azure Data Factory" 
	description="Saiba como mover dados para/do Armazenamento de Blob do Azure usando o Azure Data Factory" 
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
	ms.date="11/03/2015" 
	ms.author="spelluru"/>

# Mover dados para e do blob do Azure usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia em uma Azure Data Factory para mover dados para o Blob do Azure de outro armazenamento de dados e mover dados de outro armazenamento de dados para o Blob do Azure. Este artigo se baseia no artigo [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral de movimentação de dados com a atividade de cópia e combinações de armazenamento de dados para as quais há suporte.

## Exemplo: Copiar dados do Blob do Azure para o SQL Azure
O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

O exemplo copia os dados pertencentes a uma série temporal de um blob do Azure para uma tabela no banco de dados SQL do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

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

**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e nome de arquivo usa a parte de hora da hora de início. A configuração "external": "true" informa o serviço Data Factory que essa é uma tabela externa à data factory e não é produzida por uma atividade na data factory.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

**Conjunto de dados de saída do SQL Azure:**

Os dados de cópia do exemplo em uma tabela chamada "MyTable" em um banco de dados SQL do Azure. Você deve criar a tabela no banco de dados SQL Azure com o mesmo número de colunas que você espera que o arquivo CSV de Blob contenha. Novas linhas são adicionadas à tabela a cada hora.

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

**Pipeline com a Atividade de cópia:**

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

## Exemplo: Copiar dados do SQL Azure para o Blob do Azure
O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	O [pipeline](data-factory-create-pipelines.md) com a Atividade de cópia que usa [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties).


O exemplo copia a cada hora dados pertencentes a uma série temporal de uma tabela no banco de dados SQL do Azure para um blob. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Serviço vinculado do SQL Azure:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

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

**Conjunto de dados de entrada do SQL Azure:**

O exemplo supõe que você criou uma tabela "MyTable" no SQL Azure e que ela contém uma coluna chamada "timestampcolumn" para dados de série temporal.

Definir "external": "true" e especificar a política externalData informa o serviço Azure Data Factory que essa é uma tabela externa à fábrica de dados e não é produzida por uma atividade dessa fábrica de dados.

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

**Conjunto de dados de saída de Blob do Azure:**

Os dados são gravados em um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta para o blob é avaliado dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa as partes ano, mês, dia e horas da hora de início.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

## Propriedades do serviço vinculado de armazenamento do Azure

Você pode vincular uma conta de armazenamento do Azure a uma Azure Data Factory usando um serviço de armazenamento do Azure vinculado. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de Armazenamento do Azure vinculado.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| type | A propriedade type deve ser definida como: **AzureStorage** | Sim |
| connectionString | Especifique as informações necessárias para se conectar ao armazenamento do Azure para a propriedade connectionString. Você pode obter a connectionString para o armazenamento do Azure no Portal Clássico do Azure. | Sim |

## Propriedades de tipo de conjunto de dados de Blob do Azure

Para obter uma lista completa das seções JSON e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local, formato etc. de dados no armazenamento de dados. A seção typeProperties para o conjunto de dados do tipo **AzureBlob** tem as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| folderPath | Caminho para o contêiner e a pasta no armazenamento de blob. Exemplo: myblobcontainer\\myblobfolder\\ | Sim |
| fileName | <p>Nome do fileName de blob é opcional. </p><p>Se você especificar um nome de arquivo, a atividade (incluindo cópia) funciona no Blob específico.</p><p>Quando fileName não for especificado, a cópia incluirá todos os Blobs em folderPath para o conjunto de dados de entrada.</p><p>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estaria no seguinte formato: Data.<Guid>.txt (por exemplo: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | Não |
| partitionedBy | partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Consulte [Utilizando a seção da propriedade partitionedBy](#Leveraging-partitionedBy-property) abaixo para obter detalhes e exemplos. | Não
| formato | Há suporte para dois tipos de formatos: **TextFormat**, **AvroFormat**. Você precisa definir a propriedade de tipo em formato para qualquer um desses valores. Quando o formato for TextFormat, você pode especificar as propriedades opcionais adicionais para o formato. Consulte a seção [Especificando TextFormat](#specifying-textformat) abaixo para obter mais detalhes. | Não
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos com suporte são: GZip, Deflate e BZip2. Os níveis com suporte são: Melhor e Mais rápido. Consulte a seção [Suporte à compactação](#compression-support) para obter mais detalhes. | Não |

### Utilizando a propriedade partitionedBy
Conforme mencionado acima, você pode especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal com a seção **partitionedBy**, macros de Data Factory e variáveis do sistema: SliceStart e SliceEnd, que indicam as horas de início e término para uma fatia de dados determinada.

Consulte [Variáveis do sistema Data Factory](data-factory-scheduling-and-execution.md#data-factory-system-variables) e [Referência de funções do Data Factory](data-factory-scheduling-and-execution.md#data-factory-functions-reference) para saber mais sobre as variáveis do sistema Data Factory que você pode usar na seção partitionedBy.

Consulte os artigos [Criando conjuntos de dados](data-factory-create-datasets.md) e [Agendamento e execução](data-factory-scheduling-and-execution.md) para conhecer mais detalhes sobre conjuntos de dados de série temporal, agendamento e fatias.

#### Exemplo 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

No exemplo acima, {Slice} é substituído pelo valor da variável de sistema SliceStart da Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

#### Exemplo 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

No exemplo acima, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

### Especificando TextFormat

Se o formato é definido como **TextFormat**você pode especificar as seguintes propriedades **opcionais** na seção **Formato**.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| columnDelimiter | O caractere usado como um separador de coluna em um arquivo. Somente um caractere é permitido nesse momento. Essa marca é opcional. O valor padrão é vírgula (,). | Não |
| rowDelimiter | O caractere usado como um separador bruto no arquivo. Somente um caractere é permitido nesse momento. Essa marca é opcional. O valor padrão é qualquer um dos seguintes: ["\\r\\n", "\\r"," \\n"]. | Não |
| escapeChar | <p>O caractere especial usado como escape do delimitador de coluna mostrado no conteúdo. Essa marca é opcional. Nenhum valor padrão. Você deve especificar não mais de um caractere para essa propriedade.</p><p>Por exemplo, se você tiver a vírgula (,) como o delimitador de coluna, mas desejar ter o caractere de vírgula no texto (exemplo: "Hello, world"), você pode definir '$' como o caractere de escape e usar a cadeia de caracteres "Hello$, world" na fonte.</p><p>Observe que não é possível especificar escapeChar e quoteChar para uma tabela.</p> | Não | 
| quoteChar | <p>O caractere especial é usado como o caractere no qual colocar o valor de cadeia de caracteres. Os delimitadores de linha e coluna dos caracteres de aspas seriam tratados como parte do valor de cadeia de caracteres. Essa marca é opcional. Nenhum valor padrão. Você deve especificar não mais de um caractere para essa propriedade.</p><p>Por exemplo, se você tiver a vírgula (,) como o delimitador de coluna, mas deseja ter caractere de vírgula no texto (exemplo: <Hello  world>), você pode definir ‘"’ como o caractere de citação e usar a cadeia de caracteres <"Hello, world"> na fonte. Essa propriedade é aplicável às tabelas de entrada e saída.</p><p>Observe que não é possível especificar escapeChar e quoteChar para uma tabela.</p> | Não |
| nullValue | <p>Os caracteres usados para representar um valor nulo no conteúdo do arquivo de blob. Essa marca é opcional. O valor padrão é "\\N".</p><p>Por exemplo, com base no exemplo acima, "NaN" no blob será convertido como valor nulo, enquanto for copiado no, por exemplo, SQL Server.</p> | Não |
| encodingName | Especifique o nome de codificação. Para obter a lista de nomes de codificação válidos, confira: [Propriedade Encoding.EncodingName](https://msdn.microsoft.com/library/system.text.encoding.aspx). Por exemplo: windows-1250 ou shift\_jis. O valor padrão é UTF-8. | Não | 

#### Exemplos
O exemplo a seguir mostra algumas das propriedades de formato para TextFormat.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Para usar um escapeChar em vez de quoteChar, substitua a linha com quoteChar pelo seguinte:

	"escapeChar": "$",

### Especificando AvroFormat
Se o formato é definido como AvroFormat, não é necessário especificar nenhuma propriedade na seção Formato dentro da seção typeProperties. Exemplo:

	"format":
	{
	    "type": "AvroFormat",
	}

Para usar o formato Avro em uma tabela de Hive, consulte [Tutorial do Apache Hive](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe).

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Propriedades de tipo de atividade de cópia de Blob do Azure  
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). Propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividades.

Propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade e, no caso de atividade de cópia, variam dependendo dos tipos de fontes e coletores

**BlobSource** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Especifica se deve-se tratar a cadeia de caracteres nula ou vazia como valor nulo. | TRUE<br/>FALSE | Não |
| skipHeaderLineCount | Indica quantas linhas precisam ser ignoradas. É aplicável somente quando o conjunto de dados de entrada usa **TextFormat**. | Inteiro de 0 ao máximo. | Não | 
| recursive | Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. | True (valor padrão), False | Não | 


**BlobSink** dá suporte às seguintes propriedades na seção **typeProperties**:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Especifica se deseja adicionar o cabeçalho de definições de coluna. | TRUE<br/>FALSE (padrão) | Não |
| copyBehavior | Define o comportamento de cópia quando a origem é BlobSource ou FileSystem. | <p>Há três valores possíveis para a propriedade copyBehavior. </p><ul><li>* *PreserveHierarchy: ** preserva a hierarquia de arquivos na pasta de destino, ou seja, o caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.</li><li>**FlattenHierarchy:** todos os arquivos da pasta de origem estarão no primeiro nível da pasta de destino. Os arquivos de destino terão o nome gerado automaticamente. </li><li>**MergeFiles: (padrão)** mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome de arquivo mesclado seria o nome especificado. Caso contrário, seria o nome de arquivo gerado automaticamente.</li></ul> | Não |

### exemplos de recursive e copyBehavior
Esta seção descreve o comportamento resultante da operação de cópia para diferentes combinações de valores recursive e copyBehavior.

recursive | copyBehavior | Comportamento resultante
--------- | ------------ | --------
verdadeiro | preserveHierarchy | <p>Para uma pasta de origem Pasta1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a pasta de destino Pasta1 terá a mesma estrutura de acordo com a origem<p>asta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>.  
verdadeiro | flattenHierarchy | <p>Para uma pasta de origem Pasta1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a Pasta1 de destino terá a seguinte estrutura: <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome automaticamente gerado para Arquivo5</p>
verdadeiro | mergeFiles | <p>Para uma pasta de origem Pasta1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a pasta de destino Pasta1 terá a seguinte estrutura: <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1 + Arquivo2 + Arquivo3 + Arquivo4 + Arquivo5 os conteúdos serão mesclados em um nome do arquivo autogerado</p>
false | preserveHierarchy | <p>Para uma pasta de origem Pasta 1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a pasta de destino Pasta1 terá a seguinte estrutura<p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/></p><p>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados.</p>.
false | flattenHierarchy | <p>Para uma pasta de origem Pasta1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a pasta de destino Pasta1 terá a seguinte estrutura<p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome gerado automaticamente para Arquivo2<br/></p><p>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados.</p>.
false | mergeFiles | <p>Para uma pasta de origem Pasta 1 com a seguinte estrutura:</p> <p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Arquivo2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Subpasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Arquivo5</p>a pasta de destino Pasta1 terá a seguinte estrutura<p>Pasta1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Os conteúdos do Arquivo1 + Arquivo2 serão mesclados em um arquivo com o nome do arquivo autogerado. nome autogerado para Arquivo1</p><p>Subpasta1 com Arquivo3, Arquivo4 e Arquivo5 não são selecionados.</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0121_2016-->