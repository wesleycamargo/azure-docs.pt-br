<properties 
   pageTitle="Provisionar os clusters do Apache Spark no HDInsight | Azure" 
   description="Saiba como provisionar clusters do Spark para o Azure HDInsight usando o portal do Azure, Azure PowerShell, uma linha de comando ou o SDK HDInsight .NET" 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/10/2015"
   ms.author="nitinme"/>

# Provisionar clusters do Apache Spark no HDInsight usando opções personalizadas

Na maioria dos cenários, você pode provisionar um cluster do Spark usando o método de criação rápida, conforme descrito em [Introdução ao Apache Spark no HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md). Em determinados cenários, você talvez queira provisionar um cluster personalizado. Por exemplo, você talvez queira anexar um repositório de metadados externos para manter os metadados de Hive persistentes além do tempo de vida de um cluster, ou você talvez queira usar armazenamento adicional com o cluster.

Para esses e outros cenários, este artigo fornece instruções sobre como usar o portal do Azure, Azure PowerShell ou o SDK do HDInsight .NET para provisionar um cluster do Spark personalizado no HDInsight.


**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a id="configuration"></a>Quais são as opções de configuração diferentes?

###Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar uma conta de armazenamento do Azure adicional que também será associada ao cluster.

>[AZURE.NOTE] Não compartilhe um contêiner de armazenamento de Blob entre múltiplos clusters. Não há suporte para isso.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

###Metastore

O Spark permite definir esquema e tabelas do Hive em dados brutos. Você pode salvar esses esquemas e metadados da tabela em metastores externos. Usar o metastore ajuda a manter seus metadados do Hive, para que você não precise recriar tabelas do Hive ao provisionar um novo cluster. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído.

### Personalização do cluster

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script][hdinsight-customize-cluster].


###Rede Virtual

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-apache-spark-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] Você deve criar a rede virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> O Azure HDInsight dá suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendado designar uma única sub-rede para um cluster.

##<a id="portal"></a> Usando o portal do Azure

Os clusters do Spark no HDInsight usam um contêiner de armazenamento de blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento][azure-create-storageaccount].

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal do Azure][azure-management-portal].
2. No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.
3. Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

	![Fornecer ao Spark detalhes do cluster do HDInsight](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page1.png "Fornecer ao Spark detalhes do cluster do HDInsight")

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td>
		<td><p>Nome do cluster. </p>
			<ul>
			<li>O nome de DNS (sistema de nome de domínio) deve começar e terminar com um caractere alfanumérico e pode conter traços.</li>
			<li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
			</ul></td></tr>
	<tr><td>Tipo de cluster</td>
		<td>Para o tipo de cluster, selecione <strong>Spark</strong>.</td></tr>
	<tr><td>Sistema operacional</td>
		<td>Selecione <b>Windows Server 2012 R2 Data Center</b> para provisionar um cluster do Spark em execução no Windows. </td></tr>
	<tr><td>Versão do HDInsight</td>
		<td>Escolha a versão. Para o Spark, a única opção de versão disponível é a <b>HDInsight 3.2</b>, que usa o Spark 1.3.1.</td></tr>
	</table>
	Insira ou selecione os valores, conforme mostrado na tabela, e clique na seta para a direita.

4. Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	![Fornecer o número de nós e tipos de nós para o cluster](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page2.png "Fornecer o número de nós e tipos de nós para o cluster")

	<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>Nós de dados</td><td>Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.</td></tr>
<tr><td>Região/Rede virtual</td><td><p>Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região que a especificada aqui.</p>.<br/>Se você tiver criado uma Rede Virtual do Azure, poderá selecionar a rede que o cluster HDInsight será configurado para usar.</p><p>Para obter informações sobre como criar uma Rede Virtual do Azure, consulte <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tarefas de configuração de Rede Virtual</a>.</p></td></tr>
<tr><td>Tamanho do nó principal</td><td><p>Selecione um tamanho de VM (máquina virtual) para o nó de cabeçalho.</p></td></tr>
<tr><td>Tamanho do nó de dados</td><td><p>Selecione um tamanho de VM para os nós de dados.</p></td></tr>
</table>
	>[AZURE.NOTE] O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.


