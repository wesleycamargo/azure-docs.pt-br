<properties 
	pageTitle="Tutorial: criar e monitorar uma fábrica de dados do Azure usando o PowerShell do Azure" 
	description="Aprenda a usar o Azure PowerShell para criar e monitorar as fábricas de dados do Azure." 
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
	ms.date="05/04/2015" 
	ms.author="spelluru"/>

# Tutorial: Criar e monitorar uma fábrica de dados usando o PowerShell do Azure
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-get-started.md)
- [Using Data Factory Editor](data-factory-get-started-using-editor.md)
- [Using PowerShell](data-factory-monitor-manage-using-powershell.md)

O [Introdução ao Azure Data Factory][adf-get-started] tutorial mostra como criar e monitorar uma fábrica de dados do Azure usando o [Azure Preview Portal][azure-preview-portal]. Neste tutorial, você irá criar e monitorar uma fábrica de dados do Azure usando cmdlets do PowerShell do Azure. O pipeline na fábrica de dados que você cria neste tutorial copia dados de um Azure blob para um banco de dados do SQL Azure.

> [AZURE.NOTE]Este artigo não cobre todos os cmdlets de fábrica de dados. Consulte [dados de referência de Cmdlet de fábrica][cmdlet-reference] para documentação abrangente sobre os cmdlets de fábrica de dados.

##Pré-requisitos
Além de pré-requisitos listados no tópico de visão geral do Tutorial, você precisa ter instalado no computador do PowerShell do Azure. Se você não tiver já, baixe e instale [PowerShell do Azure][download-azure-powershell] em seu computador.

##Nesse tutorial
A tabela a seguir lista as etapas que você irá executar como parte do tutorial e suas descrições.

