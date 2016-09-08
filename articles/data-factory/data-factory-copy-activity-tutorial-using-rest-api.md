<properties 
	pageTitle="Tutorial: criar um pipeline com Atividade de Cópia usando a API REST | Microsoft Azure" 
	description="Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST API." 
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
	ms.date="08/17/2016" 
	ms.author="spelluru"/>

# Tutorial: criar um pipeline com Atividade de Cópia usando a API REST
> [AZURE.SELECTOR]
- [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Como usar o Editor do Data Factory](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Usando o PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Como usar o Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [Usando a API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
- [Como usar a API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)
- [Usando o Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)

Este tutorial mostra como criar e monitorar um data factory do Azure usando a API REST. O pipeline no data factory usa uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL.

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

> [AZURE.NOTE] 
Este artigo não cobre todas as APIs REST do Data Factory. Confira [Referência de API REST do Data Factory](https://msdn.microsoft.com/library/azure/dn906738.aspx) para obter uma documentação abrangente sobre os cmdlets de Data Factory.
  

## Pré-requisitos

- Vá para a [Visão geral do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
- Instale o [Curl](https://curl.haxx.se/dlwiz/) em seu computador. Você pode usar a ferramenta Curl com comandos REST para criar um data factory.
- Siga as instruções [deste artigo](../resource-group-create-service-principal-portal.md) para:
	1. Crie um aplicativo Web chamado **ADFCopyTutorialApp** no Azure Active Directory.
	2. Obtenha a **ID do cliente** e a **chave secreta**.
	3. Obtenha a **ID do locatário**.
	4. Atribua o aplicativo **ADFCopyTutorialApp** à função de **Colaborador do Data Factory**.
- Instale o [Azure PowerShell](../powershell-install-configure.md).
- Inicie o **PowerShell** e execute o comando a seguir. Mantenha o Azure PowerShell aberto até o fim deste tutorial. Se você fechar e reabrir, precisará executar os comandos novamente.
	1. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.
	
			Login-AzureRmAccount   
	2. Execute o comando a seguir para exibir todas as assinaturas dessa conta.

			Get-AzureRmSubscription 
	3. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NomeDaAssinaturaDoAzure**&gt; pelo nome da sua assinatura do Azure.

			Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
	1. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell.

			New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"

		Se o grupo de recursos já existe, especifique se deseja atualizá-lo (S) ou mantê-lo como (N).

		Algumas das etapas neste tutorial supõem que você utilize o grupo de recursos denominado ADFTutorialResourceGroup. Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.
  
## Criar definições JSON
Crie os arquivos JSON a seguir na pasta onde curl.exe está localizado.

### datafactory.json 
> [AZURE.IMPORTANT] O nome deve ser exclusivo e, portanto, convém colocar um prefixo/sufixo no ADFCopyTutorialDF para torná-lo um nome exclusivo.

	{  
	    "name": "ADFCopyTutorialDF",  
	    "location": "WestUS"
	}  

### azurestoragelinkedservice.json
> [AZURE.IMPORTANT] Substitua **nome da conta** e **chave da conta** pelo nome e pela chave da sua conta de armazenamento do Azure. Para saber como obter sua chave de acesso de armazenamento, confira [Exibir, copiar e regenerar chaves de acesso de armazenamento](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

	{
	    "name": "AzureStorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	        }
	    }
	}

### azuersqllinkedservice.json
> [AZURE.IMPORTANT] Substitua **servername**, **databasename**, **username** e **passwords** pelos nomes do servidor SQL do Azure e do banco de dados SQL, pela conta de usuário e pela senha da conta.

	{
	    "name": "AzureSqlLinkedService",
	    "properties": {
	        "type": "AzureSqlDatabase",
	        "description": "",
	        "typeProperties": {
	            "connectionString": "Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30"
	        }
	    }
	}


### inputdataset.json

	{
	  "name": "AzureBlobInput",
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
	    "linkedServiceName": "AzureStorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "fileName": "emp.txt",
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

A definição JSON acima define um conjunto de dados chamado **AzureBlobInput**, que representa dados de entrada para uma atividade no pipeline. Além disso, ele especifica que os dados de entrada estão localizados no arquivo **emp.txt** que está no contêiner de blob **adftutorial**.

 Observe o seguinte:

- O **tipo** do conjunto de dados foi definido como **AzureBlob**.
- **linkedServiceName** é definido como **AzureStorageLinkedService**.
- **folderPath** é definido como o contêiner **adftutorial** e **fileName** é definido como **emp.txt**.
- O **type** de formato é definido como **TextFormat**
- Há dois campos no arquivo de texto, **FirstName** e **LastName**, separados por uma vírgula (**columnDelimiter**)
- A **availability** é definida como **hourly** (frequency é definida como hour e interval é definido como 1). Portanto, o Data Factory procurará dados de entrada a cada hora na pasta raiz do contêiner de blob especificado (**adftutorial**).

Se você não especificar um **fileName** para um conjunto de dados de entrada, todos os arquivos/blobs da pasta de entrada (**folderPath**) serão considerados como entradas. Se você especificar um nome de arquivo em JSON, apenas arquivo/blob especificado será considerado como entrada.

Se você não especificar um **fileName** para uma **tabela de saída**, os arquivos gerados no **folderPath** serão nomeados no seguinte formato: Data.&lt;Guid&gt;.txt (por exemplo: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

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


### outputdataset.json
	
	{
	  "name": "AzureSqlOutput",
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


O JSON acima define um conjunto de dados chamado **AzureSqlOutput**, que representa dados de saída de uma atividade no pipeline. Além disso, ele especifica que os resultados são armazenados na tabela: **emp** no banco de dados representado pelo AzureSqlLinkedService. A seção **disponibilidade** especifica que o conjunto de dados de saída é produzido por hora (frequência: hora e intervalo: 1).

Observe o seguinte:

- O **tipo** do conjunto de dados foi definido como **AzureSQLTable**.
- **linkedServiceName** é definido como **AzureSqlLinkedService**.
- **tablename** está definido como **emp**.
- Há três colunas (**ID**, **FirstName** e **LastName**) na tabela emp no banco de dados. ID é uma coluna de identidade. Portanto, você precisa especificar somente **FirstName** e **LastName** aqui.
- A **availability** é definida como **hourly** (**frequency** definida como **hour** e **interval** definido como **1**). O serviço Data Factory gera uma fatia de dados de saída a cada hora na tabela **emp** no banco de dados SQL do Azure.

### pipeline.json

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
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-08-12T00:00:00Z",
	    "end": "2016-08-13T00:00:00Z"
	  }
	}


Observe o seguinte:

- Na seção de atividades, há apenas uma atividade cujo **type** é definido como **CopyActivity**.
- A entrada da atividade é definida como **AzureBlobInput** e a saída da atividade é definida como **AzureSqlOutput**.
- Na seção **transformation**, **BlobSource** é especificado como o tipo de origem e **SqlSink** é especificado como o tipo de coletor.

Substitua o valor da propriedade **start** pelo dia atual e o valor de **end** pelo dia seguinte. Você pode especificar apenas a parte da data e ignorar a parte de hora do valor de data/hora. Por exemplo, "2015-02-03", que é equivalente a "2015-02-03T00:00:00Z"

Ambos os valores de data/hora de início e de término devem estar no [formato ISO](http://en.wikipedia.org/wiki/ISO_8601). Por exemplo: 2014-10-14T16:32:41Z. A hora **final** é opcional, mas nós a usaremos neste tutorial.

Se você não especificar o valor para a propriedade **end**, ele será calculado como "**início + 48 horas**". Para executar o pipeline indefinidamente, especifique **9999-09-09** como o valor para a propriedade **end**.

No exemplo, como cada fatia de dados é produzida por hora, existem 24 fatias de dados.
	
> [AZURE.NOTE] Confira [Anatomia de um Pipeline](data-factory-create-pipelines.md#anatomy-of-a-pipeline) para obter detalhes sobre as propriedades JSON usadas no exemplo acima.

## Definir variáveis globais

No Azure PowerShell, execute os comandos a seguir depois de substituir os valores pelos seus próprios:

> [AZURE.IMPORTANT] Veja a seção [Pré-requisitos](#prerequisites) para obter instruções sobre como obter a ID do cliente, o segredo do cliente, a ID do locatário e ID da assinatura.

	$client_id = "<client ID of application in AAD>"
	$client_secret = "<client key of application in AAD>"
	$tenant = "<Azure tenant ID>";
	$subscription_id="<Azure subscription ID>";

	$rg = "ADFTutorialResourceGroup"
	$adf = "ADFCopyTutorialDF"

## Autenticar com o AAD 
Execute o comando a seguir para autenticar com o AAD (Azure Active Directory).

	$cmd = { .\curl.exe -X POST https://login.microsoftonline.com/$tenant/oauth2/token  -F grant_type=client_credentials  -F resource=https://management.core.windows.net/ -F client_id=$client_id -F client_secret=$client_secret };
	$responseToken = Invoke-Command -scriptblock $cmd;
	$accessToken = (ConvertFrom-Json $responseToken).access_token;
	
	(ConvertFrom-Json $responseToken) 

## Criar um data factory

Nesta etapa, você criará um Azure Data Factory chamado **ADFCopyTutorialDF**. Uma fábrica de dados pode ter um ou mais pipelines. Um pipeline em um data factory pode ter uma ou mais atividades. Por exemplo, uma atividade de cópia para copiar dados de uma fonte para um armazenamento de dados de destino. Dados de saída de uma atividade de Hive do HDInsight para executar o script Hive e transformar dados de entrada do produto. Execute os seguintes comandos para criar o data factory:

1. Atribua o comando à variável chamada **cmd**.

	Confirme se o nome do data factory especificado aqui (ADFCopyTutorialDF) corresponde ao nome especificado no **datafactory.json**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@datafactory.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/ADFCopyTutorialDF?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o data factory foi criado com êxito, você verá o JSON para o data factory nos **resultados**. Caso contrário, você verá uma mensagem de erro.

		Write-Host $results

Observe o seguinte:
 
- O nome do Azure Data Factory deve ser globalmente exclusivo. Se você vir o erro nos resultados: **O nome do data factory "ADFCopyTutorialDF" não está disponível**, faça o seguinte:
	1. Altere o nome (por exemplo, yournameADFCopyTutorialDF) no arquivo **datafactory.json**.
	2. No primeiro comando em que a variável **$cmd** é atribuída um valor, substitua ADFCopyTutorialDF pelo novo nome e execute o comando.
	3. Execute os próximos dois comandos para invocar a API REST a fim de criar o data factory e imprima os resultados da operação.
	
	Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.
- Para criar instâncias do Data Factory, você precisa ser um colaborador/administrador da assinatura do Azure
- O nome do data factory pode ser registrado futuramente como um nome DNS e tornar-se visível publicamente.
- Se você receber o erro: "**Esta assinatura não está registrada para usar o namespace Microsoft.DataFactory**", siga um destes procedimentos e tente publicar novamente:

	- No Azure PowerShell, execute o comando a seguir para registrar o provedor do Data Factory.
		
			Register-AzureRmResourceProvider -ProviderNamespace Microsoft.DataFactory
	
		Você pode executar o comando a seguir para confirmar se o provedor do Data Factory está registrado.
	
			Get-AzureRmResourceProvider
	- Faça logon no [portal do Azure](https://portal.azure.com) usando a assinatura do Azure e navegue até uma folha do Data Factory (ou) crie um data factory no portal do Azure. Essa ação registra automaticamente o provedor para você.

Antes de criar um pipeline, primeiro você precisará criar algumas entidades do Data Factory. Primeiro, você cria serviços vinculados para vincular os repositórios de dados de origem e de destino a seu repositório de dados. Em seguida, defina conjuntos de dados de entrada e saída para representar dados em repositórios de dados vinculados. Finalmente, crie o pipeline com uma atividade que usa esses conjuntos de dados.

## Criar serviços vinculados
Serviços vinculados vinculam armazenamentos de dados ou serviços de computação para uma data factory do Azure. Um repositório de dados pode ser um armazenamento do Azure, Banco de Dados SQL do Azure ou um banco de dados SQL Server local que contém os dados de entrada ou armazena dados de saída para um pipeline de Data Factory. Um serviço de computação é o serviço que processa os dados de entrada e gera dados de saída.

Nesta etapa, você cria dois serviços vinculados: **AzureStorageLinkedService** e **AzureSqlLinkedService**. O serviço vinculado AzureStorageLinkedService vincula uma conta de armazenamento do Azure e o AzureSqlLinkedService vincula um banco de dados SQL do Azure ao data factory: **ADFCopyTutorialDF**. Você criará, posteriormente neste tutorial, um pipeline que copia dados de um contêiner de blob no AzureStorageLinkedService em uma tabela SQL no AzureSqlLinkedService.

### Criar o serviço vinculado do armazenamento do Azure
Nesta etapa, você vincula a conta do Armazenamento do Azure ao data factory. Neste tutorial, você usa a conta de armazenamento do Azure para armazenar dados de entrada.

1. Atribua o comando à variável chamada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@azurestoragelinkedservice.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureStorageLinkedService?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results

### Criar serviço vinculado do Azure SQL
Nesta etapa, você vincula o banco de dados SQL do Azure ao data factory. Neste tutorial, você pode usar o mesmo banco de dados SQL do Azure para armazenar os dados de saída.

1. Atribua o comando à variável chamada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data “@azuresqllinkedservice.json” https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/linkedservices/AzureSqlLinkedService?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.
 
		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o serviço vinculado tiver sido criado com êxito, você verá o JSON do serviço vinculado nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results

## Criar conjuntos de dados

Na etapa anterior, você criou os serviços vinculados **AzureStorageLinkedService** e **AzureSqlLinkedService** para vincular uma conta de armazenamento do Azure e o banco de dados SQL do Azure ao data factory: **ADFTutorialDataFactoryIDF**. Nesta etapa, você cria conjuntos de dados que representam os dados de entrada e saída da Atividade de Cópia no pipeline criado na próxima etapa.

O conjunto de dados de entrada neste tutorial refere-se a um contêiner de blob no Armazenamento do Azure para o qual AzureStorageLinkedService aponta. O conjunto de dados de saída se refere a uma tabela SQL no banco de dados SQL do Azure para o qual AzureSqlLinkedService aponta.

### Preparar o armazenamento de blobs do Azure e o banco de dados SQL do Azure para o tutorial
Você precisa realizar as etapas a seguir a fim de preparar seu armazenamento de blobs do Azure e o banco de dados SQL do Azure para este tutorial.
 
* Crie um contêiner de blob denominado **adftutorial** no armazenamento de blobs do Azure para o qual **AzureStorageLinkedService** aponta.
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

	Se o cliente não tiver permissão para acessar o servidor SQL do Azure, você precisará configurar o firewall para o servidor SQL do Azure permitir o acesso no seu computador (endereço IP). Veja [este artigo](../sql-database/sql-database-configure-firewall-settings.md) para obter as etapas para configurar o firewall para o SQL Server do Azure.
		
### Criar conjunto de dados de entrada 
Nesta etapa, você criará um conjunto de dados denominado **AzureBlobInput** que aponta para um contêiner de blob no Armazenamento do Azure representado pelo serviço vinculado **AzureStorageLinkedService**. Esse contêiner de blob (**adftutorial**) contém os dados de entrada no arquivo: **emp.txt**.

1. Atribua o comando à variável chamada **cmd**.

		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@inputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureBlobInput?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results

### Criar conjunto de dados de saída
Nesta etapa, você cria uma tabela de saída denominada **AzureSqlOutput**. Esse conjunto de dados aponta para uma tabela SQL (**emp**) no banco de dados SQL do Azure representado por **AzureSqlLinkedService**. O pipeline copia os dados do blob de entrada na tabela **emp**.

1. Atribua o comando à variável chamada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@outputdataset.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/AzureSqlOutput?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.
  
		Write-Host $results 

## Criar um pipeline
Nesta etapa, você cria um pipeline com uma **Atividade de Cópia** que usa **AzureBlobInput** como entrada e **AzureSqlOutput** como saída.

1. Atribua o comando à variável chamada **cmd**.
 
		$cmd = {.\curl.exe -X PUT -H "Authorization: Bearer $accessToken" -H "Content-Type: application/json" --data "@pipeline.json" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datapipelines/MyFirstPipeline?api-version=2015-10-01};
2. Execute o comando usando **Invoke-Command**.

		$results = Invoke-Command -scriptblock $cmd;
3. Exiba os resultados. Se o conjunto de dados tiver sido criado com êxito, você verá o JSON do conjunto de dados nos **resultados**. Caso contrário, você verá uma mensagem de erro.

		Write-Host $results

**Parabéns!** Você criou com êxito um data factory do Azure, com um pipeline que copia dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL do Azure.

## Monitorar o pipeline
Nesta etapa, você pode usar a API REST do Data Factory para monitorar fatias produzidas pelo pipeline.

	$ds ="AzureSqlOutput"

	$cmd = {.\curl.exe -X GET -H "Authorization: Bearer $accessToken" https://management.azure.com/subscriptions/$subscription_id/resourcegroups/$rg/providers/Microsoft.DataFactory/datafactories/$adf/datasets/$ds/slices?start=1970-01-01T00%3a00%3a00.0000000Z"&"end=2016-08-12T00%3a00%3a00.0000000Z"&"api-version=2015-10-01};

	$results2 = Invoke-Command -scriptblock $cmd;


	IF ((ConvertFrom-Json $results2).value -ne $NULL) {
	    ConvertFrom-Json $results2 | Select-Object -Expand value | Format-Table
	} else {
    	    (convertFrom-Json $results2).RemoteException
	}

Execute estes comandos até ver uma fatia no estado **Pronto** ou no estado **Falha**. Quando a fatia estiver no estado Pronto, verifique os dados de saída na tabela **emp** no Banco de Dados SQL do Azure.

Para cada fatia, duas linhas de dados do arquivo de origem são copiadas para a tabela emp no Banco de Dados SQL do Azure. Portanto, você verá 24 novos registros na tabela emp quando todas as fatias forem processadas com êxito (no estado Pronto).


## Resumo
Neste tutorial, você usou uma API REST para criar um data factory do Azure e copiar dados de um blob do Azure para um banco de dados SQL do Azure. Aqui estão as etapas de alto nível executadas nesse tutorial:

1.	Foi criado um **data factory** do Azure.
2.	Foram criados **serviços vinculados**:
	1. Um serviço vinculado do Armazenamento do Azure para vincular a conta do Armazenamento do Azure que contém os dados de entrada.
	2. Um serviço vinculado do Azure SQL para vincular o banco de dados SQL do Azure que contém os dados de saída.
3.	Foram criados **conjuntos de dados** que descrevem os dados de entrada e de saída para os pipelines.
4.	Foi criado um **pipeline** com uma Atividade de Cópia com BlobSource como origem e SqlSink como coletor.

## Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de movimentação de dados](data-factory-data-movement-activities.md) | Este artigo fornece informações detalhadas sobre a Atividade de Cópia utilizada neste tutorial. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos de agendamento e execução do modelo de aplicativo do Azure Data Factory. |
| [Pipelines](data-factory-create-pipelines.md) | Este artigo o ajuda a compreender pipelines e atividades no Azure Data Factory e como usá-los para construir fluxos de trabalho orientados a dados de ponta a ponta para seu cenário ou negócio. |
| [Conjunto de dados](data-factory-create-datasets.md) | Este artigo o ajuda a entender os conjuntos de dados no Azure Data Factory.
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
 

<!---HONumber=AcomDC_0831_2016-->