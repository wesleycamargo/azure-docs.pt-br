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
	ms.date="04/05/2016"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight Usando o PowerShell do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Neste artigo, você aprenderá como gerenciar clusters HDInsight do Azure usando um console local do PowerShell do Azure com Windows PowerShell. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][hdinsight-powershell-reference].



**Pré-requisitos**

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##Instalar o Azure PowerShell 1.0 e superior

Primeiro, desinstale as versões 0.9x.

Para verificar a versão do PowerShell instalado:

	Get-Module *azure*
	
Para desinstalar a versão mais antiga, execute Programas e Recursos no painel de controle.

Há duas opções principais para a instalação do Azure PowerShell.

- [Galeria do PowerShell](https://www.powershellgallery.com/). Execute os seguintes comandos no PowerShell ISE elevado ou no console Windows PowerShell elevado:

		# Install the Azure Resource Manager modules from PowerShell Gallery
		Install-Module AzureRM
		Install-AzureRM
		
		# Import AzureRM modules for the given version manifest in the AzureRM module
		Import-AzureRM

	Para obter mais informações, veja [Galeria do PowerShell](https://www.powershellgallery.com/).

- [Microsoft Web Platform Installer (WebPI)](http://aka.ms/webpi-azps). Se você tem o Azure PowerShell 0.9.x instalado, será solicitado a desinstalar o 0.9.x. Se tiver instalado os módulos do Azure PowerShell na Galeria do PowerShell, o instalador exigirá que os módulos sejam removidos antes da instalação para assegurar a consistência do Ambiente do Azure PowerShell. Para obter instruções, veja [Instalar o Azure PowerShell 1.0 via WebPI](https://azure.microsoft.com/blog/azps-1-0/).

A WebPI receberá atualizações mensais. A Galeria do PowerShell receberá atualizações continuamente. Se você estiver familiarizado com a instalação a partir da Galeria do PowerShell, esse será o primeiro canal para os melhores e mais recentes recursos do Azure PowerShell.

##Criar clusters

Confira [Criar clusters baseados em Linux no HDInsight usando o Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##Listar clusters
Use o seguinte comando para listar todos os clusters na assinatura atual:

	Get-AzureRmHDInsightCluster

##Mostrar cluster

Use o seguinte comando para mostrar os detalhes de um cluster específico na assinatura atual:

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Excluir clusters
Use o seguinte comando para excluir um cluster:

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Dimensionar clusters
O recurso de dimensionamento de clusters permite que você altere o número de nós de trabalhador usados por um cluster em execução no Azure HDInsight sem precisar recriar o cluster.

>[AZURE.NOTE] Somente clusters HDInsight versão 3.1.3 ou superior são compatíveis. Se não tiver certeza quanto à versão de seu cluster, você poderá verificar a página Propriedades. Confira [Familiarizar-se com a interface do portal de cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

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

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
	

##Conceder/revogar acesso

Os clusters HDInsight têm os seguintes serviços Web HTTP (todos esses serviços têm pontos de extremidade RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por padrão, esses serviços são concedidos para acesso. Você pode revogar/conceder o acesso. Para revogar:

	Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Para conceder:

	$clusterName = "<HDInsight Cluster Name>"

	# Credential option 1
	$hadoopUserName = "admin"
	$hadoopUserPassword = "<Enter the Password>"
	$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
	$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

	# Credential option 2
	#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
	
	Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Ao conceder/revogar o acesso, você redefinirá o nome de usuário de cluster e a senha.

Isso também pode ser feito por meio do Portal. Consulte [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal].

##Atualizar credenciais de usuário HTTP

É o mesmo procedimento para acessar [HTTP Conveder/Revogar](#grant/revoke-access). Se o cluster recebeu o acesso HTTP, você deverá revogá-lo. E, em seguida, conceder acesso com novas credenciais de usuário HTTP.


##Encontrar a conta de armazenamento padrão

O script do Powershell a seguir demonstra como obter o nome da conta de armazenamento padrão e a chave da conta de armazenamento padrão de um cluster.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Encontrar o grupo de recursos

No modo ARM, cada cluster HDInsight pertence a um grupo de recursos do Azure. Encontrar o grupo de recursos:

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Enviar trabalhos

**Enviar trabalhos MapReduce**

Veja [Executar exemplos do MapReduce do Hadoop no HDInsight baseado em Windows](hdinsight-run-samples.md).

**Enviar trabalhos Hive**

Veja [Executar consultas do Hive usando o PowerShell](hdinsight-hadoop-use-hive-powershell.md)

**Enviar trabalhos Pig**

[Consulte Executar trabalhos do Pig usando o PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Para enviar trabalhos de Sqoop**

Consulte [Usar o Sqoop com o HDInsight](hdinsight-use-sqoop.md).

**Para enviar trabalhos de Oozie**

Consulte [Usar o Oozie com Hadoop para definir e executar um fluxo de trabalho no HDInsight](hdinsight-use-oozie.md).

##Carregar dados no armazenamento de Blob do Azure
Consulte [Carregar dados no HDInsight][hdinsight-upload-data].


## Consulte também
* [Documentação de referência do cmdlet do HDInsight][hdinsight-powershell-reference]
* [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar o HDInsight usando uma interface de linha de comando][hdinsight-admin-cli]
* [Criar clusters HDInsight][hdinsight-provision]
* [Carregar dados no HDInsight][hdinsight-upload-data]
* [Enviar trabalhos Hadoop de forma programática][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=AcomDC_0413_2016-->