Etapa | Descrição
-----| -----------
[Etapa 1: Criar uma fábrica de dados do Azure](#CreateDataFactory) | Nesta etapa, você criará uma fábrica de dados do Azure denominada **ADFTutorialDataFactoryPSH**. 
[Etapa 2: Criar serviços vinculados](#CreateLinkedServices) | Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula um armazenamento do Azure e AzureSqlLinkedService vincula um banco de dados do SQL Azure para o ADFTutorialDataFactoryPSH.
[Etapa 3: Criar a entrada e saída de conjuntos de dados](#CreateInputAndOutputDataSets) | Nesta etapa, você definirá dois conjuntos de dados (* * EmpTableFromBlob * * e **EmpSQLTable**) que são usados como tabelas de entrada e saídas para o **cópia atividade** no ADFTutorialPipeline que você criará na próxima etapa.
[Etapa 4: Criar e executar um pipeline](#CreateAndRunAPipeline) | Nesta etapa, você criará um pipeline de chamado **ADFTutorialPipeline** na fábrica dados: **ADFTutorialDataFactoryPSH**. O pipeline terá um **cópia atividade** que copia dados de um Azure blob para uma tabela de banco de dados do Azure de saída.
[Etapa 5: Monitorar o pipeline e conjuntos de dados](#MonitorDataSetsAndPipeline) | Nesta etapa, você irá monitorar os conjuntos de dados e o canal usando o Azure PowerShell nesta etapa.

## <a name="CreateDataFactory"></a>Etapa 1: Criar uma fábrica de dados do Azure
Nesta etapa, você deve usar o PowerShell do Azure para criar uma fábrica de dados do Azure denominada **ADFTutorialDataFactoryPSH**.

1. Iniciar **PowerShell do Azure** e execute os seguintes comandos. Mantenha o Azure PowerShell aberta até o final deste tutorial. Se você fechar e reabrir, você precisará executar esses comandos novamente.
	- Executar **Add-AzureAccount** e insira o nome de usuário e senha que você usa para entrar no portal de visualização do Azure.  
	- Executar **Get-AzureSubscription** para exibir todas as assinaturas para essa conta.
	- Executar **Select-AzureSubscription** para selecionar a assinatura que você deseja trabalhar. Esta assinatura deve ser a mesma que você usou no Portal de Visualização do Azure. 
2. Alterne para o **AzureResourceManager** o modo como os cmdlets de fábrica de dados do Azure estão disponíveis nesse modo.

		Switch-AzureMode AzureResourceManager 
3. Criar um grupo de recursos do Azure denominado: **ADFTutorialResourceGroup** executando o comando a seguir.
   
		New-AzureResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você use o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você usar um grupo de recursos diferentes, você precisará usá-lo no lugar de ADFTutorialResourceGroup neste tutorial. 
4. Execute o **AzureDataFactory novo** cmdlet para criar uma fábrica de dados denominada: **ADFTutorialDataFactoryPSH**.  

		New-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"


	> [AZURE.NOTE]O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **dados fábrica nome "ADFTutorialDataFactoryPSH" não está disponível**, altere o nome (por exemplo, yournameADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial.

## <a name="CreateLinkedServices"></a>Etapa 2: Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um repositório de dados pode ser um armazenamento do Azure, banco de dados SQL ou um banco de dados de SQL Server local que contém os dados de entrada ou armazena dados de saída de um pipeline de fábrica de dados. Um serviço de computação é o serviço que processa os dados de entrada e gera dados de saída.

Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. StorageLinkedService vinculado links de serviço de uma conta de armazenamento do Azure e AzureSqlLinkedService vincula um banco de dados do SQL Azure para o alocador de dados: **ADFTutorialDataFactoryPSH**. Você criará um pipeline posteriormente neste tutorial que copia dados de um contêiner de blob StorageLinkedService em uma tabela SQL em AzureSqlLinkedService.

### Criar um serviço vinculado para uma conta de armazenamento do Azure
1.	Crie um arquivo JSON chamado **StorageLinkedService.json** no **C:\ADFGetStartedPSH** com o seguinte conteúdo. Crie a pasta ADFGetStartedPSH se ele ainda não existir.

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    }
		}
2.	No **PowerShell do Azure**, alterne para o **ADFGetStartedPSH** pasta. 
3.	Você pode usar o **AzureDataFactoryLinkedService novo** cmdlet para criar um serviço vinculado. Esse cmdlet e outros cmdlets de fábrica de dados usar neste tutorial requerem que você passar valores o **ResourceGroupName** e **DataFactoryName** parâmetros. Como alternativa, você pode usar **Get-AzureDataFactory** para obter um objeto DataFactory e passe o objeto sem digitar ResourceGroupName e DataFactoryName toda vez que você executa um cmdlet. Execute o seguinte comando para atribuir a saída a **Get-AzureDataFactory** cmdlet para uma variável: **$df**. 

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoyPSH

4.	Agora, execute o **AzureDataFactoryLinkedService novo** cmdlet para criar o serviço vinculado: **StorageLinkedService**.

		New-AzureDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se você não tivesse executado o **Get-AzureDataFactory** cmdlet e atribuído a saída para **$df** variável, você precisa especificar valores para os parâmetros ResourceGroupName e DataFactoryName da seguinte maneira.
		
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se você fechar o PowerShell do Azure no meio do tutorial, você irá ter executar o cmdlet Get-AzureDataFactory próxima vez que iniciar o Azure PowerShell para concluir o tutorial.

### Criar um serviço vinculado para um banco de dados SQL do Azure
1.	Crie um arquivo JSON chamado AzureSqlLinkedService.json com o seguinte conteúdo.

		{
		    "name": "AzureSqlLinkedService",
		    "properties":
		    {
		        "type": "AzureSqlLinkedService",
		        "connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
		    }
		}
2.	Execute o seguinte comando para criar um serviço vinculado. 
	
		New-AzureDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	> [AZURE.NOTE]Confirme se o **Permitir acesso aos serviços do Azure** configuração é ativada para o servidor do SQL Azure. Para verificar e ativá-lo, faça o seguinte:
	>
	> <ol>
<li>Clique em <b>Procurar</b> hub à esquerda e clique em <b>servidores SQL</b>.</li>
<li>Selecione o seu servidor e clique em <b>configurações</b> no <b>SQL SERVER</b> lâmina.</li>
<li>No <b>configurações</b> lâmina, clique em <b>Firewall</b>.</li>
<li>No <b>configurações de firewall</b> lâmina, clique em <b>ON</b> para <b>Permitir acesso aos serviços do Azure</b>.</li>
<li>Clique em <b>ACTIVE</b> hub à esquerda para alternar para o <b>Data Factory</b> lâmina estivesse em.</li>
</ol>

## <a name="CreateInputAndOutputDataSets"></a>Etapa 3: Criar a entrada e saída de tabelas

Na etapa anterior, você criou serviços vinculados **StorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados do SQL Azure para o alocador de dados: **ADFTutorialDataFactoryPSH**. Nesta etapa, você criará tabelas que representam a entrada e saída de dados para a atividade de cópia no pipeline, que você criará na próxima etapa.

Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados que tem suporte no momento. A tabela de entrada neste tutorial se refere a um contêiner de blob no armazenamento do Azure que StorageLinkedService pontos e a tabela de saída se refere a uma tabela do SQL no banco de dados SQL do Azure que aponta AzureSqlLinkedService para.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Ignore esta etapa se você verificou o tutorial de [Introdução ao Azure Data Factory][adf-get-started] artigo.

Você precisa executar as seguintes etapas para preparar o armazenamento de BLOBs do Azure e o banco de dados do SQL Azure para este tutorial.
 
* Crie um contêiner de blob denominado **adftutorial** no Azure armazenamento de blob que **StorageLinkedService** aponta. 
* Criar e carregar um arquivo de texto **emp.txt**, como um blob para o **adftutorial** contêiner. 
* Criar uma tabela chamada **emp** o banco de dados do SQL Azure no SQL Azure banco de dados que **AzureSqlLinkedService** aponta.


1. Inicie o bloco de notas, cole o seguinte texto e salve-o como **emp.txt** para **C:\ADFGetStartedPSH** pasta no disco rígido. 

        John, Doe
		Jane, Doe
				
2. Use ferramentas como [Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/) para criar o **adftutorial** contêiner e carregar o **emp.txt** arquivo ao contêiner.

    ![Gerenciador de Armazenamento do Azure][image-data-factory-get-started-storage-explorer]
3. Use o seguinte script SQL para criar a **emp** tabela em seu banco de dados do SQL Azure.  


        CREATE TABLE dbo.emp 
		(
			ID int IDENTITY(1,1) NOT NULL,
			FirstName varchar(50),
			LastName varchar(50),
		)
		GO

		CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID); 

	Se você tiver instalado no computador do SQL Server 2014: siga as instruções do [etapa 2: conectar-se ao banco de dados SQL do gerenciamento do Azure SQL banco de dados usando o SQL Server Management Studio][sql-management-studio] artigo para se conectar ao seu servidor do SQL Azure e execute o script SQL.

	Se você tiver o Visual Studio 2013 instalado no computador: no Portal de visualização do Azure ([http://portal.azure.com](http://portal.sazure.com)), clique em **Procurar** hub à esquerda, clique **servidores SQL**, selecione o banco de dados e clique em **aberto no Visual Studio** botão na barra de ferramentas para conectar-se ao seu servidor do SQL Azure e execute o script. Se o cliente não tem permissão para acessar o servidor do SQL Azure, você precisará configurar o firewall para o servidor do SQL Azure permitir o acesso do computador (endereço IP). Consulte o artigo acima para obter as etapas configurar o firewall para o servidor do SQL Azure.
		
### Criar tabela de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no armazenamento do Azure representado pelo **StorageLinkedService** vinculado de serviço. Esse contêiner de blob (* * adftutorial * *) contém os dados de entrada no arquivo: **emp.txt**.

1.	Crie um arquivo JSON chamado **EmpBlobTable.json** no **C:\ADFGetStartedPSH** pasta com o seguinte conteúdo:

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
	
	- local **tipo** é definido como **AzureBlobLocation**.
	- **linkedServiceName** é definido como **StorageLinkedService**. 
	- **folderPath** é definido como o **adftutorial** contêiner. É possível também especificar o nome de um blob dentro da pasta. Como você não está especificando o nome do blob, dados de todos os blobs no contêiner são considerados como um entrada de dados.  
	- formato **tipo** é definido como **TextFormat**
	- Há dois campos no arquivo de texto – **FirstName** e **LastName** – separados por um caractere de vírgula (* * columnDelimiter * *)	
	- O **disponibilidade** é definido como **por hora** (* * frequência * * é definido como **hora** e **intervalo** é definido como **1** ), de modo que o serviço de dados Factory irá procurar dados de entrada a cada hora, na pasta raiz no contêiner de blob (* * adftutorial * *) especificado.

	Se você não especificar um **fileName** para um **entrada** **tabela**, todos os arquivos/blobs da pasta entrada (* * folderPath * *) são considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn. Consulte os arquivos de exemplo de [tutorial][adf-tutorial] para obter exemplos.
 
	Se você não especificar um **fileName** para um **tabela de saída**, o gerados arquivos de **folderPath** são nomeados no formato a seguir: dados. < Guid >. txt (exemplo: Data.0a405f8a 93ff 4c6f b3be f69616f1df7a.txt.).

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

	> [AZURE.NOTE]Consulte [referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.

2.	Execute o seguinte comando para criar a tabela de dados de fábrica.

		New-AzureDataFactoryTable $df -File .\EmpBlobTable.json

### Criar tabela de saída
Nesta parte da etapa, você criará uma tabela de saída denominada **EmpSQLTable** que aponta para uma tabela SQL (* * emp * *) no banco de dados SQL do Azure que é representado pelo **AzureSqlLinkedService** vinculado de serviço. O pipeline copia os dados do blob de entrada para o **emp** tabela.

1.	Crie um arquivo JSON chamado **EmpSQLTable.json** no **C:\ADFGetStartedPSH** pasta com o conteúdo a seguir.
		
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
		            "type": "AzureSQLTableLocation",
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
	
	* local **tipo** é definido como **AzureSQLTableLocation**.
	* **linkedServiceName** é definido como **AzureSqlLinkedService**.
	* **tablename** é definido como **emp**.
	* Há três colunas – **ID**, **FirstName**, e **LastName** – na tabela emp no banco de dados, mas a ID é uma coluna de identidade, então você precisa apenas especificar **FirstName** e **LastName** aqui.
	* O **disponibilidade** é definido como **por hora** (* * frequência * * definido como **hora** e **intervalo** definida como **1**). O serviço de dados Factory irá gerar uma fatia de dados de saída cada hora o **emp** tabela no banco de dados do SQL Azure.

2.	Execute o seguinte comando para criar a tabela de dados de fábrica.
	
		New-AzureDataFactoryTable $df -File .\EmpSQLTable.json


## <a name="CreateAndRunAPipeline"></a>Etapa 4: Criar e executar um pipeline
Nesta etapa, você criará um pipeline com um **cópia atividade** que usa **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1.	Crie um arquivo JSON chamado **ADFTutorialPipeline.json** no **C:\ADFGetStartedPSH** pasta com o seguinte conteúdo: 

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

	- Na seção de atividades, há apenas uma atividade cuja **tipo** é definido como **CopyActivity**.
	- A atividade é definida como de entrada **EmpTableFromBlob** e de saída para a atividade é definida como **EmpSQLTable**.
	- No **transformação** seção, **BlobSource** é especificado como o tipo de fonte e **SqlSink** é especificado como o tipo de coletor.

	> [AZURE.NOTE]Substitua o valor da **Iniciar** propriedade com o dia atual e **final** valor com o dia seguinte. Ambos começam e datetimes final deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. O **final** tempo é opcional, mas usaremos neste tutorial. Se você não especificar o valor para o **final** propriedade, ele é calculado como "* * início + 48 horas * *". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor para o **final** propriedade. No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
	> [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971)
2.	Execute o seguinte comando para criar a tabela de dados de fábrica. 
		
		New-AzureDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

## <a name="MonitorDataSetsAndPipeline"></a>Etapa 5: Monitorar a conjuntos de dados e o pipeline
Nesta etapa, você usará o PowerShell do Azure para monitorar o que está acontecendo em uma fábrica de dados do Azure.

1.	Executar **Get-AzureDataFactory** e atribuir o resultado a uma variável $df.

		$df=Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Executar **Get-AzureDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

		Get-AzureDataFactorySlice $df -TableName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	> [AZURE.NOTE]Substituir a parte de data, mês e ano de **StartDateTime** parâmetro com o ano, mês e data. Isso deve corresponder a **Iniciar** valor no pipeline de JSON.

	Você deve ver 24 fatias, uma para cada hora de 12: 00 do dia atual para 12: 00 do dia seguinte.
	
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

3.	Executar **Get-AzureDataFactoryRun** para obter os detalhes da atividade for executada por um **específico** fatia. Altere o valor da **StartDateTime** parâmetro para corresponder a **Iniciar** tempo da fatia da saída acima. O valor de **StartDateTime** deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-03-03T22:00:00Z.

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

> [AZURE.NOTE]Consulte [dados de referência de Cmdlet de fábrica][cmdlet-reference] para documentação abrangente sobre os cmdlets de fábrica de dados.

## Próximas etapas

Artigo | Descrição
------ | ---------------
[Copiar dados com o Azure dados Factory - atividade de cópia][copy-activity] | Este artigo fornece uma descrição detalhada do **cópia atividade** usado neste tutorial. 
[Ativar pipelines de trabalhar com dados no local][use-onpremises-datasources] | Este artigo possui um passo a passo que mostra como copiar dados de um **banco de dados do SQL Server local** para um blob do Azure. 
[Usar o Pig e Hive com dados de fábrica][use-pig-and-hive-with-data-factory] | Este artigo possui um passo a passo que mostra como usar **HDInsight atividade** para executar um **hive/pig** script para processar dados de entrada para gerar dados de saída.
[Tutorial: Mover e processar os arquivos de log usando o alocador de dados][adf-tutorial] | Este artigo fornece uma **passo a passo de ponta a ponta** que mostra como implementar um **cenário do mundo real** usando a fábrica de dados do Azure para transformar dados de arquivos de log em ideias.
[Usar atividades personalizadas em uma fábrica de dados][use-custom-activities] | Este artigo fornece uma explicação passo a passo com instruções passo a passo para criar um **atividade personalizada** e usá-lo em um pipeline. 
[Solucionar problemas de fábrica de dados][troubleshoot] | Este artigo descreve como **solucionar** problemas de fábrica de dados do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência de Cmdlet de fábrica de dados do Azure][cmdlet-reference] | Esse conteúdo de referência com detalhes sobre todos os **Data Factory cmdlets**.
[Referência do desenvolvedor de fábrica de dados do Azure][developer-reference] | A referência do desenvolvedor tem o conteúdo de referência abrangente de cmdlets, o script JSON, funções, etc... 

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

<!---HONumber=GIT-SubDir--> 