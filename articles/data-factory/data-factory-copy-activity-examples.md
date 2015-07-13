<properties 
	pageTitle="Exemplos para usar a atividade de cópia na Azure Data Factory" 
	description="Fornece exemplos para usar uma atividade de cópia na Azure Data Factory." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Exemplos para usar a atividade de cópia na Azure Data Factory
Você pode usar a **Atividade de cópia** em uma pipeline para copiar dados de uma fonte para um coletor (destino) em um lote. Este tópico fornece alguns exemplos para usar a atividade de cópia em um pipeline da Data Factory. Para obter uma visão geral detalhada da atividade de cópia e cenários principais aos quais ele dá suporte, consulte [Copiar dados com o Azure Data Factory][adf-copyactivity].

## Copie os dados de um banco de dados de SQL Server local para um blob do Azure
Neste exemplo, uma tabela de entrada e uma tabela de saída são definidas e as tabelas são utilizadas em uma Atividade de cópia em uma pipeline que copia dados de um banco de dados SQL Server local para um blob do Azure.

### Suposições
Este exemplo supõe que você já tenha os seguintes artefatos da Azure Data Factory:

* Grupo de recursos denominado **ADF**.
* Um data factory do Azure denominado **CopyFactory**.
* Um gateway de gerenciamento de dados denominado **mygateway** é criado e está online.  

### Criar um serviço vinculado para banco de dados do SQL Server local de origem
Nesta etapa, você cria um serviço vinculado denominado **MyOnPremisesSQLDB** que aponta para um banco de dados do SQL Server local.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Observe o seguinte:

- O **tipo** é definido como **OnPremisesSqlLinkedService**.
- **connectionString** é definido como a cadeia de conexão para um banco de dados do SQL Server. 
- **gatewayName** é definido como o nome do Gateway de gerenciamento de dados instalado no computador local e registrado com o portal de serviços da Azure Data Factory. 

