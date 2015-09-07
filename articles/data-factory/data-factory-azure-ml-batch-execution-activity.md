<properties 
	pageTitle="Criar pipelines de previsão usando a atividade de Execução em Lote de Aprendizado de Máquina do Azure | Microsoft Azure"
	description="Descreve como criar pipelines de previsão usando a Azure Data Factory e o Aprendizado de Máquina do Azure"
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
	ms.date="08/24/2015"
	ms.author="spelluru"/>

# Criar pipelines de previsão usando a atividade de Lote de Aprendizado de Máquina do Azure   
## Visão geral

> [AZURE.NOTE]Consulte os artigos [Introdução ao Azure Data Factory](data-factory-introduction.md) e [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) para começar a introdução ao serviço do Azure Data Factory.

O Azure Data Factory permite que você crie facilmente pipelines que utilizam o serviço Web do [Aprendizado de máquina do Azure][azure-machine-learning] publicado para análise preditiva. Usando o Azure Data Factory, é possível usar pipelines de big data (por exemplo, Pig e Hive) para processar os dados incluídos de várias fontes de dados, e usar os serviços Web de Aprendizado de Máquina do Azure para fazer previsões sobre dados em lote.

Você usa o Azure Data Factory para orquestrar o processamento e movimentação de dados e realizar a execução de lote usando o Aprendizado de Máquina do Azure. Para conseguir isso, você precisará fazer o seguinte:

