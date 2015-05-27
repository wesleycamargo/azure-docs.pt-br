<properties 
   pageTitle="Provisionar clusters do Hadoop no HDInsight | Azure" 
   description="Saiba como configurar clusters para o HDInsight do Azure usando o portal do Azure, PowerShell do Azure, uma linha de comando ou o SDK do .NET do HDInsight" 
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
   ms.date="04/21/2015"
   ms.author="nitinme"/>

#Provisionar clusters Hadoop no HDInsight usando opções de personalização

Apenda modos diferentes de provisionar de maneira personalizada clusters Hadoop no Azure HDInsight - usando o Portal do Azure, o PowerShell do Azure, ferramentas de linha de comando ou o SDK do .NET do HDInsight. Para obter instruções sobre como provisionar clusters HBase e Storm, consulte [Provisionar cluster HBase em HDInsight](hdinsight-hbase-get-started.md) e [Introdução ao uso do Storm no HDInsight](hdinsight-storm-getting-started.md). Consulte <a href="http://go.microsoft.com/fwlink/?LinkId=510237">Qual a diferença entre Hadoop e HBase?</a> para entender por que você opta por um em vez do outro.

## O que é um cluster HDInsight?

Você já se perguntou por que mencionamos clusters sempre que falamos de Hadoop ou Big Data? O motivo é que o Hadoop permite o processamento distribuído de grandes volumes de dados, espalhados em diferentes nós de um cluster. O cluster tem uma arquitetura de mestre/servidor subordinado com um mestre (também chamado headnode ou namenode) e qualquer número de servidores subordinados (também chamados nó de dados). Para obter mais informações, consulte [Apache Hadoop][apache-hadoop].

![Cluster do HDInsight][img-hdi-cluster]

Um cluster HDInsight resume os detalhes de implementação do Hadoop para que você não precise se preocupar com a forma de comunicação com diferentes nós de um cluster. Ao provisionar um cluster HDInsight, você provisiona recursos de computação do Azure que contêm o Hadoop e aplicativos relacionados. Para obter mais informações, consulte [Introdução ao Hadoop no HDInsight](hdinsight-hadoop-introduction.md). Os dados que sofrerem variação serão armazenados no armazenamento de Blob do Azure. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

Este artigo fornece instruções sobre diferentes maneiras de provisionar um cluster. Se você procura uma abordagem de início rápido para provisionar um cluster, consulte [Introdução ao Azure HDInsight](hdinsight-get-started.md).

**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. O Azure é uma plataforma baseada em assinatura. Os cmdlets do Windows PowerShell para HDInsight executam as tarefas usando a sua assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

##<a id="configuration"></a>Opções de configuração

###Clusters no Linux

O HDInsight fornece a opção de configuração de clusters do Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-get-started.md).

###Armazenamento adicional

Durante a configuração, você deve especificar uma conta de Armazenamento do Blob do Azure e um contêiner padrão. Eles são usados como o local de armazenamento padrão pelo cluster. Opcionalmente, você pode especificar uma conta de armazenamento do Azure adicional que também será associada ao cluster.

>[AZURE.NOTE]Não compartilhe um contêiner de armazenamento de Blob entre múltiplos clusters. Não há suporte para isso.

Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

###Metastore

O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados de Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao provisionar um novo cluster. Por padrão, o Hive usa um banco de dados integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído.

### Personalização do cluster

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento. Tais scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no portal do Azure, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).


###Rede Virtual

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE]Você deve criar a Rede Virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> O Azure HDInsight dá suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendado designar uma única sub-rede para um cluster.

##<a id="portal"></a> Usando o portal do Azure

Clusters HDInsight usam um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure localizada no mesmo data center é necessária para poder criar um cluster HDInsight. Para obter mais informações, consulte [Usar o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md). Para obter detalhes sobre como criar uma conta do Armazenamento do Azure, consulte [Como criar uma conta de armazenamento](storage-create-storage-account.md).


