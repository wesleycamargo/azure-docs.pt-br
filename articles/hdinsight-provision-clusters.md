<properties linkid="manage-services-hdinsight-provision-hadoop-clusters" urlDisplayName="HDInsight Administration" pageTitle="Provision Hadoop clusters in HDInsight | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to provision clusters for Azure HDInsight using the management portal, PowerShell, or the command line." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" documentationCenter="" title="Provision Hadoop clusters in HDInsight" authors="jgao" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Provisionar clusters Hadoop no HDInsight usando opções de personalização

Neste artigo você aprenderá as diferentes formas de provisionar de maneira personalizada um cluster Hadoop no Azure HDInsight — usando o Portal de Gerenciamento do Azure, o PowerShell, ferramentas de linha de comando ou o SDK do .NET do HDInsight . Este artigo aborda o provisionamento de clusters Hadoop. Para obter instruções sobre como provisionar um cluster HBase, consulte [Provision HBase cluster in HDInsight (Provisionar um cluster HBase no HDInsight)][]. Consulte [Qual a diferença entre Hadoop e HBase?][] para entender por que você opta por um em vez do outro.

## O que é um cluster HDInsight?

Você já pensou por que mencionamos clusters sempre que falamos sobre Hadoop ou BigData? O motivo é que o Hadoop permite o processamento distribuído de grandes volumes de dados, espalhados em diferentes nós de um cluster. O cluster tem uma arquitetura de mestre/servidor subordinado com um mestre (também chamado headnode ou namenode) e qualquer número de servidores subordinados (também chamados nó de dados). Para obter mais informações, consulte [Apache Hadoop][].

Um cluster HDInsight resume os detalhes de implementação do Hadoop para que você não precise se preocupar com a forma de comunicação com diferentes nós de um cluster. Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Para obter mais informações, consulte [Introdução ao Hadoop no HDInsight][].

Este artigo fornece instruções sobre diferentes maneiras de provisionar um cluster. Se você procura uma abordagem de início rápido para provisionar um cluster, consulte [Introdução ao Azure HDInsight][].

**Pré-requisitos:**

Antes de começar este artigo, você deve ter o seguinte:

-   Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do PowerShell do HDInsight executam as tarefas com sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][], [Ofertas para membros][] ou [Avaliação gratuita][].

## Neste artigo

-   [Usando o Portal de Gerenciamento do Azure][]
-   [Usando o PowerShell do Azure][]
-   [Usando linha de comando entre plataformas][]
-   [Usando o SDK do .NET do HDInsight][]
-   [Próximas etapas][]

## <span id="portal"></span></a> Usando o Portal de Gerenciamento do Azure

Clusters HDInsight usam um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight][]. Para obter detalhes sobre como criar uma conta de armazenamento do Azure, consulte [Como criar uma conta de armazenamento][].

> [WACOM.NOTE] Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.

**Para criar um cluster HDInsight usando a opção de criação personalizada**

