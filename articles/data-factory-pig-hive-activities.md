<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Usar o Pig e o Hive com o Azure Data Factory" description="Learn how to process data by running Pig and Hive scripts on an Azure HDInsight cluster from an Azure data factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Usar o Pig e o Hive com o Data Factory
Uma pipeline em uma data factory do Azure processa dados nos serviços de armazenamento vinculados utilizando serviços de computação vinculados. Ela contém uma sequência de atividades em que cada atividade executa  uma operação de processamento específica. Por exemplo, uma atividade de cópia copia dados de um armazenamento de fonte para um armazenamento de destino e uma atividade de HDInsight com transformações de Hive/Pig utiliza um cluster do Azure HDInsight para processar dados utilizando scripts de Hive/Pig. A atividade de HDInsight pode consumir uma ou mais entradas e produzir uma ou mais saídas. 

## Neste artigo

Descrição | da seção
------- | -----------
[Exemplo de JSON pig](#PigJSON) | Esta seção fornece o esquema JSON para definir uma atividade de HDInsight que utiliza uma transformação do Pig. 
[Exemplo de JSON Hive](#HiveJSON) | Esta seção fornece o esquema JSON para definir uma atividade de HDInsight que utiliza uma transformação do Hive. 
[Utilizando scripts de Pig e Hive que são armazenados no armazenamento de blobs do Azure](#ScriptInBlob) | Descreve como fazer referência a scripts de Pig/Hive armazenados em um armazenamento de blobs do Azure de uma atividade de HDInsight utilizando a transformação de Pig/Hive.
[Consultas de Pig e Hive parametrizados](#ParameterizeQueries) | Descreve como especificar valores para parâmetros utilizados nos scripts de Pig e Hive, usando a propriedade **extendedProperties** em JSON.
[Passo a passo: Utilizar o Hive com o Azure dados Factory](#Waltkthrough) | Fornece instruções passo a passo para criar uma pipeline que utiliza o Hive para processar os dados.  



Ao definir uma atividade de Pig ou Hive em uma pipeline de JSON, a propriedade **type** (tipo) deve ser definida como: **HDInsightActivity** (atividade de HDinsight).

## <a name="PigJSON"></a> Exemplo de JSON de Pig

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
	
- Activity **type** (tipo de atividade) é definido como **HDInsightActivity** (atividade de HDInsight).
- **linkedServiceName** (nome do serviço vinculado) é definido como **MyHDInsightLinkedService**. 
- O **tipo** da **transformação** é definido como **Pig**.
- É possível especificar o script de Pig embutido para a propriedade **script** ou armazenar os arquivos de script em um armazenamento de blobs do Azure e consultar o arquivo utilizando a propriedade **scriptPath**, que é explicada neste artigo. 
- É possível especificar parâmetros do script de Pig utilizando a **extendedProperties** (propriedades estendidas). Mais detalhes serão fornecidos posteriormente neste artigo. 


## <a name="HiveJSON"></a> ## exemplo de JSON de Hive


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
	
- Activity **type** (tipo de atividade) é definido como **HDInsightActivity** (atividade de HDInsight).
- **linkedServiceName** (nome do serviço vinculado) é definido como **MyHDInsightLinkedService**. 
- O **tipo** da **transformação** é definido como **Hive**.
- É possível especificar o script de Hive embutido para a propriedade **script** ou armazenar os arquivos de script em um armazenamento de blobs do Azure e consultar o arquivo utilizando a propriedade **scriptPath**, que é explicada neste artigo. 
- É possível especificar parâmetros do script de Hive utilizando a **extendedProperties** (propriedades estendidas). Mais detalhes serão fornecidos posteriormente neste artigo. 

> [WACOM.NOTE] Consulte a [Referência do desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema. 


## <a name="ScriptInBlob"></a>Usando scripts de Pig e Hive que são armazenados no armazenamento de blobs do Azure
É possível armazenar scripts de Pig/Hive em um armazenamento de blob do Azure associado ao cluster de HDInsight e consultá-los nas atividades de Pig/Hive utilizando as seguintes propriedades no JSON: 

* **scriptPath** - o caminho para o arquivo de script de Pig ou Hive
* **scriptLinkedService** - conta de armazenamento do Azure que contém o arquivo de script

O exemplo de JSON a seguir para uma pipeline de exemplo utiliza uma atividade de Hive que faz referência ao arquivo **transformdata.hql** armazenado na pasta **scripts** no contêiner **adfwalkthrough** no armazenamento de blobs do Azure representado pelo **StorageLinkedService**.

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
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
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

  

> [WACOM.NOTE] Consulte a [Referência do desenvolvedor](http://go.microsoft.com/fwlink/?LinkId=516908) para obter detalhes sobre os cmdlets, esquemas JSON e propriedades no esquema.

## <a name="ParameterizeQueries"></a>Consultas de Hive e Pig parametrizados
As atividades de Pig e Hive da data factory permitem que você especifique valores para os parâmetros utilizados nos scripts de Pig e Hive, usando **extendedProperties**. A seção extendedProperties consiste do nome do parâmetro e o valor do parâmetro.

Consulte o exemplo a seguir para especificar parâmetros para um script do Hive utilizando **extendedProperties**. Para utilizar os scripts de Hive parametrizados  , faça o seguinte:

1.	Defina os parâmetros na **extendedProperties**.
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


-  

## <a name="Walkthrough"></a>Passo a passo: Utilizar o Hive com o Azure Data Factory
### Pré-requisitos
1. Complete o tutorial do artigo [Introdução ao Data Factory do Azure][adfgetstarted].
2. Carregue o arquivo **emp.txt** criado no tutorial anterior como **hiveinput\emp.txt** no contêiner adftutorial no armazenamento de blob. A pasta **hiveinput** é criada automaticamente no contêiner **adftutorial** ao carregar o arquivo emp.txt com essa sintaxe. 
2. Crie o arquivo **hivequery.hql** em uma subpasta denominada **Hive** em **C:\ADFGetStarted** com o seguinte conteúdo.
    		
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
		
3.  Carregue o **hivequery.hql** no contêiner **adftutorial** no seu armazenamento de blob


### Passo a passo

#### Criar tabela de entrada
1. Crie um arquivo JSON denominado **HiveInputBlobTable.json** na pasta **C:\ADFGetStarted\Hive** com o seguinte conteúdo.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
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
	
	- location **type** é definido como **AzureBlobLocation**.
	- **linkedServiceName** (nome do serviço vinculado) é definido como **MyBlobStore** que define uma conta de armazenamento do Azure.
	- **folderPath** (caminho da pasta) especifica a pasta/contêiner de blob para os dados de entrada. 
	- **frequency=Day** e **interval=1** significa as fatias estão disponíveis diariamente
	- **waitOnExternal** significa que esses dados não são produzidos por outra pipeline, em vez disso, são produzidos externamente para a data factory. 
	

	Consulte a [Referência do desenvolvedor de data factory][developer-reference] para obter descrições das propriedades JSON.  

2. Inicie o **PowerShell do Azure** e alterne para o modo **AzureResourceManager**, se necessário.
    		
    	Switch-AzureMode AzureResourceManager

5. Alterne para a pasta: **C:\ADFGetStarted\Hive**.
6. Execute o seguinte comando para criar a tabela de entrada na **ADFTutorialDataFactory**.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	Consulte a [Referência de cmdlets da data factory][cmdlet-reference] para a visão geral detalhada dos cmdlets de data factory. 
#### Criar tabela de saída
        
1. Crie um arquivo JSON denominado **HiveOutputBlobTable.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Hive**.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. Execute o seguinte comando para criar a tabela de saída na **ADFTutorialDataFactory**.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### Criar um serviço vinculado para um cluster de HDInsight
O serviço Data Factory do Azure dá suporte à criação de um cluster sob demanda e o usa para processar entrada a fim de gerar dados de saída. Você também pode usar seu próprio cluster para fazer isso. Quando você usa o cluster HDInsight sob demanda, um cluster é criado para cada fatia. Ao passo que, ao utilizar seu próprio cluster de HDInsight, o cluster está pronto para processar a fatia imediatamente. Portanto, quando você usar cluster sob demanda, não verá os dados de saída mais rapidamente que quando usa seu próprio cluster. Para fins de exemplo, vamos usar um cluster sob demanda. 

#### Para usar um cluster HDInsight sob demanda
1. Crie um arquivo JSON denominado **HDInsightOnDemandCluster.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Hive**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**. Os cmdlets do Data Factory do Azure estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Alterne para a pasta **C:\ADFGetstarted\Hive**.
4. Execute o seguinte comando para criar o serviço vinculado para o cluster HDInsight sob demanda.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. Você deve visualizar as tabelas e serviços vinculados na folha **Data Factory** no **Portal de visualização do Azure**.    
   
#### Para usar seu próprio cluster HDInsight: 

1. Crie um arquivo JSON denominado **MyHDInsightCluster.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Hive**. Substitua clustername, nome de usuário e senha por valores adequados antes de salvar o arquivo JSON.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**.Os cmdlets do Azure Data Factory estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager
		

3. Alterne para a pasta **C:\ADFGetstarted\Hive**.
4. Execute o seguinte comando para criar o serviço vinculado para seu próprio cluster de HDInsight.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### Criar e agendar o pipeline
   
1. Crie um arquivo JSON denominado **ADFTutorialHivePipeline.json** com o seguinte conteúdo e salve-o na pasta **C:\ADFGetStarted\Hive**. Se você quiser utilizar seu próprio cluster e tiver seguido as etapas para criar o serviço vinculado **MyHDInsightCluster**, substitua **HDInsightOnDemandCluster** por **MyHDInsightCluster** em JSON a seguir. 


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
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
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

2. Execute o seguinte comando para criar a pipeline.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. Agende a pipeline.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] Substitua o valor **StartDateTime** com os três dias antes do dia atual e valor **EndDateTime** com o dia atual. StartDateTime e EndDateTime são horas UTC (Tempo Universal Coordenado) e devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. 
	> Se você não especificar **EndDateTime**, ele será calculado como "**StartDateTime + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o **EndDateTime**.
  	
	A tabela de saída está agendada para ser produzida diariamente, então haverá três fatias produzidas. 

4. Consulte a seção [Pipeline e conjunto de dados de monitoramento][adfgetstartedmonitoring] de [Introdução ao Azure Data Factory][adfgetstarted].   

## Consulte também

Artigo | Descrição
------ | ---------------
[Introdução ao Azure Data Factory][data-factory-introduction] | Este artigo apresenta os conceitos, o serviço do Azure Data Factory, o valor que ele oferece e cenários que ele oferece suporte.
[Introdução ao Data Factory do Azure][adf-getstarted] | Este artigo fornece um tutorial de ponta a ponta que mostra como criar uma data factory do Azure de exemplo que copia dados de um blob do Azure para um banco de dados SQL do Azure.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Tutorial: Mover e processar arquivos de log usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Solucionar problemas de Data factory][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure.  
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 


[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Referência do desenvolvedor]: http://go.microsoft.com/fwlink/?LinkId=516908
[Portal do Azure]: http://portal.azure.com
