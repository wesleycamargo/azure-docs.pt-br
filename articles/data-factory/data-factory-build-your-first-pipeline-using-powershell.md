<properties
	pageTitle="Crie seu primeiro pipeline do Azure Data Factory usando o Azure PowerShell"
	description="Neste tutorial, você criará um pipeline de exemplo do Azure Data Factory usando o Azure PowerShell."
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
	ms.date="12/08/2015"
	ms.author="spelluru"/>

# Crie seu primeiro pipeline do Azure Data Factory usando o Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)


Neste artigo, você aprenderá a usar o Azure PowerShell para criar seu primeiro pipeline. Este tutorial consiste nas seguintes etapas:

1.	Criação do data factory.
2.	Criação dos serviços vinculados (armazenamentos de dados, cálculos) e dos conjuntos de dados
3.	Criação do pipeline.

> [AZURE.IMPORTANT]Este artigo não fornece uma visão geral conceitual do serviço Azure Data Factory. Para obter uma visão geral detalhada do serviço, consulte [Introdução ao Azure Data Factory](data-factory-introduction.md).
> 
> Este artigo não cobre todos os cmdlets de Data Factory. Consulte [Referência de cmdlet de Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) para obter uma documentação abrangente sobre os cmdlets de Data Factory.

## Pré-requisitos
Além dos pré-requisitos listados no tópico Visão Geral do Tutorial, você precisa instalar o seguinte:

- **PowerShell do Azure**. Siga as instruções no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a última versão do Azure PowerShell no computador. 

Se você estiver usando o Azure PowerShell com a **versão < 1.0**, você precisará usar os cmdlets que estão documentados [aqui][cmdlet-reference]. Você também precisará executar os seguintes comandos antes de usar os cmdlets do Data Factory:
 
1. Inicie o Azure PowerShell e execute os comandos a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	1. Execute **Add-AzureAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	2. Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	3. Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
4. Alterne para o modo AzureResourceManager, pois os cmdlets do Azure Data Factory estão disponíveis nesse modo: **Switch-AzureMode AzureResourceManager**.


## Etapa 1: Criação da data factory

Nesta etapa, é possível usar o Azure PowerShell para criar um Azure Data Factory denominado ADFTutorialDataFactoryPSH.

1. Inicie o Azure PowerShell e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	- Execute **Login-AzureRmAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.  
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription <Name of the subscription>** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
3. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir.

		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
