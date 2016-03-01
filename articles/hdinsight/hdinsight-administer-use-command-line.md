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
	ms.date="01/04/2016"
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight usando a CLI do Azure

[AZURE.INCLUDE [seletor](../../includes/hdinsight-portal-management-selector.md)]

Saiba como usar a [Interface de Linha de Comando do Azure](../xplat-cli-install.md) para gerenciar clusters Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

Este artigo aborda apenas o uso da CLI do Azure com o HDInsight. Para obter um guia geral sobre como usar a CLI do Azure, veja [Instalar e configurar a CLI do Azure][azure-command-line-tools].

##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **CLI do azure** - consulte [Instalar e configurar a CLI do Azure](../xplat-cli-install.md) para obter informações de instalação e configuração.
- **Conecte-se ao Azure** usando o seguinte comando:

		azure login

	Para obter mais informações sobre a autenticação usando uma conta de trabalho ou escolar, consulte [Conectar-se a uma assinatura do Azure a partir da CLI do Azure](xplat-cli-connect.md).
	
- **Alterne para o modo Gerenciador de Recursos do Azure** usando o seguinte comando:

		azure config mode arm

Para obter ajuda, use a opção **-h**. Por exemplo:

	azure hdinsight cluster create -h
	
##Criar clusters

Consulte [Gerenciar clusters Hadoop baseados no Windows no HDInsight usando a CLI do Azure](hdinsight-hadoop-create-windows-clusters-cli.md).

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
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"

<!---HONumber=AcomDC_0218_2016-->