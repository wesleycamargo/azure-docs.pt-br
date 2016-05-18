<properties 
	pageTitle="Atividade de Hive" 
	description="Saiba como usar a atividade de Hive em uma Azure Data Factory para executar consultas de Hive em um cluster sob demanda/próprio de HDInsight." 
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
	ms.date="04/18/2016" 
	ms.author="spelluru"/>

# Atividade de Hive

A atividade de Hive do HDInsight em um [pipeline](data-factory-create-pipelines.md) de Data Factory executa consultas de Hive em [seu próprio cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou no cluster [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do HDInsight baseado em Windows/Linux. Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Sintaxe

	{
		"name": "Hive Activity",
	    "description": "description",
	    "type": "HDInsightHive",
	    "inputs": [
	      {
	        "name": "input tables"
	      }
	    ],
	    "outputs": [
	      {
	        "name": "output tables"
	      }
	    ],
	    "linkedServiceName": "MyHDInsightLinkedService",
	    "typeProperties": {
	      "script": "Hive script",
	      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
	      "defines": {
	        "param1": "param1Value"
	      }
	    },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
	}
	
## Detalhes da sintaxe

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade | Sim
description | Texto que descreve qual a utilidade da atividade | Não
type | HDInsightHive | Sim
inputs | Entradas consumidas pela atividade de Hive | Não
outputs | Saídas produzidas pela atividade de Hive | Sim 
linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado na Data Factory | Sim 
script | Especifique o script de Hive embutido | Não
script path | Armazene o script de Hive em um armazenamento de blob do Azure e forneça o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. Observe que o nome do arquivo diferencia maiúsculas de minúsculas. | Não 
defines | Especifique parâmetros como pares chave/valor para referenciar dentro do script de Hive usando 'hiveconf' | Não

## Exemplo

Vamos considerar um exemplo de análises de logs de jogos nos quais você deseja identificar o tempo gasto pelos usuários em jogos lançados por sua empresa.

Abaixo está um exemplo de log de jogos, que é separado por vírgulas (,) e contém os seguintes campos: ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

O **script de Hive** para processamento desses dados tem esta aparência:

	DROP TABLE IF EXISTS HiveSampleIn; 
	CREATE EXTERNAL TABLE HiveSampleIn 
	(
		ProfileID		string, 
	    SessionStart 	string, 
	    Duration 		int, 
	    SrcIPAddress 	string, 
	    GameType 		string
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
	
	DROP TABLE IF EXISTS HiveSampleOut; 
	CREATE EXTERNAL TABLE HiveSampleOut 
	(	
		ProfileID 	string, 
	    Duration 	int
	) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
	
	INSERT OVERWRITE TABLE HiveSampleOut
	Select 
		ProfileID,
		SUM(Duration)
	FROM HiveSampleIn Group by ProfileID

Para executar esse script de Hive em um pipeline de Data Factory, você precisa fazer o seguinte:

1. Criar um serviço vinculado para registrar [seu próprio cluster de cálculo HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de cálculo HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado de "HDInsightLinkedService".
2. Criar um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o Armazenamento de Blob do Azure que está hospedando os dados. Vamos chamar esse serviço vinculado de "StorageLinkedService".
3. Criar [conjuntos de dados](data-factory-create-datasets.md) apontando para os dados de entrada e de saída. Vamos chamar o conjunto de dados de entrada de "HiveSampleIn" e o conjunto de dados de saída de "HiveSampleOut"
4. Copie a consulta de Hive como um arquivo configurado pelo Armazenamento de Blob do Azure configurado na etapa 2 acima. Se o serviço vinculado para hospedagem dos dados for diferente daquele que hospeda o arquivo de consulta, crie um serviço vinculado do Armazenamento do Azure separado e consulte-o na configuração da atividade. Use **scriptPath** para especificar o caminho até o arquivo de consulta de Hive e **scriptLinkedService** para especificar o armazenamento do Azure que contém o arquivo de script.

	> [AZURE.NOTE] Você também pode fornecer o script de Hive embutido na definição da atividade usando a propriedade **script**, mas isso não é recomendado, pois todos os caracteres especiais no script dentro do documento JSON precisam ser escapados e podem causar problemas de depuração. A prática recomendada é seguir a etapa 4.
5.	Criar o pipeline abaixo com a atividade HDInsightHive para processar os dados.

		{
		  "name": "HiveActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "HiveActivitySample",
		        "type": "HDInsightHive",
		        "inputs": [
		          {
		            "name": "HiveSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "HiveSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Hour",
          			"interval": 1
        		}
		      }
		    ]
		  }
		}

6.	Implante o pipeline. Consulte o artigo [Criando pipelines](data-factory-create-pipelines.md) para obter detalhes.
7.	Monitore o pipeline usando as exibições de gerenciamento e monitoramento de data factory. Consulte o artigo [Monitorando e gerenciando pipelines da Data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes. 


## Especificando parâmetros para um script de Hive usando o elemento defines 

Considere o exemplo no qual os logs de jogos são incluídos diariamente no Armazenamento de Blob do Azure e armazenados em uma pasta particionada com data e hora. Você deseja parametrizar o script de Hive e passar o local da pasta de entrada dinamicamente durante a execução, além de produzir a saída particionada com data e hora.

Para usar os scripts de Hive parametrizados, faça o seguinte

- Defina os parâmetros em **defines**.

		{
			"name": "HiveActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		     	{
		        	"name": "HiveActivitySample",
		        	"type": "HDInsightHive",
			        "inputs": [
			          	{
				            "name": "HiveSampleIn"
				          }
		        	],
		        	"outputs": [
		          		{
				            "name": "HiveSampleOut"
				        }
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
		            		"Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		},
       					"scheduler": {
          					"frequency": "Hour",
          					"interval": 1
        				}
		        	}
		      	}
		    ]
		  }
		}

- No script de Hive, consulte o parâmetro usando **${hiveconf:parameterName}**.

		DROP TABLE IF EXISTS HiveSampleIn; 
		CREATE EXTERNAL TABLE HiveSampleIn 
		(
			ProfileID 	string, 
		    SessionStart 	string, 
		    Duration 	int, 
		    SrcIPAddress 	string, 
		    GameType 	string
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
		
		DROP TABLE IF EXISTS HiveSampleOut; 
		CREATE EXTERNAL TABLE HiveSampleOut 
		(
		    ProfileID 	string, 
		    Duration 	int
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
		
		INSERT OVERWRITE TABLE HiveSampleOut
		Select 
			ProfileID,
			SUM(Duration)
		FROM HiveSampleIn Group by ProfileID


## Consulte também
- [Atividade Pig](data-factory-pig-activity.md)
- [Atividade MapReduce](data-factory-map-reduce.md)
- [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas Spark](data-factory-spark.md)
- [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0420_2016-->