> [AZURE.NOTE]Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal do Azure][azure-management-portal].
2. No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.
3. Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

	![Fornecer detalhes do cluster HDInsight do Hadoop][image-customprovision-page1]

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome do cluster</td>
		<td><p>Nome do cluster. </p>
			<ul>
			<li>O nome de DNS (sistema de nome de domínio) deve começar e terminar com um caractere alfanumérico e pode conter traços.</li>
			<li>O campo deve ser uma cadeia de caracteres com 3 a 63 caracteres.</li>
			</ul></td></tr>
	<tr><td>Tipo de cluster</td>
		<td>Para o tipo de cluster, selecione <strong>Hadoop</strong>.</td></tr>
	<tr><td>Sistema operacional</td>
		<td>Selecione <b>Datacenter do Windows Server 2012 R2</b> para provisionar um cluster do Windows. Para provisionar um cluster do Windows, consulte <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-provision-linux-clusters/" target="_blank">Provisionar clusters Hadoop em Linux no HDInsight</a>.</td></tr>
	<tr><td>Versão do HDInsight</td>
		<td>Escolha a versão. Para o Hadoop, o padrão é o HDInsight versão 3.1, que usa o Hadoop 2.4.</td></tr>
	</table>Insira ou selecione os valores, conforme mostrado na tabela, e clique na seta para a direita.

4. Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	![Fornecer detalhes do cluster HDInsight do Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page2.png)

	<table border="1">
<tr><th>Nome</th><th>Valor</th></tr>
<tr><td>Nós de dados</td><td>Número de nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.</td></tr>
<tr><td>Região/Rede virtual</td><td><p>Escolha a mesma região da conta de armazenamento que você criou no último procedimento. O HDInsight requer que a conta de armazenamento esteja localizada na mesma região. Mais adiante na configuração, você poderá escolher somente uma conta de armazenamento que esteja na mesma região que a especificada aqui.</p><p>As regiões disponíveis são: <strong>Ásia Oriental</strong>, <strong>Sudeste da Ásia</strong>, <strong>Norte da Europa</strong>, <strong>Oeste da Europa</strong>, <strong>Leste dos EUA</strong>, <strong>Oeste dos EUA</strong>, <strong>Centro-Norte dos EUA</strong>e <strong>Centro-Sul dos EUA</strong>.<br/>Se você tiver criado uma Rede Virtual do Azure, poderá selecionar a rede que o cluster HDInsight será configurado para usar.</p><p>Para obter informações sobre como criar uma Rede Virtual do Azure, consulte <a href="http://msdn.microsoft.com/library/azure/jj156206.aspx">Tarefas de configuração de Rede Virtual</a>.</p></td></tr>
<tr><td>Tamanho do nó principal</td><td><p>Selecione um tamanho de VM (máquina virtual) para o nó de cabeçalho.</p></td></tr>
<tr><td>Tamanho do nó de dados</td><td><p>Selecione um tamanho de VM para os nós de dados.</p></td></tr>
</table>>[AZURE.NOTE]O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.


5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

    ![Fornecer detalhes de usuário e metastore do cluster do Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png)

    <table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome de usuário HTTP</td>
		<td>Especifique o nome do usuário do cluster HDInsight.</td></tr>
	<tr><td>Senha/Confirmar senha HTTP</td>
		<td>Especifique a senha do usuário do cluster HDInsight.</td></tr>
	<tr><td>Habilitar área de trabalho remota para o cluster</td>
		<td>Marque esta caixa de seleção para especificar uma data de expiração, senha e nome de usuário para um usuário de área de trabalho remota que possa acessar remotamente os nós do cluster após seu provisionamento. Você também pode habilitar área de trabalho remota mais tarde, depois que o cluster for provisionado. Para instruções, consulte <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.</td></tr>
	<tr><td>Inserir como metastore do Hive/Oozie</td>
		<td>Marque esta caixa de seleção para especificar um banco de dados SQL no mesmo datacenter que o cluster, a ser usado como metastore do Hive/Oozie. Se você marcar esta caixa de seleção, deverá especificar detalhes sobre o banco de dados SQL do Azure nas páginas seguintes do assistente. Isso será útil se você quiser reter os metadados sobre os trabalhos do Hive/Oozie após um cluster ter sido excluído.</td></tr>
	</td></tr>		
</table>Clique na seta à direita.

