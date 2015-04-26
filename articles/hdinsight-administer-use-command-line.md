<properties 
	pageTitle="Gerenciar clusters do Hadoop usando a Linha de comando de plataforma cruzada | Azure" 
	description="Saiba como usar o Cross-Platform Command-Line Interface para gerenciar clusters Hadoop no HDIsight em qualquer plataforma com suporte ao Node.js, incluindo Windows, Mac e Linux." 
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

# Gerenciar clusters Hadoop no HDInsight Usando a interface de linha de comando da plataforma cruzada

Neste artigo, você aprenderá como usar a Interface de Linha de comando de plataforma cruzada para gerenciar clusters do Hadoop no HDInsight. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que ofereça suporte ao Node.js, incluindo Windows, Mac e Linux. 

A ferramenta de linha de comando é um software livre.  O código-fonte é gerenciado no GitHub em <a href= "https://github.com/WindowsAzure/azure-sdk-tools-xplat">https://github.com/WindowsAzure/azure-sdk-tools-xplat</a>. 

Este artigo aborda apenas o uso da interface de linha de comando do Windows. Para obter um guia geral de como usar a interface de linha de comando, consulte [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][azure-command-line-tools]. Para obter documentação de referência abrangente, consulte [Ferramenta de Linha de Comando do Azure para Mac e Linux][azure-command-line-tool].


**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

- **Assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##Neste artigo

* [Instalação](#installation)
* [Baixar e importar o arquivo publishsettings da conta do Azure](#importsettings)
* [Provisionar um cluster](#provision)
* [Provisionar um cluster usando um arquivo de configuração](#provisionconfigfile)
* [Listar e mostrar clusters](#listshow)
* [Excluir um cluster](#delete)
* [Próximas etapas](#nextsteps)

##<a id="installation"></a> Instalação
A interface de linha de comando pode ser instalada usando o *Node.js Package Manager (NPM)* ou o Windows Installer.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de comando** (ou *Azure Command Prompt*, ou *Developer Command Prompt for VS2012*) em sua estação de trabalho.
4.	Execute o seguinte comando na janela de prompt de comando.

		npm install -g azure-cli

	> [AZURE.NOTE] Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: <i>C:\Program Files (x86)\nodejs;C:\Users\[username]\AppData\Roaming\npm</i> ou <i>C:\Program Files\nodejs;C:\Users\[username]\AppData\Roaming\npm</i>


5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda.  Por exemplo:
		
		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**.
2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

##<a id="importsettings"></a> Baixar e importar o arquivo publishsettings da conta do Azure

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo publishsettings. O arquivo publishsettings pode ser importado como uma definição de configuração local persistente que a interface de linha de comando usará para operações subsequentes. Você precisa importar as publishsettings somente uma vez.

> [AZURE.NOTE] O arquivo publishsettings contém informações confidenciais. É recomendável excluir o arquivo ou executar etapas adicionais para criptografar a pasta de usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.


**Para baixar e importar as publishsettings**

1.	Abra um **Prompt de comando**.
2.	Execute o comando a seguir para baixar o arquivo publishsettings.

		azure account download
 
	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	O comando mostra as instruções para baixar o arquivo, incluindo uma URL.

3.	Abra o **Internet Explorer** e navegue até a URL listada na janela do prompt de comando.
4.	Clique em **Salvar** para salvar o arquivo na estação de trabalho.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo publishsettings:

		azure account import <file>

	Na captura de tela anterior, o arquivo publishsettings foi salvo na pasta C:\HDInsight na estação de trabalho.


##<a id="provision"></a> Provisionar um cluster HDInsight

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]


O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para que seja possível criar um cluster HDInsight. 

Depois de importar o arquivo publishsettings, você pode usar o seguinte comando para criar uma conta de armazenamento:

	azure account storage create [options] <StorageAccountName>


> [AZURE.NOTE] A conta de armazenamento deve ser posicionada no mesmo datacenter. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

><ul>
<li>Sudeste Asiático</li>
<li>Norte da Europa</li>
<li>Europa Ocidental</li>
<li>Leste dos EUA</li>
<li>Oeste dos EUA</li>
</ul>


Para obter informações sobre como criar uma conta de armazenamento do Azure usando o Portal de Gerenciamento do Azure, consulte [Como criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

	-- lists storage accounts
	azure account storage list
	-- Shows a storage account
	azure account storage show <StorageAccountName>
	-- Lists the keys for a storage account
	azure account storage keys list <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte a seção *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* de [Como criar, gerenciar ou excluir uma conta de armazenamento][azure-create-storageaccount].


O comando *azure hdinsight cluster create* cria o contêiner caso ele não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
		
Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster: 

	azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][image-cli-clustercreation]

















##<a id="provisionconfigfile"></a> Provisionar um cluster do HDInsight usando um arquivo de configuração
Normalmente, você provisiona um cluster do HDInsight, executa trabalhos nele e, em seguida, exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.  
 
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


##<a id="listshow"></a> Listar e mostrar detalhes do cluster
Use os seguintes comandos para listar e mostrar os detalhes do cluster:
	
	azure hdinsight cluster list
	azure hdinsight cluster show <ClusterName>
	
![HDI.CLIListCluster][image-cli-clusterlisting]


##<a id="delete"></a> Excluir um cluster
Use o seguinte comando para excluir um cluster:

	azure hdinsight cluster delete <ClusterName>




##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster do HDInsight. Para saber mais, consulte os seguintes artigos:

* [Administrar o HDInsight usando o Portal de Gerenciamento][hdinsight-admin-portal]
* [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]
* [Introdução ao Azure HDInsight][hdinsight-get-started]
* [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][azure-command-line-tools]
* [Ferramenta de linha de comando do Azure para Mac e Linux][azure-command-line-tool]


[azure-command-line-tools]: ../xplat-cli/
[azure-command-line-tool]: ../command-line-tools/
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-get-started]: ../hdinsight-get-started/

[image-cli-account-download-import]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png 
[image-cli-clustercreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "List and show clusters"

<!--HONumber=42-->
