<properties 
	pageTitle="Gerenciar clusters de Hadoop no HDInsight com o PowerShell | Microsoft Azure" 
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
	ms.date="07/21/2015" 
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight Usando o PowerShell do Azure


O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como gerenciar clusters HDInsight do Azure usando um console local do PowerShell do Azure com Windows PowerShell. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##Provisionar clusters do HDInsight
O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de armazenamento do Azure e um contêiner de armazenamento são necessários para poder criar um cluster HDInsight.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para criar uma conta do Armazenamento do Azure**

Depois de importar o arquivo publishsettings, você pode usar o comando a seguir para criar uma conta de armazenamento:

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [lista do centro de dados](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o portal do Azure, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/).

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Para obter detalhes sobre como obter as informações usando o portal do Azure, consulte a seção "Exibir, copiar e regenerar chaves de acesso de armazenamento" de [Criar, gerenciar ou excluir uma conta de armazenamento](../storage-create-storage-account/).

**Para criar o contêiner de armazenamento do Azure**

O PowerShell do Azure não pode criar um contêiner de Blob durante o processo de provisionamento do HDInsight. Você pode criar um contêiner usando o script a seguir:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para provisionar um cluster**

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


A captura de tela a seguir mostra a execução do script:

![HDI.PS.Provision][image-hdi-ps-provision]




##Listar detalhes do cluster
Use o seguinte comando para listar todos os clusters na assinatura atual:

	Get-AzureHDInsightCluster 

Use o seguinte comando para mostrar os detalhes de um cluster específico na assinatura atual:

	Get-AzureHDInsightCluster -Name <ClusterName> 

##Excluir clusters
Use o seguinte comando para excluir um cluster:

	Remove-AzureHDInsightCluster -Name <ClusterName> 



##Conceder/revogar acesso aos serviços HTTP

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Este é um exemplo:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

No exemplo, <i>hdiv2</i> é um nome de cluster HDInsight.

>[AZURE.NOTE]Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.

Isso também pode ser feito por meio do portal do Azure. Consulte [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal].

##Dimensionar clusters
Consulte [Dimensionar clusters Hadoop no HDInsight](hdinsight-hadoop-cluster-scaling.md).

##Enviar trabalhos MapReduce
A distribuição do cluster HDInsight é fornecida com alguns exemplos de MapReduce. Um dos exemplos é para a contagem de frequências de palavras em arquivos de origem.

**Para enviar um trabalho MapReduce**

O seguinte script do PowerShell do Azure envia o trabalho de exemplo de contagem de palavras:
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
Para obter informações sobre o prefixo **wasb**, consulte [Usar o armazenamento de Blob do Azure para HDInsight][hdinsight-storage].

**Para baixar a saída do trabalho MapReduce**

O script do PowerShell do Azure a seguir recupera a saída do trabalho MapReduce do último procedimento:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Para obter mais informações sobre como desenvolver e executar trabalhos MapReduce, consulte [Usando o MapReduce com o HDInsight][hdinsight-use-mapreduce].






































##Enviar trabalhos Hive
A distribuição dos clusters HDInsight é fornecida com uma tabela Hive de exemplo chamada *hivesampletable*. Você pode usar um comando HiveSQL **SHOW TABLES** para listar as tabelas Hive em um cluster.

**Para enviar um trabalho Hive**

O script a seguir envia um trabalho Hive para listar as tabelas Hive:
	
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

O trabalho Hive mostrará primeiro as tabelas Hive criadas no cluster e os dados retornados da tabela hivesampletable.

Para obter mais informações sobre como usar o Hive, consulte [Usando o Hive com o HDInsight][hdinsight-use-hive].


##Carregar dados no armazenamento de Blob do Azure
Consulte [Carregar dados no HDInsight][hdinsight-upload-data].

##Baixar a saída do trabalho do armazenamento de Blob do Azure
Consulte a sessão [Enviar trabalhos MapReduce](#mapreduce) neste artigo.

## Consulte também
* [Documentação de referência do cmdlet do HDInsight][hdinsight-powershell-reference]
* [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar o HDInsight usando uma interface de linha de comando][hdinsight-admin-cli]
* [Provisione clusters HDInsight][hdinsight-provision]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png


 

<!---HONumber=August15_HO6-->