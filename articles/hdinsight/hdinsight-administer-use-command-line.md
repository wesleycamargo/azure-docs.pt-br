<properties
	pageTitle="Gerenciar clusters do Hadoop usando a CLI do Azure | Microsoft Azure"
	description="Como usar a CLI do Azure para gerenciar clusters do Hadoop no HDInsight"
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight usando a CLI do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar a [Interface de Linha de Comando do Azure](xplat-cli-install.md) para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

Este artigo aborda apenas o uso da CLI do Azure com o HDInsight. Para obter um guia geral sobre como usar a CLI do Azure, veja [Instalar e configurar a CLI do Azure][azure-command-line-tools].

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração.
- **Conecte-se ao Azure** usando o seguinte comando:

		azure login

	Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, consulte [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](xplat-cli-connect.md).
	
- **Alterne para o modo Gerenciador de Recursos do Azure** usando o seguinte comando:

		azure config mode arm

Para obter ajuda, use a opção **-h**. Por exemplo:

	azure hdinsight cluster create -h
	
##Criar clusters

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

Você deve ter um ARM (Gerenciamento de Recursos do Azure) e uma conta de armazenamento de Blob do Azure antes de poder criar um cluster HDInsight. Para criar um cluster HDInsight, você deve especificar o seguinte:

- **Grupo de Recursos do Azure**: é necessário criar uma conta da Análise Data Lake em um grupo de Recursos do Azure. O Gerenciador de Recursos do Azure permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada. 

	Para listar os grupos de recursos em sua assinatura:
	
		azure group list 
	
	Para criar um novo grupo de recursos:
	
		azure group create -n "<Resource Group Name>" -l "<Azure Location>"

- **Nome do cluster HDInsight**

- **Local**: um dos datacenters do Azure que dá suporte aos clusters HDInsight. Para obter uma lista dos locais com suporte, confira [Preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

- **Conta de armazenamento padrão** - o HDInsight usa um contêiner de armazenamento do Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight.

	Para criar uma nova conta de armazenamento do Azure:
	
		azure storage account create "<Azure Storage Account Name>" -g "<Resource Group Name>" -l "<Azure Location>" --type LRS

	> [AZURE.NOTE]A conta de armazenamento deve ser colocada com o HDInsight no data center. O tipo de conta de armazenamento não pode ser ZRS, porque o ZRS não dá suporte à tabela.

	Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o Portal do Azure, veja [Criar, gerenciar ou excluir uma conta do Armazenamento][azure-create-storageaccount].
	
	Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:
	
		-- Lists Storage accounts
		azure storage account list
		-- Shows a Storage account
		azure storage account show "<Storage Account Name>"
		-- Lists the keys for a Storage account
		azure storage account keys list "<Storage Account Name>" -g "<Resource Group Name>"

	Para obter detalhes sobre como obter as informações usando o Portal do Azure, veja a seção “Exibir, copiar e regenerar chaves de acesso de armazenamento” de [Criar, gerenciar ou excluir uma conta do Armazenamento][azure-create-storageaccount].

- **Contêiner de Blob Padrão (Opcional)**: o comando **azure hdinsight cluster create** cria o contêiner caso ele não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name "<Storage Account Name>" --account-key <Storage Account Key> [ContainerName]

Depois de preparar a conta de Armazenamento, você estará pronto para criar um cluster:

	azure hdinsight cluster create --resource-group <Resource Group Name> --clusterName <Cluster Name> --location <Location> --osType <Windows | Linux> --version <Cluster Version> --clusterType <Hadoop | HBase | Spark | Storm> --storageAccountName <Default Storage Account Name> --storageAccountKey <Storage Account Key> --storageContainer <Default Storage Container> --username <HDInsight Cluster Username> --password <HDInsight Cluster Password> --sshUserName <SSH Username> --sshPassword <SSH User Password> --workerNodeCount <Number of Worker Nodes>


##Criar clusters usando arquivos de configuração
Normalmente, você cria um cluster HDInsight, executa trabalhos nele e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, para que você possa reutilizá-lo toda vez que criar um cluster.

	azure hdinsight config create [options ] <Config File Path> <overwirte>
	azure hdinsight config add-config-values [options] <Config File Path>
	azure hdinsight config add-script-action [options] <Config File Path>

Exemplo: crie um arquivo de configuração que contém uma ação de script para ser executada durante a criação de um cluster.

	hdinsight config create "C:\myFiles\configFile.config"
	hdinsight config add-script-action --configFilePath "C:\myFiles\configFile.config" --nodeType HeadNode --uri <Script Action URI> --name myScriptAction --parameters "-param value"

##Criar clusters com uma ação de script

Aqui está um exemplo:

	azure hdinsight cluster create -g mahirg001 -l westus -y Linux --clusterType Hadoop --version 3.2 --defaultStorageAccountName mystorageaccount --defaultStorageAccountKey <defaultStorageAccountKey> --defaultStorageContainer mycontainer --userName admin --password <clusterPassword> --sshUserName sshuser --sshPassword <sshPassword> --workerNodeCount 1 –configurationPath "C:\myFiles\configFile.config" myNewCluster01
	
Para obter informações gerais sobre ação de script, veja [Personalizar clusters HDInsight usando a Ação de Script (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

##Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:

	azure hdinsight cluster list
	azure hdinsight cluster show <Cluster Name>

![HDI.CLIListCluster][image-cli-clusterlisting]


##Excluir clusters
Use o seguinte comando para excluir um cluster:

	azure hdinsight cluster delete <Cluster Name>

##Dimensionar clusters

Para alterar o tamanho do cluster Hadoop:

	azure hdinsight cluster resize [options] <clusterName> <Target Instance Count>


## Habilitar/desabilitar o acesso HTTP para um cluster

	azure hdinsight cluster enable-http-access [options] <Cluster Name> <userName> <password>
	azure hdinsight cluster disable-http-access [options] <Cluster Name>

## Habilitar/desabilitar o acesso RDP para um cluster

  	azure hdinsight cluster enable-rdp-access [options] <Cluster Name> <rdpUserName> <rdpPassword> <rdpExpiryDate>
  	azure hdinsight cluster disable-rdp-access [options] <Cluster Name>


##Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o Portal do Azure][hdinsight-admin-portal]
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

<!---HONumber=AcomDC_1217_2015-->