5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![Fornecer detalhes do usuário RDP e usuário administrador](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page3.png "Fornecer detalhes do usuário RDP e usuário administrador")

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome de usuário HTTP</td>
		<td>Especifique o nome do usuário do cluster HDInsight.</td></tr>
	<tr><td>Senha/Confirmar senha HTTP</td>
		<td>Especifique a senha do usuário do cluster HDInsight.</td></tr>
	<tr><td>Habilitar área de trabalho remota para o cluster</td>
		<td>Marque esta caixa de seleção para especificar uma data de expiração, senha e nome de usuário para um usuário de área de trabalho remota que possa acessar remotamente os nós do cluster após seu provisionamento. Você também pode habilitar área de trabalho remota mais tarde, depois que o cluster for provisionado. Para instruções, consulte <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.</td></tr>
	<tr><td>Inserir como metastore do Hive/Oozie</td>
		<td>Marque esta caixa de seleção para especificar um banco de dados SQL no mesmo datacenter que o cluster, a ser usado como metastore do Hive/Oozie. Se você marcar esta caixa de seleção, deverá especificar detalhes sobre o banco de dados SQL do Azure nas páginas seguintes do assistente. Isso será útil se você quiser reter os metadados sobre os trabalhos do Hive/Oozie após um cluster ter sido excluído. Para obter instruções sobre como criar um metastore, consulte <a href="https://azure.microsoft.com/pt-br/documentation/articles/sql-database-get-started/" target="_blank">Criar seu primeiro banco de dados SQL do Azure</a>.</td></tr>
	</td></tr>
</table>
	>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e em **Windows** **Azure Services**, clique em **Sim** e em **Salvar**.

    Clique na seta à direita.

6. Na página **Configurar Metastore do Hive/Oozie**, forneça os seguintes valores:

    ![Fornecer detalhes do banco de dados do metastore Hive/Oozie](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page4.png "Fornecer detalhes do banco de dados do metastore Hive/Oozie")

	Especifique um banco de dados SQL do Azure que será usado como metastore do Hive/Oozie. Você pode especificar o mesmo banco de dados para os metastores do Hive e do Oozie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo datacenter que você especificou na página <strong>Detalhes do Cluster</strong>. Especifique também o nome de usuário e senha para se conectar ao banco de dados SQL do Azure selecionado.

    >[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

    Clique na seta à direita.

7. Na página **Conta de Armazenamento**, forneça os seguintes valores:

    ![Insira os detalhes da conta de armazenamento](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page5.png "Insira os detalhes da conta de armazenamento")

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Conta de armazenamento</td>
		<td>Especifique a conta de Armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
		<ul>
			<li><strong>Usar Armazenamento Existente</strong></li>
			<li><strong>Criar Novo Armazenamento</strong></li>
			<li><strong>Usar armazenamento de outra assinatura</strong></li>
		</ul>
		</td></tr>
	<tr><td>Nome da conta</td>
		<td><ul>
			<li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de Armazenamento existente. A lista suspensa exibe somente as contas de armazenamento localizadas no mesmo datacenter onde você optou por provisionar o cluster.</li>
			<li>Se você selecionar uma das opções <strong>Criar novo armazenamento</strong> ou <strong>Usar armazenamento de outra assinatura</strong>, deverá fornecer o nome da conta de armazenamento.</li>
		</ul></td></tr>
	<tr><td>Chave de conta</td>
		<td>Se você escolheu a opção <strong>Usar armazenamento de outra assinatura</strong>, especifique a chave dessa conta do Armazenamento.</td></tr>
	<tr><td>Contêiner padrão</td>
		<td><p>Especifique o contêiner padrão da conta do Armazenamento que é utilizado como sistema de arquivos padrão para o cluster HDInsight. Se você tiver escolhido <strong>Usar Armazenamento Existente</strong> no campo <strong>Conta de Armazenamento</strong> e não existir nenhum contêiner nessa conta, por padrão, o contêiner será criado com o mesmo nome que o nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta do Armazenamento tiver um contêiner com um nome diferente do nome do cluster especificado, você poderá usar esse contêiner também.</p>
        <p>Caso opte por criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure, você precisa especificar o nome do contêiner padrão.</p>
    </td></tr>
	<tr><td>Contas de armazenamento adicionais</td>
		<td>O HDInsight dá suporte a várias contas do Armazenamento. Não há nenhum limite quanto ao número de contas do Armazenamento que um cluster pode usar. No entanto, se criar um cluster usando o Portal do Azure, você terá um limite de sete contas, devido a restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de **Conta de Armazenamento** ao assistente, onde você pode especificar as informações da conta. Por exemplo, na captura de tela acima, uma conta do Armazenamento adicional está selecionada e, portanto, a página 5 é adicionada à caixa de diálogo.</td></tr>
</table>
	Clique na seta à direita.

8. Se você optar por configurar armazenamento adicional para o cluster, na página **Conta de Armazenamento**, insira as informações da conta de armazenamento adicional:

	![Forneça os detalhes da conta de armazenamento adicional](./media/hdinsight-apache-spark-provision-clusters/HDI.CustomProvision.Page6.png "Forneça os detalhes da conta de armazenamento adicional")

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.

    > [AZURE.NOTE] Depois de escolher uma conta de armazenamento do Azure para o cluster HDInsight, você não pode excluir a conta, nem alterá-la para uma conta diferente.

8. Na página **Ações de script**, clique em **Adicionar ação de script** para fornecer detalhes sobre o script personalizado que você deseja executar para personalizar um cluster, enquanto o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-customize-cluster].

	Se você precisar usar uma ação de script, você deve fornecer as entradas a seguir.
	
	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster.</td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os Nós</b>, <b>Somente nós principais</b> ou <b>Somente nós de trabalho</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
