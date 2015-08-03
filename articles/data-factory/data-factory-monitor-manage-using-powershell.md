<properties 
	pageTitle="Tutorial: Criar e monitorar uma Azure Data Factory usando o PowerShell do Azure" 
	description="Aprenda a usar o PowerShell do Azure para criar e monitorar Azure data factories." 
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
	ms.date="07/17/2015" 
	ms.author="spelluru"/>

# Tutorial: Criar e monitorar uma data factory usando o PowerShell do Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)
- [Using Visual Studio](data-factory-get-started-using-vs.md)


O tutorial [Introdução à Azure Data Factory][adf-get-started] mostra como criar e monitorar uma Azure Data Factory usando o [Portal de Visualização do Azure][azure-preview-portal]. Neste tutorial, você vai criar e monitorar uma Azure Data Factory usando cmdlets do PowerShell do Azure. O pipeline na data factory que você cria neste tutorial copia dados de um blob do Azure em um banco de dados SQL do Azure.

> [AZURE.NOTE]Este artigo não cobre todos os cmdlets de Data Factory. Consulte [Referência de cmdlet de Data Factory][cmdlet-reference] para obter uma documentação abrangente sobre os cmdlets de Data Factory.

##Pré-requisitos
Além dos pré-requisitos listados no tópico Visão geral do tutorial, você precisa ter o PowerShell do Azure instalado no computador. Se ainda não tiver o [PowerShell do Azure][download-azure-powershell] no seu computador, baixe-o e instale-o.

##Nesse tutorial
A tabela a seguir lista as etapas que você vai executar como parte do tutorial e as suas descrições.

