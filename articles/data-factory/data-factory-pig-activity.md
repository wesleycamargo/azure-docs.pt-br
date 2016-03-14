<properties 
	pageTitle="Atividade Pig" 
	description="Saiba como usar a Atividade Pig em uma data factory do Azure para executar scripts Pig em um cluster sob demanda/próprio do HDInsight." 
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

# Atividade Pig

A atividade de Pig do HDInsight em um [pipeline](data-factory-create-pipelines.md) de Data Factory executa consultas de Pig em [seu próprio cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou no cluster [sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) do HDInsight baseado em Windows/Linux. Este artigo se baseia no artigo [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral de transformação de dados e as atividades de transformação para as quais há suporte.

## Sintaxe

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
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
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## Detalhes da sintaxe

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
name | Nome da atividade | Sim
description | Texto que descreve qual a utilidade da atividade | Não
type | HDinsightPig | Sim
inputs | Entradas consumidas pela atividade Pig | Não
outputs | Saídas produzidas pela atividade Pig | Sim
linkedServiceName | Referência ao cluster HDInsight registrado como um serviço vinculado na Data Factory | Sim
script | Especificar o script de Pig embutido | Não
caminho do script | Armazenar o script de Pig em um armazenamento de blob do Azure e fornecer o caminho para o arquivo. Use a propriedade 'script' ou 'scriptPath'. As duas não podem ser usadas juntas. Observe que o nome do arquivo diferencia maiúsculas de minúsculas. | Não
define | Especificar parâmetros como pares chave/valor para referenciar dentro do script de Pig | Não

## Exemplo

Vamos considerar um exemplo de análises de logs de jogos nos quais você deseja identificar o tempo gasto pelos usuários em jogos lançados por sua empresa.
 
Abaixo está um exemplo de log de jogos, que é separado por vírgulas (,) e contém os seguintes campos – ProfileID, SessionStart, Duration, SrcIPAddress e GameType.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

O **Script de Pig** para processamento desses dados tem esta aparência:

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Para executar esse script de Pig em um pipeline de Data Factory, você precisa fazer o seguinte:

1. Criar um serviço vinculado para registrar [seu próprio cluster de computação HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar o [cluster de cálculo HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar esse serviço vinculado de "HDInsightLinkedService".
2.	Criar um [serviço vinculado](data-factory-azure-blob-connector.md) para configurar a conexão com o armazenamento de blob do Azure que está hospedando os dados. Vamos chamar esse serviço vinculado de "StorageLinkedService".
3.	Criar [conjuntos de dados](data-factory-create-datasets.md) apontando para os dados de entrada e de saída. Vamos chamar o conjunto de dados de entrada de "PigSampleIn" e o conjunto de dados de saída de "PigSampleOut".
4.	Copie a consulta de Pig em um arquivo configurado pelo Armazenamento de Blob do Azure configurado na etapa 2 acima. Se o serviço vinculado para hospedagem dos dados for diferente daquele que hospeda o arquivo de consulta, crie um serviço vinculado separado do Armazenamento do Azure e consulte-o na configuração da atividade. Use **scriptPath** para especificar o caminho até o arquivo de script de pig e **scriptLinkedService** para especificar o Armazenamento do Azure que contém o arquivo de script.
	
	> [AZURE.NOTE] Você também pode fornecer o script de Pig embutido na definição da atividade usando a propriedade **script**, mas isso não é recomendado, pois todos os caracteres especiais no script dentro do documento JSON precisam ser escapados e talvez causem problemas de depuração. A prática recomendada é seguir a etapa 4.
5. Crie o pipeline abaixo com a atividade HDInsightPig para processar os dados.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. Implante o pipeline. Consulte o artigo [Criando pipelines](data-factory-create-pipelines.md) para obter detalhes. 
7. Monitore o pipeline usando as exibições de gerenciamento e monitoramento de data factory. Consulte o artigo [Monitoramento e gerenciando pipelines do Data Factory](data-factory-monitor-manage-pipelines.md) para obter detalhes.

## Especificando parâmetros para um script Pig usando o elemento defines

Considere o exemplo no qual os logs de jogos são incluídos diariamente no Armazenamento de Blob do Azure e armazenados em uma pasta particionada com data e hora. Convém parametrizar o script Pig e passar o local da pasta entrada dinamicamente durante a execução, além de produzir a saída particionada com data e hora.
 
Para usar os scripts Pig, faça o seguinte:

- Defina os parâmetros em **defines**.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- No Script Pig, consulte os parâmetros usando '**$parameterName**', como mostra o exemplo a seguir:

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 

<!---HONumber=AcomDC_0302_2016-->