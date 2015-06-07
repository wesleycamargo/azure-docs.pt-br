<properties 
	pageTitle="Gerenciar clusters do Hadoop usando uma interface de linha de comando | Microsoft Azure" 
	description="Saiba como usar a interface de linha de comando entre plataformas para gerenciar clusters Hadoop no HDIsight em qualquer plataforma com suporte ao Node.js, incluindo Windows, Mac e Linux." 
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
	ms.date="03/31/2015" 
	ms.author="jgao"/>

# Gerenciar clusters Hadoop no HDInsight Usando a interface de linha de comando da plataforma cruzada

Saiba como usar a interface de linha de comando do Azure para Mac, Linux e Windows para gerenciar clusters de Hadoop no Azure HDInsight. A CLI do Azure é implementada no Node. js. Ela pode ser usada em qualquer plataforma que dê suporte ao Node.js, incluindo Windows, Mac e Linux.

A CLI do Azure é de software livre. O código-fonte é gerenciado no GitHub em <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>.

Este artigo aborda apenas o uso da interface de linha de comando do Windows. Para obter um guia geral de como usar a interface de linha de comando, consulte [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][azure-command-line-tools].


##Pré-requisitos

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura do azure** - Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##Instalação
A interface de linha de comando pode ser instalada usando o *NPM (Gerenciador de Pacotes do Node.js)* ou o Windows Installer.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou o **Prompt de Comando do Azure** ou o **Prompt de Comando do Desenvolvedor para VS2012**) em sua estação de trabalho.
4.	Execute o seguinte comando na janela do prompt de comando:

		npm install -g azure-cli

	> [AZURE.NOTE]Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente **PATH**: <i>C:\\Arquivos de Programas (x86)\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i> ou <i>C:\\Arquivos de Programas\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i>


5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção **-h** em diferentes níveis para exibir as informações da Ajuda. Por exemplo:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**.
2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

##Baixar e importar o arquivo publishsettings da conta do Azure

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo publishsettings. O arquivo publishsettings pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar o arquivo publishsettings apenas uma vez.

> [AZURE.NOTE]O arquivo publishsettings contém informações confidenciais. É recomendável excluir o arquivo ou executar etapas adicionais para criptografar a pasta de usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use Criptografia de Unidade de Disco BitLocker.


**Para baixar e importar o arquivo publishsettings**

1.	Abra um prompt de comando.
2.	Execute o comando a seguir para baixar o arquivo publishsettings:

		azure account download
 
	![Conta do Azure para download da Interface de linha de comando.][image-cli-account-download-import]

	O comando mostra as instruções para baixar o arquivo, incluindo uma URL.

3.	Abra o Internet Explorer e navegue até a URL listada na janela do prompt de comando.
4.	Clique em **Salvar** para salvar o arquivo na estação de trabalho.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo publishsettings:

		azure account import <file>

	Na captura de tela anterior, o arquivo publishsettings foi salvo na pasta C:\\HDInsight na estação de trabalho.


##Provisione um cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


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
* [Como usar a CLI do Azure para Mac, Linux e Windows][azure-command-line-tools]


[azure-command-line-tools]: xplat-cli.md
[azure-create-storageaccount]: storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-get-started]: hdinsight-get-started.md

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"

<!--HONumber=54-->