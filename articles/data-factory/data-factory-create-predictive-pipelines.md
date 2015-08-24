<properties 
	pageTitle="Data Factory - Cria Pipelines de Previsão usando o Data Factory e o Aprendizado de Máquina | Microsoft Azure" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>

# Criar pipelines de previsão usando o Data Factory e o Aprendizado de Máquina do Azure 
## Visão geral

O Azure Data Factory permite que você crie facilmente pipelines que utilizam o serviço Web do [aprendizado de máquina do Azure][azure-machine-learning] publicado para análise preditiva. Isso permite que você use o Azure Data Factory para gerenciar o processamento e movimentação de dados e executar a pontuação de lote usando o Aprendizado de Máquina do Azure. Para conseguir isso, você precisará fazer o seguinte:

1. Use a atividade **AzureMLBatchScoring**.
2. **URI de solicitação** para a API de execução do lote. Você pode encontrar o URI de solicitação clicando no link **EXECUÇÃO EM LOTES** na página de serviços Web (mostrada abaixo).
3. **Chave API** para o serviço Web do Aprendizado de Máquina do Azure publicado. Você pode encontrar essas informações clicando no serviço Web publicado. 

	![Painel de Aprendizado de Máquina][machine-learning-dashboard]

Um **pipeline de previsão** tem as seguintes partes:

-	Tabelas de entrada e saídas
-	Serviços vinculados de Aprendizagem de Máquina e Armazenamento do Azure/SQL do Azure
-	Uma pipeline com Atividades de Contagem em Lote do Aprendizado de Máquina do Azure

> [AZURE.NOTE]Você pode usar parâmetros de serviço Web que são expostos por um serviço Web de aprendizado de máquina do Azure publicado em pipelines de ADF (Azure Data Factory). Para obter mais informações, consulte a seção Parâmetros de serviço Web neste artigo.

## Exemplo
Este exemplo usa o armazenamento do Azure para conter tanto os dados de entrada quanto os de saída. Você também pode usar o banco de dados SQL do Azure em vez de usar o armazenamento do Azure.

É recomendável que você estude o tutorial [Introdução à Azure Data Factory][adf-getstarted] antes de passar por esse exemplo e que use o Editor da Data Factory para criar os artefatos de Data Factory (serviços vinculados, tabelas, pipeline) neste exemplo.
 

