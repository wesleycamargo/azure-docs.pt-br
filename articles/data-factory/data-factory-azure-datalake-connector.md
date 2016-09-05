<properties
	pageTitle="Mover dados de/para o Repositório Data Lake do Azure | Azure Data Factory"
	description="Saiba como mover dados para/do Repositório Data Lake do Azure usando o Azure Data Factory"
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
	ms.date="08/22/2016"
	ms.author="spelluru"/>

# Mover dados para e do Repositório Data Lake do Azure usando o Azure Data Factory
Este artigo descreve como você pode usar a Atividade de Cópia no Azure Data Factory para mover dados para fora/dentro do Azure Data Lake Store para fora/dentro de outro repositório de dados. Este artigo se baseia no artigo sobre as [atividades de movimentação de dados](data-factory-data-movement-activities.md), que apresenta uma visão geral da movimentação de dados com a atividade de cópia e combinações de repositório de dados permitidas.

> [AZURE.NOTE]
Crie uma conta do Azure Data Lake Store antes de criar um pipeline com uma Atividade de Cópia para mover dados para dentro/fora de um Azure Data Lake Store. Para saber mais sobre o Azure Data Lake Store, confira [Introdução ao Azure Data Lake Store](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Veja o tutorial [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas de como criar um Data Factory, serviços vinculados, conjuntos de dados e um pipeline. Use os trechos de código JSON com o Editor do Data Factory, Visual Studio ou Azure PowerShell para criar as entidades do Data Factory.

## Assistente de cópia de dados
A maneira mais fácil de criar um pipeline que copia dados para fora/dentro do Azure Data Lake Store é usar o Assistente de cópia de dados. Confira [Tutorial: Criar um pipeline usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md) para ver um breve passo a passo sobre como criar um pipeline usando o Assistente de cópia de dados.

Os exemplos a seguir fornecem amostras de definições de JSON que você pode usar para criar um pipeline usando o [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md) ou o [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md), ou o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tais exemplos mostram como copiar dados de/para o Azure Data Lake Store e o Armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados **diretamente** de qualquer uma das fontes a qualquer um dos coletores declarados [aqui](data-factory-data-movement-activities.md#supported-data-stores) usando a Atividade de Cópia no Azure Data Factory.


## Exemplo: copiar dados do Blob do Azure para o Repositório Data Lake do Azure
O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureBlob](#azure-blob-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) de saída do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Um [pipeline](data-factory-create-pipelines.md) com Atividade de cópia que usa [BlobSource](#azure-blob-copy-activity-type-properties) e [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

O exemplo copia dados de série temporal de um Armazenamento de Blobs do Azure para um Azure Data Lake Store de hora em hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.


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

**Serviço vinculado do Azure Data Lake:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Criar um Serviço Vinculado do Azure Data Lake usando o Editor do Data Factory
O procedimento a seguir lista as etapas para criar um serviço vinculado do Repositório Data Lake do Azure usando o Editor do Data Factory.

1. Clique em **Novo repositório de dados** na barra de comandos e escolha **Repositório Data Lake do Azure**.
2. No editor de JSON, para a propriedade **dataLakeStoreUri**, insira o URI do Data Lake.
3. Clique no botão **Autorizar** na barra de comandos. Você deve ver uma janela pop-up.

	![Botão Autorizar](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Use as suas credenciais para entrar. Agora, a propriedade **authorization** no JSON deve ser atribuída a um valor.
5. (opcional) Especifique valores para parâmetros opcionais, como **accountName**, **subscriptionID** e **resourceGroupName** no JSON (ou) exclua essas propriedades do JSON.
6. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

> [AZURE.IMPORTANT] O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. **Reautorize** usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Confira a seção [Serviço vinculado do Azure Data Lake Store](#azure-data-lake-store-linked-service-properties) para obter detalhes.



**Conjunto de dados de entrada de Blob do Azure:**

Os dados são coletados de um novo blob a cada hora (frequência: hora, intervalo: 1). O caminho de pasta e nome de arquivo para o blob são avaliados dinamicamente com base na hora de início da fatia que está sendo processada. O caminho da pasta usa parte da hora de início do dia, mês e ano e o nome de arquivo usa a parte da hora de início. A configuração "external": "true" informa o serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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


**Conjunto de dados de saída do Azure Data Lake:**

O exemplo copia dados para um repositório do Azure Data Lake. Os novos dados são copiados para o repositório Data Lake a cada hora.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Pipeline com a Atividade de cópia:**

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **BlobSource** e o tipo **sink** está definido como **AzureDataLakeStoreSink**.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## Exemplo: copiar dados do Repositório Data Lake do Azure para o Blob do Azure
O exemplo a seguir mostra:

1.	Um serviço vinculado do tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2.	Um serviço vinculado do tipo [AzureStorage](#azure-storage-linked-service-properties).
3.	Um [conjunto de dados](data-factory-create-datasets.md) de entrada do tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4.	Um [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](#azure-blob-dataset-type-properties).
5.	Um [pipeline](data-factory-create-pipelines.md) com uma Atividade de cópia que usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties).

O exemplo copia dados de série temporal de um Azure Data Lake Store para um blob do Azure a cada hora. As propriedades JSON usadas nesses exemplos são descritas nas seções após os exemplos.

**Exemplo de serviço vinculado do Repositório Data Lake do Azure:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Veja as etapas no exemplo anterior para obter a URL de autorização.

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

**Conjunto de dados de entrada do Azure Data Lake:**

Configurar **"external": true** informa ao serviço Data Factory que a tabela é externa ao Data Factory e não é produzida por uma atividade no Data Factory.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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

O pipeline contém uma Atividade de Cópia que está configurada para usar os conjuntos de dados de entrada e saída e é agendada para ser executada a cada hora. Na definição JSON do pipeline, o tipo **source** está definido como **AzureDataLakeStoreSource** e o tipo **sink** está definido como **BlobSink**.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Propriedades do Serviço Vinculado do Repositório Data Lake do Azure

Você pode vincular uma conta de armazenamento do Azure a uma Azure Data Factory usando um serviço de armazenamento do Azure vinculado. A tabela a seguir fornece a descrição para elementos JSON específicas para o serviço de Armazenamento do Azure vinculado.

| Propriedade | Descrição | Obrigatório |
| :-------- | :----------- | :-------- |
| type | A propriedade type deve ser definida como: **AzureDataLakeStore** | Sim |
| dataLakeStoreUri | Especifica informações sobre a conta do Repositório Data Lake do Azure. Ela está no seguinte formato: https://<Nome da conta Azure Data Lake>.azuredatalakestore.net/webhdfs/v1 | Sim |
| authorization | Clique no botão **Autorizar** no **Editor do Data Factory** e insira as suas credenciais, que atribui a URL de autorização gerada automaticamente a essa propriedade. | Sim |
| sessionId | A ID de sessão OAuth da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. Essa configuração é gerada automaticamente quando você usa o Editor do Data Factory. | Sim |  
| accountName | Nome de conta do Data Lake | Não |
| subscriptionId | ID de assinatura do Azure. | Não (se não for especificado, a assinatura do Data Factory é usada). |
| resourceGroupName | Nome do grupo de recursos do Azure | Não (se não for especificado, o grupo de recursos do Data Factory é usado). |

## Expiração do token 
O código de autorização gerado usando o botão **Autorizar** expira após algum tempo. Confira a tabela a seguir para ver os tempos de expiração para os diferentes tipos de contas de usuário. Talvez você veja a mensagem de erro a seguir quando o **token de autenticação expirar**: "Erro na operação da credencial: invalid\_grant - AADSTS70002: erro ao validar as credenciais. AADSTS70008: a concessão de acesso fornecida expirou ou foi revogada. ID do rastreamento: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID da correlação: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Carimbo de data/hora: 2015-12-15 21-09-31Z".


| Tipo de usuário | Expira após |
| :-------- | :----------- | 
| Contas de usuários NÃO gerenciadas pelo Azure Active Directory (@hotmail.com, @live.com etc.). | 12 horas |
| Contas de usuários gerenciadas pelo AAD (Azure Active Directory) | 14 dias após a última execução da fatia. <br/>90 dias, se uma fatia com base em serviços vinculados do OAuth for executada pelo menos uma vez a cada 14 dias.<br/> |

Se você alterar sua senha antes do momento de expiração do token, o token expirará imediatamente e você verá o erro mencionado nesta seção.

Para evitar/resolver este erro, reautorize usando o botão **Autorizar** quando o **token expirar** e reimplante o serviço vinculado. Você também pode gerar valores para as propriedades **sessionId** e **authorization** programaticamente usando o código na seção a seguir.

### Para gerar valores sessionId e authorization programaticamente 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Veja os tópicos [Classe AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [Classe AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [Classe AuthorizationSessionGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) para obter detalhes sobre as classes do Data Factory usadas no código. Adicione uma referência à versão **2.9.10826.1824** de **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** para a classe WindowsFormsWebAuthenticationDialog usada no código.
 

## Propriedades de tipo de Conjunto de Dados do Azure Data Lake

Para obter uma lista completa das seções JSON e propriedades disponíveis para definir conjuntos de dados, consulte o artigo [Criando conjuntos de dados](data-factory-create-datasets.md). Seções como structure, availability e policy de um conjunto de dados JSON são similares para todos os tipos de conjunto de dados (SQL Azure, Blob do Azure, Tabela do Azure etc.).

A seção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre o local, formato etc. dos dados no repositório de dados. A seção typeProperties do conjunto de dados do tipo **AzureDataLakeStore** tem as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
| :-------- | :----------- | :-------- |
| folderPath | Caminho para o contêiner e a pasta no repositório do Azure Data Lake. | Sim |
| fileName | O nome do arquivo no repositório Azure Data Lake. fileName é opcional e diferencia maiúsculas de minúsculas. <br/><br/>Se você especificar um nome de arquivo, a atividade (incluindo a cópia) funcionará no arquivo específico.<br/><br/>Quando fileName não for especificado, a cópia incluirá todos os arquivos em folderPath no conjunto de dados de entrada.<br/><br/>Quando fileName não for especificado para um conjunto de dados de saída, o nome do arquivo gerado estará no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt | Não |
| partitionedBy | partitionedBy é uma propriedade opcional. Você pode usá-lo para especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal. Por exemplo, folderPath pode ser parametrizado para cada hora dos dados. Confira a seção [Usando a propriedade partitionedBy](#using-partitionedby-property) para obter detalhes e exemplos. | Não |
| formato | Os seguintes tipos de formato têm suporte: **TextFormat**, **AvroFormat**, **JsonFormat** e **OrcFormat**. Defina a propriedade **type** sob formato como um desses valores. Confira as seções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), Especificando [JsonFormat](#specifying-jsonformat) e [Especificando OrcFormat](#specifying-orcformat) para obter detalhes. Se você quiser copiar arquivos no estado em que se encontram entre repositórios baseados em arquivo (cópia binária), ignore a seção de formato nas duas definições de conjunto de dados de entrada e de saída.| Não
| compactação | Especifique o tipo e o nível de compactação para os dados. Os tipos compatíveis são: **GZip**, **Deflate** e **BZip2** e os níveis permitidos são: **Melhor** e **Mais rápido**. Atualmente, as configurações de compactação não são compatíveis para dados em **AvroFormat** ou **OrcFormat**. Consulte a seção [Suporte à compactação](#compression-support) para obter mais detalhes. | Não |

### Usando a propriedade partitionedBy
Você pode especificar um folderPath dinâmico e o nome de arquivo para dados de série temporal com a seção **partitionedBy**, macros de Data Factory e variáveis do sistema: SliceStart e SliceEnd, que indicam as horas de início e término para uma fatia de dados determinada.

Confira os artigos [Criando conjuntos de dados](data-factory-create-datasets.md) e [Agendamento e execução](data-factory-scheduling-and-execution.md) para saber mais detalhes sobre os conjuntos de dados de série temporal, agendamento e fatias.

#### Exemplo 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy":
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Nesse exemplo, {Slice} é substituído pelo valor da variável de sistema SliceStart do Data Factory no formato (AAAAMMDDHH) especificado. O SliceStart refere-se à hora de início da fatia. O folderPath é diferente para cada fatia. Por exemplo: wikidatagateway/wikisampledataout/2014100103 ou wikidatagateway/wikisampledataout/2014100104

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

Neste exemplo, ano, mês, dia e hora do SliceStart são extraídos em variáveis separadas que são usadas pelas propriedades folderPath e fileName.

[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
 

### Suporte à compactação  
O processamento de grandes conjuntos de dados pode causar afunilamentos de E/S e de rede. Portanto, os dados compactados em repositórios não apenas aceleram a transferência de dados pela rede e economizam espaço em disco, mas também oferecem aprimoramentos consideráveis de desempenho no processamento de Big Data. No momento, a compactação tem suporte para repositórios de dados baseados em arquivo, por exemplo, Blob do Azure ou o Sistema de Arquivos Local.

Para especificar a compactação de um conjunto de dados, use a propriedade **compactação** no conjunto de dados JSON, como no exemplo a seguir:

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
A seção **compactação** tem duas propriedades:
  
- **Tipo:** o codec de compactação, que pode ser **GZIP**, **Deflate** ou **BZIP2**.
- **Nível:** a taxa de compactação, que pode ser **Ideal** ou **Mais rápida**.
	- **Mais rápida:** a operação de compactação deve ser concluída o mais rápido possível, mesmo se o arquivo resultante não for compactado da maneira ideal.
	- **Ideal**: a operação de compactação deve ser concluída da maneira ideal, mesmo se a operação demorar mais tempo para ser concluída.
	
	Consulte o tópico [Nível de compactação](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx) para saber mais.

Suponha que o conjunto de dados de exemplo seja usado como a saída de uma atividade de cópia. A atividade de cópia compacta os dados de saída com o codec GZIP usando a melhor taxa e, em seguida, grava os dados compactados em um arquivo chamado pagecounts.csv.gz no Azure Data Lake Store.

Quando você especifica a propriedade de compactação em um conjunto de dados de entrada JSON, o pipeline lê dados compactados da origem. Quando você especifica a propriedade em um conjunto de dados de saída JSON, a atividade de cópia pode gravar dados compactados no destino. Aqui estão alguns exemplos de cenários:

- Leia os dados compactados em GZIP de um Repositório de Azure Data Lake, descompacte-os e grave os dados resultantes em um banco de dados SQL do Azure. Nesse caso, você define o conjunto de dados de entrada do Repositório do Azure Data Lake com a propriedade de compactação JSON.
- Leia os dados de um arquivo de texto sem formatação do Sistema de arquivos local, compacte-os usando o formato GZip e grave os dados compactados em um Repositório de Azure Data Lake. Nesse caso, você define um conjunto de dados do Repositório de Azure Data Lake com a propriedade de compactação JSON.
- Leia dados compactados GZIP de um Repositório de Azure Data Lake, descompacte-os, compacte-os usando BZIP2 e grave os dados resultantes em um Repositório de Azure Data Lake. Defina o conjunto de tipo de compactação como GZIP e BZIP2 para os conjuntos de dados de entrada e saída, respectivamente.


## Propriedades de tipo de Atividade de Cópia do Azure Data Lake  
Para obter uma lista completa das seções e propriedades disponíveis para definir atividades, consulte o artigo [Criando pipelines](data-factory-create-pipelines.md). As propriedades, como nome, descrição, tabelas de entrada e saída, várias políticas, etc. estão disponíveis para todos os tipos de atividade.

As propriedades disponíveis na seção typeProperties da atividade, por outro lado, variam de acordo com cada tipo de atividade. Para a atividade de Cópia, elas variam de acordo com os tipos de fonte e coletor

**AzureDataLakeStoreSource** dá suporte à seção **typeProperties** das seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| recursiva | Indica se os dados são lidos recursivamente por meio de subpastas ou somente da pasta especificada. | True (valor padrão), False | Não |



**AzureDataLakeStoreSink** dá suporte à seção **typeProperties** das seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Especifica o comportamento da cópia. | **PreserveHierarchy:** preserva a hierarquia de arquivos na pasta de destino. O caminho relativo do arquivo de origem para a pasta de origem é idêntico ao caminho relativo do arquivo de destino para a pasta de destino.<br/><br/>**FlattenHierarchy:** todos os arquivos da pasta de origem são criados no primeiro nível da pasta de destino. Os arquivos de destino são criados com o nome gerado automaticamente.<br/><br/>**MergeFiles:** mescla todos os arquivos da pasta de origem em um arquivo. Se o nome do arquivo/blob for especificado, o nome do arquivo mesclado será o nome especificado; caso contrário, será o nome de arquivo gerado automaticamente. | Não |


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## Desempenho e Ajuste  
Veja o [Guia de Desempenho e Ajuste da Atividade de Cópia](data-factory-copy-activity-performance.md) para saber mais sobre os principais fatores que afetam o desempenho e a movimentação de dados (Atividade de Cópia) no Azure Data Factory, além de várias maneiras de otimizar esse processo.

<!---HONumber=AcomDC_0824_2016-->