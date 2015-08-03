<properties 
	pageTitle="Data Factory - Cria pipelines de previsão usando o Data Factory e o Aprendizado de Máquina | Azure" 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Criar pipelines de previsão usando o Data Factory e o Aprendizado de Máquina do Azure 
## Visão geral
Você pode colocar em operação modelos publicados de [Aprendizado de Máquina do Azure][azure-machine-learning] em pipelines da Azure Data Factory. Essas pipelines são chamadas de pipelines de previsão. Para criar uma pipeline de previsão, você precisará de:

-	Chave de API do modelo do espaço de trabalho publicado e o URL de pontuação em lote (consulte a imagem abaixo)
-	Um blob do Azure mantém o arquivo CSV de entrada (ou) um banco de dados SQL do Azure que contém os dados de entrada a contabilizar. 
-	Um armazenamento de blob do Azure que conterá o arquivo CSV com resultados de contagem (ou) um banco de dados SQL do Azure que conterá os dados de saída. 

	![Painel de Aprendizado de Máquina][machine-learning-dashboard]

	A URL de contagem em lote para o AzureMLLinkedService é obtida como indicado na imagem acima, menos ‘**ajuda**’: https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Um **pipeline de previsão** tem as seguintes partes:

-	Tabelas de entrada e saídas
-	Serviços vinculados de Aprendizagem de Máquina e Armazenamento do Azure/SQL do Azure
-	Uma pipeline com Atividades de Contagem em Lote do Aprendizado de Máquina do Azure

> [AZURE.NOTE]Você pode usar parâmetros de serviço Web que são expostos por um serviço Web de aprendizado de máquina do Azure publicado em pipelines de ADF (Azure Data Factory). Para obter mais informações, consulte a seção Parâmetros de serviço Web neste artigo.

## Exemplo
Este exemplo usa o armazenamento do Azure para conter tanto os dados de entrada quanto os de saída. Você também pode usar o banco de dados SQL do Azure em vez de usar o armazenamento do Azure.

É recomendável que você estude o tutorial [Introdução à Azure Data Factory][adf-getstarted] antes de passar por esse exemplo e que use o Editor da Data Factory para criar os artefatos de Data Factory (serviços vinculados, tabelas, pipeline) neste exemplo.
 

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
	
	O arquivo csv de contagem de lote deve ter a linha de cabeçalho de coluna. Se você estiver usando a **Atividade de cópia** para criar/mover o csv para dentro do armazenamento de blob, você deve definir a propriedade de coleta **blobWriterAddHeader** como **true**. Por exemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **Erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho '', linha 1, posição 1**.
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


4. Crie um serviço vinculado do tipo **AzureMLLinkedService**, fornecendo a chave API e a URL pontuação de lote do modelo.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Por fim, crie um pipeline que contenha um **AzureMLBatchScoringActivity**. Ela obterá o local do arquivo de entrada de tabelas de entrada, chamará a API de pontuação em lote e copiará o saída da pontuação em lote para o blob especificado na sua tabela de saída. Ao contrário de algumas outras atividades de Data Factory, oAzureMLBatchScoringActivity pode ter apenas uma entrada e uma tabela de saída.

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
		        ],

				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z"
		    }
		}

	Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.

## Parâmetros de serviço Web
Você pode usar parâmetros de serviço Web que são expostos por um serviço Web de aprendizado de máquina do Azure publicado em pipelines de ADF (Azure Data Factory). Você pode criar uma experiência de aprendizado de máquina do Azure e publicá-la como um serviço Web, então usar esse serviço Web em várias atividades ou pipelines ADF, passando entradas diferentes via parâmetros de serviço Web.

