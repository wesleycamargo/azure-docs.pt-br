<properties
	pageTitle="Criar sua primeira data factory (modelo do ARM) | Microsoft Azure"
	description="Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando um modelo do Gerenciador de Recursos do Azure."
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
	ms.topic="hero-article"
	ms.date="08/01/2016"
	ms.author="spelluru"/>

# Tutorial: Criar a sua primeira Azure Data Factory usando o modelo do Azure Resource Manager
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-build-your-first-pipeline.md)
- [Como usar o Editor do Data Factory](data-factory-build-your-first-pipeline-using-editor.md)
- [Usando o PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Como usar o Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Como usar o Modelo do Gerenciador de Recursos](data-factory-build-your-first-pipeline-using-arm.md)


Neste artigo, você aprenderá a usar os modelos do Gerenciador de Recursos do Azure (ARM) para criar seu primeiro data factory do Azure.


## Pré-requisitos
Além dos pré-requisitos listados no tópico Visão Geral do Tutorial, você precisa instalar o seguinte:

- Você **deve** ler o artigo [Visão Geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir as etapas de pré-requisito antes de continuar.
- **Instale o Azure PowerShell**. Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a última versão do Azure PowerShell no computador.
- Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Para obter uma visão geral detalhada do serviço, leia a [Introdução ao Azure Data Factory](data-factory-introduction.md).
- Veja [Criando modelos do Gerenciador de Recursos do Azure](../resource-group-authoring-templates.md) para saber mais sobre os modelos do Gerenciador de Recursos do Azure (ARM).

> [AZURE.IMPORTANT]
Você deve concluir as etapas de pré-requisito na [Visão geral do tutorial](data-factory-build-your-first-pipeline.md) para fazer o passo a passo deste artigo.

## Criar um modelo ARM

Crie um arquivo JSON denominado **ADFTutorialARM.json** na pasta **C:\\ADFGetStarted** com este conteúdo:

O modelo permite que você crie as seguintes entidades do Data Factory.

1. Um **data factory** chamado **TutorialDataFactoryARM**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma Atividade de Cópia para copiar dados de um armazenamento de dados de origem para um de destino e uma atividade do Hive do HDInsight para executar o script do Hive para transformar os dados de entrada em dados de saída do produto.
2. Dois **serviços vinculados**: **StorageLinkedService** e **HDInsightOnDemandLinkedService**. Esses serviços vinculados vincularão a conta do Armazenamento do Azure e o cluster do Azure HDInsight sob demanda ao data factory. A conta do Armazenamento do Azure manterá os dados de entrada e de saída para o pipeline neste exemplo. O serviço vinculado do HDInsight é usado para executar o script do Hive especificado na atividade do pipeline neste exemplo. Você precisa identificar quais serviços de armazenamento/computação de dados serão usados em seu cenário e vincular esses serviços ao data factory criando serviços vinculados.
3. Dois **conjuntos de dados** (entrada/saída): **AzureBlobInput** e **AzureBlobOutput**. Esses conjuntos de dados representam os dados de entrada e de saída para o processamento do Hive. Esses conjuntos de dados fazem referência ao **StorageLinkedService** que você criou anteriormente neste tutorial. O serviço vinculado aponta para uma conta do Armazenamento do Azure e os conjuntos de dados especificam o contêiner, a pasta e o nome do arquivo no armazenamento que contém os dados de entrada e de saída.

Clique na guia **Usando o Editor do Data Factory** para alternar para o artigo com detalhes sobre as propriedades JSON usadas neste modelo.

> [AZURE.IMPORTANT] Altere os valores das variáveis **storageAccountName** e **storageAccountKey**. Altere o **dataFactoryName** também porque o nome precisa ser exclusivo.


	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<AZURE STORAGE ACCOUNT NAME>" ,
	        "storageAccountKey":  "<AZURE STORAGE ACCOUNT KEY>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "AzureStorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobInputDataset": "AzureBlobInput",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
        					"typeProperties": {
                                "clusterSize": 4,
                                "version":  "3.2",
            					"timeToLive": "00:05:00",
                                "osType": "windows",
            					"linkedServiceName": "[variables('storageLinkedServiceName')]",
    						}
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
						    }
	                    },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "[variables('storageLinkedServiceName')]",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        }
						    }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2016-04-01T00:00:00Z",
						        "end": "2016-04-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}

Clique na guia **Usando o Editor do Data Factory** para alternar para o artigo que tem detalhes sobre as propriedades JSON usadas neste modelo.

Observe o seguinte:

