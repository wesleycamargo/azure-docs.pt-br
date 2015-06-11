<properties 
	pageTitle="Chamar o Programa MapReduce da Data Factory do Azure" 
	description="Saiba como processar dados executando programas MapReduce em um cluster HDInsight do Azure em uma fábrica de dados do Azure." 
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

# Chamar Programas MapReduce da Data Factory
Este artigo descreve como chamar um **MapReduce** programa a partir de um pipeline de fábrica de dados do Azure usando o **HDInsight atividade** com **transformação MapReduce**.

## Introdução 
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a transformação MapReduce da Atividade do HDInsight.
 
Consulte [usar o Pig e Hive com dados Factory][data-factory-pig-hive-activities] para obter detalhes sobre como executar o Pig/Hive scripts em um HDInsight cluster de um pipeline de fábrica de dados do Azure usando transformações de Pig/ramificação da atividade HDInsight.

## JSON para atividade de HDInsight usando a transformação MapReduce 

Na definição do JSON para a atividade de HDInsight:
 
1. Definir o **tipo** do **atividade** para **HDInsightActivity**.
2. Definir o **tipo** do **transformação** para **MapReduce**.
3. Especifique o nome da classe para **className** propriedade.
4. Especifique o caminho para o arquivo JAR incluindo o nome do arquivo **jarFilePath** propriedade.
5. Especificar o serviço vinculado que faz referência ao armazenamento de Blob do Azure que contém o arquivo JAR **jarLinkedService** propriedade.   
6. Especifique quaisquer argumentos para o programa MapReduce no **argumentos** seção. 

   
 

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
Você pode baixar um exemplo para usar a atividade de HDInsight com o MapReduce transformação de: [dados fábrica amostras no GitHub][data-factory-samples].

## Consulte também

Artigo | Descrição
------ | ---------------
[Tutorial: Mover e processar os arquivos de log usando o alocador de dados][adf-tutorial] | Este artigo fornece uma explicação de ponta a ponta que mostra como implementar um near real usando o alocador de dados do Azure para transformar dados de arquivos de log em ideias de cenário do mundo. Neste tutorial, você irá usar transformações Pig e Hive para processar dados. 
[Referência do desenvolvedor de fábrica de dados do Azure][developer-reference] | A referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, o script JSON, funções, etc... 


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

<!---HONumber=GIT-SubDir--> 