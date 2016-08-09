<properties 
	pageTitle="Tutorial: Criar um pipeline com a Atividade de Cópia usando o Azure PowerShell" 
	description="Neste tutorial, você criará um pipeline do Azure Data Factory com uma Atividade de Cópia usando o Azure PowerShell." 
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
	ms.topic="get-started-article" 
	ms.date="08/01/2016" 
	ms.author="spelluru"/>

# Tutorial: Criar um pipeline com a Atividade de Cópia usando o Azure PowerShell
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

O tutorial [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) mostra como criar e monitorar uma data factory do Azure usando o [Portal do Azure][azure-portal]. Neste tutorial, você vai criar e monitorar uma Azure Data Factory usando cmdlets do PowerShell do Azure. O pipeline no data factory que você cria neste tutorial usa uma atividade de cópia para copiar dados de um blob do Azure para um banco de dados SQL do Azure.

A Atividade de Cópia executa a movimentação de dados no Azure Data Factory e é capacitada por um serviço globalmente disponível que pode copiar dados entre vários armazenamentos de dados de forma segura, confiável e escalonável. Confira o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

> [AZURE.IMPORTANT] 
Leia o artigo [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e conclua as etapas de pré-requisito antes de executar este tutorial.
>   
> Este artigo não cobre todos os cmdlets de Data Factory. Consulte [Referência de cmdlet de Data Factory](https://msdn.microsoft.com/library/dn820234.aspx) para obter uma documentação abrangente sobre os cmdlets de Data Factory.
  

##Pré-requisitos
Além dos pré-requisitos listados no tópico Visão Geral do Tutorial, você precisa instalar o seguinte:

- **PowerShell do Azure**. Siga as instruções no artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a última versão do Azure PowerShell no computador.

Se você estiver usando o Azure PowerShell com a **versão < 1.0**, precisará usar os cmdlets que estão documentados [aqui][old-cmdlet-reference]. Você também precisará executar os seguintes comandos antes de usar os cmdlets do Data Factory:

1. Inicie o Azure PowerShell e execute os comandos a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	1. Execute **Add-AzureAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	2. Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	3. Execute **Select-AzureSubscription** para selecionar a assinatura com a qual deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
4. Alterne para o modo AzureResourceManager, pois os cmdlets do Azure Data Factory estão disponíveis nesse modo: **Switch-AzureMode AzureResourceManager**.
  

##Neste tutorial
A tabela a seguir lista as etapas que você vai executar como parte do tutorial e as suas descrições.

Etapa | Descrição
-----| -----------
[Criar uma Data Factory do Azure](#create-data-factory) | Nesta etapa, você criará uma Azure Data Factory denominada **ADFTutorialDataFactoryPSH**. 
[Criar serviços vinculados](#create-linked-services) | Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O StorageLinkedService vincula um armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à ADFTutorialDataFactoryPSH.
[Criar conjuntos de dados de entrada e saída](#create-datasets) | Nesta etapa, você definirá dois conjuntos de dados (**EmpTableFromBlob** e **EmpSQLTable**) que são usados como tabelas de entrada e saída para a **Atividade de Cópia** no ADFTutorialPipeline que você criará na próxima etapa.
[Criar e executar uma pipeline](#create-pipeline) | Nesta etapa, você criará um pipeline chamado **ADFTutorialPipeline** na data factory: **ADFTutorialDataFactoryPSH**. O pipeline terá uma **Atividade de Cópia** que copia dados de um blob do Azure em uma tabela de banco de dados de saída do Azure.
[Conjuntos de dados do monitor e pipeline](#monitor-pipeline) | Nesta etapa, você vai monitorar os conjuntos de dados e o pipeline usando o PowerShell do Azure.

## Criar um data factory
Nesta etapa, é possível utilizar o PowerShell do Azure para criar uma Azure Data Factory denominada **ADFTutorialDataFactoryPSH**.

1. Inicie o Azure PowerShell e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechá-la e reabri-la, precisará executar esses comandos novamente.
	- Execute **Login-AzureRmAccount** e insira o nome de usuário e a senha que você usa para entrar no Portal do Azure.
	- Execute **Get-AzureSubscription** para exibir todas as assinaturas dessa conta.
	- Execute **Select-AzureSubscription <Nome da assinatura> ** para selecionar a assinatura com a qual você deseja trabalhar. Esta assinatura deve ser igual à que você usou no portal do Azure.
3. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir.
   
		New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

	Algumas das etapas neste tutorial supõem que você utiliza o grupo de recursos denominado **ADFTutorialResourceGroup**. Se você utilizar um grupo de recursos diferente, será necessário usá-lo no lugar de ADFTutorialResourceGroup neste tutorial.
4. Execute o cmdlet **New-AzureRmDataFactory** para criar um data factory denominada **ADFTutorialDataFactoryPSH**.

		New-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH –Location "West US"

	
Observe o seguinte:
 
- O nome da data factory do Azure deve ser globalmente exclusivo. Se você receber o erro: **O nome da data factory "ADFTutorialDataFactoryPSH" não está disponível**, altere o nome (por exemplo, yournameADFTutorialDataFactoryPSH). Use esse nome em vez de ADFTutorialFactoryPSH ao executar as etapas neste tutorial. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
- Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure
- O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se publicamente visível.
- Se você receber o erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente:

	- No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado.
	
			Get-AzureRmResourceProvider
	- Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie uma data factory no portal do Azure. Isso registra automaticamente o provedor para você.

## Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um repositório de dados pode ser um armazenamento do Azure, Banco de Dados SQL do Azure ou um banco de dados SQL Server local que contém os dados de entrada ou armazena dados de saída para um pipeline de Data Factory. Um serviço de computação é o serviço que processa os dados de entrada e gera dados de saída.

Nesta etapa, você criará dois serviços vinculados: **StorageLinkedService** e **AzureSqlLinkedService**. O serviço vinculado StorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryPSH**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no StorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### Criar um serviço vinculado para uma conta de armazenamento do Azure
1.	Crie um arquivo JSON chamado **StorageLinkedService.json** em **C:\\ADFGetStartedPSH** com o conteúdo a seguir. Crie a pasta ADFGetStartedPSH se ela ainda não existir.

			{
		  		"name": "StorageLinkedService",
		  		"properties": {
	    			"type": "AzureStorage",
		    		"typeProperties": {
		      			"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
		    		}
		  		}
			}

	Substitua **nomedaconta** pelo nome da sua conta de armazenamento e **chavedaconta** pela chave da sua conta de armazenamento do Azure.
2.	No **PowerShell do Azure**, alterne para a pasta **ADFGetStartedPSH**.
3.	Você pode usar o cmdlet **New-AzureRmDataFactoryLinkedService** para criar um serviço vinculado. Esse cmdlet e outros cmdlets de Data Factory que você usa neste tutorial exigem que os valores sejam passados aos parâmetros **ResourceGroupName** e **DataFactoryName**. Como alternativa, você pode usar **Get-AzureRmDataFactory** para obter um objeto DataFactory e passá-lo sem digitar ResourceGroupName e DataFactoryName sempre que executar um cmdlet. Execute o comando a seguir para atribuir a saída do cmdlet **Get-AzureRmDataFactory** a uma variável **$df**.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH

4.	Agora, execute o cmdlet **New-AzureRmDataFactoryLinkedService** para criar o serviço vinculado: **StorageLinkedService**.

		New-AzureRmDataFactoryLinkedService $df -File .\StorageLinkedService.json

	Se você não tivesse executado o cmdlet **Get-AzureRmDataFactory** e atribuído a saída à variável **$df**, precisaria especificar valores para os parâmetros ResourceGroupName e DataFactoryName, como demonstrado a seguir.
		
		New-AzureRmDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactoryPSH -File .\StorageLinkedService.json

	Se você fechar o Azure PowerShell no meio do tutorial, será preciso executar o cmdlet Get-AzureRmDataFactory da próxima vez que iniciar o Azure PowerShell para concluí-lo.

### Criar um serviço vinculado para um banco de dados SQL do Azure
1.	Crie um arquivo JSON chamado AzureSqlLinkedService.json com o conteúdo a seguir.

			{
				"name": "AzureSqlLinkedService",
				"properties": {
					"type": "AzureSqlDatabase",
					"typeProperties": {
				      	"connectionString": "Server=tcp:<server>.database.windows.net,1433;Database=<databasename>;User ID=user@server;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
					}
		  		}
			}

	Substitua **servername**, **databasename**, **username@servername** e **password** pelos nomes de seu servidor SQL do Azure, banco de dados, conta de usuário e senha.

2.	Execute o comando a seguir para criar um serviço vinculado.
	
		New-AzureRmDataFactoryLinkedService $df -File .\AzureSqlLinkedService.json

	Confirme se a configuração **Permitir acesso aos serviços do Azure** está ATIVADA para o servidor SQL do Azure. Para verificar e ativar essa configuração, faça o seguinte:

	1. Clique no hub **PROCURAR** à esquerda e clique em **Servidores SQL**.
	2. Selecione seu servidor e clique em **CONFIGURAÇÕES** na folha SQL SERVER.
	3. Na folha **CONFIGURAÇÕES**, clique em **Firewall**.
	4. Na folha **Configurações de Firewall**, clique em **ATIVADO** para **Permitir acesso aos serviços do Azure**.
	5. Clique no hub **ATIVO** à esquerda para alternar para a folha **Data Factory** em que você estava.
	

## Criar conjuntos de dados

Na etapa anterior, você criou os serviços vinculados **StorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure à data factory: **ADFTutorialDataFactoryPSH**. Nesta etapa, você criará conjuntos de dados que representam os dados de entrada e saída da Atividade de Cópia no pipeline que você criará na próxima etapa.

Uma tabela é um conjunto de dados retangular e é o único tipo de conjunto de dados que tem suporte no momento. A tabela de entrada neste tutorial refere-se a um contêiner de blob no Armazenamento do Azure apontado pelo StorageLinkedService, enquanto a tabela de saída refere-se a uma tabela SQL no banco de dados SQL do Azure apontada pelo AzureSqlLinkedService.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Ignore esta etapa se tiver lido o tutorial do artigo [Copiar dados do Armazenamento de Blobs para o Banco de Dados SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Você precisa realizar as etapas a seguir para preparar seu armazenamento de blob do Azure e o banco de dados SQL do Azure para este tutorial.
 
* Crie um contêiner de blob denominado **adftutorial** no armazenamento de blob do Azure apontado pelo **StorageLinkedService**.
* Crie e carregue um arquivo de texto, **emp.txt**, como um blob para o contêiner **adftutorial**.
* Crie uma tabela denominada **emp** no Banco de Dados SQL do Azure apontada pelo **AzureSqlLinkedService**.


1. Inicie o Bloco de Notas, cole o texto a seguir e salve-o como **emp.txt** na pasta **C:\\ADFGetStartedPSH** no disco rígido.

        John, Doe
		Jane, Doe
				
2. Use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/) para criar o contêiner **adftutorial** e carregar o arquivo **emp.txt** no contêiner.

    ![Gerenciador de Armazenamento do Azure](media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png)
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

	Se você tem o Visual Studio 2013 instalado no computador: no Portal do Azure ([http://portal.azure.com](http://portal.sazure.com)), clique no hub **PROCURAR** à esquerda, clique em **Servidores SQL**, selecione o banco de dados e clique no botão **Abrir no Visual Studio** na barra de ferramentas para se conectar ao seu servidor SQL do Azure e executar o script. Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Consulte o artigo acima para obter as etapas para configurar o firewall para o SQL Server do Azure.
		
### Criar conjunto de dados de entrada 
Uma tabela é um conjunto de dados retangular e tem um esquema. Nesta etapa, você criará uma tabela denominada **EmpBlobTable** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **StorageLinkedService**. Esse contêiner de blob (**adftutorial**) contém os dados de entrada no arquivo: **emp.txt**.

1.	Crie um arquivo JSON denominado **EmpBlobTable.json** na pasta **C:\\ADFGetStartedPSH** com o seguinte conteúdo:

			{
			  "name": "EmpTableFromBlob",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureBlob",
			    "linkedServiceName": "StorageLinkedService",
			    "typeProperties": {
				  "fileName": "emp.txt",
			      "folderPath": "adftutorial/",
			      "format": {
			        "type": "TextFormat",
			        "columnDelimiter": ","
			      }
			    },
			    "external": true,
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}
	
	Observe o seguinte:
	
	- O **tipo** do conjunto de dados foi definido como **AzureBlob**.
	- **linkedServiceName** é definido como **StorageLinkedService**.
	- **folderPath** é definido como o contêiner **adftutorial**.
	- **fileName** está definido como **emp.txt**. Se você não especificar o nome do blob, os dados de todos os blobs no contêiner serão considerados como dados de entrada.
	- O **type** de formato é definido como **TextFormat**
	- Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)
	- A **disponibilidade** é definida como **por hora** (a **frequência** é definida como **hora** e o **intervalo** é definido como **1** ), de modo que o serviço Data Factory procure dados de entrada a cada hora na pasta raiz do contêiner de blob (**adftutorial**) especificado.

	Se você não especificar um **fileName** para uma **tabela** de **entradas**, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada de asn.
 
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

2.	Execute o comando a seguir para criar o conjunto de dados de Data Factory.

		New-AzureRmDataFactoryDataset $df -File .\EmpBlobTable.json

### Criar conjunto de dados de saída
Nesta parte da etapa, você cria uma tabela de saída chamada **EmpSQLTable** que aponta para uma tabela SQL (**emp**) no banco de dados SQL do Azure, representada pelo serviço vinculado **AzureSqlLinkedService**. O pipeline copia os dados do blob de entrada na tabela **emp**.

1.	Crie um arquivo JSON denominado **EmpSQLTable.json** na pasta **C:\\ADFGetStartedPSH** com o conteúdo a seguir.
		
			{
			  "name": "EmpSQLTable",
			  "properties": {
			    "structure": [
			      {
			        "name": "FirstName",
			        "type": "String"
			      },
			      {
			        "name": "LastName",
			        "type": "String"
			      }
			    ],
			    "type": "AzureSqlTable",
			    "linkedServiceName": "AzureSqlLinkedService",
			    "typeProperties": {
			      "tableName": "emp"
			    },
			    "availability": {
			      "frequency": "Hour",
			      "interval": 1
			    }
			  }
			}

     Observe o seguinte:
	
	* O **tipo** do conjunto de dados foi definido como **AzureSqlTable**.
	* **linkedServiceName** é definido como **AzureSqlLinkedService**.
	* **tablename** está definido como **emp**.
	* Há três colunas – **ID**, **FirstName** e **LastName** – na tabela emp no banco de dados, mas ID é uma coluna de identidade, portanto, você precisa especificar somente **FirstName** e **LastName**.
	* A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

2.	Execute o comando a seguir para criar o conjunto de dados de Data Factory.
	
		New-AzureRmDataFactoryDataset $df -File .\EmpSQLTable.json


## Criar um pipeline
Nesta etapa, você cria um pipeline com uma **Atividade de Cópia** que utiliza **EmpTableFromBlob** como entrada e **EmpSQLTable** como saída.

1.	Crie um arquivo JSON denominado **ADFTutorialPipeline.json** na pasta **C:\\ADFGetStartedPSH** com o conteúdo a seguir:
	
			 {
			  "name": "ADFTutorialPipeline",
			  "properties": {
			    "description": "Copy data from a blob to Azure SQL table",
			    "activities": [
			      {
			        "name": "CopyFromBlobToSQL",
			        "description": "Push Regional Effectiveness Campaign data to Azure SQL database",
			        "type": "Copy",
			        "inputs": [
			          {
			            "name": "EmpTableFromBlob"
			          }
			        ],
			        "outputs": [
			          {
			            "name": "EmpSQLTable"
			          }
			        ],
			        "typeProperties": {
			          "source": {
			            "type": "BlobSource"
			          },
			          "sink": {
			            "type": "SqlSink"
			          }
			        },
			        "Policy": {
			          "concurrency": 1,
			          "executionPriorityOrder": "NewestFirst",
			          "style": "StartOfInterval",
			          "retry": 0,
			          "timeout": "01:00:00"
			        }
			      }
			    ],
			    "start": "2015-12-09T00:00:00Z",
			    "end": "2015-12-10T00:00:00Z",
			    "isPaused": false
			  }
			}

	Observe o seguinte:

	- Na seção de atividades, há apenas uma atividade cujo **tipo** é definido como **Cópia**.
	- A entrada da atividade é definida como **EmpTableFromBlob** e a saída da atividade é definida como **EmpSQLTable**.
	- Na seção **transformation**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

	Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora de **end** é opcional, mas nós o usaremos neste tutorial.
	
	Se você não especificar o valor para a propriedade **término**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9/9/9999** como o valor da propriedade **end**.
	
	No exemplo acima, como cada fatia de dados é produzida por hora, haverá 24 fatias de dados.
	
	Consulte a [Referência de script JSON](http://go.microsoft.com/fwlink/?LinkId=516971) para obter detalhes sobre as propriedades JSON.
2.	Execute o comando a seguir para criar a tabela de Data Factory.
		
		New-AzureRmDataFactoryPipeline $df -File .\ADFTutorialPipeline.json

**Parabéns!** Você criou um data factory do Azure, serviços vinculados, tabelas e uma pipeline e a pipeline agendada com êxito.

## Monitorar o pipeline
Nesta etapa, você usará o PowerShell do Azure para monitorar o que está acontecendo em uma Azure Data Factory.

1.	Execute **Get-AzureRmDataFactory** e atribua a saída a uma variável $df.

		$df=Get-AzureRmDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactoryPSH
 
2.	Execute **Get-AzureRmDataFactorySlice** para obter detalhes sobre todas as fatias do **EmpSQLTable**, que é a tabela de saída do pipeline.

		Get-AzureRmDataFactorySlice $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T00:00:00

	Substitua a parte de data, mês e ano do parâmetro **StartDateTime** pelo ano, mês e data atuais. Isso deve corresponder ao valor de **Start** no pipeline de JSON.

	Você deve ver 24 fatias, uma para cada hora desde 12h do dia atual até 12h do dia seguinte.
	
	**Primeira fatia:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/3/2015 12:00:00 AM
		End               : 3/3/2015 1:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     :
		LongRetryCount    : 0

	**Última fatia:**

		ResourceGroupName : ADFTutorialResourceGroup
		DataFactoryName   : ADFTutorialDataFactoryPSH
		TableName         : EmpSQLTable
		Start             : 3/4/2015 11:00:00 PM
		End               : 3/4/2015 12:00:00 AM
		RetryCount        : 0
		Status            : Waiting
		LatencyStatus     : 
		LongRetryCount    : 0

3.	Execute **Get-AzureRmDataFactoryRun** para obter os detalhes de execuções de atividade para uma fatia **específica**. Altere o valor do parâmetro **StartDateTime** para corresponder a hora de **Start** da fatia da saída acima. O valor de **StartDateTime** deve estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-03-03T22:00:00Z.

		Get-AzureRmDataFactoryRun $df -DatasetName EmpSQLTable -StartDateTime 2015-03-03T22:00:00

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

## Resumo
Neste tutorial, você criou uma data factory do Azure para copiar dados de um blob do Azure para um banco de dados SQL do Azure. Você utilizou o PowerShell para criar a data factory, os serviços vinculados, os conjuntos de dados e um pipeline. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Foi criada uma **data factory** do Azure.
2.	Foram criados **serviços vinculados**:
	1. Um serviço vinculado do **Armazenamento do Azure** para vincular sua conta do Armazenamento do Azure que contém os dados de entrada.
	2. Um serviço vinculado do **SQL do Azure** para vincular seu banco de dados SQL do Azure que contém os dados de saída.
3.	Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4.	Foi criado um **pipeline** com uma **Atividade de Cópia** com **BlobSource** como origem e **SqlSink** como coletor.

## Veja também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajudará a compreender pipelines e atividades no Azure Data Factory e como aproveitá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajudará a entender os conjuntos de dados no Azure Data Factory.
| [Monitorar e gerenciar pipelines usando o Aplicativo de Monitoramento](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorar, gerenciar e depurar seus pipelines usando o Aplicativo de Monitoramento e Gerenciamento. 



[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234.aspx
[old-cmdlet-reference]: https://msdn.microsoft.com/library/azure/dn820234(v=azure.98).aspx
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[azure-portal]: http://portal.azure.com
[download-azure-powershell]: ../powershell-install-configure.md
[data-factory-introduction]: data-factory-introduction.md

[image-data-factory-get-started-storage-explorer]: ./media/data-factory-copy-activity-tutorial-using-powershell/getstarted-storage-explorer.png

[sql-management-studio]: ../sql-database/sql-database-manage-azure-ssms.md
 

<!---HONumber=AcomDC_0803_2016-->