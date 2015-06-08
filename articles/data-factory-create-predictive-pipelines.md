<properties 
	pageTitle="Data Factory - Cria pipelines de previsão usando o Data Factory e o Aprendizado de Máquina | Azure" 
	description="Descreve como criar criar pipelines de previsão usando Azuer dados fábrica e aprendizado de máquina do Azure" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Criar pipelines de previsão usando o Data Factory e o Aprendizado de Máquina do Azure 
## Visão geral
Você pode colocar em operação publicado [aprendizado de máquina do Azure][azure-machine-learning] modelos em pipelines de fábrica de dados do Azure. Esses canais são chamados de pipelines de previsão. Para criar uma pipeline de previsão, você precisará de:

-	Chave de API do modelo do espaço de trabalho publicado e o URL de pontuação em lote (consulte a imagem abaixo)
-	Um blob do Azure mantém o arquivo CSV de entrada (ou) um banco de dados do SQL Azure que contém a entrada de dados de armazenamento para totalizados. 
-	Um armazenamento de BLOBs do Azure que conterá o arquivo CSV resultados pontuação (ou) um banco de dados do SQL Azure que armazenará os dados de saída. 

	![Painel de aprendizado de máquina][machine-learning-dashboard]

	O lote a pontuação de URL para o AzureMLLinkedService é obtida como indicado na imagem acima, menos ' * * Ajuda * * ': https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Um **pipeline previsão** possui as seguintes partes:

-	Tabelas de entrada e saídas
-	Azure SQL Azure/armazenamento e Azure ML vinculados serviços
-	Uma pipeline com Atividades de Contagem em Lote do Aprendizado de Máquina do Azure

> [AZURE.NOTE]Você pode usar parâmetros de serviço da Web que são expostos por um serviço da Web de aprendizado de máquina do Azure publicado em pipelines de fábrica de dados do Azure (AAD). Para obter mais informações, consulte a seção de parâmetros de serviço da Web neste artigo.

## Exemplo
Este exemplo usa o armazenamento do Azure para manter os dados de entrada e saídos. Você também pode usar o banco de dados do SQL Azure em vez de usar o armazenamento do Azure.

É recomendável que você passe por meio de [Introdução ao Azure Data Factory][adf-getstarted] tutorial antes de passar por esse exemplo e use o Editor de fábrica de dados para criar os artefatos de fábrica de dados (serviços vinculados, tabelas, pipeline) neste exemplo.
 

1. Crie um serviço vinculado para o Armazenamento do Azure. Se os arquivos de saída e entrada de pontuação estiverem em diferentes contas de armazenamento, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Criar a entrada e saída de tabelas do Data Factory do Azure. Observe que ao contrário de algumas outras tabelas de fábrica de dados, eles devem conter ambos **folderPath** e **fileName** valores. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Provavelmente, você precisará incluir algumas atividades upstream para transformar a entrada para o formato de arquivo CSV e colocá-lo na conta de armazenamento de cada fatia. Nesse caso, não inclua as configurações de "waitOnExternal" mostradas no exemplo a seguir e seu ScoringInputBlob será a tabela de saída de uma atividade diferente.

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
	
	O lote pontuação arquivo csv deve ter a linha de cabeçalho de coluna. Se você estiver usando o **cópia atividade** para criar/mover o csv no armazenamento de blob, você deve definir a propriedade coletor **blobWriterAddHeader** para **true**. Por exemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho ', linha 1, posição 1**.
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


4. Criar um serviço vinculado do tipo: **AzureMLLinkedService**, fornecendo a chave API e o modelo de URL pontuação de lote.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Por fim, criar um pipeline que contém um **AzureMLBatchScoringActivity**. Ela obterá o local do arquivo de entrada de tabelas de entrada, chamará a API de pontuação em lote e copiará o saída da pontuação em lote para o blob especificado na sua tabela de saída. Ao contrário de algumas outras atividades de Data Factory, oAzureMLBatchScoringActivity pode ter apenas uma entrada e uma tabela de saída.

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


