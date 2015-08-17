<properties 
	pageTitle="Usar o Pig e o Hive com o Azure Data Factory" 
	description="Saiba como processar dados executando scripts Pig e Hive em um cluster HDInsight do Azure em uma fábrica de dados do Azure." 
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
	ms.date="07/26/2015" 
	ms.author="spelluru"/>

# Usar o Pig e o Hive com o Data Factory
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a atividade de HDInsight com transformação Pig/Hive em um pipeline do Azure Data Factory. Consulte [Invocar programas MapReduce da Data Factory][data-factory-map-reduce] para obter detalhes sobre como executar os programas MapReduce em um cluster HDInsight de um pipeline da Azure Data Factory.

## Passo a passo: utilizar o Hive com a Azure Data Factory
Este passo a passo fornece instruções passo a passo para usar uma atividade de HDInsight com a transformação Hive em um pipeline de data factory.

### Pré-requisitos
1. Complete o tutorial do artigo [Introdução à Azure Data Factory][adfgetstarted].
2. Crie o arquivo **hivequery.hql** em uma subpasta denominada **Hive** em **C:\\ADFGetStarted** com o conteúdo a seguir.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;

	> [AZURE.NOTE]Para usar o mecanismo **Tez** para executar consultas do Hive no arquivo HQL, adicione "**set hive.execution.engine=tez**;" na parte superior do arquivo.
		
3.  Carregue o **hivequery.hql** no contêiner **adftutorial** no seu armazenamento de blob


### Passo a passo

#### Criar tabela de saída
        
1. No **Editor Data Factory**, clique em **Novo conjunto de dados** e, em seguida, clique em **Armazenamento de Blobs do Azure** na barra de comandos.
2. Substitua o script JSON no painel direito pelo script JSON a seguir:

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Clique em **Implantar** na barra de comandos para implantar a tabela.


### Criar um serviço vinculado para um cluster de HDInsight
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. O cluster HDInsight sob demanda é automaticamente criado e gerenciado pelo serviço do Azure Data Factory para processar dados. Consulte [Serviço HDInishgt vinculado sob demanda](https://msdn.microsoft.com/library/dn893526.aspx) para obter detalhes sobre o serviço HDInsight vinculado sob demanda. Para fins de exemplo, vamos usar um cluster sob demanda. Observe que leva + de 15 minutos para o cluster HDInsight ser criado sob demanda e você será cobrado apenas pela hora quando o cluster HDInsight estiver funcionando plenamente.

#### Para usar um cluster HDInsight sob demanda
1. Clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight sob demanda** no menu.
2. No script JSON, faça o seguinte: 
	1. Para a propriedade **clusterSize**, especifique o tamanho do cluster do HDInsight.
	2. Para a propriedade **jobsContainer**, especifique o nome do contêiner padrão onde os logs de cluster serão armazenados. Para fins deste tutorial, especifique **adfjobscontainer**.
	3. Para a propriedade **timeToLive**, especifique quanto tempo o cluster pode ficar ocioso antes de ser excluído. 
	4. Para a propriedade **version**, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente será usada.  
	5. Para o **linkedServiceName**, especifique **StorageLinkedService** que você criou no tutorial do guia de Introdução. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.
   
   
#### Para usar seu próprio cluster HDInsight: 

1. Clique em **Nova computação** na barra de comandos e selecione **Cluster HDInsight** no menu.
2. No script JSON, faça o seguinte: 
	1. Para a propriedade **clusterUri**, insira a URL para seu HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para a propriedade **UserName**, digite o nome de usuário que tem acesso ao cluster HDInsight.
	3. Para a propriedade **Password**, especifique a senha para o usuário. 
	4. Para a propriedade **LinkedServiceName**, digite **StorageLinkedService**. Este é o serviço vinculado que você criou no tutorial do guia de Introdução. 

2. Clique em **Implantar** na barra de comandos para implantar o serviço vinculado.

### Criar e agendar o pipeline
   
1. Clique em **Novo pipeline** na barra de comandos. Se você não vir o comando, clique em **... (Reticências)** para vê-lo. 
2. Substitua o JSON no painel direito pelo script JSON a seguir. Se você quiser utilizar seu próprio cluster e tiver seguido as etapas para criar o serviço vinculado **HDInsightLinkedService**, substitua **HDInsightOnDemandLinkedService** por **HDInsightLinkedService** no JSON a seguir. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"defines":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptPath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]Substitua o valor **StartDateTime** pelos três dias anteriores ao dia atual e valor **EndDateTime** pelo dia atual. StartDateTime e EndDateTime precisam ambos estar em [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A tabela de saída está agendada para ser produzida diariamente, então haverá três fatias produzidas.
	> 
	> Substitua o **nome da sua conta de armazenamento** e pela chave da conta de armazenamento.
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.
2. Clique em **Implantar** na barra de comandos para implantar o pipeline.
4. Consulte a seção [Monitorar conjuntos de dados e pipeline][adfgetstartedmonitoring] do artigo [Introdução à Data Factory][adfgetstarted]. 

	> [AZURE.NOTE]Na folha **detalhes de execução da atividade** para uma fatia de uma tabela de saída (selecione a tabela de saída -> selecione a fatia -> selecione uma atividade executada no portal), você verá links para logs criados pelo cluster HDInsight. Você pode examiná-los no próprio portal ou baixá-los em seu computador.
  

## Exemplo de JSON de Pig
Ao definir uma atividade de Pig ou Hive em um pipeline de JSON, a propriedade **type** deve ser definida como: **HDInsightActivity**.

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"defines":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Observe o seguinte:**
	
- O **type** da atividade é definido como **HDInsightActivity**.
- **linkedServiceName** é definido como **MyHDInsightLinkedService**. Consulte a seção de serviço vinculado HDInsight abaixo para obter detalhes sobre como criar um serviço vinculado HDInsight.
- O **type** da **transformation** é definido como **Pig**.
- É possível especificar o script de Pig embutido para a propriedade **script** ou armazenar os arquivos de script em um armazenamento de blobs do Azure e consultar o arquivo utilizando a propriedade **scriptPath**, que é explicada neste artigo. 
- É possível especificar parâmetros do script de Pig utilizando **defines**. Mais detalhes serão fornecidos posteriormente neste artigo. 


## Exemplo de JSON de Hive


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"defines":
			{	
				"param1": "param1Value"
            }
		}
	}