1. Crie um **serviço vinculado** para o **Armazenamento do Azure**. Se os arquivos de saída e entrada de pontuação estiverem em diferentes contas de armazenamento, você precisará de dois serviços vinculados. Aqui está um exemplo JSON:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Criar a **entrada** de **tabelas** do Azure Data Factory. Observe que ao contrário de algumas outras tabelas de Data Factory, essas devem conter os valores **folderPath** e **fileName**. Você pode usar o particionamento para fazer com que cada execução em lote (cada fatia de dados) processe ou produza arquivos de entrada e saída exclusiva. Provavelmente, você precisará incluir algumas atividades upstream para transformar a entrada para o formato de arquivo CSV e colocá-lo na conta de armazenamento de cada fatia. Nesse caso, não inclua as configurações de **external** e **externalData** mostradas no exemplo a seguir e seu ScoringInputBlob será a tabela de saída de uma atividade diferente.

		{
		  "name": "ScoringInputBlob",
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
	
	O arquivo csv de contagem de lote deve ter a linha de cabeçalho de coluna. Se você estiver usando a **Atividade de cópia** para criar/mover o csv para dentro do armazenamento de blob, você deve definir a propriedade de coleta **blobWriterAddHeader** como **true**. Por exemplo:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Se o arquivo csv não tem a linha de cabeçalho, você poderá ver o seguinte erro: **Erro na atividade: erro ao ler a cadeia de caracteres. Token inesperado: StartObject. Caminho '', linha 1, posição 1**.
3. Criar a **saída** de **tabelas** do Azure Data Factory. Este exemplo usa o particionamento para criar um caminho de saída exclusivo para cada execução de divisão. Sem isso, a atividade substituiria o arquivo.

		{
		  "name": "ScoringResultBlob",
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

4. Crie um **serviço vinculado** do tipo **AzureMLLinkedService**, fornecendo a chave API e a URL de pontuação de lote do modelo.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Por fim, crie um pipeline que contenha um **AzureMLBatchScoringActivity**. Ela obterá o local do arquivo de entrada de tabelas de entrada, chamará a API de pontuação em lote e copiará o saída da pontuação em lote para o blob especificado na sua tabela de saída. Ao contrário de algumas outras atividades de Data Factory, oAzureMLBatchScoringActivity pode ter apenas uma entrada e uma tabela de saída.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchScoring",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "ScoringInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "ScoringResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
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

	Ambos os valores de data/hora de **início** e de **término** devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O tempo **final** é opcional. Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**. Consulte a [Referência de script JSON](https://msdn.microsoft.com/library/dn835050.aspx) para obter detalhes sobre as propriedades JSON.

## Parâmetros de serviço Web
Você pode usar parâmetros de serviço Web que são expostos por um serviço Web de aprendizado de máquina do Azure publicado em pipelines de ADF (Azure Data Factory). Você pode criar uma experiência de aprendizado de máquina do Azure e publicá-la como um serviço Web, então usar esse serviço Web em várias atividades ou pipelines ADF, passando entradas diferentes via parâmetros de serviço Web.

### Passar valores para parâmetros de serviço Web
Adicione uma seção **typeProperties** à seção **AzureMLBatchScoringActivty** no pipeline JSON para especificar valores para parâmetros de serviço Web nessa seção, conforme mostrado no exemplo a seguir:

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Você também pode usar [Funções do Data Factory](https://msdn.microsoft.com/library/dn835056.aspx) para passar valores para os parâmetros do serviço Web conforme mostrado no exemplo a seguir:

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Os parâmetros de serviço Web diferenciam maiúsculas de minúsculas, portanto, garanta que os nomes que você especificar na atividade de JSON correspondam aos expostos pelo serviço Web.

### Módulos de leitor e gravador

Um cenário comum de uso de parâmetros de serviço Web é o uso de leitores e gravadores do SQL do Azure. O módulo do leitor é usado para carregar dados em uma experiência de serviços de gerenciamento de dados fora do Estúdio de aprendizado de máquina do Azure e o módulo de gravador é usado para salvar os dados de suas experiências nos serviços de gerenciamento de dados fora do Estúdio de aprendizado de máquina do Azure. Para obter detalhes sobre o leitor/gravador do SQL Azure/Blob do Azure, consulte os tópicos [Leitor](https://msdn.microsoft.com/library/azure/dn905997.aspx) e [Gravador](https://msdn.microsoft.com/library/azure/dn905984.aspx) na biblioteca MSDN. O exemplo na seção anterior usou o leitor de blobs do Azure e o gravador de blobs do Azure. Esta seção discute usando o leitor do SQL do Azure e o gravador do SQL do Azure.

#### SQL Azure como uma fonte de dados
No Estúdio AM do Azure, você pode criar uma experiência e publicar um serviço Web com um leitor do SQL do Azure para a entrada. O leitor do SQL Azure tem propriedades de conexão que podem ser expostas como parâmetros de serviços Web, permitindo que os valores para as propriedades de conexão sejam passados em tempo de execução na solicitação de contagem de lote.

Em tempo de execução, os detalhes da tabela de Data Factory de entrada serão usados pelo serviço Data Factory para preencher os parâmetros de serviço Web. Observe que você deve usar nomes padrão (Nome do servidor de banco de dados, Nome do banco de dados, Nome de conta de usuário do servidor, Senha de conta de usuário do servidor) para os parâmetros de serviço Web para que essa integração com o serviço Data Factory funcione.

Se você tiver quaisquer parâmetros de serviço Web adicionais, use a seção **webServiceParameters** da atividade JSON. Se você especificar valores para parâmetros de leitor do SQL do Azure nesta seção, os valores substituirão os valores pegos do serviço vinculado de entrada do SQL do Azure. Não é recomendável especificar valores para o leitor do SQL do Azure diretamente na seção webServiceParameters. Use a seção para passar valores para quaisquer parâmetros adicionais.

Para usar um leitor do SQL do Azure por meio de um pipeline da Azure Data Factory, faça o seguinte:

- Criar um **serviço vinculado do SQL do Azure**. 
- Criar uma **tabela** de Data Factory que use **AzureSqlTable**.
- Defina essa **tabela** da Data Factory como o **entrada** para o **AzureMLBatchScoringActivity** no pipeline JSON. 



#### SQL Azure como um coletor de dados
Assim como com o leitor do SQL Azure, um gravador do SQL Azure também pode ter suas propriedades expostas como parâmetros de serviço Web. Um gravador do SQL do Azure usa as configurações do serviço vinculado associado à tabela de entrada ou tabela de saída. A tabela a seguir descreve quando o serviço vinculado de entrada é utilizado versus o serviço vinculado de saída.

| Entrada/Saída | A entrada é SQL do Azure | A entrada é Blob do Azure |
| ------------ | ------------------ | ------------------- |
| A saída é SQL do Azure | <p>O serviço Data Factory usa as informações da cadeia de conexão do serviço de ENTRADA vinculado para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure.</p><p>Se o leitor do SQL Azure e o gravador do SQL Azure em seu modelo Azure ML compartilham os mesmos parâmetros de serviço Web mencionados acima, está tudo bem. Se eles não compartilham os mesmos parâmetros de serviço Web, por exemplo, se o gravador do SQL Azure usa nomes de parâmetros: Database server name1, Database name1, Server user account name1, Server user account password1 (com "1" no final), você deve passar valores para esses parâmetros de serviço Web de SAÍDA na seção webServiceParameters da atividade JSON.</p><p>Você pode passar valores para quaisquer outros parâmetros de serviço Web usando a seção webServiceParameters da atividade JSON.</p> | <p>O serviço Data Factory usa as informações da cadeia de conexão do serviço vinculado de SAÍDA para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure.</p><p>Você pode passar valores para quaisquer outros parâmetros de serviço Web usando a seção webServiceParameters da atividade JSON. <p>O blob de entrada será usado como o local de entrada.</p> |
|A saída é o Blob do Azure | O serviço Data Factory usa as informações da cadeia de conexão do serviço de entrada vinculado para gerar os parâmetros de serviço Web com nomes: "Nome do servidor de banco de dados", "Nome do banco de dados", "Nome de conta de usuário do servidor", "Senha de conta de usuário do servidor". Observe que você deve usar esses nomes padrão para parâmetros de serviço Web no Estúdio AM do Azure. | <p>Você deve passar valores para qualquer parâmetro de serviço Web usando a seção WebServiceParameters da atividade JSON.</p><p>Os blobs serão usados como locais de entrada e saída.</p> |
    

> [AZURE.NOTE]O Gravador do SQL Azure poderá encontrar violações de chave, se estiver substituindo uma coluna de identidade. Certifique-se de estruturar sua tabela de saída para evitar essa situação.
> 
> Você pode usar tabelas de preparo com uma atividade de procedimento armazenado para mesclar linhas ou truncar os dados antes da contagem. Se você usar essa abordagem, defina a configuração de simultaneidade da executionPolicy como 1.

#### Blob do Azure como uma fonte

Ao usar o módulo de leitor em uma experiência de Aprendizado de Máquina do Azure, você pode especificar os Blobs do Azure como uma entrada. Os arquivos no armazenamento de blob do Azure podem ser os arquivos de saída (por exemplo, 000000\_0) que são produzidos por um script de Pig e Hive em execução no HDInsight. O módulo de leitor permite que você leia arquivos (com nenhuma extensão), configurando a propriedade do **caminho para o diretório, blob ou contêiner** do módulo do leitor para apontar para a contêiner/pasta que contém os arquivos conforme mostrado abaixo. Observação: o asterisco (isto é, *) **especifica que todos os arquivos na pasta/contêiner (ou seja, data/aggregateddata/year=2014/month-6/*)** serão lidos como parte da experiência.

![Propriedades de Blob do Azure](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### Exemplo de como usar parâmetros de serviço Web
#### Pipeline com AzureMLBatchScoringActivity com parâmetros de serviço Web

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchScoring",
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
	        "linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	        "typeProperties": {
	          "webServiceParameters": {
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

 

<!---HONumber=August15_HO7-->