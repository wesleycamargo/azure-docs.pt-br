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
	ms.date="09/22/2015" 
	ms.author="spelluru"/>

# Chamar Programas MapReduce da Data Factory
Este artigo descreve como invocar um programa **MapReduce** de um pipeline do Azure Data Factory usando a **Atividade de HDInsight MapReduce**.

## Introdução 
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a transformação MapReduce da Atividade do HDInsight.
 
Consulte o artigo sobre [Pig](data-factory-pig-activity) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre como executar os scripts Pig/Hive em um cluster do HDInsight de um pipeline da Azure Data Factory usando transformações Pig/Hive da Atividade do HDInsight.

## JSON para atividade de HDInsight usando a transformação MapReduce 

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
		        "name": "MyMahoutActivity",
		        "description": "Custom Map Reduce to generate Mahout result",
		        "inputs": [
		          {
		            "Name": "MahoutInput"
		          }
		        ],
		        "outputs": [
		          {
		            "Name": "MahoutOutput"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "type": "HDInsightMapReduce",
		        "typeProperties": {
		          "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		          "jarFilePath": "<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		          "jarLinkedService": "StorageLinkedService",
		          "arguments": [
		            "-s",
		            "SIMILARITY_LOGLIKELIHOOD",
		            "--input",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Input/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--output",
		            "$$Text.Format('wasb://<container>@<accountname>.blob.core.windows.net/Mahout/Output/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            "--maxSimilaritiesPerItem",
		            "500",
		            "--tempDir",
		            "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		          ]
		        },
		        "policy": {
		          "concurrency": 1,
		          "executionPriorityOrder": "OldestFirst",
		          "retry": 3,
		          "timeout": "01:00:00"
		        }
		      }
		    ]
		  }
		}

Você pode usar a transformação MapReduce para executar qualquer arquivo de jar do MapReduce em um cluster do HDInsight. Na seguinte definição de JSON de exemplo de uma pipeline, a Atividade HDInsight é configurada para executar um arquivo JAR do Mahout.

## Amostra
Você pode baixar um exemplo para usar a Atividade de HDInsight com a transformação MapReduce de: [Exemplos de Data Factory no GitHub](data-factory-samples.md).


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=Sept15_HO4-->