**Observe o seguinte:**
	
- O **type** da atividade é definido como **HDInsightActivity**.
- **linkedServiceName** é definido como **MyHDInsightLinkedService**. 
- O **type** da **transformation** é definido como **Hive**.
- É possível especificar o script de Hive embutido para a propriedade **script** ou armazenar os arquivos de script em um armazenamento de blobs do Azure e consultar o arquivo utilizando a propriedade **scriptPath**, que é explicada neste artigo. 
- É possível especificar parâmetros do script de Hive utilizando **defines**. Mais detalhes serão fornecidos posteriormente neste artigo. 

> [AZURE.NOTE]Consulte a [Referência do Desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema.


## Usando scripts de Pig e Hive na atividade de HDInsight
É possível armazenar scripts de Pig/Hive em um armazenamento de blob do Azure associado ao cluster de HDInsight e consultá-los nas atividades de Pig/Hive utilizando as seguintes propriedades no JSON:

* **scriptPath** – o caminho para o arquivo de script de Pig ou Hive
* **scriptLinkedService** – conta de armazenamento do Azure que contém o arquivo de script

O exemplo de JSON a seguir para um pipeline de exemplo utiliza uma atividade de Hive que faz referência ao arquivo **transformdata.hql** armazenado na pasta **scripts** no contêiner **adfwalkthrough** no armazenamento de blobs do Azure representado pelo **StorageLinkedService**.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptPath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"defines":
						{
						}		
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


> [AZURE.NOTE]Para usar o mecanismo **Tez** para executar uma consulta de Hive, execute "**set hive.execution.engine=tez**;" antes de executar a consulta de Hive.
> 
> Consulte a [Referência do Desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema.

## Consultas de Hive e Pig Parametrizados
As atividades de Pig e Hive da Data Factory permitem que você especifique valores para os parâmetros utilizados nos scripts de Pig e Hive, usando **defines**. A seção defines consiste no nome do parâmetro e o valor do parâmetro.

Consulte o exemplo a seguir para especificar parâmetros para um script do Hive utilizando **defines**. Para utilizar os scripts de Hive parametrizados, faça o seguinte:

1.	Defina os parâmetros em **defines**.
2.	No script de Hive em linha (ou) arquivo de script de Hive armazenado no armazenamento de blog, consulte o parâmetro utilizando **${hiveconf:parameterName}**.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"defines":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


## Consulte também

Artigo | Descrição
------ | ---------------
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um passo a passo de ponta a ponta que mostra como implantar um cenário próximo ao do mundo real usando a Azure Data Factory para transformar dados de arquivos de log em ideias.
[Referência do desenvolvedor da Azure Data Factory][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc… 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=August15_HO6-->