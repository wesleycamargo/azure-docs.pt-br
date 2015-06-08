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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Usar o Pig e o Hive com o Data Factory
Um pipeline em uma fábrica de dados do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa uma operação de processamento específica. Este artigo descreve como usar a atividade de HDInsight com Pig/Hive transformação em um pipeline de fábrica de dados do Azure. Consulte [chamar programas MapReduce da fábrica de dados][data-factory-map-reduce] para obter detalhes sobre como executar o MapReduce programas em um HDInsight cluster a partir de um pipeline de fábrica de dados do Azure.

## Passo a passo: Usar o Hive com o alocador de dados do Azure
Este passo a passo fornece instruções passo a passo para usar uma atividade de HDInsight com o Hive transformação em um pipeline de fábrica de dados.

### Pré-requisitos
1. Concluir o tutorial de [Introdução ao Azure Data Factory][adfgetstarted] artigo.
2. Carregar **emp.txt** arquivo criado no tutorial anterior como **hiveinput\emp.txt** para o contêiner adftutorial no armazenamento de blob. O **hiveinput** pasta é criada automaticamente na **adftutorial** contêiner ao carregar o arquivo emp.txt com essa sintaxe.

	> [AZURE.NOTE]O arquivo emp.txt é apenas um arquivo fictício para este passo a passo. Os dados de entrada reais é proveniente do **hivesampletable** que já existe no cluster HDInsight. O pipeline não usa o arquivo emp.txt.
	
2. Criar **hivequery.hql** arquivos em uma subpasta chamada **Hive** em **C:\ADFGetStarted** com o seguinte conteúdo.
    		
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

	> [AZURE.NOTE]Para usar o **Tez** mecanismo para executar consultas do Hive no arquivo HQL, adicione "* * definir hive.execution.engine=tez**;" na parte superior do arquivo.
		
3.  Carregar o **hivequery.hql** para o **adftutorial** contêiner em seu armazenamento de blob


### Passo a passo

#### Criar tabela de entrada
1. No **DATA FACTORY** lâmina para o **ADFTutorialDataFactory**, clique em **autor e implantar** para iniciar o Editor de fábrica de dados.
	
	![Blade de fábrica de dados][data-factory-blade]

2. No **editor de fábrica dados**, clique em **novo conjunto de dados**, e, em seguida, clique em **armazenamento de BLOBs do Azure** na barra de comandos.
3. Substitua o script JSON no painel à direita com o script JSON a seguir:    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**Observe o seguinte:**
	
	- local **tipo** é definido como **AzureBlobLocation**.
	- **linkedServiceName** é definido como **StorageLinkedService** que define uma conta de armazenamento do Azure.
	- **folderPath** Especifica o blob container\folder para os dados de entrada. 
	- **freqüência = dia** e **intervalo = 1** significa que as fatias estão disponíveis diariamente
	- **waitOnExternal** significa que esses dados não são produzidos pelo pipeline de outro, em vez disso, produzido externamente para o alocador de dados. 
	

	Consulte [dados de referência do desenvolvedor de fábrica][developer-reference] para obter descrições das propriedades JSON.

2. Clique em **Deploy** na barra de comando para implantar a tabela.
  
#### Criar tabela de saída
        
1. No **editor de fábrica dados**, clique em **novo conjunto de dados**, e, em seguida, clique em **armazenamento de BLOBs do Azure** na barra de comandos.
2. Substitua o script JSON no painel à direita com o script JSON a seguir:

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

2. Clique em **Deploy** na barra de comando para implantar a tabela.


### Criar um serviço vinculado para um cluster de HDInsight
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda.

#### Para usar um cluster HDInsight sob demanda
1. Clique em **nova computação** da barra de comandos e selecione **cluster HDInsight sob demanda** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterSize** propriedade, especifique o tamanho do cluster do HDInsight.
	2. Para o **jobsContainer** propriedade, especifique o nome do recipiente padrão onde os logs de cluster serão armazenados. Para fins deste tutorial, especifique **adfjobscontainer**.
	3. Para o **timeToLive** propriedade, especifique quanto tempo o cluster pode ficar ocioso antes de serem excluído. 
	4. Para o **versão** propriedade, especifique a versão do HDInsight que você deseja usar. Se você excluir essa propriedade, a versão mais recente é usada.  
	5. Para o **linkedServiceName**, especifique **StorageLinkedService** que você tivesse criado em Get tutorial de Introdução. 

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

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.
   
   
#### Para usar seu próprio cluster HDInsight: 

1. Clique em **nova computação** da barra de comandos e selecione **cluster HDInsight** no menu.
2. Faça o seguinte no script JSON: 
	1. Para o **clusterUri** propriedade, digite a URL para o HDInsight. Por exemplo: https://<clustername>.azurehdinsight.net/     
	2. Para o **nome de usuário** propriedade, digite o nome de usuário que tenha acesso ao cluster HDInsight.
	3. Para o **senha** propriedade, digite a senha do usuário. 
	4. Para o **LinkedServiceName** propriedade, digite **StorageLinkedService**. Este é o serviço vinculado que você tivesse criado no tutorial do guia de Introdução. 

2. Clique em **Deploy** na barra de comandos para implantar o serviço vinculado.

