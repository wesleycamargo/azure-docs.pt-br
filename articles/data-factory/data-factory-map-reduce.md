<properties 
	pageTitle="Chamar o Programa MapReduce da Data Factory do Azure" 
	description="Saiba como processar dados executando programas MapReduce em um cluster HDInsight do Azure em uma Azure Data Factory." 
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
	ms.date="04/01/2016" 
	ms.author="spelluru"/>

# Chamar Programas MapReduce da Data Factory
A atividade MapReduce do HDInsight em um [pipeline](data-factory-create-pipelines.md) do Data Factory executa programas MapReduce no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight baseado em Windows/Linux, ou em um [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Introdução 
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar atividade do HDInsight MapReduce.
 
Consulte [Pig](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre como executar scripts do Pig/Hive em um cluster do HDInsight baseado no Windows/Linux a partir de um pipeline de fábrica de dados do Azure usando atividades do HDInsight Pig e Hive.

## JSON para atividade do HDInsight MapReduce 

Na definição do JSON para a atividade de HDInsight:
 
1. Defina o **tipo** da **atividade** como **HDInsightActivity**.
3. Especifique o nome da classe para a propriedade **className**.
4. Especifique o caminho para o arquivo JAR, incluindo o nome do arquivo para a propriedade **jarFilePath**.
5. Especifique o serviço vinculado que faz referência ao Armazenamento de Blob do Azure que contém o arquivo JAR para a propriedade **jarLinkedService**.   
6. Especifique todos os argumentos para o programa MapReduce na seção **arguments**. Em tempo de execução, você verá alguns argumentos extras (por exemplo: mapreduce.job.tags) do framework MapReduce. Para diferenciar seus argumentos com os argumentos MapReduce, considere usar opção e valor como argumentos, conforme mostrado no exemplo a seguir (- s, --input - output etc... são opções seguidas imediatamente por seus valores).

		{
		    "name": "MahoutMapReduceSamplePipeline",
		    "properties": {
		        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		        "activities": [
		            {
		                "type": "HDInsightMapReduce",
		                "typeProperties": {
		                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
		                    "jarLinkedService": "StorageLinkedService",
		                    "arguments": [
		                        "-s",
		                        "SIMILARITY_LOGLIKELIHOOD",
		                        "--input",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
		                        "--output",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
		                        "--maxSimilaritiesPerItem",
		                        "500",
		                        "--tempDir",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
		                    ]
		                },
		                "inputs": [
		                    {
		                        "name": "MahoutInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "MahoutOutput"
		                    }
		                ],
		                "policy": {
		                    "timeout": "01:00:00",
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "MahoutActivity",
		                "description": "Custom Map Reduce to generate Mahout result",
		                "linkedServiceName": "HDInsightLinkedService"
		            }
		        ],
		        "start": "2014-01-03T00:00:00Z",
		        "end": "2014-01-04T00:00:00Z",
		        "isPaused": false,
		        "hubName": "mrfactory_hub",
		        "pipelineMode": "Scheduled"
		    }
		}
	
	

Você pode usar a atividade do HDInsight MapReduce para executar qualquer arquivo de jar do MapReduce em um cluster do HDInsight. Na seguinte definição de JSON de exemplo de uma pipeline, a Atividade HDInsight é configurada para executar um arquivo JAR do Mahout.

## Exemplo no GitHub
Você pode baixar um exemplo para usar a atividade do MapReduce HDInsight em: [Exemplos do Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).

## Executando o programa de contagem de palavras
O pipeline neste exemplo executa o programa de contagem de palavras de mapa/redução no cluster do Azure HDInsight.

### Serviços vinculados
Primeiro, crie um serviço vinculado para vincular o armazenamento do Azure que é usado pelo cluster do Azure HDInsight à fábrica de dados do Azure. Se você copiar/colar o código a seguir, não se esqueça de substituir o **nome da conta** e a **chave de conta** pelo nome e chave do armazenamento do Azure.

#### Serviço vinculado de armazenamento do Azure

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
Em seguida, você cria um serviço vinculado para vincular seu cluster do HDInsight do Azure para a fábrica de dados do Azure. Se você copiar/colar o código a seguir, substitua o **nome do cluster do HDInsight** pelo nome do seu cluster do HDInsight e altere os valores de nome e senha do usuário.

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
O pipeline neste exemplo não tem entradas. Você precisará especificar um conjunto de dados de saída para a atividade do HDInsight MapReduce. Isso é apenas um conjunto de dados fictício que é necessário para direcionar a agenda de pipeline.

	{
	    "name": "MROutput",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "WordCountOutput1.txt",
	            "folderPath": "example/data/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline
O pipeline neste exemplo tem apenas uma atividade que seja do tipo: HDInsightMapReduce. Algumas das propriedades importantes no JSON são:

Propriedade | Observações
:-------- | :-----
type | O tipo deve ser definido como **HDInsightMapReduce**. 
className | Nome da classe é: **wordcount**
jarFilePath | Caminho para o arquivo jar que contém a classe acima. Se você copiar/colar o código a seguir, não se esqueça de alterar o nome do cluster. 
jarLinkedService | Serviço vinculado do Armazenamento do Azure que contém o arquivo jar. Esse é o armazenamento que está associado ao cluster do HDInsight. 
argumentos | O programa wordcount leva dois argumentos, uma entrada e uma saída. O arquivo de entrada é o davinci.txt.
frequência/intervalo | Os valores dessas propriedades correspondem ao conjunto de dados de saída. 
linkedServiceName | refere-se ao serviço vinculado do HDInsight criado anteriormente.   

	{
	    "name": "MRSamplePipeline",
	    "properties": {
	        "description": "Sample Pipeline to Run the Word Count Program",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "wordcount",
	                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "/example/data/gutenberg/davinci.txt",
	                        "/example/data/WordCountOutput1"
	                    ]
	                },
	                "outputs": [
	                    {
	                        "name": "MROutput"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "MRActivity",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-03T00:00:00Z",
	        "end": "2014-01-04T00:00:00Z"
	    }
	}

## Executar programas Spark
Você pode usar a atividade MapReduce para executar programas Spark no cluster HDInsight Spark. Consulte [Invoke Spark programs from Azure Data Factory (Invocar programas Spark da Azure Data Factory)](data-factory-spark.md) para obter detalhes.

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#monitor-pipelines

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## Consulte também
- [Atividade de Hive](data-factory-hive-activity.md)
- [Atividade Pig](data-factory-pig-activity.md)
- [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas Spark](data-factory-spark.md)
- [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0518_2016-->