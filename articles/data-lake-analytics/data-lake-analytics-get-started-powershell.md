---
title: "Introdução ao Azure Data Lake Analytics usando o Azure PowerShell | Microsoft Docs"
description: 'Use o Azure PowerShell para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics usando o U-SQL e enviar o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 2bc3e4573ff4f202c3c8492e8110dc35c7e8ff2a
ms.lasthandoff: 04/27/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: introdução à Análise Azure Data Lake usando o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como usar o Azure PowerShell para criar contas da Análise Azure Data Lake, definir trabalhos da Análise Data Lake em [U-SQL](data-lake-analytics-u-sql-get-started.md)e enviar trabalhos para contas da Análise Data Lake. Para saber mais sobre o Data Lake Analytics, consulte a [Visão geral do Azure Data Lake Analytics](data-lake-analytics-overview.md).

Neste tutorial, você desenvolverá um trabalho que lê um arquivo TSV (valores separados por tabulação) e o converte em um arquivo CSV (valores separados por vírgulas). Para acompanhar o mesmo tutorial usando outras ferramentas compatíveis, clique nas guias na parte superior desta seção.

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Como instalar e configurar o PowerShell do Azure](/powershell/azure/overview).

## <a name="create-data-lake-analytics-account"></a>Criar conta da Análise Data Lake
Você deve ter uma conta da Análise Data Lake antes de executar trabalhos. Para criar uma conta da Análise Data Lake, você deve especificar o seguinte:

* **Grupo de Recursos do Azure**: é necessário criar uma conta da Análise Data Lake em um grupo de Recursos do Azure. O [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada.  

    Para enumerar os grupos de recursos em sua assinatura:

        Get-AzureRmResourceGroup

    Para criar um novo grupo de recursos:

        New-AzureRmResourceGroup `
            -Name "<Your resource group name>" `
            -Location "<Azure Data Center>" # For example, "East US 2"
* **Nome da conta da Análise Data Lake**
* **Local**: um dos datacenters do Azure que dá suporte à Análise Data Lake.
* **Conta padrão do Data Lake**: cada conta da Análise Data Lake tem uma conta padrão do Data Lake.

    Para criar uma nova conta do Data Lake:

        New-AzureRmDataLakeStoreAccount `
            -ResourceGroupName "<Your Azure resource group name>" `
            -Name "<Your Data Lake account name>" `
            -Location "<Azure Data Center>"  # For example, "East US 2"

  > [!NOTE]
  > O nome da conta do Data Lake deve conter apenas letras minúsculas e números.
  >
  >

**Para criar uma conta da Análise Data Lake**

1. Abra o ISE do PowerShell em sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $resourceGroupName = "<ResourceGroupName>"
        $dataLakeStoreName = "<DataLakeAccountName>"
        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $location = "East US 2"

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
            -Name $dataLakeAnalyticsName `
            -ResourceGroupName $resourceGroupName `
            -Location $location `
            -DefaultDataLake $dataLakeStoreName

        Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
        Get-AzureRmDataLakeAnalyticsAccount `
            -ResourceGroupName $resourceGroupName `
            -Name $dataLakeAnalyticsName  

## <a name="upload-data-to-data-lake"></a>Carregar dados no Data Lake
Neste tutorial, você processará alguns logs de pesquisa.  O log de pesquisa pode ser armazenado no Repositório Azure Data Lake ou no armazenamento de Blob do Azure.

Um arquivo de log de pesquisa de exemplo foi copiado em um contêiner público do Blob do Azure. Use o seguinte script do PowerShell para baixar o arquivo em sua estação de trabalho e carregue o arquivo em sua conta padrão do Repositório Azure Data Lake de sua conta da Análise Data Lake.

    $dataLakeStoreName = "<The default Data Lake Store account name>"

    $localFolder = "C:\Tutorials\Downloads\" # A temp location for the file.
    $storageAccount = "adltutorials"  # Don't modify this value.
    $container = "adls-sample-data"  #Don't modify this value.

    # Create the temp location    
    New-Item -Path $localFolder -ItemType Directory -Force

    # Download the sample file from Azure Blob storage
    $context = New-AzureStorageContext -StorageAccountName $storageAccount -Anonymous
    $$blobs = Get-AzureStorageBlob -Container $container -Context $context
    $blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder

    # Upload the file to the default Data Lake Store account    
    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $localFolder"SearchLog.tsv" -Destination "/Samples/Data/SearchLog.tsv"

O seguinte script do PowerShell mostra como obter o nome padrão do Repositório Azure Data Lake de uma conta da Análise Data Lake:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticsName).Properties.DefaultDataLakeStoreAccount
    echo $dataLakeStoreName

