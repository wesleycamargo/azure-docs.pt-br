<properties 
	pageTitle="Atividade de Transmissão do Hadoop" 
	description="Saiba como usar a Atividade de Transmissão do Hadoop em um Azure Data Factory para executar programas de Transmissão do Hadoop em um cluster HDInsight sob demanda ou em seu próprio cluster HDInsight." 
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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Atividade de Transmissão do Hadoop
Você pode usar a atividade HDInsightStreamingActivity para invocar um trabalho de Transmissão do Hadoop de um pipeline do Azure Data Factory. O trecho de código JSON a seguir mostra a sintaxe para usar HDInsightStreamingActivity em um arquivo JSON do pipeline.

A Atividade de Transmissão do HDInsight em um [pipeline](data-factory-create-pipelines.md) do Data Factory executa programas de Transmissão do Hadoop [em seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou cluster HDInsight [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) baseado no Windows/Linux. Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Exemplo de JSON
O cluster HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (DaVinci). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

Observe o seguinte:

1. Defina **linkedServiceName** como o nome do serviço vinculado que aponta para o cluster HDInsight no qual o trabalho do MapReduce de transmissão será executado.
2. Defina o tipo da atividade como **HDInsightStreaming**.
3. Para a propriedade **mapper**, especifique o nome do executável do mapeador. No exemplo acima, cat.exe é o executável do mapeador.
4. Para a propriedade **reducer**, especifique o nome do executável do redutor. No exemplo acima, wc.exe é o executável do redutor.
5. Para a propriedade de tipo **input**, especifique o arquivo de entrada (incluindo o local) para o mapeador. No exemplo: "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt": adfsample é o contêiner de blob, example/data/Gutenberg é a pasta e davinci.txt é o blob.
6. Para a propriedade de tipo **output**, especifique o arquivo de saída (incluindo o local) para o redutor. A saída do trabalho de Transmissão do Hadoop será gravada no local especificado para essa propriedade.
7. Na seção **filePaths**, especifique os caminhos para os executáveis do mapeador e do redutor. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contêiner de blob, example/apps é a pasta e wc.exe é o executável.
8. Para a propriedade **fileLinkedService**, especifique o serviço vinculado do Armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths.
9. Para a propriedade **arguments**, especifique os argumentos para o trabalho de transmissão.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando ela é definida como Falha, os logs são baixados somente em caso de falha de execução. Quando ela é definida como Todos, os logs sempre são baixados, não importa o status de execução.

> [AZURE.NOTE] Conforme mostrado no exemplo, você precisará especificar um conjunto de dados de saída para a atividade de Streaming do Hadoop para a propriedade de **saídas**. Isso é apenas um conjunto de dados fictício que é necessário para direcionar a agenda de pipeline. Você não precisa especificar qualquer conjunto de dados de entrada para a atividade da propriedade **entradas**.

	
## Exemplo
O pipeline neste passo a passo executa o programa de mapa/redução de streaming de contagem de palavras no cluster do HDInsight do Azure.

### Serviços vinculados

#### Serviço vinculado de armazenamento do Azure
Primeiro, crie um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster do Azure HDInsight à fábrica de dados do Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o nome da conta e a chave de conta pelo nome e chave do armazenamento do Azure.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Serviço vinculado do Azure HDInsight
Em seguida, você cria um serviço vinculado para vincular seu cluster do HDInsight do Azure para a fábrica de dados do Azure. Se você copiar/colar o código a seguir, substitua o nome do cluster do HDInsight pelo nome do seu cluster do HDInsight e altere os valores de nome e senha do usuário.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Conjunto de dados

#### Conjunto de dados de saída
O pipeline neste exemplo não tem entradas. Você precisará especificar um conjunto de dados de saída para a atividade de streaming do HDInsight. Isso é apenas um conjunto de dados fictício que é necessário para direcionar a agenda de pipeline.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline

O pipeline neste exemplo tem apenas uma atividade que seja do tipo: **HDInsightStreaming**.

O cluster HDInsight é preenchido automaticamente com os programas de exemplo (wc.exe e cat.exe) e os dados (DaVinci). Por padrão, o nome do contêiner que é usado pelo cluster HDInsight é o nome do próprio cluster. Por exemplo, se o nome do cluster for myhdicluster, o nome do contêiner de blob associado seria myhdicluster.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

<!---HONumber=AcomDC_0204_2016-->