Consulte [Serviço SQL vinculado local](https://msdn.microsoft.com/library/dn893523.aspx) para obter detalhes sobre o elementos JSON para definir um serviço SQL vinculado local.
 
### Criar um serviço vinculado para o blob de destino do Azure
Nesta etapa, você cria um serviço vinculado denominado **MyAzureStorage** que aponta para um armazenamento de blobs do Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Observe o seguinte:

- O **tipo** é definido como **AzureStorageLinkedService**.
- **connectionString** - especifique o nome da conta e chave de conta para o armazenamento do Azure.

Consulte [Serviço vinculado de armazenamento do Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure.

### Tabela de entrada JSON
O script JSON a seguir define uma tabela de entrada que se refere a uma tabela SQL: **MyTable** em um banco de dados de SQL Server local que o serviço vinculado **MyOnPremisesSQLDB** define. Observe que **name** é o nome da tabela do Azure Data Factory e **tableName** é o nome da tabela SQL em um banco de dados de SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Observe o seguinte:

- O **tipo** é definido como **OnPremisesSqlServerTableLocation**.
- **tableName** é definido como **MyTable**, que contém os dados de origem. 
- **linkedServiceName** é definido como **MyOnPremisesSQLDB**, o serviço vinculado criado para o banco de dados SQL local.

Consulte [Propriedades de local do SQL local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a uma tabela do SQL Server.

### Tabela de saída JSON
O script JSON a seguir define uma tabela de saída, **MyAzureBlob**; que se refere a um blob do Azure, **MyBlob**; na pasta blob, **MySubFolder**; no contêiner de blob, **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Observe o seguinte:
 
- O **tipo** é definido como **AzureBlobLocation**.
- **folderPath** é definido como **MyContainer/MySubFolder**, que contém o blob detentor dos dados copiados. 
- **fileName** é definido como **MyBlob**, o blob que conterá os dados de saída.
- **linkedServiceName** é definido como **MyAzureStorge**, o serviço vinculado criado para o armazenamento do Azure.    

Consulte [Propriedades de local do blob do Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a um blob do Azure.

### Pipeline (com a Atividade de cópia) JSON
Neste exemplo, um pipeline: **CopyActivityPipeline** está definido com as seguintes propriedades:

- A propriedade **tipo** é definida como **CopyActivity**.
- **MyOnPremTable** é especificado como a entrada (marca de **entradas**).
- **MyAzureBlob** é especificado como a saída (marca de **saídas**)
- A seção **Transformação** contém duas subseções: **fonte** e **coletor**. O tipo de fonte é configurado como **SqlSource** e o tipo de coletor como **BlobSink**. O **sqlReaderQuery** define a transformação (projeção) a ser realizada na fonte. Para obter detalhes sobre todas as propriedades, consulte a [Referência de script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Consulte [Referência de JSON de pipeline](https://msdn.microsoft.com/library/dn834988.aspx) para obter detalhes sobre os elementos JSON para definir um pipeline de Data Factory e [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx) para propriedades de SqlSource (por exemplo: **sqlReaderQuery **no exemplo) e BlobSink. 


## Copie os dados de um sistema de arquivos local para um blob do Azure
Você pode usar a Atividade de cópia para copiar arquivos de um sistema de arquivos local (compartilhamentos de rede Windows/Linux ou host local do Windows) para um Blob do Azure. O host pode ser Windows ou Linux com o Samba configurado. O gateway de gerenciamento de dados deve ser instalado em um computador Windows que possa se conectar ao host.

### Suposições
Esse exemplo pressupõe o seguinte:

- **Host** - o nome do servidor que hospeda o sistema de arquivos é: **\contoso**.
- **Pasta** - o nome da pasta que contém os arquivos de entrada é: **marketingcampaign\regionaldata\{fatia}, onde os arquivos são particionados em uma pasta chamada {fatia} como, por exemplo, 2014121112 (ano de 2014, mês 12, dia 11, hora 12). 
### Criar um serviço vinculado de sistema de arquivos local
O JSON de exemplo a seguir pode ser usado para criar um serviço vinculado denominado **FolderDataStore** do tipo **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]Lembre-se de usar o caractere de escape '' para nomes de host e pastas em arquivos JSON. Para **\Contoso**, use **\\Contoso**.

Consulte [Serviço vinculado de sistema de arquivos local](https://msdn.microsoft.com/library/dn930836.aspx) para obter detalhes sobre os elementos JSON para definir um serviço vinculado local de sistema de arquivos.

### Criar um serviço vinculado para o blob de destino do Azure
O JSON de exemplo a seguir pode ser usado para criar um serviço vinculado denominado **MyAzureStorage** do tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [Serviço vinculado de armazenamento do Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure.

### Criar a tabela de entrada
O script JSON a seguir define uma tabela de entrada que se refere a um serviço vinculado de sistema de arquivos local criado anteriormente.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

Consulte [Propriedades de local do sistema de arquivos local](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a um sistema de arquivos local.

### Criar a tabela de saída
O script JSON a seguir define uma tabela de saída: **AzureBlobDest**, que se refere a um blob do Azure: **MyBlob** na pasta blob: **MySubFolder** no contêiner de blob: **MyContainer**.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consulte [Propriedades de local do blob do Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a um blob do Azure.

### Criar o pipeline
O pipeline JSON a seguir define um pipeline com uma atividade de cópia que copia dados do sistema de arquivos local para o blob de destino do Azure.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
	                    "source": {
	                        "type": "FileSystemSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 4,
	                    "timeout": "00:05:00"
	                }
	            }
	        ]
	    }
	}

O pipeline neste exemplo copia o conteúdo como binário, sem nenhuma análise e sem realização de nenhuma transformação. Observe que você pode aproveitar a **simultaneidade** para copiar fatias de arquivos em paralelo. Isso será útil quando você deseja mover as fatias que já aconteceram no passado.

> [AZURE.NOTE]Atividades simultâneas de cópia com o mesmo host por meio do caminho UNC com diferentes contas de usuário podem levar a erros como "várias conexões a um servidor ou recurso compartilhado pelo mesmo usuário, usando mais de um nome de usuário, não são permitidas". Essa é a restrição do sistema operacional, por motivos de segurança. Agende as atividades de cópia com gateways diferentes, ou instale o gateway dentro do host e use "localhost" ou "local" em vez do caminho UNC.

Consulte [Referência de JSON de pipeline](https://msdn.microsoft.com/library/dn834988.aspx) para obter detalhes sobre os elementos JSON para definir um pipeline de Data Factory e [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx) para propriedades de FileSystemSource e BlobSink.

### Cenários adicionais para uso de tabelas de sistema de arquivos

#### Copiar todos os arquivos em uma pasta específica
Observe que apenas **folderPath** é especificado no JSON de exemplo.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Copiar todos os arquivos CSV na pasta específica
Observe que o **fileFilter** é definido como ***.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Copiar um arquivo específico
Observe que o **fileFiter** é definido como um arquivo específico: **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Copie os dados de um banco de dados Oracle local para um blob do Azure
Você pode usar a atividade de cópia para copiar arquivos de um banco de dados do Oracle local para um Blob do Azure.

### Criar um serviço vinculado para um banco de dados Oracle local
O JSON a seguir pode ser usado para criar um serviço vinculado que aponta para um banco de dados Oracle local. Observe que o **tipo** é definido como **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Consulte [Serviço vinculado Oracle local](https://msdn.microsoft.com/library/dn948537.aspx) para obter detalhes sobre os elementos JSON para definir um serviço vinculado Oracle local.

### Criar um serviço vinculado para o blob de destino do Azure
O JSON de exemplo a seguir pode ser usado para criar um serviço vinculado denominado **MyAzureStorage** do tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Consulte [Serviço vinculado de armazenamento do Azure](https://msdn.microsoft.com/library/dn893522.aspx) para obter detalhes sobre os elementos JSON para definir um serviço vinculado de armazenamento do Azure.

### Criar a tabela de entrada
O JSON de exemplo a seguir pode ser usado para criar uma tabela da Azure Data Factory que se refere a uma tabela em um banco de dados Oracle local. Observe que o **tipo do local** é definido como **OnPremisesOracleTableLocation**.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

Consulte [Propriedades de localidade do Oracle local](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a uma tabela em um banco de dados Oracle local.

### Criar a tabela de saída
O script JSON a seguir define uma tabela de saída, **MyAzureBlob**; que se refere a um blob do Azure, **MyBlob**; na pasta blob, **MySubFolder**; no contêiner de blob, **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Consulte [Propriedades de local do blob do Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) para obter detalhes sobre os elementos JSON para definir uma tabela da Data Factory que se refere a um blob do Azure.

### Criar o pipeline
O pipeline de exemplo a seguir tem uma atividade de cópia que copia dados de uma tabela de banco de dados Oracle para um blob de armazenamento do Azure.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND "Timestamp" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy": {
	                    "concurrency": 3,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2015-03-01T00:00:00Z",
	        "end": "2015-03-15T00:00:00Z",
	        "isPaused": false
	    }
	}

Consulte [Referência de JSON de pipeline](https://msdn.microsoft.com/library/dn834988.aspx) para obter detalhes sobre os elementos JSON para definir um pipeline de Data Factory e [Origens e coletores com suporte](https://msdn.microsoft.com/library/dn894007.aspx) para propriedades de OracleSource e BlobSink.

## Consulte também

- [Copiar dados com o Azure Data Factory][adf-copyactivity]
- [Atividade de Cópia - Referência de Scripting JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Vídeo: apresentando a atividade de cópia do Azure Data Factory][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=62-->