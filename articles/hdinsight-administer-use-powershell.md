<properties linkid="manage-services-hdinsight-administer-hdinsight-hadoop-clusters-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Manage Hadoop clusters in HDInsight with Azure PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, Hadoop, administration, administer" description="Learn how to perform administrative tasks for the Hadoop clusters in HDInsight using Azure PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Manage Hadoop clusters in HDInsight using Azure PowerShell" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Gerenciar clusters Hadoop no HDInsight Usando o PowerShell do Azure

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como gerenciar clusters HDInsight usando um console local do PowerShell do Azure com Windows PowerShell. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][].

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

-   Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do HDInsight executam as tarefas com sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Ofertas para membros][] ou [Avaliação gratuita][].

-   Uma estação de trabalho com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][].

## Neste artigo

-   [Provisionar um cluster][]
-   [Listar e mostrar clusters][]
-   [Excluir um cluster][]
-   [Conceder/revogar acesso aos serviços HTTP][]
-   [Enviar trabalhos MapReduce][]
-   [Enviar trabalhos Hive][]
-   [Carregar dados no Armazenamento de Blob][]
-   [Baixar dados de saída do MapReduce do Armazenamento de Blob][]

## <span id="provision"></span></a>Provisione um cluster HDInsight

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de armazenamento do Azure e um contêiner de armazenamento são necessários para poder criar um cluster HDInsight.

**Para criar uma conta de Armazenamento do Azure**

Depois de importar o arquivo publishsettings, você pode usar o seguinte comando para criar uma conta de armazenamento:

    # Create an Azure storage account
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # For example, "West US"

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] A conta de armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

> -   Sudeste Asiático
> -   Norte da Europa
> -   Europa Ocidental
> -   Leste dos EUA
> -   Oeste dos EUA

Para obter informações sobre como criar uma conta de Armazenamento do Azure usando o Portal de Gerenciamento, consulte [Como criar uma conta de armazenamento][].

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

    # List storage accounts for the current subscription
    Get-AzureStorageAccount
    # List the keys for a storage account
    Get-AzureStorageKey <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* em [Como gerenciar contas de armazenamento][].

**Para criar o contêiner de armazenamento do Azure**

O PowerShell não pode criar um contêiner de Blob durante o processo de provisionamento do HDInsight. Você pode criar um contêiner usando o script a seguir:

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Create a storage context object
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext

**Para provisionar um cluster**

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Get the storage account key
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Create a new HDInsight cluster
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

A captura de tela a seguir mostra a execução do script:

![HDI.PS.Provision][]

## <span id="listshow"></span></a> Listar e mostrar detalhes do cluster

Use os seguintes comandos para listar e mostrar os detalhes do cluster:

**Para listar todos os clusters na assinatura atual**

    Get-AzureHDInsightCluster 

**Para mostrar os detalhes do cluster específico da assinatura atual**

    Get-AzureHDInsightCluster -Name <ClusterName> 

## <span id="delete"></span></a> Excluir um cluster

Use o seguinte comando para excluir um cluster:

    Remove-AzureHDInsightCluster -Name <ClusterName> 

## <span id="httpservice"></span></a> Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Este é um exemplo:

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

No exemplo, *hdiv2* é um nome de cluster HDInsight.

> [WACOM.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome e a senha de usuário do cluster.

Isso também pode ser feito usando o Portal de Gerenciamento do Windows Azure. Consulte [Administrar o HDInsight usando o Portal de Gerenciamento][].

## <span id="mapreduce"></span></a> Enviar trabalhos MapReduce

A distribuição do cluster HDInsight é fornecida com alguns exemplos de MapReduce. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem.

**Para enviar um trabalho MapReduce**

O script do PowerShell a seguir envia o trabalho do exemplo de contagem de palavras:

    $clusterName = "<HDInsightClusterName>"            

    # Define the MapReduce job
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Run the job and show the standard error 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] O *hadoop-examples.jar* é fornecido com clusters HDInsight versão 2.1. O arquivo foi renomeado para *hadoop-mapreduce.jar* em clusters HDInsight versão 3.0.

Para obter informações sobre o prefixo WASBx, consulte [Usar o nto de Blob do Azure para HDInsight][hdinsight-storage].

**Para baixar a saída do trabalho MapReduce**

O script do PowerShell a seguir recupera a saída do trabalho MapReduce do último procedimento:

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Create the storage account context object
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Download the output to local computer
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Display the output
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obter mais informações sobre como desenvolver e executar trabalhos MapReduce, consulte [Usando o MapReduce com o HDInsight][].

## <span id="hive"></span></a> Enviar trabalhos Hive

A distribuição dos clusters HDInsight é fornecida com uma tabela Hive de exemplo chamada *hivesampletable*. Você pode usar um "show tables;" do HiveSQL para listar as tabelas Hive em um cluster.

**Para enviar um trabalho Hive**

O script a seguir envia um trabalho hive para listar as tabelas Hive:

    $clusterName = "<HDInsightClusterName>"               

    # HiveQL query
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

O trabalho Hive mostrará primeiro as tabelas Hive criadas no cluster e os dados retornados de hivesampletable.

Para obter mais informações sobre como usar o Hive, consulte [Usando o Hive com o HDInsight][].

## <span id="upload"></span></a>Carregar dados no Armazenamento de Blob

Consulte [Carregar dados no HDInsight][].

## <span id="download"></span></a>Baixar a saída do MapReduce do Armazenamento de Blob

Consulte a sessão [Enviar trabalhos MapReduce][] neste artigo.

## Consulte também

-   [Documentação de referência do cmdlet do HDInsight][Referência a cmdlets do HDInsight]
-   [Administrar o HDInsight usando o Portal de Gerenciamento][]
-   [Administrar o HDInsight usando a interface de linha de comando][]
-   [Provisione clusters HDInsight][]
-   [Carregar dados no HDInsight][]
-   [Enviar trabalhos Hadoop de forma programática][]
-   [Introdução ao Azure HDInsight][]

  [Referência a cmdlets do HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [Opções de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Instalar e configurar o PowerShell do Azure]: ../install-configure-powershell/
  [Provisionar um cluster]: #provision
  [Listar e mostrar clusters]: #listshow
  [Excluir um cluster]: #delete
  [Conceder/revogar acesso aos serviços HTTP]: #httpservices
  [Enviar trabalhos MapReduce]: #mapreduce
  [Enviar trabalhos Hive]: #hive
  [Carregar dados no Armazenamento de Blob]: #upload
  [Baixar dados de saída do MapReduce do Armazenamento de Blob]: #download
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Como gerenciar contas de armazenamento]: ../storage-manage-storage-account/
  [HDI.PS.Provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
  [Administrar o HDInsight usando o Portal de Gerenciamento]: ../hdinsight-administer-use-management-portal/
  [Usando o MapReduce com o HDInsight]: ../hdinsight-use-mapreduce/
  [Usando o Hive com o HDInsight]: ../hdinsight-use-hive/
  [Carregar dados no HDInsight]: ../hdinsight-upload-data/
  [Administrar o HDInsight usando a interface de linha de comando]: ../hdinsight-administer-use-command-line/
  [Provisione clusters HDInsight]: ../hdinsight-provision-clusters/
  [Enviar trabalhos Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [hdinsight-storage]: ../hdinsight-use-blob-storage/

