<properties
	pageTitle="Gerenciar clusters do Hadoop usando a CLI do Azure | Microsoft Azure"
	description="Como usar a CLI do Azure para gerenciar clusters do Hadoop no HDInsight"
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
	ms.date="06/08/2015"
	ms.author="jgao"/>

# Gerenciar clusters do Hadoop no HDInsight usando a interface de linha de comando do Azure (CLI do Azure)

Saiba como usar a CLI do Azure para gerenciar clusters do Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

A CLI do Azure é de software livre. O código-fonte é gerenciado no GitHub em <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>.

Este artigo aborda apenas o uso da CLI do Azure com o HDInsight. Para obter um orientação geral sobre como usar a CLI do Azure, consulte [Cmo usar a CLI do Azure][azure-command-line-tools].


##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../xplat-cli.md) para obter informações de instalação e configuração.

##Instalação

Se você ainda não tiver feito isso, use o documento [Instalar e configurar a CLI do Azure](../xplat-cli.md) para instalar e configurar a CLI do Azure.

##Provisionar um cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]


O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight.

Depois de importar o arquivo publishsettings, você pode usar o comando a seguir para criar uma conta de armazenamento:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE]A conta de armazenamento deve ser colocada com o HDInsight no data center.


Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o Portal do Azure, consulte [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:

	-- Lists Storage accounts
	azure account storage list
	-- Shows a Storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a Storage account
	azure account storage keys list <StorageAccountName>

Para obter detalhes sobre como obter as informações usando o portal do Azure, consulte a seção "Exibir, copiar e regenerar chaves de acesso de armazenamento" de [Criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].


O comando **azure hdinsight cluster create** cria o contêiner caso ela não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster:

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##Provisionar um cluster HDInsight usando um arquivo de configuração
Normalmente, você provisiona um cluster HDInsight, executa trabalhos nele e, em seguida, exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.

	azure hdinsight cluster config create <file>

	azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

	azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
	       --storageAccountKey "<StorageAccountKey>"

	azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

	azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
	       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

	azure hdinsight cluster create --config <file>



![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


##Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Excluir um cluster
Use o seguinte comando para excluir um cluster:

	azure hdinsight cluster delete <ClusterName>




##Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o portal do Azure][hdinsight-admin-portal]
* [Administrar clusters HDInsight usando o PowerShell do Azure][hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Como usar a CLI do Azure][azure-command-line-tools]


[azure-command-line-tools]: ../xplat-cli.md
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"
 

<!---HONumber=62-->