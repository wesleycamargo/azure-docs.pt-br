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
	ms.date="10/25/2015" 
	ms.author="spelluru"/>

# Atividade de Transmissão do Hadoop
Você pode usar a atividade HDInsightStreamingActivity para invocar um trabalho de Transmissão do Hadoop de um pipeline do Azure Data Factory. O trecho de código JSON a seguir mostra a sintaxe para usar HDInsightStreamingActivity em um arquivo JSON do pipeline.

A Atividade de Transmissão do HDInsight em um [pipeline](data-factory-create-pipelines.md) do Data Factory executa programas de Transmissão do Hadoop [em seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight ou no cluster HDInsight [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Este artigo baseia-se no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md), que apresenta uma visão geral da transformação de dados e as atividades de transformação para as quais há suporte.

## Exemplo

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "getDebugInfo": "Failure",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

Observe o seguinte:

1. Defina **linkedServiceName** como o nome do serviço vinculado que aponta para o cluster HDInsight no qual o trabalho do MapReduce de transmissão será executado.
2. Defina o tipo da atividade como **HDInsightStreaming**.
3. Para a propriedade **mapper**, especifique o nome do executável do mapeador. No exemplo acima, cat.exe é o executável do mapeador.
4. Para a propriedade **reducer**, especifique o nome do executável do redutor. No exemplo acima, wc.exe é o executável do redutor.
5. Para a propriedade **input**, especifique o arquivo de entrada (incluindo o local) para o mapeador. No exemplo: “wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt”: adfsample é o contêiner de blob, example/data/Gutenberg é a pasta e davinci.txt é o blob.
6. Para a propriedade **output**, especifique o arquivo de saída (incluindo o local) para o redutor. A saída do trabalho de Transmissão do Hadoop será gravada no local especificado para essa propriedade.
7. Na seção **filePaths**, especifique os caminhos para os executáveis do mapeador e do redutor. No exemplo: “adfsample/example/apps/wc.exe”, adfsample é o contêiner de blob, example/apps é a pasta e wc.exe é o executável.
8. Para a propriedade **fileLinkedService**, especifique o serviço vinculado do Armazenamento do Azure que representa o armazenamento do Azure que contém os arquivos especificados na seção filePaths.
9. Para a propriedade **arguments**, especifique os argumentos para o trabalho de transmissão.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando ela é definida como Falha, os logs são baixados somente em caso de falha de execução. Quando ela é definida como Todos, os logs sempre são baixados, não importa o status de execução. 

	

<!---HONumber=Nov15_HO1-->