6. Na página **Configurar Metastore do Hive/Oozie**, forneça os seguintes valores:

    ![Fornecer usuário do cluster HDInsight do Hadoop](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png)

	Especifique um banco de dados SQL do Azure que será usado como metastore do Hive/Oozie. Você pode especificar o mesmo banco de dados para os metastores do Hive e do Oozie. Esse Banco de Dados SQL deve estar no mesmo data center que o cluster HDInsight. A caixa de listagem lista apenas os bancos de dados SQL no mesmo datacenter que você especificou na página <strong>Detalhes do Cluster</strong>. Especifique também o nome de usuário e senha para se conectar ao banco de dados SQL do Azure selecionado.

    >[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

    Clique na seta à direita.


7. Na página **Conta de Armazenamento**, forneça os seguintes valores:

    ![Fornecer conta de armazenamento para o cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png)

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
</table>Clique na seta à direita.

7. Se você optar por configurar armazenamento adicional para o cluster, na página **Conta de Armazenamento**, insira as informações da conta de armazenamento adicional:

	![Fornecer detalhes de armazenamento adicionais para o cluster HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png)

    Aqui, mais uma vez, você tem a opção de escolher entre um armazenamento existente, criar um novo armazenamento ou usar um armazenamento de outra assinatura do Azure. O procedimento para fornecer os valores é semelhante à etapa anterior.

    > [AZURE.NOTE]Depois de escolher uma conta de armazenamento do Azure para o cluster HDInsight, você não pode excluir a conta, nem alterá-la para uma conta diferente.

8. Na página **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre o script personalizado que você deseja executar para personalizar um cluster, enquanto o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).
	
	![Configurar a Ação de Script para personalizar um cluster do HDInsight](./media/hdinsight-provision-clusters/HDI.CustomProvision.Page7.png)

	<table border='1'>
	<tr><th>Propriedade</th><th>Valor</th></tr>
	<tr><td>Nome</td>
		<td>Especifique um nome para a ação de script.</td></tr>
	<tr><td>URI do script</td>
		<td>Especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster.</td></tr>
	<tr><td>Tipo de nó</td>
		<td>Especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os nós</b>, <b>Somente headnodes</b> ou <b>Somente nós de dados</b>.
	<tr><td>Parâmetros</td>
		<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
</table>Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

##<a id="powershell"></a> Usando o PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](install-configure-powershell.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight][hdinsight-powershell-reference].

> [AZURE.NOTE]Embora os scripts nesta seção possam ser usados para configurar um cluster HDInsight em uma Rede Virtual do Azure, eles não criarão uma Rede Virtual do Azure. Para obter informações sobre como criar uma Rede Virtual do Azure, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).

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

> [AZURE.NOTE]Os cmdlets do PowerShell do Azure são a única maneira recomendada de alterar as variáveis de configuração em um cluster do HDInsight. As alterações feitas aos arquivos de configuração do Hadoop durante uma conexão ao cluster por meio da área de trabalho remota podem ser sobrescritas em caso de aplicação de patch no cluster. Os valores da configuração definidos por meio do PowerShell do Azure serão preservados se ocorrer uma aplicação de patch no cluster.

- Execute os seguintes comandos em uma janela de console do PowerShell do Azure:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created

		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system

		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
        $hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
        $hadoopUserPassword = "<HadoopUserPassword>"    

        $secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)            

		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster

        # The location of the HDInsight cluster. It must be in the same data center as the Storage account.
        $location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location} 

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Os comandos $hadoopUserName e $hadoopUserPassword são usados para criar a conta de usuário do Hadoop para o cluster. Você usará essa conta para conectar-se ao cluster e executar trabalhos. Se você usar a opção Criação Rápida no portal do Azure para provisionar um cluster, o nome de usuário do Hadoop padrão é "admin". Não confunda essa conta com a conta de usuário do protocolo RDP. A conta de usuário do RDP deve ser diferente da conta de usuário do Hadoop. Para obter mais informações, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal de Gerenciamento do Azure][hdinsight-admin-portal].

	Pode levar vários minutos para que o provisionamento do cluster seja concluído.

	![HDI.CLI.Provision][image-hdi-ps-provision]



**Para provisionar um cluster HDInsight usando as opções de configuração personalizadas**

Enquanto provisiona um cluster, você pode usar as outras opções de configuração, como conectar-se a mais de um contêiner de armazenamento de Blob do Azure ou usar uma rede virtual ou banco de dados SQL do Azure para metastores do Hive e do Oozie. Isso permite que você separe o tempo de vida de seus dados e metadados do tempo de vida do cluster.

> [AZURE.NOTE]Os cmdlets do Windows PowerShell são a única maneira recomendada de alterar as variáveis de configuração em um cluster HDInsight. As alterações feitas aos arquivos de configuração do Hadoop durante uma conexão ao cluster por meio da área de trabalho remota podem ser sobrescritas em caso de aplicação de patch no cluster. Os valores da configuração definidos por meio do PowerShell do Azure serão preservados se ocorrer uma aplicação de patch no cluster.

