<properties 
	pageTitle="Execute o script U-SQL na Análise Azure Data Lake do Azure Data Factory" 
	description="Saiba como processar dados executando scripts U-SQL no serviço de computação da Análise Azure Data Lake." 
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
	ms.date="10/27/2015" 
	ms.author="spelluru"/>

# Execute o script U-SQL na Análise Azure Data Lake do Azure Data Factory 
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve a **Atividade do U-SQL da Análise Data Lake** que executa um script **U-SQL** em um serviço vinculado de computação da **Análise Azure Data Lake**.

> [AZURE.NOTE]Você deve criar uma conta da Análise Azure Data Lake antes de criar um pipeline com uma atividade do U-SQL da Análise Data Lake. Para saber mais sobre a Análise Azure Data Lake, veja [Introdução à Análise Azure Data Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Consulte o tutorial [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para obter etapas detalhadas para criar uma fábrica de dados, serviços vinculados, conjuntos de dados e um pipeline. Use os trechos de código JSON com o Editor do Data Factory, Visual Studio ou Azure PowerShell para criar as entidades do Data Factory.

## Serviço Vinculado da Análise Azure Data Lake
Você cria um serviço vinculado da **Análise Azure Data Lake** para vincular um serviço de computação da Análise Azure Data Lake a um Azure Data Factory antes de usar a atividade do U-SQL da Análise Data Lake em um pipeline.

O exemplo a seguir fornece uma definição de JSON para um serviço vinculado da Análise Azure Data Lake.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


A tabela a seguir fornece as descrições das propriedades usadas na definição de JSON.

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
Tipo | A propriedade de tipo deve ser definida como: **AzureDataLakeAnalytics**. | Sim
accountName | Nome da conta da Análise Azure Data Lake. | Sim
dataLakeAnalyticsUri | URI da Análise Azure Data Lake. | Não 
autorização | O código de autorização é recuperado automaticamente depois de clicar no botão **Autorizar** no Editor do Data Factory e concluir o logon OAuth. | Sim 
subscriptionId | ID de assinatura do Azure | Não (se não for especificado, a assinatura do Data Factory é usada). 
resourceGroupName | Nome do grupo de recursos do Azure | Não (se não for especificado, o grupo de recursos do Data Factory é usado).
sessionId | ID da sessão de autorização OAuth. Cada ID da sessão é exclusiva e pode ser usado somente uma vez. Isso é gerado automaticamente no Editor Data Factory. | Sim

   
 
## Atividade do U-SQL da Análise Data Lake 

O seguinte trecho de código JSON define um pipeline com uma Atividade do U-SQL da Análise Data Lake. A definição de atividade tem uma referência para o serviço vinculado da Análise Azure Data Lake criado anteriormente.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


A tabela a seguir descreve os nomes e as descrições de propriedades que são específicas a esta atividade.

Propriedade | Descrição | Obrigatório
:-------- | :----------- | :--------
type | A propriedade type deve ser definida como **DataLakeAnalyticsU-SQL**. | Sim
scriptPath | Caminho para a pasta que contém o script U-SQL. | Não (se você usar o script)
scriptLinkedService | Serviço vinculado que vincula o armazenamento que contém o script para a fábrica de dados | Não (se você usar o script)
script | Especificar script embutido em vez de especificar scriptPath e scriptLinkedService. Por exemplo: “script”: “CREATE DATABASE test”. | Não (se você usar scriptPath e scriptLinkedService)
degreeOfParallelism | O número máximo de nós que serão usados simultaneamente para executar o trabalho. | Não
prioridade | Determina quais trabalhos de todos os que estão na fila devem ser selecionados para serem executados primeiro. Quanto menor o número, maior a prioridade. | Não 
parameters | Parâmetros do script U-SQL | Não 

Consulte [Definição do Script SearchLogProcessing.txt](#script-definition) para a definição do script.

### Conjuntos de dados de entrada e saída de exemplo

#### Conjunto de dados de entrada
Neste exemplo, os dados de entrada residem em um Repositório Azure Data Lake (arquivo SearchLog.tsv na pasta datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Conjunto de dados de saída
Neste exemplo, os dados de saída produzidos pelo script U-SQL são armazenados em um Repositório Azure Data Lake (pasta datalake/output).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Exemplo de serviço vinculado do Repositório Azure Data Lake
Veja a definição de exemplo de serviço vinculado do Repositório Azure Data Lake usado pelos conjuntos de dados de entrada/saída acima.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Veja [Mover dados para e do Repositório Azure Data Lake](data-factory-azure-datalake-connector.md) para obter descrições das propriedades JSON no serviço vinculado do Repositório Azure Data Lake acima e trechos de código JSON de conjunto de dados.

### Definição do Script

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Os valores para os parâmetros **@in** e **@out** no script U-SQL acima são passados dinamicamente por ADF usando a seção ‘parâmetros’. Veja a seção "parâmetros" acima na definição do pipeline.

Você pode especificar outras propriedades viz. degreeOfParallelism, prioridade, etc., bem como em sua definição de pipeline para os trabalhos executados no serviço de Análise Azure Data Lake.

<!---HONumber=AcomDC_0114_2016-->