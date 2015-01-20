<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Chamar o Programa MapReduce da Data Factory do Azure" description="Saiba como processar os dados ao executar os programas MapReduce em um cluster HDInsight do Azure a partir de dados de fábrica do Azure." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Chamar Programas MapReduce da Data Factory
Este artigo descreve como chamar um programa **MapReduce** a partir de uma pipeline da Data Factory do Azure usando a **Atividade de HDInsight ** com **Transformação MapReduce**. 

## Introdução 
Uma pipeline em uma data factory do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. 

- **Atividade de Cópia** copia dados de um armazenamento de origem para um armazenamento de destino. Para saber mais sobre a Atividade de Cópia, consulte [Copiar dados com a Data Factory][data-factory-copy-activity]. 
- **Atividade de HDInsight** processa dados executando scripts Hive/Pig ou programas MapReduce em um cluster HDInsight. A Atividade de HDInsight oferece suporte a três transformação: **Hive**, **Pig**, e **MapReduce**. A Atividade de HDInsight pode consumir uma ou mais entradas e produzir uma ou mais saídas.
 
Consulte [Usar o Pig e Hive com Data Factory][data-factory-pig-hive-activities] para obter detalhes sobre como executar os scripts Pig/Hive em um cluster do HDInsight de uma pipeline da Data Factory do Azure usando transformações Pig/Hive da Atividade do HDInsight. Este artigo descreve como usar a transformação MapReduce da Atividade do HDInsight.

## Pipeline de JSON
No arquivo JSON para uma pipeline:
 
1. Definir o **tipo** da **atividade** para **HDInsightActivity**.
2. Definir o **tipo** da **transformação** para **MapReduce**.
3. Especifique o nome da classe para propriedade **className**.
4. Especifique o caminho para o arquivo JAR incluindo o nome do arquivo para propriedade **jarFilePath**.
5. Especificar o serviço vinculado que faz referência ao Armazenamento de Blob do Azure que contém o arquivo JAR para a propriedade**jarLinkedService**.   
6. Especifique quaisquer argumentos para o programa MapReduce na seção **argumentos**. 

Você pode usar a transformação MapReduce para executar qualquer arquivo de jar do MapReduce em um cluster do HDInsight. Na seguinte definição de JSON de exemplo de uma pipeline, a Atividade HDInsight é configurada para executar um arquivo JAR do Mahout.   
 

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

## Amostra
Você pode baixar um exemplo para usar a Atividade de HDInsight com a transformação MapReduce: [Exemplos de Data Factory no GitHub][data-factory-samples].  

## Consulte também

Artigo | Descrição
------ | ---------------
[Introdução à Data Factory do Azure][data-factory-introduction] | Este artigo apresenta os conceitos, o serviço da Data Factory do Azure, o valor que ele oferece e os cenários que ele dá suporte.
[Introdução à Data Factory do Azure][adf-getstarted]Este artigo fornece um tutorial de ponta a ponta que mostra como criar um exemplo de data factory do Azure que copia dados de um blob do Azure para um banco de dados SQL do Azure.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local em um blob do Azure.
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando a Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em uma pipeline.
[Solucionar problemas de Data Factory][troubleshoot]| Este artigo descreve como solucionar problemas da Data Factory do Azure.
[Referência do Desenvolvedor da Data Factory do Azure][developer-reference]A Referência do Desenvolvedor tem um conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
