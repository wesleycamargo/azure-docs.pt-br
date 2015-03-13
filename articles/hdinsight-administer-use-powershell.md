<properties 
	pageTitle="Gerenciar clusters do Hadoop no HDInsight com o PowerShell do Azure | Azure" 
	description="Saiba como realizar tarefas administrativas para os clusters Hadoop no HDInsight usando o PowerShell do Azure." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight Usando o PowerShell do Azure

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como gerenciar clusters do Hadoop no HDInsight usando um console local do PowerShell do Azure com o Windows PowerShell. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do HDInsight executam as tarefas com sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

- Uma estação de trabalho com o PowerShell do Azure. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][Powershell-install-configure].

			
	

## Neste artigo

* [Provisionar um cluster](#provision)
* [Listar e mostrar clusters](#listshow)
* [Excluir um cluster](#delete)
* [Conceder/revogar acesso aos serviços HTTP](#httpservices)
* [Enviar trabalhos MapReduce](#mapreduce)
* [Enviar trabalhos do Hive](#hive)
* [Carregar dados no Armazenamento de Blob](#upload)
* [Baixar dados de saída do MapReduce por meio do Armazenamento de Blob](#download)


##<a id="provision"></a> Provisionar um cluster HDInsight
O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de armazenamento do Azure e um contêiner de armazenamento são necessários para poder criar um cluster HDInsight. 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**Para criar uma conta de Armazenamento do Azure**

Depois de importar o arquivo publishsettings, você pode usar o seguinte comando para criar uma conta de armazenamento:

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] A conta de armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

><ul>
<li>Sudeste Asiático</li>
<li>Norte da Europa</li>
<li>Europa Ocidental</li>
<li>Leste dos EUA</li>
<li>Oeste dos EUA</li>
</ul>



Para obter informações sobre como criar uma conta de armazenamento do Azure usando o Portal de Gerenciamento, consulte [Como criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/).

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte a seção *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* de [Como criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/).

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

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

**Para listar todos os clusters na assinatura atual**

	Get-AzureHDInsightCluster 

**Para mostrar os detalhes do cluster específico da assinatura atual**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Excluir um cluster
Use o seguinte comando para excluir um cluster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Conceder/revogar acesso aos serviços HTTP

Os clusters do HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso.  Este é um exemplo:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

No exemplo <i>hdiv2</i> é um nome de cluster do HDInsight.

>[AZURE.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome de usuário e a senha do cluster.

Isso também pode ser feito usando o Portal de Gerenciamento do Microsoft Azure. Consulte [Administrar o HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal].

##<a id="mapreduce"></a> Enviar trabalhos do MapReduce
A distribuição do cluster do HDInsight é fornecida com alguns exemplos do MapReduce. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem.

**Para enviar um trabalho MapReduce**

O script do PowerShell a seguir envia o trabalho do exemplo de contagem de palavras: 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* é fornecido com clusters HDInsight versão 2.1. O arquivo foi renomeado para *hadoop-mapreduce.jar* em clusters HDInsight versão 3.0.

Para obter informações sobre o prefixo WASB, consulte [Usar o armazenamento de Blob do Azure para HDInsight][hdinsight-
storage].

**Para baixar a saída do trabalho MapReduce**

O script do PowerShell a seguir recupera a saída do trabalho do MapReduce do último procedimento:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obter mais informações sobre como desenvolver e executar trabalhos MapReduce, consulte [Usando o MapReduce com o HDInsight][hdinsight-use-mapreduce].






































##<a id="hive"></a> Enviar trabalhos do Hive
A distribuição dos clusters HDInsight é fornecida com uma tabela Hive de exemplo chamada *hivesampletable*. Você pode usar um "show tables;" do HiveSQL para listar as tabelas Hive em um cluster.

**Para enviar um trabalho Hive**

O script a seguir envia um trabalho do Hive para listar as tabelas do Hive:
	
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

O trabalho do Hive mostrará primeiro as tabelas do Hive criadas no cluster e os dados retornados de hivesampletable.

Para obter mais informações sobre como usar o Hive, consulte [Usando o Hive com o HDInsight][hdinsight-use-hive].


##<a id="upload"></a>Carregar dados no Armazenamento de Blob
Consulte [Carregar dados no HDInsight][hdinsight-upload-data].

##<a id="download"></a>Baixar a saída do MapReduce do Armazenamento de Blob
Consulte a sessão [Enviar trabalhos do MapReduce](#mapreduce) neste artigo.

## Consulte também
* [Documentação de referência do cmdlet do HDInsight][hdinsight-powershell-reference]
* [Administrar o HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal]
* [Administrar o HDInsight usando a interface de linha de comando][hdinsight-admin-cli]
* [Provisionar clusters do HDInsight][hdinsight-provision]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Enviar trabalhos Hadoop de modo programático][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


<!--HONumber=42-->