- Execute os seguintes comandos em uma janela do Windows PowerShell:

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

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
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
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

**Para listar os clusters HDInsight**

- Execute o seguinte comando em uma janela de console do PowerShell do Azure para listar o cluster HDInsight e verificar se ele foi provisionado com êxito:

		Get-AzureHDInsightCluster -Name <ClusterName>


##<a id="cli"></a> Usando linha de comando entre plataformas

> [AZURE.NOTE]A partir de 29/08/2014, não será possível usar a CLI (interface de linha de comando) entre plataformas para associar um cluster a uma Rede Virtual do Azure.

Outra opção para o provisionamento de um cluster HDInsight é a interface de linha de comando entre plataformas. A ferramenta de linha de comando é implementada no Node.js. Ela pode ser usada em qualquer plataforma que dá suporte a Node. js, incluindo Windows, Mac e Linux. Você pode instalar a CLI nos seguintes locais:

- **SDK do Node.js** - <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **CLI entre plataformas** - <a href="https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Para ver um guia geral sobre como usar a interface de linha de comando, consulte [CLI do Azure para Mac, Linux e Windows](xplat-cli.md).

As instruções a seguir explicam como instalar a linha de comando entre plataformas em Linux e Windows e como usar a linha de comando para provisionar um cluster.

- [Configurar a CLI do Azure para Linux](#clilin)
- [Configurar a CLI do Azure para Windows](#cliwin)
- [Provisionar clusters HDInsight usando a CLI do Azure](#cliprovision)

#### <a id="clilin"></a>Configurar uma linha de comando entre plataformas para Linux

Execute os procedimentos a seguir para configurar seu computador Linux para usar as ferramentas de linha de comando do Azure:

- Instalar uma linha de comando entre plataformas usando o NPM (Gerenciador de pacote do Node.js)
- Conecte-se à sua assinatura do Azure

**Para instalar a interface de linha de comando usando o NPM**

1.	Abra uma janela de terminal no seu computador Linux e execute o seguinte comando:

		sudo npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectar-se à sua assinatura do Azure**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.

> [AZURE.NOTE]O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use Criptografia de Unidade de Disco BitLocker.


1.	Abra uma janela de terminal.
2.	Execute o seguinte comando para fazer logon em sua assinatura do Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	O comando inicia a página da Web na qual se baixa o arquivo de configurações de publicação. Se a página da Web não se abrir, clique no link na janela do terminal para abrir a página do navegador e fazer logon no portal.

3.	Baixe o arquivo de configurações de publicação para o computador.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configurar uma linha de comando entre plataformas para Windows

Execute os seguintes procedimentos para configurar seu computador Windows para usar as ferramentas de linha de comando do Azure:

- Instalar uma linha de comando entre plataformas (usando NPM ou o Windows Installer)
- Baixar e importar as configurações de publicação da conta do Azure


A interface de linha de comando pode ser instalada usando o NPM ou o Windows Installer. A Microsoft recomenda instalar usando apenas uma das duas opções.

**Para instalar a interface de linha de comando usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou o *Prompt de Comando do Azure* ou o *Prompt de Comando do Desenvolvedor para VS2012*) em sua estação de trabalho.
4.	Execute o seguinte comando na janela do prompt de comando:

		npm install -g https://github.com/Azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: <i>C:\\Arquivos de Programas (x86)\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i> ou <i>C:\\Arquivos de Programas\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i>

5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a interface de linha de comando usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**. 2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando entre Plataformas** e siga o assistente do Web Platform Installer.

**Para baixar e importar as configurações de publicação**

Antes de usar a interface de linha de comando, você deve configurar a conectividade entre a estação de trabalho e o Azure. As informações da assinatura do Azure são usadas pela interface de linha de comando para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a interface de linha de comando irá usar para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.

> [AZURE.NOTE]O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.


1.	Abra um **Prompt de comando**.
2.	Execute o comando a seguir para baixar o arquivo de configurações da publicação:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	O comando inicia a página da Web na qual se baixa o arquivo de configurações de publicação.

3.	No prompt para salvar o arquivo, clique em **Salvar** e forneça um local onde o arquivo deve ser salvo.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Provisionar clusters HDInsight usando uma CLI do Azure

Os seguintes procedimentos são necessários para provisionar um cluster HDInsight usando a CLI do Azure:

- Criar uma conta de Armazenamento do Azure
- Provisionar um cluster

**Para criar uma conta do Armazenamento do Azure**

O HDInsight usa um contêiner de Armazenamento de Blob do Azure como o sistema de arquivos padrão. Uma conta de Armazenamento do Azure é necessária para poder criar um cluster HDInsight. A conta de armazenamento deve estar localizada no mesmo datacenter.

- Na janela do prompt de comando, execute o seguinte comando para criar uma conta do Armazenamento do Azure:

		azure storage account create [options] <StorageAccountName>

	Quando um local for solicitado, selecione o local onde um cluster HDInsight pode ser provisionado. O armazenamento deve estar no mesmo local que o cluster HDInsight. Atualmente, apenas as regiões **Ásia Oriental**, **Sudeste da Ásia**, **Norte da Europa**, **Oeste da Europa**, **Leste dos EUA**, **Oeste dos EUA**, **Centro-Norte dos EUA** e **Centro-Sul dos EUA** podem hospedar clusters HDInsight.

Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o portal do Azure, consulte [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para obter detalhes sobre como obter as informações usando o portal do Azure, consulte a seção *Exibir, copiar e regenerar chaves de acesso de armazenamento* de [Criar, gerenciar ou excluir uma conta de armazenamento](storage-create-storage-account.md).

Um cluster HDInsight também requer um contêiner em uma conta do Armazenamento. Se a conta de armazenamento fornecida ainda não tiver um contêiner, o comando *azure hdinsight cluster create* solicitará um nome de contêiner e também o criará. No entanto, se você desejar criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

**Para provisionar um cluster HDInsight**

- Execute o seguinte comando na janela do prompt de comando:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para provisionar um cluster HDInsight usando um arquivo de configuração**

Normalmente, você provisiona um cluster HDInsight, executa os trabalhos e exclui o cluster para reduzir o custo. A interface de linha de comando oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo toda vez que provisionar um cluster.

- Execute os seguintes comandos na janela do prompt de comando:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para listar e mostrar detalhes do cluster**

- Use os seguintes comandos para listar e mostrar os detalhes do cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para excluir um cluster**

- Use o seguinte comando para excluir um cluster:

		azure hdinsight cluster delete <ClusterName>



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


9. Na função Main(), copie e cole o código a seguir:

        string certfriendlyname = "<CertificateFriendlyName>";     // Friendly name for the certificate you created earlier  
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
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);

        // Create the Storage account if it doesn't exist

        // Create the container if it doesn't exist.

		// Create an HDInsightClient object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
        var client = HDInsightClient.Connect(creds);

		// Supply the cluster information
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

10. Substitua as variáveis no início da função Main().

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.



##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Usar o Sqoop com o HDInsight](hdinsight-use-sqoop.md) - aprenda como copiar dados entre o HDInsight e o Banco de Dados SQL ou o SQL Server
* [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md) - aprenda como trabalhar com o HDInsight usando o PowerShell do Azure
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation] - descubra o SDK do HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[hdinsight-hbase-custom-provision]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-storage]: hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hadoop-hdinsight-intro]: hdinsight-hadoop-introduction.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-storm-get-started]: hdinsight-storm-getting-started.md

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: xplat-cli.md