## Parâmetros de serviço da Web
Você pode usar parâmetros de serviço da Web que são expostos por um serviço da Web de aprendizado de máquina do Azure publicado em pipelines de fábrica de dados do Azure (AAD). Você pode criar uma experiência de aprendizado de máquina do Azure e publicá-lo como um serviço da web e usar o serviço da web em várias atividades, passando entradas diferentes por meio de parâmetros de serviço da Web ou ADF pipelines.

### Passar valores para parâmetros de serviço Web
Adicionar uma **transformação** seção para o **AzureMLBatchScoringActivty** seção no pipeline do JSON para especificar valores para parâmetros de serviço da Web na seção conforme mostrado no exemplo a seguir:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Você também pode usar [funções de fábrica dados](https://msdn.microsoft.com/library/dn835056.aspx) passar valores para a Web service parâmetros conforme mostrado no exemplo a seguir:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, para garantir que os nomes que você especificar na atividade de JSON corresponde às expostos pelo serviço da Web.

### Gravadores e leitores do SQL azure
Um cenário comum de uso de parâmetros de serviço da Web é o uso do Azure SQL leitores e gravadores. O módulo do leitor é usado para carregar dados em uma experiência de gerenciamento de serviços de dados fora do Azure Studio de aprendizado de máquina e o módulo de gravador é salvar os dados de suas experiências nos serviços de gerenciamento de dados fora do Azure Studio de aprendizado de máquina. Para obter detalhes sobre o leitor/gravador do SQL Azure/Blob do Azure, consulte [leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) tópicos na biblioteca MSDN. O exemplo na seção anterior usado o leitor de BLOBs do Azure e o gravador de BLOBs do Azure. Esta seção discute usando o leitor do SQL Azure e o gravador do SQL Azure.

#### Leitor do SQL Azure
No Azure ML Studio, você pode criar uma experiência e publicar um serviço da Web com um leitor do SQL Azure para a entrada. O leitor do SQL Azure tem propriedades de conexão que podem ser expostas como parâmetros de serviços da Web, permitindo que os valores para as propriedades de conexão a ser passado em tempo de execução em lote de solicitação de pontuação.

Em tempo de execução, os detalhes da tabela de fábrica de dados de entrada serão usados pelo serviço de dados de fábrica para preencher os parâmetros de serviço da Web. Observe que você deve usar nomes padrão (nome do servidor de banco de dados, nome do banco de dados, nome de conta de usuário, senha de conta de usuário do servidor) para os parâmetros de serviço da Web para essa integração com o serviço de fábrica de dados para trabalhar.

Se você tiver qualquer Web adicional parâmetros de serviço, use o **webServiceParameters** seção da atividade JSON. Se você especificar valores para parâmetros de leitor do SQL Azure nesta seção, os valores substituirá os valores pegou do serviço do SQL Azure vinculadas entrado. É recomendável não que especificar valores para o leitor do SQL Azure diretamente na seção webServiceParameters. Use a seção para passar valores para parâmetros adicionais.

Para usar um leitor do SQL Azure por meio de um pipeline de fábrica de dados do Azure, faça o seguinte:

- Criar um **SQL Azure vinculadas serviço**. 
- Criar uma fábrica de dados **tabela** que usa **AzureSqlTableLocation**.
- Defina essa fábrica de dados **tabela** como o **entrada** para o **AzureMLBatchScoringActivity** no pipeline de JSON. 



#### Gravador do SQL Azure
Como com o leitor do SQL Azure, um gravador do SQL Azure também podem ter suas propriedades expostas como parâmetros de serviço da Web. Um gravador do SQL Azure usa as configurações do serviço vinculado associado à tabela de entrada ou a tabela de saída. A tabela a seguir descreve quando a entrada do serviço vinculado é utilizado versus saída vinculado service.

<table>
<tr>
<td>Entrada/saída</td>
<td><b>A entrada é SQL Azure</b></td>
<td><b>A entrada é BLOBs do Azure</b></td>
</tr>
<tr>
<td><b>Saída é SQL Azure</b></td>
<td><p>O serviço de dados Factory usa as informações da cadeia de conexão do serviço de entrada vinculado para gerar os parâmetros de serviço da web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Azure ML Studio.</p>
<p>Se o leitor do SQL Azure e o gravador do SQL Azure em seu modelo Azure ML compartilham os mesmos parâmetros de serviço Web mencionados acima, está tudo bem. Se eles não compartilham mesmo paramers de serviço Web, por exemplo, se o gravador do SQL Azure usa nomes de parâmetros: server name1, Nome1 banco de dados, name1 de conta de usuário do servidor, usuário do servidor de banco de dados da conta password1 (com "1" no final), você deve passar valores para esses parâmetros de serviço de web de saída na seção webServiceParameters da atividade JSON.</p>
<p>
Você pode passar valores para quaisquer outros parâmetros de serviço da Web usando a seção webServiceParameters da atividade JSON.  
</p>

</td>
<td>
<p>O serviço de dados Factory usa as informações da cadeia de conexão do serviço de saída vinculado para gerar os parâmetros de serviço da web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Azure ML Studio.</p>
<p>Você pode passar valores para quaisquer outros parâmetros de serviço da Web usando a seção webServiceParameters da atividade JSON. <p>Blob de entrada será usado como o local de entrada.</p>
</td>
</tr>
<tr>
<td><b>Saída é BLOBs do Azure</b></td>
<td>O serviço de dados Factory usa as informações da cadeia de conexão do serviço de entrada vinculado para gerar os parâmetros de serviço da web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Azure ML Studio.
</td>
<td>
<p>Você deve passar valores para qualquer parâmetro de serviço da Web usando a seção WebServiceParameters da atividade JSON.</p> 

<p>BLOBs serão usados como entradas e saídas locais.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Gravador do SQL Azure podem encontrar violações de chave, se está substituindo uma coluna de identidade. Certifique-se de que a estrutura de sua tabela de saída para evitar essa situação.
> 
> Você pode usar tabelas de preparo com uma atividade de procedimento armazenado para mesclar linhas ou truncar os dados antes de pontuação. Se você usar essa abordagem, defina a configuração de simultaneidade da executionPolicy como 1.

### Exemplo de como usar parâmetros de serviço da Web
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
	    	]
		}
	}
 
