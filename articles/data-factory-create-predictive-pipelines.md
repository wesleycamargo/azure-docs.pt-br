<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data Factory - Cria pipelines de previsão usando o Data Factory e o Aprendizado de Máquina | Azure" description="Descreve como criar pipelines de previsão usando o Azure Data Factory e o aprendizado de máquina do Azure" metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Criar pipelines de previsão usando o Data Factory e o Aprendizado de Máquina do Azure 
Você pode colocar em operação modelos publicados de [Aprendizado de Máquina do Azure][azure-machine-learning] em pipelines do Data Factory do Azure. Essas pipelines são chamados de pipelines de previsão. Para criar uma pipeline de previsão, você precisará de:

-	Chave de API do modelo do espaço de trabalho publicado e o URL de pontuação em lote (consulte a imagem abaixo)
-	Um armazenamento de blob do Azure mantém o arquivo CSV de entrada para ser contado.
-	O arquivo CSV de resultados de contagem manterá o armazenamento de blob do Azure.

	![Machine Learning Dashboard][machine-learning-dashboard]

	A URL de pontuação em lote para o AzureMLLinkedService é obtida como indicado na imagem acima, menos '**ajuda**':  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Uma **pipeline de previsão** possui as seguintes partes:

-	Tabelas de entrada e saídas
-	Armazenamento do Azure e serviços vinculados ao Aprendizagem de Máquinna do Azure
-	Uma pipeline com Atividades de Contagem em Lote do Aprendizado de Máquina do Azure

## Exemplo



1. Crie um serviço vinculado para o Armazenamento do Azure. Se os arquivos de saída e entrada de pontuação estiverem em diferentes contas de armazenamento, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Criar a entrada e saída de tabelas do Data Factory do Azure. Observe que ao contrário de algumas outras tabelas de Data Factory, essas devem conter os valores **folderPath** e **fileName**. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Provavelmente, você precisará incluir algumas atividades upstream para transformar a entrada para o formato de arquivo CSV e colocá-lo na conta de armazenamento de cada fatia. Nesse caso, não inclua as configurações de "waitOnExternal" mostradas no exemplo a seguir e seu ScoringInputBlob será a tabela de saída de uma atividade diferente.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. Este exemplo de saída usa o particionamento para criar um caminho de saída exclusivo para cada execução de fatia. Sem isso, a atividade substituiria o arquivo.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Crie um serviço vinculado do tipo: **AzureMLLinkedService**, fornecendo a chave API e o modelo de URL de pontuação em lote.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Por fim, crie uma pipeline que contenha **AzureMLBatchScoringActivity**. Ela obterá o local do arquivo de entrada de tabelas de entrada, chamará a API de pontuação em lote e copiará o saída da pontuação em lote para o blob especificado na sua tabela de saída. Ao contrário de algumas outras atividades de Data Factory, oAzureMLBatchScoringActivity pode ter apenas uma entrada e uma tabela de saída.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## Consulte também

Artigo | Descrição
------ | ---------------
[Introdução ao Data Factory do Azure][adf-introduction] | Este artigo fornece uma visão geral do serviço de Data Factory do Azure e os cenários em que ele oferece suporte.
[Introdução ao Data Factory do Azure][adf-getstarted] | Este artigo fornece um tutorial básico que fornece instruções passo a passo para criar e monitorar um Data Factory de exemplo.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local em um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída.
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando a Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em uma pipeline.
[Solucionar problemas de Data Factory][troubleshoot]| Este artigo descreve como solucionar problemas do Data Factory do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência do Desenvolvedor da Data Factory do Azure][developer-reference]A Referência do Desenvolvedor tem um conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