Etapa | Descrição
-----| -----------
[Etapa 1: Criar uma Azure Data Factory](#CreateDataFactory) | Nesta etapa, você criará uma Azure Data Factory denominada **ADFTutorialDataFactoryPSH**. 
[Etapa 2: Criar serviços vinculados](#CreateLinkedServices) | Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula um armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à ADFTutorialDataFactoryPSH.
[Etapa 3: Criar conjuntos de dados de entrada e saída](#CreateInputAndOutputDataSets) | Nesta etapa, você definirá dois conjuntos de dados (**EmpTableFromBlob** e **EmpSQLTable**) que são usados como tabelas de entrada e saída para a **Atividade de Cópia** no ADFTutorialPipeline que você criará na próxima etapa.
[Etapa 4: Criar e executar um pipeline](#CreateAndRunAPipeline) | Nesta etapa, você criará um pipeline chamado **ADFTutorialPipeline** na data factory: **ADFTutorialDataFactoryPSH**. O pipeline terá uma **Atividade de Cópia** que copia dados de um blob do Azure em uma tabela de banco de dados de saída do Azure.
[Etapa 5: Monitorar conjuntos de dados e pipeline](#MonitorDataSetsAndPipeline) | Nesta etapa, você vai monitorar os conjuntos de dados e o pipeline usando o PowerShell do Azure.

## <a name="CreateDataFactory"></a>Etapa 1: Criar uma Azure Data Factory
Nesta etapa, é possível utilizar o PowerShell do Azure para criar uma Azure Data Factory denominada **ADFTutorialDataFactoryPSH**.

1. Inicie o **PowerShell do Azure** e execute os comandos a seguir. Mantenha o PowerShell do Azure aberto até o final deste tutorial. Se você fechá-lo e reabri-lo, será preciso executar esses comandos novamente.
	- Execute **Add-AzureAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal de Visualização do Azure.  
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure. 
2. Alterne para o modo **AzureResourceManager**, pois os cmdlets da Azure Data Factory estão disponíveis nesse modo.

		Switch-AzureMode AzureResourceManager 
3. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você utiliza o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial. 
4. Execute o cmdlet **New-AzureDataFactory** para criar uma data factory denominada **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactoryPSH" não está disponível**, altere o nome (por exemplo, yournameADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial.

## <a name="CreateLinkedServices"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um repositório de dados pode ser um armazenamento do Azure, Banco de Dados SQL do Azure ou um banco de dados SQL Server local que contém os dados de entrada ou armazena dados de saída para um pipeline de Data Factory. Um serviço de computação é o serviço que processa os dados de entrada e gera dados de saída.

Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O serviço vinculado StorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryPSH**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no StorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### Criar um serviço vinculado para uma conta de armazenamento do Azure
1.	Crie um arquivo JSON chamado **StorageLinkedService.json** em **C:\\ADFGetStartedPSH** com o conteúdo a seguir. Crie a pasta ADFGetStartedPSH se ela ainda não existir.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	No **PowerShell do Azure**, alterne para a pasta **ADFGetStartedPSH**. 
3.	É possível usar o cmdlet **New-AzureDataFactoryLinkedService** para criar um serviço vinculado. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, é possível usar **Get-AzureDataFactory** para obter um objeto DataFactory e passar o objeto sem digitar ResourceGroupName e DataFactoryName toda vez que você executa um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzureDataFactory** a uma variável: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Agora, execute o cmdlet **New-AzureDataFactoryLinkedService** para criar o serviço vinculado: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se você não tivesse executado o cmdlet **Get-AzureDataFactory** e atribuído a saída à variável **$df**, você precisaria especificar valores para os parâmetros ResourceGroupName e DataFactoryName como demonstrado a seguir.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se você fechar o PowerShell do Azure no meio do tutorial, será preciso executar o cmdlet Get-AzureDataFactory da próxima vez que iniciar o PowerShell do Azure para concluir o tutorial.

### Criar um serviço vinculado para um banco de dados SQL do Azure
1.	Crie um arquivo JSON chamado AzureSqlLinkedService.json com o conteúdo a seguir.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Execute o comando a seguir para criar um serviço vinculado. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Confirme se a configuração **Permitir acesso aos serviços do Azure** está ATIVADA para o servidor SQL do Azure. Para verificar e ativar essa configuração, faça o seguinte:

	1. Clique no hub **PROCURAR** à esquerda e clique em **Servidores SQL**.
	2. Selecione seu servidor e clique em **CONFIGURAÇÕES** na folha SQL SERVER.
	3. Na folha **CONFIGURAÇÕES**, clique em **Firewall**.
	4. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
	5. Clique no hub **ATIVO** à esquerda para alternar para a folha **Data Factory** em que você estava.
	

## <a name="CreateInputAndOutputDataSets"></a>Etapa 3: Criar tabelas de entrada e saída

Na etapa anterior, você criou os serviços vinculados **StorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryPSH**. Nesta etapa, você criará tabelas que representam os dados de entrada e saída da Atividade de Cópia no pipeline que será criado na próxima etapa.

Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados que tem suporte no momento. A tabela de entrada neste tutorial refere-se a um contêiner de blob no Armazenamento do Azure apontado pelo StorageLinkedService, enquanto a tabela de saída refere-se a uma tabela SQL no banco de dados SQL do Azure apontada pelo AzureSqlLinkedService.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Pule esta etapa se tiver lido o tutorial do artigo [Introdução à Azure Data Factory][adf-get-started].

Você precisa realizar as etapas a seguir para preparar seu armazenamento de blob do Azure e o banco de dados SQL do Azure para este tutorial.
 
* Crie um contêiner de blob denominado **adftutorial** no armazenamento de blob do Azure apontado pelo **StorageLinkedService**. 
* Crie e carregue um arquivo de texto, **emp.txt**, como um blob para o contêiner **adftutorial**. 
* Crie uma tabela denominada **emp** no Banco de Dados SQL do Azure apontada pelo **AzureSqlLinkedService**.


1. Inicie o Bloco de Notas, cole o texto a seguir e salve-o como **emp.txt** na pasta **C:\\ADFGetStartedPSH** no disco rígido. 

        John, Doe
		Jane, Doe
				
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure][image-data-factory-get-started-storage-explorer]
3. Use o script SQL a seguir para criar a tabela **emp** no seu Banco de Dados SQL do Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Se você tiver o SQL Server 2014 instalado no computador: siga as instruções da [Etapa 2: Conectar-se ao Banco de Dados SQL do artigo Gerenciando o Banco de Dados SQL do Azure usando o SQL Server Management Studio][sql-management-studio] para se conectar ao servidor SQL do Azure e executar o script SQL.

	Se você tiver o Visual Studio 2013 instalado no computador: no Portal de Visualização do Azure ([http://portal.azure.com](http://portal.sazure.com)), clique no hub **PROCURAR** à esquerda, clique em **Servidores SQL**, selecione o banco de dados e clique no botão **Abrir no Visual Studio** na barra de ferramentas para se conectar ao seu servidor SQL do Azure e executar o script. Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Consulte o artigo acima para saber como configurar o firewall do servidor SQL do Azure.
		
### Criar tabela de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **StorageLinkedService**. Esse contêiner de blob (**adftutorial**) contém os dados de entrada no arquivo **emp.txt**.

1.	Crie um arquivo JSON denominado **EmpBlobTable.json** na pasta **C:\\ADFGetStartedPSH** com o seguinte conteúdo:

		{
	    	"name": "EmpTableFromBlob",
	        "properties":
	        {
	            "structure":  
	                [ 
	                { "name": "FirstName", "type": "String"},
	                { "name": "LastName", "type": "String"}
	            ],
	            "location": 
	            {
	                "type": "AzureBlobLocation",
	                "folderPath": "adftutorial/",
	                "format":
	                {
	                    "type": "TextFormat",
	                    "columnDelimiter": ","
	                },
	                "linkedServiceName": "StorageLinkedService"
	            },
	            "availability": 
	            {
	                "frequency": "hour",
	                "interval": 1,
	                "waitonexternal": {}
	                }
	        }
		}
	
	Observe o seguinte:
	
	- O **type** do local é definido como **AzureBlobLocation**.
	- **linkedServiceName** é definido como **StorageLinkedService**. 
	- **folderPath** é definido como o contêiner **adftutorial**. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
	- O **type** de formato é definido como **TextFormat**
	- Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)	
	- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1** ), de modo que o serviço Data Factory vai procurar dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado.

	Se você não especificar um **fileName** para uma **tabela** de **entrada**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos do [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.<Guid>.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

	Para definir **folderPath** e **fileName** dinamicamente com base no horário **SliceStart**, use a propriedade **partitionedBy**. No exemplo a seguir, folderPath usa o ano, mês e dia de SliceStart (hora de início da fatia que está sendo processada) e fileName usa a hora de SliceStart. Por exemplo, se uma fatia é produzida para 2014-10-20T08:00:00, o folderName é definido como wikidatagateway/wikisampledataout/2014/10/20 e o fileName é definido como 08.csv.

	  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
        "fileName": "{Hour}.csv",
        "partitionedBy": 
        [
        	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
            { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
            { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
            { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
        ],

	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

2.	Execute o comando a seguir para criar a tabela de Data Factory.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Criar tabela de saída
Nesta parte da etapa, você criará uma tabela de saída denominada **EmpSQLTable** que aponta para uma tabela SQL (**emp**) no banco de dados SQL do Azure que é representada pelo serviço vinculado **AzureSqlLinkedService**. O pipeline copia os dados do blob de entrada na tabela **emp**.

1.	Crie um arquivo JSON denominado **EmpSQLTable.json** na pasta **C:\\ADFGetStartedPSH** com o conteúdo a seguir.
		
		{
		    "name": "EmpSQLTable",
		    "properties":
		    {
		        "structure":
		        [
		            { "name": "FirstName", "type": "String"},
		            { "name": "LastName", "type": "String"}
		        ],
		        "location":
		        {
		            "type": "AzureSqlTableLocation",
		            "tableName": "emp",
		            "linkedServiceName": "AzureSqlLinkedService"
		        },
		        "availability": 
		        {
		            "frequency": "Hour",
		            "interval": 1            
		        }
		    }
		}

     Observe o seguinte:
	
	* o **tipo** de local é definido para **AzureSqlTableLocation**.
	* **linkedServiceName** é definido como **AzureSqlLinkedService**.
	* **tablename** está definido como **emp**.
	* Há três colunas – **ID**, **FirstName** e **LastName** – na tabela emp no banco de dados, mas ID é uma coluna de identidade, portanto, você precisa especificar somente **FirstName** e **LastName**.
	* A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

2.	Execute o comando a seguir para criar a tabela de Data Factory.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Etapa 4: Criar e executar um pipeline
Nesta etapa, você cria um pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1.	Crie um arquivo JSON denominado **ADFTutorialPipeline.json** na pasta **C:\\ADFGetStartedPSH** com o conteúdo a seguir: 

		{
		    "name": "ADFTutorialPipeline",
		    "properties":
		    {   
		        "description" : "Copy data from an Azure blob to an Azure SQL table",
		        "activities":   
		        [
		            {
		                "name": "CopyFromBlobToSQL",
		                "description": "Copy data from the adftutorial blob container to emp SQL table",
		                "type": "CopyActivity",
		                "inputs": [ {"name": "EmpTableFromBlob"} ],
		                "outputs": [ {"name": "EmpSQLTable"} ],     
		                "transformation":
		                {
		                    "source":
		                    {                               
		                        "type": "BlobSource"
		                    },
		                    "sink":
		                    {
		                        "type": "SqlSink"
		                    }   
		                },
		                "Policy":
		                {
		                    "concurrency": 1,
		                    "executionPriorityOrder": "NewestFirst",
		                    "style": "StartOfInterval",
		                    "retry": 0,
		                    "timeout": "01:00:00"
		                }       
		            }
		        ],
		        "start": "2015-03-03T00:00:00Z",
		        "end": "2015-03-04T00:00:00Z"
		    }
		}  

	Observe o seguinte:

	- Na seção de atividades, há apenas uma atividade cujo **type** é definido como **CopyActivity**.
	- A entrada da atividade é definida como **EmpTableFromBlob** e a saída da atividade é definida como **EmpSQLTable**.
	- Na seção **transformation**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

	Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **end** é opcional, mas nós o usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.
2.	Execute o comando a seguir para criar a tabela de Data Factory. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

## <a name="MonitorDataSetsAndPipeline"></a>Etapa 5: Monitorar os conjuntos de dados e o pipeline
Nesta etapa, você usará o PowerShell do Azure para monitorar o que está acontecendo em uma Azure Data Factory.

1.	Execute **Get-AzureDataFactory** e atribua o resultado a uma variável $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Execute **Get-AzureDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Substitua a parte de data, mês e ano do parâmetro **StartDateTime** pelo ano, mês e data atuais. Isso deve corresponder ao valor de **Start** no pipeline de JSON.

	Você deve ver 24 fatias, uma para cada hora desde 12h do dia atual até 12h do dia seguinte.
	
	**Primeira fatia:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     :
		LongRetryCount    : 0

	**Última fatia:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : PendingExecution
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Executar **Get-AzureDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia **específica**. Altere o valor do parâmetro **StartDateTime** para corresponder a hora de **Start** da fatia da saída acima. O valor de **StartDateTime** deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-03-03T22:00:00Z.

		Get-AzureDataFactoryRun $df -TableName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

	Você deverá ver uma saída semelhante ao seguinte:

		Id                  : 3404c187-c889-4f88-933b-2a2f5cd84e90_635614488000000000_635614524000000000_EmpSQLTable
		ResourceGroupName   : ADFTutorialResourceGroup
		DataFactoryName     : ADFTutorialDataFactoryPSH
		TableName           : EmpSQLTable
		ProcessingStartTime : 3/3/2015 11:03:28 PM
		ProcessingEndTime   : 3/3/2015 11:04:36 PM
		PercentComplete     : 100
		DataSliceStart      : 3/8/2015 10:00:00 PM
		DataSliceEnd        : 3/8/2015 11:00:00 PM
		Status              : Succeeded
		Timestamp           : 3/8/2015 11:03:28 PM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        :
		ActivityName        : CopyFromBlobToSQL
		PipelineName        : ADFTutorialPipeline
		Type                : Copy

Consulte [Referência de cmdlet de Data Factory][cmdlet-reference] para obter uma documentação abrangente sobre os cmdlets de Data Factory.

## Próximas etapas

Artigo | Descrição
------ | ---------------
[Copiar dados com a Azure Data Factory — Atividade de Cópia][copy-activity] | Este artigo fornece uma descrição detalhada da **Atividade de Cópia** utilizada neste tutorial. 
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo apresenta um passo a passo que mostra como copiar dados de um **banco de dados de SQL Server local** em um blob do Azure. 
[Usar o Pig e o Hive com a Data Factory][use-pig-and-hive-with-data-factory] | Este artigo apresenta um passo a passo que mostra como usar a **Atividade de HDInsight** para executar um script do **hive/pig** para processar dados de entrada a fim de gerar dados de saída.
[Tutorial: Mover e processar arquivos de log usando a Data Factory][adf-tutorial] | Este artigo fornece um **passo a passo completo** que mostra como implantar um **cenário do mundo real** usando a Azure Data Factory para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em uma Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma **atividade personalizada** e usá-la em um pipeline. 
[Solucionar problemas da Data Factory][troubleshoot] | Este artigo descreve como **solucionar problemas** da Azure Data Factory. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência de cmdlet da Azure Data Factory][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os **cmdlets da Data Factory**.
[Referência do desenvolvedor da Azure Data Factory][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc… 

[copy-activity]: data-factory-copy-activity.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/dn820234.aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[data-factory-create-storage]: ../storage-create-storage-account.md

[adf-get-started]: data-factory-get-started.md
[azure-preview-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-create-sql-database]: ../sql-database-create-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-monitor-manage-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database-manage-azure-ssms.md#Step2
 

<!---HONumber=July15_HO4-->