</table>
	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.



##<a id="powershell"></a> Usando o PowerShell do Azure

Esta seção fornece instruções sobre como provisionar um cluster do Apache Spark no Azure HDInsight usando o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure][powershell-install-configure]. Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell][hdinsight-admin-powershell]. Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE] Embora os scripts nesta seção possam ser usados para configurar um cluster HDInsight em uma Rede Virtual do Azure, eles não criarão uma Rede Virtual do Azure. Para obter informações sobre como criar uma Rede Virtual do Azure, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).

Os procedimentos a seguir são necessários para provisionar um cluster HDInsight usando o PowerShell do Azure:

- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Crie um cluster HDInsight

Você pode usar o console do Windows PowerShell ou o ISE (Ambiente de Script Integrado) do Windows PowerShell para executar os scripts.
 
O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Você precisa ter uma conta do Armazenamento do Azure e um contêiner de armazenamento para poder criar um cluster HDInsight. A conta do Armazenamento deve estar localizada no mesmo datacenter que o cluster HDInsight.

**Para se conectar à sua conta do Azure**

		Add-AzureAccount 

Será solicitado que você insira suas credenciais de conta do Azure.

**Para criar uma conta do Armazenamento do Azure**

		$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
		$location = "<MicrosoftDataCenter>"				# For example, "West US"

		# Create an Azure Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos do Windows PowerShell para recuperar as informações:

		# List Storage accounts for the current subscription
		Get-AzureStorageAccount

		# List the keys for a Storage account
		Get-AzureStorageKey "<StorageAccountName>"

**Para criar o contêiner de armazenamento de Blob do Azure**

		$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
		$containerName="<ContainerName>"				# Provide a container name

		# Create a storage context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
		                                       -StorageAccountKey $storageAccountKey  

		# Create a Blob storage container
		New-AzureStorageContainer -Name $containerName -Context $destContext

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

**Para provisionar um cluster HDInsight**

- Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $credentials = Get-Credential		              # User name and password for the Hadoop account. You will use this account to connect to the cluster and run jobs.
           
        # Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credentials -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName -ClusterSizeInNodes $clusterNodes -Version "3.2" -ClusterType Spark

	>[AZURE.NOTE] As credenciais especificadas são usadas para criar a conta de usuário do Hadoop para o cluster. Você usará essa conta para conectar-se ao cluster e executar trabalhos. Se você usar a opção Criação Rápida no portal do Azure para provisionar um cluster, o nome de usuário do Hadoop padrão é "admin". Não confunda essa conta com a conta de usuário do protocolo RDP. A conta de usuário do RDP deve ser diferente da conta de usuário do Hadoop. Para obter mais informações, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal de Gerenciamento do Azure][hdinsight-admin-portal].

	Pode levar vários minutos para que o provisionamento do cluster seja concluído.

	![HDI.PS.Provision](./media/hdinsight-apache-spark-provision-clusters/hdi-spark-ps-provisioning.png "Provisionar o cluster do Spark usando a configuração básica no PowerShell")


**Para provisionar um cluster HDInsight usando as opções de configuração personalizadas**

Ao provisionar um cluster, você pode usar outras opções de configuração, como conectar-se a mais de um contêiner de armazenamento de Blob do Azure ou usar um banco de dados SQL do Azure para os metastores do Hive e do Oozie. Isso permite que você separe o tempo de vida de seus dados e metadados do tempo de vida do cluster.

> [AZURE.NOTE] Os cmdlets do Windows PowerShell são a única maneira recomendada de alterar as variáveis de configuração em um cluster HDInsight. As alterações feitas aos arquivos de configuração do Hadoop durante uma conexão ao cluster por meio da área de trabalho remota podem ser sobrescritas em caso de aplicação de patch no cluster. Os valores da configuração definidos por meio do PowerShell do Azure serão preservados se ocorrer uma aplicação de patch no cluster.

