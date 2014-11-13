<properties urlDisplayName="HDInsight Administration" pageTitle="Gerenciar clusters Hadoop usando o Cross-Platform Command-Line | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure, hadoop, administration" description="Saiba como usar o Cross-Platform Command-Line Interface para gerenciar clusters Hadoop no HDIsight em qualquer plataforma com suporte ao Node.js, incluindo Windows, Mac e Linux." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administrar clusters Hadoop usando o Cross-platform Command-line Interface" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# Gerenciar clusters Hadoop no HDInsight Usando a interface de linha de comando da plataforma cruzada

Neste artigo, você aprenderá como usar a interface de linha de comando da plataforma cruzada para gerenciar clusters Hadoop no HDInsight. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que dê suporte a Node.js, incluindo Windows, Mac e Linux.

A ferramenta de linha de comando é software livre. O código fonte é gerenciado no GitHub em <https://github.com/WindowsAzure/azure-sdk-tools-xplat>.

Este artigo aborda apenas o uso da interface de linha de comando do Windows. Para obter um guia geral de como usar a interface de linha de comando, consulte [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]. Para obter documentação de referência abrangente, consulte [Ferramenta de Linha de Comando do Azure para Mac e Linux][Ferramenta de Linha de Comando do Azure para Mac e Linux].

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

-   **Assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][Opções de compra], [Ofertas para membros][Ofertas para membros] ou [Avaliação gratuita][Avaliação gratuita].

## Neste artigo

-   [Instalação][Instalação]
-   [Baixar e importar as publishsettings da conta do Azure][Baixar e importar as publishsettings da conta do Azure]
-   [Provisionar um cluster][Provisionar um cluster]
-   [Provisionar um cluster usando um arquivo de configuração][Provisionar um cluster usando um arquivo de configuração]
-   [Listar e mostrar clusters][Listar e mostrar clusters]
-   [Excluir um cluster][Excluir um cluster]
-   [Próximas etapas][Próximas etapas]

## <span id="installation"></span></a> Instalação

A interface de linha de comando pode ser instalada usando o *NPM (Gerenciador de Pacotes do Node.js)* ou o Windows Installer.

**Para instalar a interface de linha de comando usando o NPM**

1.  Procure o **www.nodejs.org**.
2.  Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.  Abra o **Prompt de Comando** (ou o *Prompt de Comando do Azure* ou o *Prompt de Comando do Desenvolvedor para VS2012*) em sua estação de trabalho.
4.  Execute o seguinte comando na janela de prompt de comando.

        npm install -g azure-cli

    > [WACOM.NOTE] Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: *C:\\Program Files (x86)\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm* ou *C:\\Program Files\\nodejs;C:\\Users[username]\\AppData\\Roaming\\npm*

5.  Execute o comando a seguir para verificar a instalação:

        azure hdinsight -h

    Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

        azure -h
        azure hdinsight -h
        azure hdinsight cluster -h
        azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.  Navegue até **http://azure.microsoft.com/pt-br/downloads/**.
2.  Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

## <span id="importsettings"></span></a> Baixar e importar as publishsettings da conta do Azure

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo publishsettings. O arquivo publishsettings pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar as publishsettings apenas uma vez.

> [WACOM.NOTE] O arquivo publishsettings contém informações confidenciais. É recomendável excluir o arquivo ou executar etapas adicionais para criptografar a pasta de usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.

**Para baixar e importar as publishsettings**

1.  Abra um **Prompt de comando**.
2.  Execute o comando a seguir para baixar o arquivo publishsettings.

        azure account download

    ![HDI.CLIAccountDownloadImport][HDI.CLIAccountDownloadImport]

    O comando mostra as instruções para baixar o arquivo, incluindo uma URL.

3.  Abra o **Internet Explorer** e navegue até a URL listada na janela do prompt de comando.
4.  Clique em **Salvar** para salvar o arquivo na estação de trabalho.
5.  Na janela do prompt de comando, execute o seguinte comando para importar o arquivo publishsettings:

        azure account import <file>

    Na captura de tela anterior, o arquivo publishsettings foi salvo na pasta C:\\HDInsight na estação de trabalho.