4. Execute o cmdlet **New-AzureRmDataFactory** para criar um data factory denominado DataFactoryMyFirstPipelinePSH.  

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH –Location "West US"

	> [AZURE.IMPORTANT]O nome do Azure Data Factory deve ser globalmente exclusivo. Se você receber o erro: **O nome do data factory "DataFactoryMyFirstPipelinePSH" não está disponível**, altere o nome (por exemplo, seunomeADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
	> 
	> O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.

Nas etapas subsequentes, você aprenderá a criar serviços vinculados, conjuntos de dados e o pipeline que você usará neste tutorial.

## Etapa 2: Criação de serviços vinculados e conjuntos de dados
Nesta etapa, você vinculará sua conta de Armazenamento do Azure e um cluster do Azure HDInsight sob demanda ao data factory e criará um conjunto de dados para representar os dados de saída do processamento do Hive.

### Criar o serviço vinculado do armazenamento do Azure
1.	Crie um arquivo JSON chamado StorageLinkedService.json na pasta C:\\ADFGetStartedPSH com o conteúdo a seguir. Crie a pasta ADFGetStartedPSH se ela ainda não existir.

		{
		    "name": "StorageLinkedService",
		    "properties": {
		        "type": "AzureStorage",
		        "description": "",
		        "typeProperties": {
		            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		        }
		    }
		}

	Substitua **nome da conta** pelo nome de sua conta de armazenamento do Azure e **chave de conta** pela chave de acesso de sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, consulte [Exibir, copiar e regenerar chaves de acesso de armazenamento](http://azure.microsoft.com/documentation/articles/storage-create-storage-account/#view-copy-and-regenerate-storage-access-keys).

2.	No Azure PowerShell, alterne para a pasta ADFGetStartedPSH.
3.	Você pode usar o cmdlet **New-AzureDataFactoryLinkedService** para criar um serviço vinculado. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros *ResourceGroupName* e *DataFactoryName*. Como alternativa, é possível usar **Get-AzureRmDataFactory** para obter um objeto **DataFactory** e passar o objeto sem digitar *ResourceGroupName* e *DataFactoryName* sempre que você executar um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzureDataFactory** a uma variável **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

4.	Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se você não tivesse executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, precisaria especificar valores para os parâmetros *ResourceGroupName* e *DataFactoryName*, como demonstrado a seguir.

		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se você fechar o Azure PowerShell no meio do tutorial, precisará executar o cmdlet **Get-AzureRmDataFactory** na próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### Criar o serviço vinculado do Azure HDInsight
Agora, você criará um serviço vinculado para um cluster HDInsight do Azure sob demanda que será usado para executar o script Hive.

1.	Crie um arquivo JSON chamado HDInsightOnDemandLinkedService.json na pasta C:\\ADFGetStartedPSH com o conteúdo a seguir.


		{
		  "name": "HDInsightOnDemandLinkedService",
		  "properties": {
		    "type": "HDInsightOnDemand",
		    "typeProperties": {
		      "version": "3.2",
		      "clusterSize": 1,
		      "timeToLive": "00:30:00",
		      "linkedServiceName": "StorageLinkedService"
		    }
		  }
		}

	A tabela a seguir fornece descrições das propriedades de JSON usadas no trecho de código:

	Propriedade | Descrição
	-------- | -----------
	Versão | Isso especifica a versão do HDInsight criada como a 3.2.
	ClusterSize | Cria um cluster do HDInsight de um nó.
	TimeToLive | Especifica tempo ocioso de cluster HDInsight antes de ser excluído.
	linkedServiceName | Especifica a conta de armazenamento que será usada para armazenar os logs gerados pelo HDInsight
2. Execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado denominado HDInsightOnDemandLinkedService.

		New-AzureRmDataFactoryLinkedService $df -File .\HDInsightOnDemandLinkedService.json


### Criar o conjunto de dados de saída
Agora, você criará o conjunto de dados de saída para representar os dados armazenados no armazenamento de Blob do Azure.

1.	Crie um arquivo JSON denominado OutputTable.json na pasta C:\\ADFGetStartedPSH com o seguinte conteúdo:

		{
		  "name": "AzureBlobOutput",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "data/partitioneddata",
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
		}

	No exemplo anterior, você criou um conjunto de dados chamado **AzureBlobOutput** e especificou a estrutura dos dados produzidos pelo script do Hive. Além disso, você especifica que os resultados são armazenados no contêiner de blob denominado **data** e na pasta chamada **partitioneddata**. A seção **availability** especifica que o conjunto de dados de saída é produzido mensalmente.

2. Execute o comando a seguir no Azure PowerShell para criar o conjunto de dados do Data Factory.

		New-AzureRmDataFactoryDataset $df -File .\OutputTable.json

## Etapa 3: Criação do seu primeiro pipeline
Nesta etapa, você criará seu primeiro pipelines.

1.	Crie um arquivo JSON denominado MyFirstPipelinePSH.json na pasta C:\\ADFGetStartedPSH com o conteúdo a seguir:

	> [AZURE.IMPORTANT]Substitua **storageaccountname** pelo nome da sua conta de armazenamento no JSON.

		{
		  "name": "MyFirstPipeline",
		  "properties": {
		    "description": "My first Azure Data Factory pipeline",
		    "activities": [
		      {
		        "type": "HDInsightHive",
		        "typeProperties": {
		          "scriptPath": "script/partitionweblogs.hql",
		          "scriptLinkedService": "StorageLinkedService",
		          "defines": {
		            "partitionedtable": "wasb://data@<storageaccountname>.blob.core.windows.net/partitioneddata"
		          }
		        },
		        "outputs": [
		          {
		            "name": "AzureBlobOutput"
		          }
		        ],
		        "policy": {
		          "concurrency": 1,
		          "retry": 3
		        },
		        "name": "RunSampleHiveActivity",
		        "linkedServiceName": "HDInsightOnDemandLinkedService"
		      }
		    ],
		    "start": "2014-01-01",
		    "end": "2014-01-02"
		  }
		}

	No exemplo anterior, você cria um pipeline que consiste de uma única atividade que usa o Hive para processar dados em um cluster HDInsight.

	O arquivo de script do Hive, partitionweblogs.hql, é armazenado na conta de armazenamento do Azure (especificada pelo scriptLinkedService, chamado StorageLinkedService) e em um contêiner chamado **script**.

	A seção **defines** é usada para especificar as configurações de tempo de execução que serão passadas para o script de hive como valores de configuração do Hive (por exemplo, ${hiveconf:PartitionedData}).

	As propriedades **start** e **end** do pipeline especificam o período ativo do pipeline.

	Na atividade de JSON, você especifica que o script do Hive deve ser executado na computação especificada pelo serviço vinculado – **HDInsightOnDemandLinkedService**.
2. Execute o comando a seguir para criar a tabela de Data Factory.

		New-AzureRmDataFactoryPipeline $df -File .\MyFirstPipelinePSH.json
5. Parabéns, você criou com sucesso seu primeiro pipeline usando o Azure PowerShell!

### <a name="MonitorDataSetsAndPipeline"></a> Monitorar os conjuntos de dados e o pipeline
Nesta etapa, você usará o Azure PowerShell para monitorar o que está acontecendo em um Azure Data Factory.

1.	Execute **Get-AzureRmDataFactory** e atribua a saída a uma variável **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name DataFactoryMyFirstPipelinePSH

2.	Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

	Observe que o valor de StartDateTime especificado aqui é a mesma hora de início especificada no pipeline de JSON. Você deve ver saídas semelhantes às seguintes.

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : DataFactoryMyFirstPipelinePSH
		TableName         : AzureBlobOutput
		Start             : 1/1/2014 12:00:00 AM
		End               : 2/1/2014 12:00:00 AM
		RetryCount        : 0
		Status            : InProgress
		LatencyStatus     :
		LongRetryCount    : 0

3.	Execute **Get-AzureRmDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia específica.

		Get-AzureRmDataFactoryRun $df -DatasetName AzureBlobOutput -StartDateTime 2014-01-01

	Você deve ver saídas semelhantes às seguintes.

		Id                  : 4dbc6a07-537d-4005-a53e-6b9a4b844089_635241312000000000_635268096000000000_AzureBlobOutput
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : DataFactoryMyFirstPipelinePSH
		TableName           : AzureBlobOutput
		ProcessingStartTime : 7/7/2015 1:14:18 AM
		ProcessingEndTime   : 12/31/9999 11:59:59 PM
		PercentComplete     : 0
		DataSliceStart      : 1/1/2014 12:00:00 AM
		DataSliceEnd        : 2/1/2014 12:00:00 AM
		Status              : AllocatingResources
		Timestamp           : 7/7/2015 1:14:18 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : RunSampleHiveActivity
		PipelineName        : MyFirstPipeline
		Type                : Script

	Pode continuar executando este cmdlet até ver a fatia no estado Ready ou Failed. Quando a fatia estiver no estado Ready, verifique a pasta partitioneddata no contêiner de dados em seu armazenamento de blob para os dados de saída. Observe que a criação de um cluster do HDInsight sob demanda geralmente leva algum tempo.

Consulte [Referência de cmdlet de Data Factory](https://msdn.microsoft.com/library/azure/dn820234.aspx) para obter uma documentação abrangente sobre os cmdlets de Data Factory.



## Próximas etapas
Neste artigo, você criou um pipeline com uma atividade de transformação (atividade do HDInsight) que executa um script Hive em um cluster do HDInsight do Azure sob demanda. Para saber como usar uma Atividade de Cópia para copiar dados de um Blob do Azure para o SQL do Azure, confira [Tutorial: copiar dados de um Blob do Azure para o SQL do Azure](./data-factory-get-started.md).


[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx

<!---HONumber=AcomDC_1210_2015-->