- Execute os seguintes comandos em uma janela do Windows PowerShell:

		$subscriptionName = "<Azure subscription>"
		$clusterName = "<cluster name>"
		$clusterNodes = <cluster size in nodes>
		                
		# Get credentials for Hadoop user and Hive/Oozie metastores
		$clusterCredentials = Get-Credential
		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"
		
		# Get default storage account, storage container, and add-on storage account
		$storageAccountName_Default = "<Default storage account name>"
		$containerName_Default = $clusterName
		$storageAccountName_Add1 = "<Add-on storage account name>"
		
		# Get information about Hive and Oozie metastores
		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>""
		$oozieSQLDatabaseName = "<SQLDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Retrieve storage account keys and storage account location
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName_Default | %{$_.Location}
		
		# Specify custom configuration and cluster type
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes -ClusterType Spark -VirtualNetworkId $vnetID -SubnetName $subNetName | Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default | Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 | Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore | Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore
		
		# Provision the cluster with custom configuration                
		New-AzureHDInsightCluster -Name $clusterName -Config $config -Credential $clusterCredentials -Location $location -Version "3.2"

	>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando estiver na exibição do servidor, clique em **Configurar** e em **Windows** **Azure Services**, clique em **Sim** e em **Salvar**.

	Pode levar vários minutos para que o provisionamento do cluster seja concluído.

**Para listar os clusters HDInsight**

- Execute o seguinte comando em uma janela de console do PowerShell do Azure para listar o cluster HDInsight e verificar se ele foi provisionado com êxito:

		Get-AzureHDInsightCluster -Name <ClusterName>

##<a id="sdk"></a> Usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework.

Os procedimentos a seguir devem ser realizados para provisionar um cluster HDInsight usando o SDK:

- Instalar o SDK do .NET do HDInsight
- Crie um certificado autoassinado
- Criar um aplicativo de console
- Executar o aplicativo


**Para instalar o SDK do .NET do HDInsight**

Você pode instalar a compilação publicada mais recentemente do SDK no [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). As instruções serão mostradas no próximo procedimento.

**Para criar um certificado autoassinado**

Crie um certificado autoassinado, instale-o em sua estação de trabalho e o carregue para sua assinatura do Azure. Para obter instruções, consulte [Criar um certificado autoassinado](http://go.microsoft.com/fwlink/?LinkId=511138).


**Para criar um aplicativo de console do Visual Studio**

1. Abra o Visual Studio 2013.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Propriedade</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Valor</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Categoria</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Modelos/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Modelo</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Aplicativo de console</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Nome</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateHDICluster</td></tr>
</table>

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Esse comando adiciona bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.

8. Adicione o seguinte usando as instruções na parte superior do arquivo :

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning.Data;


9. Na função main (), copie e cole o seguinte código:

        string thumbprint = "<CertificateFriendlyName>";  
        string subscriptionid = "<AzureSubscriptionID>";
        string clustername = "<HDInsightClusterName>";
        string location = "<MicrosoftDataCenter>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string containername = "<HDInsightDefaultContainerName>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Get the certificate object from certificate store by using the friendly name to identify it
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
        ClusterCreateParametersV2 clusterInfo = new ClusterCreateParametersV2()
		{
		    Name = clustername,
		    Location = location,
		    DefaultStorageAccountName = storageaccountname,
		    DefaultStorageAccountKey = storageaccountkey,
		    DefaultStorageContainer = containername,
		    UserName = username,
		    Password = password,
		    ClusterSizeInNodes = clustersize,
		    Version = "3.2",
		    OSType = OSType.Windows,
			ClusterType = ClusterType.Spark		    
		};


		// Create the cluster
        Console.WriteLine("Creating the HDInsight cluster ...");

        ClusterDetails cluster = client.CreateCluster(clusterInfo);

        Console.WriteLine("Created cluster: {0}.", cluster.ConnectionUrl);
        Console.WriteLine("Press ENTER to continue ...");
        Console.ReadKey();

10. Substitua as variáveis no início da função main().

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.


##<a id="nextsteps"></a> Próximas etapas

* Consulte [Executar análises de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md) para aprender a usar o Apache Spark no HDInsight com ferramentas de BI como Power BI e Tableau. 
* Consulte [Usar o Spark no HDInsight para a criação de aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md) para aprender a criar aplicativos de aprendizado de máquina usando o Apache Spark no HDInsight.
* Consulte [Usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming.md) para aprender a criar aplicativos de streaming usando o Apache Spark no HDInsight.
* Consulte [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md) para aprender a usar o Gerenciador de Recursos para gerenciar os recursos alocados para o cluster do Spark.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hadoop-hdinsight-intro]: ../hdinsight-hadoop-introduction/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-storm-get-started]: ../hdinsight-storm-getting-started/

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/

[azure-create-storageaccount]: ../storage-create-storage-account/

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[powershell-install-configure]: ../install-configure-powershell/




[89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Use o Sqoop com o HDInsight"

<!---HONumber=August15_HO6-->