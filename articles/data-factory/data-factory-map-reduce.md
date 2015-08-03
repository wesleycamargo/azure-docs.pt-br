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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Chamar Programas MapReduce da Data Factory
Este artigo descreve como invocar um programa **MapReduce** de um pipeline da Azure Data Factory usando a **Atividade de HDInsight** com **Transformação MapReduce**.

## Introdução 
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a transformação MapReduce da Atividade do HDInsight.
 
Consulte [Usar o Pig e Hive com Data Factory][data-factory-pig-hive-activities] para obter detalhes sobre como executar os scripts Pig/Hive em um cluster do HDInsight de um pipeline da Azure Data Factory usando transformações Pig/Hive da Atividade do HDInsight.

## JSON para atividade de HDInsight usando a transformação MapReduce 

Na definição do JSON para a atividade de HDInsight:
 
1. Defina **type** de **activity** como **HDInsightActivity**.
2. Defina **type** de **transformation** como **MapReduce**.
3. Especifique o nome da classe para a propriedade **className**.
4. Especifique o caminho para o arquivo JAR, incluindo o nome do arquivo para a propriedade **jarFilePath**.
5. Especifique o serviço vinculado que faz referência ao Armazenamento de Blob do Azure que contém o arquivo JAR para a propriedade **jarLinkedService**.   
6. Especifique todos os argumentos para o programa MapReduce na seção **arguments**. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

Você pode usar a transformação MapReduce para executar qualquer arquivo de jar do MapReduce em um cluster do HDInsight. Na seguinte definição de JSON de exemplo de uma pipeline, a Atividade HDInsight é configurada para executar um arquivo JAR do Mahout.

## Amostra
Você pode baixar um exemplo para usar a Atividade de HDInsight com a transformação MapReduce de: [Exemplos de Data Factory no GitHub][data-factory-samples].

## Consulte também

Artigo | Descrição
------ | ---------------
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo apresenta um passo a passo completo que mostra como implementar um cenário parecido com um do mundo real usando a Azure Data Factory para transformar dados dos arquivos de log em informações. Neste tutorial, você vai usar transformações Pig e Hive para processar dados. 
[Referência do desenvolvedor da Azure Data Factory][developer-reference] | A Referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc… 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=July15_HO4-->