> [!NOTE]
> O Portal do Azure fornece uma interface do usuário para copiar os arquivos de dados de exemplo na conta padrão do Repositório do Azure Data Lake. Para obter instruções, veja [Introdução à Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md#prepare-source-data).
>
>

A Análise Data Lake também pode acessar o armazenamento de Blob do Azure.  Para carregar dados para o armazenamento de Blob do Azure, veja [Usando o Azure PowerShell com o Armazenamento do Azure](../storage/storage-powershell-guide-full.md).

## <a name="submit-data-lake-analytics-jobs"></a>Enviar trabalhos da Análise Data Lake
Os trabalhos da Análise Data Lake são escritos na linguagem U-SQL. Para saber mais sobre o U-SQL, confira [Introdução à linguagem U-SQL](data-lake-analytics-u-sql-get-started.md) e [Referência da linguagem U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Para criar um script de trabalho da Análise Data Lake**

* Crie um arquivo de texto com o seguinte script U-SQL e salve o arquivo de texto em sua estação de trabalho:

        @searchlog =
            EXTRACT UserId          int,
                    Start           DateTime,
                    Region          string,
                    Query           string,
                    Duration        int?,
                    Urls            string,
                    ClickedUrls     string
            FROM "/Samples/Data/SearchLog.tsv"
            USING Extractors.Tsv();

        OUTPUT @searchlog   
            TO "/Output/SearchLog-from-Data-Lake.csv"
        USING Outputters.Csv();

    Este script U-SQL lê o arquivo de dados de origem usando **Extractors.Tsv()**, em seguida, cria um arquivo csv usando **Outputters.Csv()**.

    Não modifique os dois caminhos, a menos que você copie o arquivo de origem para um local diferente.  A Análise Data Lake criará a pasta de saída se ela não existir.

    É mais simples usar caminhos relativos para arquivos armazenados em contas padrão do Data Lake. Você também pode usar caminhos absolutos.  Por exemplo,

        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

    Você deve usar caminhos absolutos para acessar os arquivos em contas do Armazenamento vinculadas.  A sintaxe para os arquivos armazenados na conta do Armazenamento do Azure vinculada é:

        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv

  > [!NOTE]
  > Atualmente, não há suporte para o contêiner de Blob do Azure com permissões de acesso de blobs públicos ou de contêineres públicos.    
  >
  >

**Para enviar o trabalho**

1. Abra o ISE do PowerShell em sua estação de trabalho do Windows.
2. Execute o seguinte script:

        $dataLakeAnalyticsName = "<DataLakeAnalyticsAccountName>"
        $usqlScript = "c:\tutorials\data-lake-analytics\copyFile.usql"

        $job = Submit-AzureRmDataLakeAnalyticsJob -Name "convertTSVtoCSV" -AccountName $dataLakeAnalyticsName –ScriptPath $usqlScript

        Wait-AdlJob -Account $dataLakeAnalyticsName -JobId $job.JobId

        Get-AzureRmDataLakeAnalyticsJob -AccountName $dataLakeAnalyticsName -JobId $job.JobId

    No script, o arquivo de script U-SQL é armazenado em c:\tutorials\data-lake-analytics\copyFile.usql. Atualize o caminho do arquivo de acordo.

Depois que o trabalho for concluído, você poderá usar os seguintes cmdlets para listar o arquivo e baixá-lo:

    $resourceGroupName = "<Resource Group Name>"
    $dataLakeAnalyticName = "<Data Lake Analytic Account Name>"
    $destFile = "C:\tutorials\data-lake-analytics\SearchLog-from-Data-Lake.csv"

    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticName).Properties.DefaultDataLakeAccount

    Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -path "/Output"

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "/Output/SearchLog-from-Data-Lake.csv" -Destination $destFile

## <a name="see-also"></a>Confira também
* Para ver o mesmo tutorial usando outras ferramentas, clique nos seletores de guias na parte superior da página.
* Para ver uma consulta mais complexa, consulte [Analisar logs de site usando a Análise Data Lake do Azure](data-lake-analytics-analyze-weblogs.md).
* Para começar a desenvolver aplicativos U-SQL, consulte [Desenvolver scripts U-SQL usando as Ferramentas do Data Lake para Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Para aprender a usar o U-SQL, veja [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para obter as tarefas de gerenciamento, veja [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md).
* Para obter uma visão geral da Análise do Data Lake, veja [Visão geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

