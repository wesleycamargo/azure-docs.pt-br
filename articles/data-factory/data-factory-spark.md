<properties 
	pageTitle="Invocar programas Spark do Azure Data Factory" 
	description="Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce." 
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Invocar Programas Spark pelo Data Factory
## Introdução
Você pode usar a Atividade MapReduce em um pipeline do Data Factory para executar programas Spark em seu cluster Spark HDInsight. Consulte o artigo [Atividade MapReduce](data-factory-map-reduce.md) para obter informações detalhadas sobre como usar a atividade antes de ler esse artigo.

## Amostra do Spark no GitHub
O [Spark - amostra de Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) mostra como usar a atividade MapReduce para invocar um programa Spark. O programa Spark apenas copia dados de um contêiner de Blob do Azure para outro.

## Entidades de Data Factory
A pasta **Spark-ADF/src/ADFJsons** contém arquivos para entidades de Data Factory (serviços vinculados, conjuntos de dados, pipeline).

Há dois **serviços vinculados** neste exemplo: Armazenamento do Azure e Azure HDInsight. Você precisa especificar o nome do armazenamento do Azure e valores de chave em **StorageLinkedService.json** e clusterUri, nome de usuário e senha em **HDInsightLinkedService.json**.

Há dois **conjuntos de dados** neste exemplo: **input.json** e **output.json**. Esses arquivos estão localizados na pasta **Conjuntos de dados**. Esses arquivos representam conjuntos de dados de entrada e saída para a atividade MapReduce

Há um **pipeline** na amostra na pasta **ADFJsons/Pipeline**. Essa é a entidade mais importante que você precisa revisar para entender como invocar um programa Spark usando a atividade MapReduce.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

Como você pode ver, a atividade MapReduce está configurada para invocar **spark-adf-job-bin.jar** no contêiner **libs** no seu armazenamento do Azure (especificado em StorageLinkedService.json). O código-fonte para este programa está na pasta Spark-ADF/src/main/java/com/adf/spark e chama spark-enviar e executar trabalhos Spark.

Esse programa MapReduce (spark-adf-job-bin.jar) em execução no cluster Spark HDInsight invoca um programa Spark **sparkdemoapp_2.10-1.0.jar** e passa os argumentos recebidos via atividade MapReduce (mostrados no JSON acima) para o programa Spark. **sparkdemoapp_2.10-1.0.jar** contém código-fonte Scala que copia dados de um contêiner de blobs do Azure para outro. Você pode substituir este jar do aplicativo de demonstração por outro jar que contém qualquer trabalho que você está tentando executar usando o Spark.

Para resumir, a **atividade MapReduce** invoca o programa MapReduce **spark-adf-job-bin.jar** que invoca o programa Spark **sparkdemoapp_2.10-1.0.jar**. Para executar seu próprio programa Spark, substitua sparkdemoapp_2.10-1.0.jar pelo seu próprio.

> [AZURE.NOTE] Você precisa usar seu próprio cluster Spark HDInsight com essa abordagem para invocar programas Spark usando a atividade MapReduce. Não há suporte para o uso de um clister HDInsight sob demanda.


## Veja também
- [Atividade de Hive](data-factory-hive-activity.md)
- [Atividade Pig](data-factory-pig-activity.md)
- [Atividade MapReduce](data-factory-map-reduce.md)
- [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0629_2016-->