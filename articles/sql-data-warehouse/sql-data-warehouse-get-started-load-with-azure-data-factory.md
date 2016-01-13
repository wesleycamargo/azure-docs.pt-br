<properties
	pageTitle="Carregar dados com o Azure Data Factory | Microsoft Azure"
	description="Saiba como carregar dados com o Azure Data Factory"
	services="sql-data-warehouse"
	documentationCenter="NA"
	authors="lodipalm"
	manager="barbkess"
	editor=""
	tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="lodipalm"/>

# Carregar dados com o Azure Data Factory

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 Este tutorial mostrará como criar um pipeline no Azure Data Factory que moverá os dados dos Blobs de Armazenamento do Azure para um SQL Data Warehouse. Com as etapas a seguir, você vai:

+ Configurar dados de exemplo em um Blob de Armazenamento do Azure.
+ Conectar recursos ao Azure Data Factory.
+ Crie um pipeline para mover dados de Blobs de Armazenamento para o SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]

## Recursos

Para este tutorial, você precisará do seguinte:

   + **Armazenamento de Blob do Azure**: o Blob de Armazenamento do Azure será a fonte de dados para o pipeline. Você pode usar um blob existente ou [provisionar um novo](../storage/storage-create-storage-account/).

   + **SQL Data Warehouse**: neste tutorial, você moverá dados para o SQL Data Warehouse. Se você ainda não tiver uma instância configurada, poderá aprender a fazer isso [aqui](sql-data-warehouse-get-started-provision.md). Além disso, a instância precisará ser configurada com nosso conjunto de dados do AdventureWorks DW. Se você não tiver provisionado o data warehouse com os dados de exemplo, poderá [carregá-lo manualmente](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Azure Data Factory**: o Azure Data Factory concluirá a carga real e se você precisar de mais informações sobre como definir o Azure Data Factory ou criar pipelines, poderá ver isso [aqui](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Quanto tudo estiver pronto, você poderá prosseguir e preparar seus dados e criar seu pipeline do Azure Data Factory.

## Dados de exemplo

Além das diferentes partes do pipeline, também precisaremos de alguns dados de exemplo que poderão ser usados para carregar dados no Azure Data Factory.

1. Primeiro, [baixe os dados de exemplo](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Esses dados funcionarão em conjunto com os dados de exemplo que já estiverem em seus dados de exemplo, fornecendo mais três anos de dados de vendas.

2. Depois que os dados forem baixados, você poderá movê-los para seu armazenamento de blob, executando o roteiro abaixo no AZCopy:

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Consulte a [documentação do AZCopy](../storage/storage-use-azcopy/) para obter informações adicionais sobre como instalar o AZCopy e como trabalhar com ele.

Agora que temos nossos dados no lugar, podemos prosseguir para sua fábrica de dados para criar o pipeline que moverá os dados da conta de armazenamento para o SQL Data Warehouse.

## Usando o Azure Data Factory

Agora que tudo está configurado, podemos começar a configurar o pipeline navegando até a instância do Azure Data Factory no Portal do Azure. Isso pode ser feito por meio do [Portal Clássico do Azure](portal.azure.com), selecionando a fábrica de dados no menu à esquerda.

Aqui haverá três etapas para configurar um pipeline de fábrica de dados do Azure para transferir dados para o data warehouse: vinculação dos serviços, definição dos conjuntos de dados e criação do pipeline.

### Criando serviços vinculados

A primeira etapa é vincular sua conta de armazenamento do Azure e o SQL Data Warehouse à fábrica de dados.

1. Primeiro, inicie o processo de registro clicando na seção 'Serviços Vinculados' de sua fábrica de dados e clicando em 'Novo armazenamento de dados'. Escolha um nome para registrar seu armazenamento do azure, selecione o Armazenamento do Azure como seu tipo e insira o Nome de Conta e a Chave da Conta.

2. Para registrar o SQL Data Warehouse, você precisará navegar até a seção 'Criar e Implantar', selecionar 'Novo Armazenamento de Dados' e 'Azure SQL Data Warehouse'. Em seguida, você precisará preencher o modelo abaixo:

		{
		    "name": "<Linked Service Name>",
		    "properties": {
		        "description": "",
		        "type": "AzureSqlDW",
		        "typeProperties": {
		            "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
		        }
		    }
		}

### Registrando conjuntos de dados

Depois de criar os serviços vinculados, teremos de definir os conjuntos de dados. Aqui, isso significa definir a estrutura dos dados que está sendo movida do armazenamento para o data warehouse. Ler mais sobre a criação

1. Inicie este processo navegando até a seção 'Criar e Implantar' de sua fábrica de dados.

2. Clique em 'Novo conjunto de dados' e em 'Armazenamento de Blob do Azure' para vincular o armazenamento à fábrica de dados. Você pode usar o script abaixo para definir os dados no armazenamento de Blob do Azure:

		{
			"name": "<Dataset Name>",
			"properties": {
				"type": "AzureBlob",
				"linkedServiceName": "<linked storage name>",
				"typeProperties": {
					"folderPath": "<containter name>",
					"fileName": "FactInternetSales.csv",
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



3. Agora, definiremos também nosso conjunto de dados para o SQL Data Warehouse. Começamos da mesma forma, clicando em 'Novo conjunto de dados' e em 'Azure SQL Data Warehouse'.

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

		{
		  "name": "DWDataset",
		  "properties": {
			"type": "AzureSqlDWTable",
			"linkedServiceName": "AzureSqlDWLinkedService",
			"typeProperties": {
			  "tableName": "FactInternetSales"
			},
			"availability": {
			  "frequency": "Hour",
			  "interval": 1
			}
		  }
		}

### Configurando o seu pipeline

Por fim, vamos configurar e executar o pipeline no Azure Data Factory. Esta é a operação que concluirá a movimentação de dados reais. Você pode encontrar uma exibição completa das operações que podem ser executadas com o SQL Data Warehouse e o Azure Data Factory [aqui](../data-factory/data-factory-azure-sql-data-warehouse-connector/).

Na seção 'Criar e Implantar', agora clique em 'Mais Comandos' e em 'Novo Pipeline'. Depois de criar o pipeline, você poderá usar o código abaixo para transferir os dados para o data warehouse:

	{
	"name": "<Pipeline Name>",
	"properties": {
		"description": "<Description>",
		"activities": [
			{
				"type": "Copy",
				"typeProperties": {
					"source": {
						"type": "BlobSource",
						"skipHeaderLineCount": 1
					},
					"sink": {
						"type": "SqlDWSink",
						"writeBatchSize": 0,
						"writeBatchTimeout": "00:00:10"
					}
				},
				"inputs": [
					{
						"name": "<Storage Dataset>"
					}
				],
				"outputs": [
					{
						"name": "<Data Warehouse Dataset>"
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
				"name": "Sample Copy",
				"description": "Copy Activity"
			}
		],
		"start": "<Date YYYY-MM-DD>",
		"end": "<Date YYYY-MM-DD>",
		"isPaused": false
	}
	}
	

<!---HONumber=AcomDC_0107_2016-->