[azure-create-storageaccount]: storage-create-storage-account.md

[apache-hadoop]: http://go.microsoft.com/fwlink/?LinkId=510084
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: install-configure-powershell.md

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[image-customprovision-page1]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page1.png "Fornecer detalhes do cluster HDInsight do Hadoop"
[image-customprovision-page3]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page3.png "Fornecer usuários de cluster do Hadoop do HDInsight"
[image-customprovision-page4]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page4.png "Fornecer detalhes da conta de armazenamento para o cluster do Hadoop do HDInsight"
[image-customprovision-page5]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page5.png "Fornecer detalhes adicionais da conta de armazenamento para o cluster do Hadoop do HDInsight"
[image-customprovision-page6]: ./media/hdinsight-provision-clusters/HDI.CustomProvision.Page6.png "Configurar a Ação de Script para personalizar um cluster do HDInsight"


[image-cli-account-download-import]: ./media/hdinsight-provision-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-provision-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-provision-clusters/HDI.CLIListClusters.png "Listar e mostrar clusters"

[image-hdi-ps-provision]: ./media/hdinsight-provision-clusters/HDI.ps.provision.png

[img-hdi-cluster]: ./media/hdinsight-provision-clusters/HDI.Cluster.png

[89e2276a]: hdinsight-use-sqoop.md "Use o Sqoop com o HDInsight"

<!--HONumber=54-->