### Passar valores para parâmetros de serviço Web
Adicione uma seção **transformação** à seção **AzureMLBatchScoringActivty** no pipeline do JSON para especificar valores para parâmetros de serviço Web nessa seção, conforme mostrado no exemplo a seguir:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Você também pode usar [Funções do Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.

### Gravadores e leitores do SQL do Azure
Um cenário comum de uso de parâmetros de serviço Web é o uso de leitores e gravadores do SQL do Azure. O módulo do leitor é usado para carregar dados em uma experiência de serviços de gerenciamento de dados fora do Estúdio de aprendizado de máquina do Azure e o módulo de gravador é usado para salvar os dados de suas experiências nos serviços de gerenciamento de dados fora do Estúdio de aprendizado de máquina do Azure. Para obter detalhes sobre o leitor/gravador do SQL Azure/Blob do Azure, consulte os tópicos [Leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN. O exemplo na seção anterior usou o leitor de blobs do Azure e o gravador de blobs do Azure. Esta seção discute usando o leitor do SQL do Azure e o gravador do SQL do Azure.

#### Leitor do SQL do Azure
No Estúdio AM do Azure, você pode criar uma experiência e publicar um serviço Web com um leitor do SQL do Azure para a entrada. O leitor do SQL Azure tem propriedades de conexão que podem ser expostas como parâmetros de serviços Web, permitindo que os valores para as propriedades de conexão sejam passados em tempo de execução na solicitação de contagem de lote.

Em tempo de execução, os detalhes da tabela de Data Factory de entrada serão usados pelo serviço Data Factory para preencher os parâmetros de serviço Web. Observe que você deve usar nomes padrão (Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor, Senha de conta de usuário do servidor) para os parâmetros de serviço Web para que essa integração com o serviço Data Factory funcione.

Se você tiver quaisquer parâmetros de serviço Web adicionais, use a seção **webServiceParameters** da atividade JSON. Se você especificar valores para parâmetros de leitor do SQL do Azure nesta seção, os valores substituirão os valores pegos do serviço vinculado de entrada do SQL do Azure. Não é recomendável especificar valores para o leitor do SQL do Azure diretamente na seção webServiceParameters. Use a seção para passar valores para quaisquer parâmetros adicionais.

Para usar um leitor do SQL do Azure por meio de um pipeline da Azure Data Factory, faça o seguinte:

- Criar um **serviço vinculado do SQL do Azure**. 
- Criar uma **tabela** de Data Factory que use **AzureSqlTableLocation**.
- Defina essa **tabela** da Data Factory como o **entrada** para o **AzureMLBatchScoringActivity** no pipeline JSON. 



#### Gravador do SQL do Azure
Assim como com o leitor do SQL Azure, um gravador do SQL Azure também pode ter suas propriedades expostas como parâmetros de serviço Web. Um gravador do SQL do Azure usa as configurações do serviço vinculado associado à tabela de entrada ou tabela de saída. A tabela a seguir descreve quando o serviço vinculado de entrada é utilizado versus o serviço vinculado de saída.

| Entrada/Saída | A entrada é SQL do Azure | A entrada é Blob do Azure |
| ------------ | ------------------ | ------------------- |
| A saída é SQL do Azure | <p>O serviço Data Factory usa as informações da cadeia de conexão do serviço INPUT vinculado para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure.</p><p>Se o leitor do SQL Azure e o gravador do SQL Azure em seu modelo Azure ML compartilham os mesmos parâmetros de serviço Web mencionados acima, está tudo bem. Se eles não compartilham os mesmos parâmetros de serviço Web, por exemplo, se o gravador do SQL Azure usa nomes de parâmetros: Database server name1, Database name1, Server user account name1, Server user account password1 (com "1" no final), você deve passar valores para esses parâmetros de serviço Web OUTPUT na seção webServiceParameters da atividade JSON.</p><p>Você pode passar valores para quaisquer outros parâmetros de serviço da Web usando a seção webServiceParameters da atividade JSON.</p> | <p>O serviço Data Factory usa as informações da cadeia de conexão do serviço vinculado OUTPUT para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure.</p><p>Você pode passar valores para quaisquer outros parâmetros de serviço Web usando a seção webServiceParameters da atividade JSON. <p>O blob de entrada será usado como o local de entrada.</p> |
|A saída é o Blob do Azure | O serviço Data Factory usa as informações da cadeia de conexão do serviço de entrada vinculado para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure. | <p>Você deve passar valores para qualquer parâmetro de serviço Web usando a seção WebServiceParameters da atividade JSON.</p><p>Os blobs serão usados como locais de entrada e saída.</p> |
    

> [AZURE.NOTE]O Gravador do SQL Azure poderá encontrar violações de chave, se estiver substituindo uma coluna de identidade. Certifique-se de estruturar sua tabela de saída para evitar essa situação.
> 
> Você pode usar tabelas de preparo com uma atividade de procedimento armazenado para mesclar linhas ou truncar os dados antes da contagem. Se você usar essa abordagem, defina a configuração de simultaneidade da executionPolicy como 1.

### Exemplo de como usar parâmetros de serviço Web
#### Pipeline com AzureMLBatchScoringActivity com parâmetros de serviço Web

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	],

			"start": "2015-02-13T00:00:00Z",
        	"end": "2015-02-14T00:00:00Z"
		}
	}
 
No exemplo JSON acima:

- O modelo de AM do Azure usa o Leitor do SQL do Azure e Gravador do SQL do Azure
- Quando expostos por meio do serviço Web, os nomes padrão são usados para os parâmetros
	- Para o **leitor**: Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor e Senha de conta de usuário do servidor.
	- Para o **gravador**: Nome do servidor de banco de dados1, Nome do banco de dados1, Nome de conta de usuário do servidor1 e Senha de conta de usuário do servidor1.
	
		Observe que o leitor e gravador não compartilham parâmetros nesse caso.  
- O serviço Data Factory gera automaticamente valores os parâmetros de serviço Web com os nomes **Nome do servidor de banco de dados**, **Nome do banco de dados**, **Nome de conta de usuário do servidor** e **Senha de conta de usuário do servidor**, que correspondem aos nomes do leitor de entrada. Portanto, não é necessário passar explicitamente os valores para esses parâmetros via **webServiceParameters** na atividade de JSON abaixo.  
- Os parâmetros para o gravador (aqueles com sufixo “1”) não são preenchidos automaticamente pelo serviço Data Factory. Portanto, você precisa especificar valores para esses parâmetros na seção **webServiceParameters** da atividade JSON.  
- **ID do cliente**, **rótulos contabilizados** e **probabilidades contabilizadas** são salvos como colunas separadas por vírgulas. 
- O **Nome de tabela de dados** neste exemplo corresponde a uma tabela no banco de dados de saída.
- Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.




## Consulte também

Artigo | Descrição
------ | ---------------
[Referência do desenvolvedor da Azure Data Factory][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, scripts JSON, biblioteca de classes .NET, funções, etc… 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=July15_HO4-->