1. Criar um serviço vinculado de Aprendizado de Máquina do Azure. Você precisará o seguinte:
	1. **URI de solicitação** para a API de execução do lote. Você pode encontrar o URI de solicitação clicando no link **EXECUÇÃO EM LOTES** na página de serviços Web (mostrada abaixo).
	1. **Chave API** para o serviço Web publicado do Aprendizado de Máquina do Azure. Você pode encontrar a chave API clicando no serviço Web publicado. 
 2. Use a atividade **AzureMLBatchExecution**.

	![Painel de Aprendizado de Máquina](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![URI do lote](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Cenário: Experimentos usando entradas/saídas de serviço Web que se referem aos dados no armazenamento de Blob do Azure
Nesse cenário, o serviço Web de aprendizado de máquina do Azure faz previsões usando dados de um arquivo em um armazenamento de blob do Azure e armazena os resultados de previsão no armazenamento de blob. O JSON a seguir define um pipeline do Azure Data Factory com uma atividade AzureMLBatchExecution. A atividade possui o conjunto de dados **DecisionTreeInputBlob** como entrada e **DecisionTreeResultBlob** como a saída. O **DecisionTreeInputBlob** é passado como entrada para o serviço da Web usando a propriedade JSON **webServiceInput** e **DecisionTreeResultBlob** como uma saída para o serviço Web usando a propriedade JSON **webServiceOutputs**. Apenas os conjuntos de dados que são entradas/saídas para a atividade podem ser passados como saídas e entradas de serviço Web.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE]Apenas as entradas e saídas da atividade AzureMLBatchExecution podem ser passadas como parâmetros para o serviço Web. Por exemplo, no trecho JSON acima, DecisionTreeInputBlob é uma entrada para a atividade de AzureMLBatchExecution, que é passada como entrada para o serviço Web a través do parâmetro webServiceInput.

### Exemplo

Este exemplo usa o armazenamento do Azure para conter tanto os dados de entrada quanto os de saída.

É recomendável que você estude o tutorial [Compile seu primeiro pipeline com a Data Factory][adf-build-1st-pipeline] antes de passar por esse exemplo e que use o Editor da Data Factory para criar os artefatos de Data Factory (serviços vinculados, conjuntos de dados, pipeline) neste exemplo.
 

1. Crie um **serviço vinculado** para o **Armazenamento do Azure**. Se os arquivos de saída e entrada estiverem em diferentes contas de armazenamento, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Criar o **entrada** de **conjunto de dados** do Azure Data Factory. Observe que ao contrário de alguns outros conjuntos de dados de Data Factory, esses devem conter os valores **folderPath** e **fileName**. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Provavelmente, você precisará incluir algumas atividades upstream para transformar a entrada para o formato de arquivo CSV e colocá-lo na conta de armazenamento de cada fatia. Nesse caso, não inclua as configurações de **external** e **externalData** mostradas no exemplo a seguir e seu DecisionTreeInputBlob será o conjunto de dados de saída de uma atividade diferente.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	O arquivo csv de entrada deve ter a linha de cabeçalho de coluna. Se você estiver usando a **Atividade de cópia** para criar/mover o csv para dentro do armazenamento de blob, você deve definir a propriedade de coleta **blobWriterAddHeader** como **true**. Por exemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **Erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho '', linha 1, posição 1**.
3. Criar a **saída** de **conjunto de dados** do Azure Data Factory. Este exemplo usa o particionamento para criar um caminho de saída exclusivo para cada execução de divisão. Sem isso, a atividade substituiria o arquivo.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Crie um **serviço vinculado** do tipo **AzureMLLinkedService**, fornecendo a chave API e a URL de execução de lote do modelo.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Por fim, crie um pipeline que contenha uma atividade **AzureMLBatchScoring**. Ela obterá o local do arquivo de entrada dos conjuntos de dados de entrada, chamará a API de execução em lote de Aprendizado de Máquina do Azure e copiará a saída da execução em lote para o blob especificado no seu conjunto de dados de saída. 

	> [AZURE.NOTE]A atividade AzureMLBatchExecution pode ter zero ou mais entradas e uma ou mais saídas.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **término** é opcional. Se você não especificar o valor para a propriedade **término**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.

	> [AZURE.NOTE]A especificação de entrada para a atividade AzureMLBatchExecution é opcional.

## Cenário: Experimentos usando módulos Leitor/Gravador para fazer referência a dados em vários armazenamentos

Outro cenário comum durante a criação de experimentos AM do Azure é usar módulos Leitor e Gravador. O módulo leitor é usado para carregar dados em um experimento e o módulo gravador é usado para salvar os dados dos experimentos. Para obter detalhes sobre os módulos leitor/gravador, consulte os tópicos [Leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN.

Ao usar os módulos leitor e gravador, é recomendável usar um parâmetro de serviço Web para cada propriedade desses módulos leitor/gravador. Esses parâmetros da Web permitem que você configure os valores durante o tempo de execução. Por exemplo, você poderia criar um experimento com um módulo leitor que usa um banco de dados SQL do Azure: XXX.database.windows.net. Depois que o serviço web tiver sido implantado, você precisa habilitar os consumidores do serviço Web para especificar outro servidor SQL do Azure chamado YYY.database.windows.net. Você pode usar um parâmetro de serviço Web para permitir que esse valor seja configurado.

> [AZURE.NOTE]A saída e entrada de serviço Web são diferentes dos parâmetros de serviço Web. No primeiro cenário, você viu como uma entrada e saída podem ser especificados para um serviço Web do AM do Azure. Nesse cenário, você passará parâmetros para um serviço Web que correspondem às propriedades dos módulos leitor/gravador.

Vejamos um cenário para o uso de parâmetros de serviço Web. Você tem um serviço Web implantado do Aprendizado de Máquina do Azure que está usando um módulo leitor para ler dados de uma das fontes de dados de Aprendizado de Máquina do Azure com suporte (por exemplo: banco de dados SQL do Azure). Após a execução do lote, os resultados são gravados usando um módulo Gravador (banco de dados SQL do Azure). Não há entradas e saídas de serviço Web definidas nos experimentos. Nesse caso, é recomendável que você configure os parâmetros de serviço Web relevantes para os módulos leitor e gravador. Isso permitirá que os módulos leitor/gravador sejam configurados ao usar a atividade AzureMLBatchExecution. Você especifica parâmetros de serviço Web na seção **globalParameters** na atividade de JSON da seguinte maneira.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

Você também pode usar [Funções do Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.

### Uso de um módulo Leitor para ler dados de vários arquivos no Blob do Azure
Os pipelines de big data (Pig, Hive, etc...) podem gerar um ou mais arquivos de saída sem extensões. Por exemplo, quando você especifica uma tabela externa do Hive, os dados para a tabela externa do Hive podem ser armazenados no armazenamento de blob do Azure com o seguinte nome 000000\_0. É possível usar o módulo leitor em um experimento para ler vários arquivos e usá-los para previsões.

Ao usar o módulo de leitor em uma experiência de Aprendizado de Máquina do Azure, é possível especificar o Blob do Azure como uma entrada. Os arquivos no armazenamento de blob do Azure podem ser os arquivos de saída (por exemplo, 000000\_0) que são produzidos por um script de Pig e Hive em execução no HDInsight. O módulo leitor permite que você leia arquivos (sem extensões), configurando a propriedade do **caminho para o diretório, blob ou contêiner** do módulo Leitor para apontar para a contêiner/pasta que contém os arquivos conforme mostrado abaixo. Observação: o asterisco (isto é, *) **especifica que todos os arquivos na pasta/contêiner (ou seja, data/aggregateddata/year=2014/month-6/*)** serão lidos como parte da experiência.

![Propriedades de Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Exemplo 
#### Pipeline com a atividade AzureMLBatchExecution com parâmetros de serviço Web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
No exemplo JSON acima:

- O serviço Web implantado de Aprendizado de Máquina do Azure usa um modulo leitor e gravador para ler/gravar dados de/para um banco de dados SQL do Azure. Este serviço Web expõe os seguintes quatro parâmetros: Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor e Senha de conta de usuário do servidor.  
- Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **término** é opcional. Se você não especificar o valor para a propriedade **término**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.
 

## Perguntas frequentes

**P:** Estou usando a atividade AzureMLBatchScoring. Devo mudar para a atividade AzureMLBatchExecution?

**R:** Sim. Se você estiver usando a atividade AzureMLBatchScoring para integrar-se ao Aprendizado de Máquina do Azure, recomendamos que você use a atividade AzureMLBatchExecution mais recente. Estamos substituindo a atividade AzureMLBatchScoring e ela será removida em uma versão futura.

A atividade AzureMLBatchExecution foi introduzida na versão de agosto de 2015 do Azure SDK e Azure PowerShell.

A atividade AzureMLBatchExecution não requer uma entrada (se não se requerem dependências de entrada). Ela também permite que você seja explícito sobre se você deseja usar a entrada de serviço Web e saída de serviço Web ou não. Se você optar por usar a entrada/saída de serviço Web, ele permite que você especifique os conjuntos de dados de Blob do Azure relevantes a serem usados. Além disso, permite que você especifique claramente os valores para os parâmetros de serviço Web que são fornecidos pelo serviço Web.

Se você deseja continuar usando a atividade AzureMLBatchScoring, consulte o artigo [Atividade de pontuação em lotes de MA do Azure](data-factory-create-predictive-pipelines.md) para obter detalhes.


**P:** Tenho vários arquivos que são gerados pelos meus pipelines de big data. Posso usar a atividade AzureMLBatchExecution para trabalhar em todos os arquivos?

**R:** Sim. Consulte a seção **Uso de um módulo Leitor para ler dados de vários arquivos no Blob do Azure** para obter detalhes.








[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=August15_HO9-->