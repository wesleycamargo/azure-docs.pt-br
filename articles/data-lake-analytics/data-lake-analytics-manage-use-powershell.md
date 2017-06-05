---
title: Gerenciar o Azure Data Lake Analytics usando o Azure PowerShell | Microsoft Docs
description: "Saiba como gerenciar trabalhos, fontes de dados e usuários da Análise Data Lake. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 4dd1ba30101d364fa52738a4e1c3e07874c5ed1f
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017


---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerenciar a Análise Azure Data Lake usando o Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerenciar contas, fontes de dados, usuários e trabalhos da Análise Azure Data Lake usando o Azure PowerShell. Para ver os tópicos de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **PowerShell do Azure**. Confira a seção de pré-requisitos em [Usando o Azure PowerShell com o Gerenciador de Recursos do Azure](../powershell-azure-resource-manager.md).

## <a name="running-the-snippets"></a>Executando os trechos

Os trechos do PowerShell neste tutorial usam essas variáveis para armazenar essas informações

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="manage-accounts"></a>Gerenciar Contas

### <a name="create-a-data-lake-analytics-account"></a>Criar uma conta da Análise Data Lake

```
New-AzureRmResourceGroup -Name  $rg -Location $location
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="create-a-data-lake-analytics-account-using-a-template"></a>Criar uma conta do Data Lake Analytics usando um modelo

Você também pode usar um modelo do Grupo de Recursos do Azure. Um modelo para criar uma conta do Data Lake Analytics e a conta do Data Lake Store podem ser encontrados no [Apêndice A](#appendix-a). Salve o modelo em um arquivo com o modelo .json e use o seguinte script do PowerShell para chamá-lo:

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


### <a name="list-accounts"></a>Listar contas

Listar contas do Data Lake Analytics na assinatura atual

    Get-AzureRmDataLakeAnalyticsAccount

Listar contas da Análise Data Lake em um grupo de recursos específico

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obter detalhes de uma conta específica da Análise Data Lake

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Testar a existência de uma conta específica da Análise Data Lake

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

O cmdlet retornará **True** ou **False**.

### <a name="delete-data-lake-analytics-accounts"></a>Excluir contas da Análise Data Lake
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

A exclusão de uma conta da Análise de Data Lake não excluirá a conta dependente do Armazenamento do Data Lake. O exemplo a seguir exclui a conta da Análise Data Lake e a conta padrão do Repositório Data Lake

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerenciar as fontes de dados da conta
No momento, a Análise Data Lake dá suporte às seguintes fontes de dados:

* [Repositório Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Armazenamento do Azure](../storage/storage-introduction.md)

Quando você cria uma conta da Análise, é necessário designar uma conta do Armazenamento do Azure Data Lake como a conta de armazenamento padrão. A conta padrão do Repositório Data Lake é usada para armazenar metadados de trabalho e logs de auditoria de trabalho. Depois de criar uma conta da Análise, é possíveis adicionar outras contas do Armazenamento do Data Lake e/ou uma conta do Armazenamento do Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Encontrar a conta padrão do Repositório Data Lake
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Adicionar outras contas de armazenamento de Blob do Azure
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar outras contas do Repositório Data Lake
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"

    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Listar fontes de dados:
    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts



<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gerenciar trabalhos
Você deve ter uma conta da Análise Data Lake antes de criar um trabalho.  Para saber mais, consulte [Gerenciar contas da Análise Data Lake](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Listar trabalhos
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 

    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"    

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Exibir detalhes do trabalho
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>

### <a name="submit-jobs"></a>Enviar trabalhos
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [!NOTE]
> A prioridade padrão de um trabalho é de 1.000 e o nível padrão de paralelismo de um trabalho é 1.
> 
> 

### <a name="cancel-jobs"></a>Cancelar trabalhos
    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Gerenciar itens do catálogo
O catálogo do U-SQL é usado para estruturar dados e código para que eles possam ser compartilhados por scripts U-SQL. O catálogo possibilita o melhor desempenho possível com dados no Azure Data Lake. Para saber mais, consulte [Usar o Catálogo do U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-catalog-items"></a>Listar itens do catálogo
    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database



    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

### <a name="get-catalog-item-details"></a>Obter detalhes do item de catálogo
    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

### <a name="test-existence-of--catalog-item"></a>Testar a existência de um item do catálogo
    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

## <a name="use-azure-resource-manager-groups"></a>Usar os grupos do Gerenciador de Recursos do Azure
Aplicativos normalmente são compostos por vários componentes, como, por exemplo, um aplicativo Web, banco de dados, servidor de banco de dados, armazenamento e serviços de terceiros. O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo. Para saber mais, consulte [Visão geral do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de Análise Data Lake pode incluir os seguintes componentes:

* Conta da Análise Azure Data Lake
* Conta padrão do Armazenamento do Azure Data Lake obrigatória
* Adicionar outras contas do Armazenamento do Azure Data Lake
* Contas do Armazenamento do Azure adicionais

Você pode criar todos esses componentes em um grupo ARM para torná-los mais fáceis de serem gerenciados.

![Conta e armazenamento da Análise Azure Data Lake](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta da Análise Data Lake e as contas de armazenamento dependentes devem ser colocadas no mesmo data center do Azure.
No entanto, o grupo ARM pode estar localizado em um data center diferente.  

## <a name="see-also"></a>Confira também
* [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
* [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
* [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

## <a name="appendix-a---data-lake-analytics-arm-template"></a>Apêndice A - modelo do ARM da Análise Data Lake
O modelo ARM a seguir pode ser usado para implantar uma conta da Análise Data Lake e sua conta dependente do Repositório Data Lake.  Salve-o como um arquivo .json e use o script do PowerShell para chamar o modelo. Para saber mais, confira [Implantar um aplicativo com um modelo do Gerenciador de Recursos do Azure](../azure-resource-manager/resource-group-template-deploy.md) e [Criando modelos do Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

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
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
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