### Criar e agendar o pipeline
   
1. Clique em **novo pipeline** na barra de comandos. Se você não vir o comando, clique em **... (Reticências)** Para vê-lo. 
2. Substitua o seguinte script JSON JSON no painel à direita. Se você quiser usar seu próprio cluster e seguido as etapas para criar o **HDInsightLinkedService** vinculado de serviço, substitua **HDInsightOnDemandLinkedService** com **HDInsightLinkedService** em JSON a seguir. 


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
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
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

	> [AZURE.NOTE]Substitua **StartDateTime** valor com três dias antes do dia atual e **EndDateTime** valor com o dia atual. StartDateTime e EndDateTime devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A tabela de saída está agendada para ser produzida diariamente, então haverá três fatias produzidas.
	
	> [AZURE.NOTE]Substitua **sua conta de armazenamento** em JSON com o nome da sua conta de armazenamento.
	
	Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.
2. Clique em **Deploy** na barra de comando para implantar o pipeline.
4. Consulte [monitorar conjuntos de dados e pipeline][adfgetstartedmonitoring] seção [Introdução aos dados fábrica][adfgetstarted] artigo. 

	> [AZURE.NOTE]No **detalhes de execução da atividade** lâmina para uma fatia de uma tabela de saída (selecione a tabela de saída -> selecione Fatia -> selecione uma atividade executada no portal de), você verá links para logs criados com o cluster HDInsight. Você pode examiná-los no próprio portal ou baixá-los em seu computador.
  

## Exemplo de JSON de Pig
Ao definir uma atividade de Pig ou Hive em um pipeline de JSON, o **tipo** propriedade deve ser definida como: **HDInsightActivity**.

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
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**Observe o seguinte:**
	
- Atividade **tipo** é definido como **HDInsightActivity**.
- **linkedServiceName** é definido como **MyHDInsightLinkedService**. Consulte a seção de serviço HDInsight vinculada abaixo para obter detalhes sobre como criar um serviço HDInsight vinculado.
- O **tipo** do **transformação** é definido como **Pig**.
- Você pode especificar Pig script embutido para o **script** propriedade ou o armazenamento de arquivos de script do Azure armazenamento de blob e consulte o arquivo usando **scriptPath** propriedade, que é explicada neste artigo. 
- Especificar parâmetros para o script de Pig usando o **extendedProperties**. Mais detalhes serão fornecidos posteriormente neste artigo. 


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
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**Observe o seguinte:**
	
- Atividade **tipo** é definido como **HDInsightActivity**.
- **linkedServiceName** é definido como **MyHDInsightLinkedService**. 
- O **tipo** do **transformação** é definido como **Hive**.
- Você pode especificar o Hive script embutido para o **script** propriedade ou o armazenamento de arquivos de script do Azure armazenamento de blob e consulte o arquivo usando **scriptPath** propriedade, que é explicada neste artigo. 
- Especificar parâmetros para o script do Hive usando o **extendedProperties**. Mais detalhes serão fornecidos posteriormente neste artigo. 

> [AZURE.NOTE]Consulte [referência do desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema.


## Usando scripts do Pig e Hive na atividade de HDInsight
É possível armazenar scripts de Pig/Hive em um armazenamento de blob do Azure associado ao cluster de HDInsight e consultá-los nas atividades de Pig/Hive utilizando as seguintes propriedades no JSON:

* **scriptPath** – caminho para o arquivo de script Pig ou seção
* **scriptLinkedService** – conta de armazenamento do Azure que contém o arquivo de script

O exemplo a seguir JSON para um pipeline de exemplo usa uma atividade de Hive que faz referência a **transformdata.hql** arquivo armazenado no **scripts** pasta o **adfwalkthrough** contêiner no armazenamento de blob do Azure representado pelo **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
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


> [AZURE.NOTE]Para usar o **Tez** mecanismo para executar uma consulta de Hive, execute "* * definir hive.execution.engine=tez**;" antes de executar a consulta de Hive.
> 
> Consulte [referência do desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema.

## Consultas de Hive e Pig Parametrizados
As atividades de dados fábrica Pig e Hive permitem que você especifique valores para os parâmetros usados em scripts Pig e Hive, usando **extendedProperties**. A seção extendedProperties consiste no nome do parâmetro e o valor do parâmetro.

Consulte o exemplo a seguir para especificar parâmetros para um script do Hive usando **extendedProperties**. Para utilizar os scripts de Hive parametrizados, faça o seguinte:

1.	Defina os parâmetros no **extendedProperties**.
2.	No script do Hive na linha (ou) armazenados no armazenamento de blog de arquivo de script de Hive, consulte usando o parâmetro **${hiveconf:parameterName}**.

   
    		
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
							"extendedProperties":
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
[Tutorial: Mover e processar os arquivos de log usando o alocador de dados][adf-tutorial] | Este artigo fornece uma explicação de ponta a ponta que mostra como implementar um near real usando o alocador de dados do Azure para transformar dados de arquivos de log em ideias de cenário do mundo.
[Referência do desenvolvedor de fábrica de dados do Azure][developer-reference] | A referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, o script JSON, funções, etc... 

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

<!---HONumber=GIT-SubDir-->