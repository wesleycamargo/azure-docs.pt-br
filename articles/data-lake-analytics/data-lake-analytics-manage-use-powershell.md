<properties 
   pageTitle="Gerenciar a Análise Azure Data Lake usando o Azure PowerShell | Azure" 
   description="Saiba como gerenciar trabalhos, fontes de dados e usuários da Análise Data Lake." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/01/2015"
   ms.author="jgao"/>

# Gerenciar a Análise Azure Data Lake usando o Azure PowerShell

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos da Análise Azure Data Lake usando o Azure PowerShell. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Veja [Obter avaliação gratuita do Azure]https://azure.microsoft.com/pt-BR/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##Instalar o Azure PowerShell 1.0 e superior

Primeiro, você deve desinstalar as versões 0.9x.

Para verificar a versão do PowerShell instalado:

	Get-Module *azure*
	
Para desinstalar a versão mais antiga, execute Programas e Recursos no painel de controle.

Há duas opções principais para a instalação do Azure PowerShell.

- [Galeria do PowerShell](https://www.powershellgallery.com/). Execute os seguintes comandos do console do ISE do PowerShell ou Windows PowerShell com privilégios elevados:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Install the Azure Service Management module from PowerShell Gallery
		Install-Module Azure
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM
		
		# Import Azure Service Management module
		Import-Module Azure

	Para obter mais informações, veja [Galeria do PowerShell](https://www.powershellgallery.com/).

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). Se você tem o Azure PowerShell 0.9.x instalado, será solicitado a desinstalar o 0.9.x. Se você instalou os módulos do Azure PowerShell na Galeria do PowerShell, o instalador exige que os módulos sejam removidos antes da instalação para assegurar a consistência do Ambiente do Azure PowerShell. Para obter instruções, veja [Instalar o Azure PowerShell 1.0 via WebPI](https://azure.microsoft.com/blog/azps-1-0/).

A WebPI receberá atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Se você estiver familiarizado com a instalação a partir da Galeria do PowerShell, esse será o primeiro canal para os melhores e mais recentes recursos do Azure PowerShell.

**Para listar os cmdlets**:

	Get-Command *Azure*DataLakeAnalytics*

**Para se conectar ao Azure, use os seguintes cmdlets**:

	Login-AzureRmAccount
	Get-AzureRmSubscription  # for finding the Azure Subscription ID
	Set-AzureRmContext -SubscriptionID <Azure Subscription ID>
	
## Gerenciar contas

Antes de executar qualquer trabalho da Análise Data Lake, você deve ter uma conta da Análise Data Lake. Ao contrário do Azure HDInsight, você não paga por uma conta da Análise quando ela não estiver executando um trabalho. Você paga apenas pelo tempo em que um trabalho é executado. Para obter mais informações, confira [Visão geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

###Criar contas

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeStoreName = "<DataLakeAccountName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$location = "<Microsoft Data Center>"
	
	Write-Host "Create a resource group ..." -ForegroundColor Green
	New-AzureRmResourceGroup `
		-Name  $resourceGroupName `
		-Location $location
	
	Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
	New-AzureRmDataLakeStoreAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeStoreName `
		-Location $location 
	
	Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
	New-AzureRmDataLakeAnalyticsAccount `
		-Name $dataLakeAnalyticsAccountName `
		-ResourceGroupName $resourceGroupName `
		-Location $location `
		-DefaultDataLake $dataLakeStoreName
	
	Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
	Get-AzureRmDataLakeAnalyticsAccount `
		-ResourceGroupName $resourceGroupName `
		-Name $dataLakeAnalyticsAccountName  

Você também pode usar um modelo do Grupo de Recursos do Azure. Um modelo para criar uma conta da Análise do Data Lake e a conta do Repositório do Data Lake podem ser encontrados no [Apêndice A](#appendix-a). Salve o modelo em um arquivo com o modelo .json e use o seguinte script do PowerShell para chamá-lo:


	$AzureSubscriptionID = "<Your Azure Subscription ID>"
	
	$ResourceGroupName = "<New Azure Resource Group Name>"
	$Location = "EAST US 2"
	$DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
	$DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
	
	$DeploymentName = "MyDataLakeAnalyticsDeployment"
	$ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
	
	Login-AzureRmAccount
	
	Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
	
	# Create the resource group
	New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
	
	# Create the Data Lake Analytics account with the default Data Lake Store account.
	$parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
	New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###Listar conta

Listar contas da Análise Data Lake na assinatura atual

	Get-AzureRmDataLakeAnalyticsAccount
	
A saída:

	Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
	Location   : eastus2
	Name       : learn1021adla
	Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
	Tags       : {}
	Type       : Microsoft.DataLakeAnalytics/accounts

Listar contas da Análise Data Lake em um grupo de recursos específico

	Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obter detalhes de uma conta específica da Análise Data Lake

	Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Testar a existência de uma conta específica da Análise Data Lake

	Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

O cmdlet retornará **True** ou **False**.

###Excluir contas da Análise Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

A exclusão de uma conta da Análise não excluirá a conta dependente do Armazenamento do Data Lake. O exemplo a seguir exclui a conta da Análise Data Lake e a conta padrão do Repositório Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

	Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName 
	Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## Gerenciar as fontes de dados da conta

A Análise Data Lake atualmente dá suporte às seguintes fontes de dados:

- [Armazenamento do Azure Data Lake](data-lake-storage-overview.md)
- [Armazenamento do Azure](storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta da Análise, é possíveis adicionar outras contas do Armazenamento do Data Lake e/ou uma conta do Armazenamento do Azure.

### Encontrar a conta padrão do Repositório Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount


### Adicionar outras contas de armazenamento de Blob do Azure

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureStorageAccountName = "<AzureStorageAccountName>"
	$AzureStorageAccountKey = "<AzureStorageAccountKey>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### Adicionar outras contas do Repositório Data Lake

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	$AzureDataLakeName = "<DataLakeStoreName>"
	
	Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -AccountName $dataLakeAnalyticName -DataLake $AzureDataLakeName 

### Listar fontes de dados:

	$resourceGroupName = "<ResourceGroupName>"
	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DataLakeStoreAccounts
	(Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.StorageAccounts
	


<!-- ################################ -->
<!-- ################################ -->
## Gerenciar trabalhos

Você deve ter uma conta da Análise Data Lake antes de criar um trabalho. Para obter mais informações, veja [Gerenciar contas da Análise do Data Lake](#manage-data-lake-analytics-accounts).

### Listar trabalhos

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -State Running, Queued
	#States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Result Cancelled
	#Results: Cancelled, Failed, None, Successed 
	
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Name <Job Name>
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -Submitter <Job submitter>

	# List all jobs submitted on January 1 (local time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter "2015/01/01"
		-SubmittedBefore "2015/01/02"	

	# List all jobs that succeeded on January 1 after 2 pm (UTC time)
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-State Ended
		-Result Succeeded
		-SubmittedAfter "2015/01/01 2:00 PM -0"
		-SubmittedBefore "2015/01/02 -0"

	# List all jobs submitted in the past hour
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-SubmittedAfter (Get-Date).AddHours(-1)

### Exibir detalhes do trabalho

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
	Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName -JobID <Job ID>
	
### Enviar trabalhos

	$dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

	#Pass script via path
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-ScriptPath $scriptPath

	#Pass script contents
	Submit-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-Name $jobName `
		-Script $scriptContents

> [AZURE.NOTE]A prioridade padrão de um trabalho é de 1.000 e o nível padrão de paralelismo de um trabalho é 1.


### Cancelar trabalhos

	Stop-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticName `
		-JobID $jobID


## Gerenciar itens do catálogo

O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. Para obter mais informações, veja [Usar o catálogo do U-SQL](data-lake-analytics-use-u-sql-catalog.md).

###Listar itens do catálogo

	#List databases
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database
	
	
	
	#List tables
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo"

###Obter detalhes do item de catálogo 

	#Get a database
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"
	
	#Get a table
	Get-AzureRmDataLakeAnalyticsCatalogItem `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Table `
		-Path "master.dbo.mytable"

###Testar a existência de um item do catálogo

	Test-AzureRmDataLakeAnalyticsCatalogItem  `
		-AccountName $adlAnalyticsAccountName `
		-ItemType Database `
		-Path "master"

###Criar segredo do catálogo
	New-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")

### Modificar segredo do catálogo
	Set-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master" `
			-Secret (Get-Credential -UserName "username" -Message "Enter the password")



###Excluir segredo do catálogo
	Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
			-AccountName $adlAnalyticsAccountName `
			-DatabaseName "master"


## Usar os grupos do Gerenciador de Recursos do Azure

Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para obter mais informações, veja [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).

Um serviço da Análise do Data Lake pode incluir os seguintes componentes:

- Conta da Análise Azure Data Lake
- Conta padrão do Armazenamento do Azure Data Lake obrigatória
- Adicionar outras contas do Armazenamento do Azure Data Lake
- Contas do Armazenamento do Azure adicionais

Você pode criar todos esses componentes em um grupo ARM para torná-los mais fáceis de serem gerenciados.

![Conta e armazenamento da Análise Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta da Análise Data Lake e as contas de armazenamento dependentes devem ser colocadas no mesmo datacenter do Azure. No entanto, o grupo ARM pode estar localizado em um datacenter diferente.

##Consulte também 

- [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-use-portal.md)
- [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##Apêndice A - modelo do ARM da Análise Data Lake

O modelo ARM a seguir pode ser usado para implantar uma conta da Análise Data Lake e sua conta dependente do Repositório Data Lake. Salve-o como um arquivo .json e use o script do PowerShell para chamar o modelo. Para obter mais informações, veja [Implantar um aplicativo com um modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md#deploy-with-powershell) e [Criando modelos do Gerenciador de Recursos do Azure](resource-group-authoring-templates.md).

	{
		"$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"adlAnalyticsName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Analytics account to create."
				}
			},
			"adlStoreName": {
				"type": "string",
				"metadata": {
					"description": "The name of the Data Lake Store account to create."
				}
			}
		},
		"resources": [{
			"name": "[parameters('adlAnalyticsName')]",
			"type": "Microsoft.DataLakeAnalytics/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": ["[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]"],
			"tags": {
				
			},
			"properties": {
				"defaultDataLakeAccount": "[parameters('adlStoreName')]"
				}
			}
		},
		{
			"name": "[parameters('adlName')]",
			"type": "Microsoft.DataLakeStore/accounts",
			"location": "East US 2",
			"apiVersion": "2015-10-01-preview",
			"dependsOn": [],
			"tags": {
				
			}
		}],
		"outputs": {
			"adlAnalyticsAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
			},
			"adlStoreAccount": {
				"type": "object",
				"value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
			}
		}
	}

<!---HONumber=AcomDC_1203_2015-->