1.  Entre no [Portal de Gerenciamento do Azure][].
2.  No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.
3.  Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

    ![HDI.CustomCreateCluster][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriedade</th>
    <th align="left">Valor</th>
    </tr>
    <tr class="odd">
    <td align="left">Nome do cluster</td>
    <td align="left"><p>Nome do cluster.</p>
    <ul>
    <li>O nome DNS deve iniciar e terminar com alfa numéricos e pode conter traços.</li>
    <li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Tipo de cluster</td>
    <td align="left">Para o tipo de cluster, selecione <strong>Hadoop</strong>.</td>
    </tr>
    <tr class="odd">
    <td align="left">Versão do HDInsight</td>
    <td align="left">Escolha a versão. Para o Hadoop, o padrão é o HDInsight versão 3.1, que usa o Hadoop 2.4.</td>
    </tr>
    </table>

    Insira ou selecione os valores, conforme mostrado na tabela, e clique na seta para a direita.

4.  Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	<table border="1">
    <tr><th> Nome                </th><th> Valor </th></tr>
	<tr><td> Nós de dados        </td><td> Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único.  <br />                                                                                                                                                                                                                                                                                                                                                                                                                
                           O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite. </td></tr>
    <tr><td> Região/Rede virtual </td><td> Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região especificada aqui. As regiões disponíveis são: <strong>Ásia Oriental</strong>, <strong>Sudeste da Ásia</strong>, <strong>Norte da Europa</strong>, <strong>Oeste da Europa</strong>, <strong>Leste dos EUA</strong>, <strong>Oeste dos EUA</strong>, <strong>Centro-Norte dos EUA</strong> e <strong>Centro-Sul dos EUA</strong> </td></tr>
	</table>

5.  Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![HDI.CustomCreateCluster.ClusterUser][]

	<table border="1">
    <tr><th> Propriedade                        </th><th> Valor </th></tr>
    <tr><td> Nome de usuário                    </td><td> Especifique o nome do usuário do cluster HDInsight. </td></tr>
    <tr><td> Senha/Confirmar senha              </td><td> Especifique a senha do usuário do cluster HDInsight.    </td></tr>
    <tr><td> Inserir Hive/Oozie Metastore       </td><td> Marque esta caixa de seleção para especificar um banco de dados SQL no mesmo data center que o cluster a ser usado como o metastore do Hive/Oozie. Isso será útil se você quiser reter os metadados sobre os trabalhos do Hive/Oozie após um cluster ter sido excluído. </td></tr>
    <tr><td> Banco de dados da metastore        </td><td> Especifique o banco de dados SQL do Azure que será usado como o metastore para Hive/OOzie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo data center, conforme especificado na página <strong>Detalhes do Cluster</strong>. </td></tr>
    <tr><td> Usuário do banco de dados          </td><td> Especifique o usuário do Banco de Dados SQL que será usado para a conexão ao banco de dados. </td></tr>
    <tr><td> Senha do usuário do banco de dados  </td><td> Especifique a senha do usuário do Banco de Dados SQL. </td></tr>
	</table>

    > [WACOM.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e, para **Serviços do Windows Azure**, clique em **Sim** e em **Salvar**.

    Clique na seta à direita.

6.  Na página **Conta de Armazenamento**, forneça seguinte valor:

    ![HDI.CustomCreateCluster.StorageAccount][]

    <table>
    <colgroup>
    <col width="50%" />
    <col width="50%" />
    </colgroup>
    <tr class="header">
    <th align="left">Propriedade</th>
    <th align="left">Valor</th>
    </tr>
    <tr class="odd">
    <td align="left">Conta de armazenamento</td>
    <td align="left">Especifique a conta de armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
    <ul>
    <li>Usar Armazenamento Existente</li>
    <li>Criar Novo Armazenamento</li>
    <li>Usar Armazenamento de Outra Assinatura</li>
    </ul></td>
    </tr>
    <tr class="even">
    <td align="left">Nome da conta</td>
    <td align="left"><ul>
    <li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de armazenamento existente. A lista suspensa exibe somente as contas de armazenamento localizadas no mesmo data center onde você optou por provisionar o cluster.</li>
    <li>Se você selecionar as opções <strong>Criar novo armazenamento</strong> ou <strong>Usar armazenamento de outra assinatura</strong>, deverá fornecer o nome da conta de armazenamento.</li>
    </ul></td>
    </tr>
    <tr class="odd">
    <td align="left">Chave de conta</td>
    <td align="left">Se você selecionar a opção <strong>Usar armazenamento de outra assinatura</strong>, especifique a chave de conta para essa conta de armazenamento.</td>
    </tr>
    <tr class="even">
    <td align="left">Contêiner padrão</td>
    <td align="left"><p>Especifica o contêiner padrão na conta de armazenamento usado como o sistema de arquivos padrão para o cluster HDInsight. Se você selecionar <strong>Usar Armazenamento Existente</strong> para o campo <strong>Conta de Armazenamento</strong>, e não houver contêineres existentes nessa conta, o contêiner será criado por padrão com o mesmo nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta de armazenamento existente tiver contêiner com um nome diferente do nome do cluster que você especificou, você também pode usar esse contêiner.</p>
    <p>Caso opte por criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure, especifique o nome do contêiner padrão</p></td>
    </tr>
    <tr class="odd">
    <td align="left">Contas de armazenamento adicionais</td>
    <td align="left">O HDInsight oferece suporte a várias contas de armazenamento. Não há nenhum limite de contas de armazenamento adicionais que podem ser usadas por um cluster. No entanto, se criar um cluster usando o Portal de Gerenciamento, você terá um limite de sete, devido às restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de Conta de Armazenamento ao assistente, onde você pode especificar as informações da conta. Por exemplo, na captura de tela acima, uma conta de armazenamento adicional está selecionada, e portanto a página cinco é adicionada à caixa de diálogo.</td>
    </tr>
    </table>

    Clique na seta à direita.

7.  Na página **Conta de Armazenamento**, insira as informações da conta de armazenamento adicional:

    ![HDI.CustomCreateCluster.AddOnStorage][]

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.

    Clique na marca de seleção para começar a provisionar o cluster. Quando o provisionamento for concluído, a coluna de status mostrará **Em Execução**.

    > [WACOM.NOTE] Depois de escolher uma conta de armazenamento do Azure para o cluster HDInsight, você não poderá excluir a conta nem alterá-la para uma conta diferente.

## <span id="powershell"></span></a> Usando o PowerShell do Azure

O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece informações sobre como provisionar um cluster HDInsight usando Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do HDInsight Powershell, consulte [Instalar e configurar o PowerShell o Azure][]. Para obter mais informações sobre como usar o PowerShell com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell][]. Para obter a lista de cmdlets do PowerShell do HDInsight, consulte [Referência a cmdlets do HDInsight][].

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell:

-   Criar uma conta de Armazenamento do Azure
-   Criar um contêiner de Blob do Azure
-   Criar um cluster HDInsight

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de armazenamento do Azure e um contêiner de armazenamento são necessários para poder criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight.

**Para criar uma conta de Armazenamento do Azure**

-   Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

        $storageAccountName = "<StorageAcccountName>"   # Provide a storage account name 
        $location = "<MicrosoftDataCenter>"             # For example, "West US"

        # Create an Azure storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

    Se você já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, poderá usar os seguintes comandos do PowerShell para recuperar as informações:

        # List storage accounts for the current subscription
        Get-AzureStorageAccount

        # List the keys for a storage account
        Get-AzureStorageKey "<StorageAccountName>"

**Para criar o contêiner de armazenamento de Blob do Azure**

-   Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

        $storageAccountName = "<StorageAccountName>"    # Provide the storage account name 
        $storageAccountKey = "<StorageAccountKey>"      # Provide either primary or secondary key
        $containerName="<ContainerName>"                # Provide a container name

        # Create a storage context object
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                               -StorageAccountKey $storageAccountKey  

        # Create a Blob storage container
        New-AzureStorageContainer -Name $containerName -Context $destContext

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster.

**Para provisionar o cluster HDInsight**

-   Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

        $subscriptionName = "<SubscriptionName>"        # Name of the Azure subscription.
        $storageAccountName = "<StorageAccountName>"    # Azure storage account that hosts the default container. 
        $containerName = "<ContainerName>"              # Azure Blob container that is used as the default file system for the HDInsight cluster.

        $clusterName = "<HDInsightClusterName>"         # The name you will name your HDInsight cluster.
        $location = "<MicrosoftDataCenter>"             # The location of the HDInsight cluster. It must in the same data center as the storage account.
        $clusterNodes = <ClusterSizeInNodes>            # The number of nodes in the HDInsight cluster.

        # Get the storage primary key based on the account name
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

        # Create a new HDInsight cluster
        New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

    Quando solicitado, insira as credenciais para o cluster. Pode levar vários minutos para que a provisão do cluster seja concluída.

    ![HDI.CLI.Provision][]

**Para provisionar um cluster HDInsight usando as opções de configuração personalizadas**

Enquanto provisiona um cluster, você pode usar as outras opções de configuração, como conectar-se a mais de um armazenamento de Blob do Azure ou usar um banco de dados SQL do Azure para metastores do Hive e do Oozie. Isso permite separar o tempo de vida de seus dados e metadados do tempo de vida do cluster.

-   Execute os seguintes comandos em uma janela do Windows PowerShell:

        $subscriptionName = "<SubscriptionName>"
        $clusterName = "<ClusterName>"
        $location = "<MicrosoftDataCenter>"
        $clusterNodes = <ClusterSizeInNodes>

        $storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
        $containerName_Default = "<DefaultFileSystemContainerName>"

        $storageAccountName_Add1 = "<AdditionalStorageAccountName>"

        $hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
        $hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
        $oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
        $oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

        # Get the storage account keys
        Select-AzureSubscription $subscriptionName
        $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
        $storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

        $oozieCreds = Get-Credential -Message "Oozie metastore"
        $hiveCreds = Get-Credential -Message "Hive metastore"

        # Create a Blob storage container
        $dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
        New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

        # Create a new HDInsight cluster
        $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
            Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
            Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
            Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
                New-AzureHDInsightCluster -Name $clusterName -Location $location

    > [WACOM.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e, para **Serviços do Windows Azure**, clique em **Sim** e em **Salvar**.

**Para listar os clusters HDInsight**

-   Execute os seguintes comandos em uma janela de console do PowerShell do Azure para listar o cluster HDInsight e verificar se ele foi provisionado com êxito:

        Get-AzureHDInsightCluster -Name <ClusterName>

## <span id="cli"></span></a> Usando linha de comando entre plataformas

Outra opção para o provisionamento de um cluster HDInsight é a interface de linha de comando entre plataformas. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que dê suporte a Node.js, incluindo Windows, Mac e Linux. A ferramenta de linha de comando é software livre. O código-fonte é gerenciado no GitHub em <https://github.com/Azure/azure-sdk-tools-xplat>. Para obter um guia geral de como usar a interface de linha de comando, consulte [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux][]. Para obter documentação de referência abrangente, consulte [Ferramenta de Linha de Comando do Azure para Mac e Linux][]. Este artigo aborda apenas o uso da interface de linha de comando do Windows.

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando a linha de comando entre plataformas:

-   Instalar linha de comando da plataforma cruzada
-   Baixar e importar as configurações de publicação da conta do Azure
-   Criar uma conta de Armazenamento do Azure
-   Provisionar um cluster

A interface de linha de comando pode ser instalada usando o *NPM (Gerenciador de Pacotes do Node.js)* ou o Windows Installer. A Microsoft recomenda instalar usando apenas uma das duas opções.

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

1.  Navegue até **<http://azure.microsoft.com/en-us/downloads/>**.
2.  Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

**Para baixar e importar as configurações de publicação**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar as configurações de publicação apenas uma vez.

> [WACOM.NOTE] O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.

1.  Abra um **Prompt de comando**.
2.  Execute o comando a seguir para baixar o arquivo de configurações da publicação.

        azure account download

    ![HDI.CLIAccountDownloadImport][]

    O comando inicia a página da Web na qual baixar o arquivo de configurações de publicação.

3.  No prompt para salvar o arquivo, clique em **Salvar** e forneça um local onde o arquivo deve ser salvo.
4.  Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

        azure account import <file>

    Na captura de tela anterior, o arquivo de configurações de publicação foi salvo na pasta C:\\HDInsight na estação de trabalho.

**Para criar uma conta de Armazenamento do Azure**

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter.

-   Na janela do prompt de comando, execute o seguinte comando para criar uma conta de armazenamento do Azure:

        azure storage account create [options] <StorageAccountName>

    Quando um local for solicitado, selecione o local onde um cluster HDInsight pode ser provisionado. O armazenamento deve estar no mesmo local que o cluster HDInsight. Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.

Para obter informações sobre como criar uma conta de Armazenamento do Azure usando o Portal de Gerenciamento do Azure, consulte [Como criar uma conta de armazenamento][].

Se você já tiver uma conta de armazenamento, mas não souber o nome e a chave da conta, poderá usar os seguintes comandos para recuperar as informações:

    -- lists storage accounts
    azure storage account list

    -- Shows information for a storage account
    azure storage account show <StorageAccountName>

    -- Lists the keys for a storage account
    azure storage account keys list <StorageAccountName>

Para obter detalhes de como obter informações usando o portal de gerenciamento, consulte *Como: Exibir, copiar e regenerar chaves de acesso de armazenamento* em [Como gerenciar contas de armazenamento][].

Um cluster HDInsight também requer um contêiner dentro de uma conta de armazenamento. Se a conta de armazenamento fornecida ainda não tiver um contêiner, o comando *azure hdinsight cluster create* solicitará um nome de contêiner e também o criará. No entanto, se você desejar criar o contêiner com antecedência, poderá usar o seguinte comando:

    azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]
        

Depois de preparar a conta de armazenamento e o contêiner de blob, você estará pronto para criar um cluster.

**Para provisionar o cluster HDInsight**

-   Execute o seguinte comando na janela do prompt de comando:

        azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <SorageContainerName> --nodes <NumberOfNodes> --location <DataCenterLocation> --username <HDInsightClusterUsername> --clusterPassword <HDInsightClusterPassword>

    ![HDI.CLIClusterCreation][]

**Para provisionar um cluster HDInsight usando um arquivo de configuração**

Normalmente, você provisiona um cluster HDInsight, executa os trabalhos e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.

-   Execute os seguintes comandos na janela do prompt de comando:

        #Create the config file
        azure hdinsight cluster config create <file> 

        #Add commands to create a basic cluster
        azure hdinsight cluster config set <file> --clusterName <ClusterName> --nodes <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --username "<Username>" --clusterPassword "<UserPassword>"

        #If requred, include commands to use additional blob storage with the cluster
        azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
               --storageAccountKey "<StorageAccountKey>"

        #If required, include commands to use a SQL database as a Hive metastore
        azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
               --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

        #If required, include commands to use a SQL database as an Oozie metastore 
        azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
               --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

        #Run this command to create a cluster using the config file     
        azure hdinsight cluster create --config <file>

    > [WACOM.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e, para **Serviços do Windows Azure**, clique em **Sim** e em **Salvar**.

    ![HDI.CLIClusterCreationConfig][]

**Para listar e mostrar detalhes do cluster**

-   Use os seguintes comandos para listar e mostrar os detalhes do cluster:

        azure hdinsight cluster list
        azure hdinsight cluster show <ClusterName>

    ![HDI.CLIListCluster][]

**Para excluir um cluster**

-   Use o seguinte comando para excluir um cluster:

        azure hdinsight cluster delete <ClusterName>

## <span id="sdk"></span></a> Usando o SDK do .NET do HDInsight

O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET.

Os procedimentos a seguir devem ser realizados para provisionar um cluster HDInsight usando o SDK:

-   Instale o SDK do HDInsight .NET
-   Crie um certificado autoassinado
-   Criar um aplicativo de console
-   Executar o aplicativo

**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet][]. As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

1.  Crie um certificado autoassinado usado para autenticar as solicitações. Você pode usar o IIS ou [makecert][] para criar o certificado.

2.  Navegue até o local do certificado, clique com o botão direito do mouse no certificado, clique em **Instalar Certificado** e instale o certificado no repositório pessoal do computador. Edite as propriedades do certificado para atribuir a ele um nome amigável.

3.  Importe o certificado no Portal de Gerenciamento do Azure. No portal, clique em **Configurações** na lado inferior esquerdo da página e clique em **Certificados de Gerenciamento**. Na parte inferior da página, clique em **Carregar** e sigas as instruções para carregar o arquivo .cer criado na etapa anterior.

    ![HDI.ClusterCreate.UploadCert][]

**Para criar um aplicativo de console do Visual Studio**

1.  Abra o Visual Studio 2013.

2.  No menu Arquivo, clique em **Novo** e **Projeto**.

3.  No Novo Projeto, digite ou selecione os seguintes valores:

	<table border="1">
    <tr><th> Propriedade </th><th> Valor                      </th></tr>
    <tr><td> Categoria   </td><td> Modelos/Visual C#/Windows </td></tr>
    <tr><td> Modelo      </td><td> Aplicativo de console      </td></tr>
    <tr><td> Nome        </td><td> CreateHDICluster           </td></tr>
	</table>

4.  Clique em **OK** para criar o projeto.

5.  No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6.  Execute os seguintes comandos no console para instalar os pacotes.

        Install-Package Microsoft.WindowsAzure.Management.HDInsight

    Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7.  No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

8.  Adicione o seguinte usando as instruções na parte superior do arquivo :

        using System.Security.Cryptography.X509Certificates;
        using Microsoft.WindowsAzure.Management.HDInsight;
        using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

9.  Na função Main(), copie e cole o código a seguir:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate your created earlier  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the storage account if it doesn't exist.

        // Create the container if it doesn't exist.

        // Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

        // Supply th cluster information
        ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
        {
            Name = clustername,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = containername,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize
        };

        // Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue.");
        Console.ReadKey();

10. Substitua as variáveis no início da função main().

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.

## <span id="nextsteps"></span></a>Próximas etapas

Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

-   [Introdução ao Azure HDInsight][]
-   [Administrar o HDInsight usando o PowerShell][]
-   [Enviar trabalhos Hadoop de forma programática][]
-   [Documentação do SDK do Azure HDInsight][]

  [Provision HBase cluster in HDInsight (Provisionar um cluster HBase no HDInsight)]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Qual a diferença entre Hadoop e HBase?]: http://go.microsoft.com/fwlink/?LinkId=510237
  [Apache Hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
  [Introdução ao Hadoop no HDInsight]: ../hdinsight-introduction/
  [Introdução ao Azure HDInsight]: ../hdinsight-get-started/
  [Opções de compra]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Ofertas para membros]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Avaliação gratuita]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Usando o Portal de Gerenciamento do Azure]: #portal
  [Usando o PowerShell do Azure]: #powershell
  [Usando linha de comando entre plataformas]: #cli
  [Usando o SDK do .NET do HDInsight]: #sdk
  [Próximas etapas]: #nextsteps
  [Usar o Armazenamento de Blob do Azure com o HDInsight]: ../hdinsight-use-blob-storage/
  [Como criar uma conta de armazenamento]: ../storage-create-storage-account/
  [Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
  [HDI.CustomCreateCluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
  [HDI.CustomCreateCluster.ClusterUser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
  [HDI.CustomCreateCluster.StorageAccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
  [HDI.CustomCreateCluster.AddOnStorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png
  [Instalar e configurar o PowerShell o Azure]: ../install-configure-powershell/
  [Administrar o HDInsight usando o PowerShell]: ../hdinsight-administer-use-powershell/
  [Referência a cmdlets do HDInsight]: http://msdn.microsoft.com/en-us/library/windowsazure/dn479228.aspx
  [HDI.CLI.Provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png
  [Como usar as Ferramentas de Linha de Comando do Azure para Mac e Linux]: ../xplat-cli/
  [Ferramenta de Linha de Comando do Azure para Mac e Linux]: ../command-line-tools/
  [HDI.CLIAccountDownloadImport]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
  [Como gerenciar contas de armazenamento]: ../storage-manage-storage-account/
  [HDI.CLIClusterCreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
  [HDI.CLIClusterCreationConfig]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
  [HDI.CLIListCluster]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Listar e mostrar clusters"
  [NuGet]: http://nuget.codeplex.com/wikipage?title=Getting%20Started
  [makecert]: http://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx
  [HDI.ClusterCreate.UploadCert]: ./media/hdinsight-get-started/HDI.ClusterCreate.UploadCert.png
  [Enviar trabalhos Hadoop de forma programática]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Documentação do SDK do Azure HDInsight]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