No exemplo acima JSON:

- O modelo de ML Azure usa o Azure SQL leitor e gravador do SQL Azure
- Quando exposto por meio do serviço da Web, os nomes padrão são usados para os parâmetros
	- Para o **leitor**: nome do servidor, nome do banco de dados, nome de conta de usuário e senha de conta de usuário do servidor de banco de dados.
	- Para o **Gravador**: banco de dados server name1, Nome1 banco de dados, name1 de conta de usuário do servidor e password1 de conta de usuário do servidor.
	
		Observe que o leitor e gravador não compartilham parâmetros nesse caso.  
- O serviço de fábrica de dados gera automaticamente valores para parâmetros de serviço da Web com os nomes **nome do servidor de banco de dados**, **nome do banco de dados**, **nome de conta de usuário do servidor**, e **senha de conta de usuário do servidor**, que correspondem aos nomes do leitor de entrada. Portanto, não é necessário passar explicitamente os valores para esses parâmetros via **webServiceParameters** na atividade de JSON abaixo.  
- Os parâmetros para o gravador (aqueles com sufixo '1') não são preenchidos automaticamente pelo serviço de dados de fábrica. Portanto, você precisa especificar valores para esses parâmetros na **webServiceParameters** seção da atividade JSON.  
- **ID do cliente**, **pontuadas rótulos**, e **pontuadas probabilidades** são salvos como colunas separada por vírgulas. 
- O **nome de tabela de dados** neste exemplo corresponde a uma tabela no banco de dados de saída.




## Consulte também

Artigo | Descrição
------ | ---------------
[Referência do desenvolvedor de fábrica de dados do Azure][developer-reference] | A referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, biblioteca de classes do .NET, funções, etc... 

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

<!---HONumber=GIT-SubDir-->