- O Data Factory cria um cluster HDInsight **baseado no Windows** para você com o JSON acima. Você também pode fazer com que ele crie um cluster HDInsight **baseado em Linux**. Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.
- Você pode usar **seu próprio cluster HDInsight** em vez de usar um cluster HDInsight sob demanda. Confira [Serviço vinculado do HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- O cluster HDInsight cria um **contêiner padrão** no armazenamento de blobs especificado em JSON (**linkedServiceName**). O HDInsight não exclui esse contêiner quando o cluster é excluído. Esse comportamento é intencional. Com o serviço vinculado HDInsight sob demanda, um cluster HDInsight é criado sempre que uma fatia precisa ser processada, a menos que haja um cluster ativo existente (**timeToLive**), e é excluído quando o processamento é concluído.

	À medida que mais e mais fatias forem processadas, você verá muitos contêineres no armazenamento de blobs do Azure. Se você não precisa deles para solução de problemas dos trabalhos, convém excluí-los para reduzir o custo de armazenamento. O nome desses contêineres segue um padrão: "adf**nomedodatafactory**-**nomedoserviçovinculado**-carimbodedatahora". Use ferramentas como [Gerenciador de Armazenamento da Microsoft](http://storageexplorer.com/) para excluir contêineres do armazenamento de blobs do Azure.

Confira [Serviço vinculado do HDInsight sob demanda](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.

> [AZURE.NOTE] Você pode encontrar outro exemplo de modelo ARM para criar um data factory do Azure no [Github](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Criar um data factory

1. Inicie o **Azure PowerShell** e execute o comando a seguir.
	- Execute **Login-AzureRmAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	- Execute o comando a seguir para selecionar uma assinatura na qual você deseja criar o data factory. Get-AzureRmSubscription -SubscriptionName <NOME DA ASSINATURA> | Set-AzureRmContext
1. Execute o comando a seguir para implantar entidades do Data Factory usando o modelo do ARM criado na Etapa 1.

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Monitorar o pipeline
 
1.	Depois de fazer logon no [Portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **Data factories**. ![Procurar-> Data factories](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Na folha **Data Factories**, clique no data factory (**TutorialFactoryARM**) que você criou.
2.	Na folha **Data Factory** do seu data factory, clique em **Diagrama**. ![Bloco Diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	Na **Exibição de Diagrama**, você terá uma visão geral dos pipelines e dos conjuntos de dados usados neste tutorial.
	
	![Exibição de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png)
8. Na Exibição de diagrama, clique duas vezes no conjunto de dados **AzureBlobOutput**. Você verá que a fatia que está sendo processada.

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Quando o processamento for concluído, você verá a fatia no estado **Pronto**. Observe que a criação de um cluster do HDInsight sob demanda geralmente leva algum tempo (20 minutos, aproximadamente).

	![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)
10. Quando a fatia estiver no estado **Pronto**, verifique a pasta **partitioneddata** no contêiner **adfgetstarted** em seu armazenamento de blobs para os dados de saída.

Confira [Monitorar os conjuntos de dados e o pipeline](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como usar as folhas do portal do Azure para monitorar o pipeline e os conjuntos de dados que você criou neste tutorial.

Você também pode usar Monitorar e gerenciar aplicativos para monitorar os pipelines de dados. Veja [Monitorar e gerenciar os pipelines do Azure Data Factory usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) para obter detalhes sobre a utilização do aplicativo.

> [AZURE.IMPORTANT] O arquivo de entrada é excluído quando a fatia é processada com êxito. Portanto, se você quiser executar novamente a fatia ou fazer o tutorial novamente, carregue o arquivo de entrada (input.log) na pasta inputdata do contêiner adfgetstarted.

## Modelo do ARM para criar um gateway
Aqui está um exemplo de modelo do ARM para criar um gateway lógico na parte traseira. Observe que você precisa instalar um gateway em seu computador local ou na VM IaaS do Azure e registrar o gateway no serviço Data Factory usando uma chave. Confira [Mover dados entre o local e a nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter detalhes.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "GatewayUsingArmDF",
	        "apiVersion": "2015-10-01",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "eastus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "gateways",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "name": "GatewayUsingARM",
	                    "properties": {
	                    	"description": "my gateway"
						}
	                }            
				]
	        }
	    ]
	}

O modelo cria uma data factory chamada GatewayUsingArmDF com um gateway chamado: GatewayUsingARM.

## Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como a transformação do Hive do HDInsight usado neste tutorial) com suporte do Azure Data Factory. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajudará a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajudará a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 

  

<!---HONumber=AcomDC_0803_2016-->