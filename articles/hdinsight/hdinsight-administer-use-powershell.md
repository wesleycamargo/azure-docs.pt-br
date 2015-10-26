<properties
	pageTitle="Gerenciar clusters de Hadoop no HDInsight com o PowerShell | Microsoft Azure"
	description="Saiba como realizar tarefas administrativas para os clusters Hadoop no HDInsight usando o PowerShell do Azure."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight Usando o PowerShell do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como gerenciar clusters HDInsight do Azure usando um console local do PowerShell do Azure com Windows PowerShell. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].



**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

	> [AZURE.NOTE]Os scripts do PowerShell fornecidos neste artigo usam o modo do gerenciador de recursos do Azure. Para garantir que os exemplos funcionem para você, baixe o último Azure PowerShell usando o Microsoft Web Platform Installer.

##Provisionar clusters do HDInsight

Os clusters HDInsight requerem um grupo de recursos do Azure e um contêiner de Blob em uma Conta de armazenamento do Azure:

- O Grupo de recursos do Azure é um contêiner lógico para recursos do Azure. O grupo de recursos do Azure e o cluster HDInsight não precisam estar no mesmo local. Para obter mais informações, consulte [Usando o PowerShell do Azure com o Gerenciador de Recursos do Azure](powershell-azure-resource-manager.md)
- O HDInsight usa um Contêiner de blob de uma Conta de armazenamento do Azure como o sistema de arquivos padrão. Uma conta de armazenamento do Azure e um contêiner de armazenamento são necessários para poder criar um cluster HDInsight. A conta de armazenamento padrão e o cluster HDInsight precisam estar no mesmo local.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Para criar um grupo de recursos do Azure**

1. Verifique se você está no modo de recursos do Azure:

		Switch-AzureMode -Name AzureResourceManager

2. Conecte-se à sua conta do Azure e selecione uma assinatura (caso você tenha várias assinaturas).

		Add-AzureAccount
		Select-AzureSubscription

3. Criar um novo grupo de recursos:

	New-AzureResourceGroup -name <AzureResourceGroupName> -Location <AzureDataCente> #, por exemplo, "Oeste dos EUA"

	[AZURE.INCLUDE [lista do centro de dados](../../includes/hdinsight-pricing-data-centers-clusters.md)]

**Para criar uma conta do Armazenamento do Azure**

	New-AzureStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

	For a full list of the storage account types, see [https://msdn.microsoft.com/pt-BR/library/azure/hh264518.aspx](https://msdn.microsoft.com/pt-BR/library/azure/hh264518.aspx).


Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o portal de visualização do Azure, confira [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

Para obter detalhes sobre como obter as informações usando o portal de visualização, confira a seção "Exibir, copiar e regenerar teclas de acesso de armazenamento" de [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

**Para criar o contêiner de armazenamento do Azure**

O PowerShell do Azure não pode criar um contêiner de Blob durante o processo de provisionamento do HDInsight. Você pode criar um contêiner usando o script a seguir:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**Para provisionar um cluster**

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Listar detalhes do cluster
Use o seguinte comando para listar todos os clusters na assinatura atual:

	Get-AzureHDInsightCluster

Use o seguinte comando para mostrar os detalhes de um cluster específico na assinatura atual:

	Get-AzureHDInsightCluster -ResourceGroupName <ResouceGroupName> -ClusterName <ClusterName>

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

Isso também pode ser feito por meio do portal de visualização. Confira [Administrar o HDInsight usando o portal de visualização do Azure][hdinsight-admin-portal].

##Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

>[AZURE.NOTE]Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades. Confira [Familiarizar-se com a interface do portal de cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

O impacto da alteração do número de nós de dados em cada tipo de cluster com suporte do HDInsight:

- O Hadoop

	Você pode aumentar continuamente o número de nós de trabalhador em um cluster Hadoop em execução sem afetar os trabalhos pendentes ou em execução. Novos trabalhos também podem ser enviados enquanto a operação está em andamento. Falhas em uma operação de dimensionamento são normalmente manipuladas para que o cluster sempre seja deixado em um estado funcional.

	Quando um cluster Hadoop é reduzido verticalmente pela diminuição do número de nós de dados, alguns dos serviços no cluster são reiniciados. Isso faz com que todos os trabalhos em execução e pendentes falhem após a conclusão da operação de dimensionamento. Você pode, no entanto, reenviar os trabalhos quando a operação for concluída.

- HBase

	Você pode adicionar ou remover diretamente nós do cluster HBase enquanto ele é executado. Servidores Regionais são equilibrados automaticamente em alguns minutos após o término da operação de dimensionamento. No entanto, você pode equilibrar manualmente os servidores regionais fazendo logon no nó de cabeçalho do cluster e executando os seguintes comandos em uma janela de prompt de comando:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Para obter mais informações sobre como usar o shell do HBase, confira
- Storm

	Você pode adicionar ou remover nós de dados continuamente para seu cluster Strom enquanto ele é executado. Mas, após a conclusão bem-sucedida da operação de dimensionamento, você precisará redistribuir a topologia.

	A redistribuição pode ser feita de duas maneiras:

	* Interface da Web Storm
	* Ferramenta CLI (interface de linha de comando)

	Consulte a [documentação do Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

	A IU da Web do Storm está disponível no cluster HDInsight:

	![hdinsight storm dimensionar novo balanceamento](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Aqui está um exemplo de como usar o comando CLI para reequilibrar a topologia do Storm:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para alterar o tamanho do cluster Hadoop usando o PowerShell do Azure, execute este comando no computador cliente:

	Set-AzureHDInsightClusterSize -Name <ClusterName> -ClusterSizeInNodes <NewSize>



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
* [Administrar o HDInsight usando o portal de visualização do Azure][hdinsight-admin-portal]
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

<!---HONumber=Oct15_HO3-->