## <span id="provision"></span></a>Provisione um cluster HDInsight

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight.

Depois de importar o arquivo publishsettings, você pode usar o seguinte comando para criar uma conta de armazenamento:

    azure account storage create [options] <StorageAccountName>

> [WACOM.NOTE] A conta de armazenamento deve ser colocada no mesmo data center. No momento, você só poderá provisionar clusters do HDInsight nos seguintes datacenters:

> -   Sudeste Asiático
> -   Norte da Europa
> -   Europa Ocidental
> -   Leste dos EUA
> -   Oeste dos EUA

Para obter informações sobre como criar uma conta de Armazenamento do Azure usando o Portal de Gerenciamento do Azure, consulte [Como criar uma conta de armazenamento][Como criar uma conta de armazenamento].

Se já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, você poderá usar os seguintes comandos para recuperar as informações:

    -- lists storage accounts
    azure account storage list
    -- Shows a storage account
    azure account storage show <StorageAccountName>
    -- Lists the keys for a storage account
    azure account storage keys list <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* em [Como gerenciar contas de armazenamento][Como gerenciar contas de armazenamento].

O comando *azure hdinsight cluster create* cria o contêiner caso ela não exista. Se você optar por criar o contêiner com antecedência, poderá usar o seguinte comando:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster:

    azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName <StorageAccountName> --storageAccountKey <storageAccountKey> --storageContainer <StorageContainer> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

![HDI.CLIClusterCreation][HDI.CLIClusterCreation]

## <span id="provisionconfigfile"></span></a> Provisionar um cluster HDInsight usando um arquivo de configuração

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
         

![HDI.CLIClusterCreationConfig][HDI.CLIClusterCreationConfig]

## <span id="listshow"></span></a> Listar e mostrar detalhes do cluster

Use os seguintes comandos para listar e mostrar os detalhes do cluster:

    azure hdinsight cluster list
    azure hdinsight cluster show <ClusterName>

![HDI.CLIListCluster][HDI.CLIListCluster]

## <span id="delete"></span></a> Excluir um cluster

Use o seguinte comando para excluir um cluster:

    azure hdinsight cluster delete <ClusterName>

## <span id="nextsteps"></span></a>Próximas etapas

Neste artigo, você aprendeu a executar diferentes tarefas administrativas de cluster HDInsight. Para saber mais, consulte os seguintes artigos:

-   [Administrar o HDInsight usando o Portal de Gerenciamento][Administrar o HDInsight usando o Portal de Gerenciamento]
-   [Administrar o HDInsight usando o PowerShell][Administrar o HDInsight usando o PowerShell]
-   [Introdução ao Azure HDInsight][Introdução ao Azure HDInsight]
-   [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]
-   [Ferramenta de linha de comando do Azure para Mac e Linux][Ferramenta de Linha de Comando do Azure para Mac e Linux]

  [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: ../xplat-cli/
  [Ferramenta de Linha de Comando do Azure para Mac e Linux]: ../command-line-tools/
  [Opções de compra]: http://azure.microsoft.com/pt-br/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/pt-br/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/pt-br/pricing/free-trial/
  [Instalação]: #installation
  [Baixar e importar as publishsettings da conta do Azure]: #importsettings
  [Provisionar um cluster]: #provision
  [Provisionar um cluster usando um arquivo de configuração]: #provisionconfigfile
  [Listar e mostrar clusters]: #listshow
  [Excluir um cluster]: #delete
  [Próximas etapas]: #nextsteps
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-administer-use-command-line/HDI.CLIAccountDownloadImport.png
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Como gerenciar contas de armazenamento]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-administer-use-command-line/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-administer-use-command-line/HDI.CLIListClusters.png "Listar e mostrar clusters"
  [Administrar o HDInsight usando o Portal de Gerenciamento]: ../hdinsight-administer-use-management-portal/
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
