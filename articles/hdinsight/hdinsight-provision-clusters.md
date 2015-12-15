<properties
   pageTitle="Criar clusters Hadoop no HDInsight | Microsoft Azure"
   	description="Saiba como criar clusters para o Azure HDInsight usando o Portal do Azure, o Azure PowerShell, uma linha de comando ou o SDK do .NET."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="11/16/2015"
   ms.author="jgao"/>

# Criar clusters Hadoop no HDInsight

Aprenda a planejar para criar clusters do HDInsight.


###Pré-requisitos:

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opções de configuração básicas

Veja a seguir as opções de configuração básica para criar um cluster HDInsight.

- **Nome do cluster**

	O nome do cluster é usado para identificar um cluster. O nome do cluster deve seguir as diretrizes seguintes:

	- O campo deve ser uma cadeia com 3 a 63 caracteres.
	- O campo pode conter apenas letras, números e hifens.

- **Nome da assinatura**

	Um cluster HDInsight é vinculado a uma assinatura do Azure.

- **Nome do grupo de recursos**

	O Gerenciador de Recursos do Azure (ARM) permite trabalhar com os recursos do seu aplicativo como um grupo, designado um Grupo de Recursos do Azure. Você pode implantar, atualizar, monitorar ou excluir todos os recursos do seu aplicativo com uma única operação coordenada. Para obter mais informações, veja [Visão geral do Gerenciador de Recursos do Azure](resource-group-overview.md).
	
- **Sistema operacional**

	Você pode criar clusters HDInsight em um dos dois seguintes sistemas operacionais:
	- **HDInsight no Windows (Windows Server 2012 R2 Datacenter)**:
	- **HDInsight no Linux (Ubuntu 12.04 LTS para Linux)**: o HDInsight oferece a opção de configurar clusters Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-get-started.md).

- **Tipo de cluster** e **tamanho do cluster (também conhecidos como nós de dados)**

	O HDInsight permite que os clientes implantem uma variedade de tipos de cluster, para diferentes cargas de trabalho de análise de dados. Os tipos do cluster oferecidos atualmente são:

	- Clusters do Hadoop: para cargas de trabalho de consulta e análise
	- Clusters do HBase: para cargas de trabalho NoSQL
	- Clusters do Storm: para cargas de trabalho de processamento de eventos em tempo real
	- Clusters do Spark (visualização): para cargas de trabalho de processamento na memória, consultas interativas, fluxo e aprendizado de máquina.

	![Clusters do HDInsight](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]O *cluster Azure HDInsight* também é chamado de *clusters Hadoop no HDInsight* ou de *cluster HDInsight*. Às vezes, ele é usado alternadamente com o *cluster Hadoop*. Todos eles denotam clusters do Hadoop hospedados no ambiente do Microsoft Azure.

	Em um determinado tipo cluster, há diferentes funções para os diferentes nós, que permitem que um cliente dimensione os nós em uma determinada função apropriada para os detalhes de sua carga de trabalho. Por exemplo, um cluster do Hadoop pode ter seus nós de trabalho criados com uma grande quantidade de memória se o tipo de análise executada tiver um uso intensivo de memória.

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Os clusters Hadoop para HDInsight são implantados com duas funções:

	- Nó principal (2 nós)
	- Nó de dados (pelo menos 1 nó)

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	Clusters do HBase para HDInsight são implantados com três funções:
	- Servidores principais (2 nós)
	- Servidores de região (pelo menos 1 nó)
	- Nós mestre/zookeeper (3 nós)

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Clusters do Storm para HDInsight são implantados com três funções:
	- Nós Nimbus (2 nós)
	- Servidores de supervisão (pelo menos 1 nó)
	- Nós zookeeper (3 nós)


	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Clusters do Spark para HDInsight são implantados com três funções:
	- Nó principal (2 nós)
	- Nó de trabalho (pelo menos 1 nó)
	- Nós zookeeper (3 nós) (gratuito para Zookeepers A1)

	Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído (clusters não podem ser desalocados ou colocados em espera). O tamanho do cluster afeta seu preço. Para fins de aprendizado, é recomendável usar 1 nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]O limite de tamanho do cluster varia segundo as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.

- **Versão do HDInsight**

	É usada para determinar a versão do HDInsight a ser usada para este cluster. Para obter mais informações, confira [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)


- **Local (Região)**

	O cluster HDInsight e sua conta de armazenamento padrão devem estar localizados no mesmo local do Azure.
	
	![Regiões do Azure](./media/hdinsight-provision-clusters/Azure.regions.png)

	Para obter uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamanho do nó**

	![tamanhos de nó de vm do hdinsight](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Selecione o tamanho da VM para os nós. Para obter mais informações, confira [Tamanhos para Serviços de Nuvem](cloud-services-sizes-specs.md)

	O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.


- **Usuários do HDInsight**

	Os clusters do HDInsight permitem que você configure duas contas de usuário durante o provisionamento:

	- Usuário HTTP. O nome de usuário padrão é admin, com a configuração básica do Portal do Azure.
	- Usuário RDP (clusters do Windows): é usado para conectar-se ao cluster usando o RDP. Quando cria a conta, você deve definir uma data de validade que esteja dentro de 90 dias a partir do dia atual.
	- Usuário SSH (clusters do Linux): é usado para conectar-se ao cluster usando SSH. Você pode criar contas de usuário SSH adicionais depois que o cluster for criado seguindo as etapas em [Usar o SSH com o Hadoop baseado em Linux no HDInsight do Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).



- **Conta de armazenamento do Azure**

	O HDFS original usa um dos muitos discos locais no cluster. O HDInsight usa o armazenamento de Blob do Azure em vez do armazenamento de dados. O armazenamento de blob do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blob. O armazenamento de dados no armazenamento de blob permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.

	Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blob do Azure na conta de armazenamento do Azure. Alguns processos de criação requerem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blob sejam criados anteriormente. O contêiner de armazenamento de Blob é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. Além disso, o cluster também pode acessar qualquer contêiner de Blob configurado com acesso de leitura público completo ou acesso de leitura para blobs somente. Para obter mais informações sobre o acesso restrito, veja [Gerenciar o acesso aos Recursos do Armazenamento do Azure](storage-manage-access-to-resources.md).

	![Armazenamento do HDInsight](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Um contêiner de armazenamento de Blob oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem:

	![Armazenamento do blob do Azure](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]Não compartilhe um contêiner de armazenamento de Blob entre múltiplos clusters. Não há suporte para isso.

	Para obter mais informações sobre o uso de repositórios de blob secundários, consulte [Usando o Armazenamento de Blob do Azure com o HDInsight](hdinsight-use-blob-storage.md).

- **Metastore do Hive/Oozie**

	O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados de Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao criar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Por exemplo, você tem um cluster criado com um metastore do Hive. Você criou algumas tabelas do Hive. Depois de excluir o cluster e recriá-lo usando o mesmo metastore do Hive, você poderá ver as tabelas do Hive criadas no cluster original.
    
    > [AZURE.NOTE]A configuração de Metastore não está disponível para tipos de cluster HBase.

## Opções de configuração avançada

### Personalizar clusters usando a personalização de clusters do HDInsight

Às vezes, você deseja configurar os arquivos de configuração:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Os clusters não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, você pode usar a personalização de clusters do HDInsight durante o processo de criação.

A seguir, há um exemplo de script do Azure PowerShell para personalização de uma configuração do Hive:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

Alguns outros exemplos de personalização de outros arquivos de configuração:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Para obter mais informações, confira o blog de Azim Uddin chamado [Personalizando a criação de Clusters HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personalizar clusters usando a ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante a criação. Esses scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no Portal, cmdlets do Windows PowerShell do HDInsight ou SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).


### Usar redes virtuais do Azure

A [Rede Virtual do Azure](http://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Você deve criar a Rede Virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, veja [Criar um cluster Hadoop em uma rede virtual](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade. Use o cmdlet Get-AzureVNetConfig do Azure PowerShell para verificar se uma rede virtual do Azure existente é baseada no local. Se sua rede virtual não for baseada no local, você tem as seguintes opções:
>
> - Exportar a configuração de rede virtual existente e criar uma nova rede virtual. Todas as novas redes virtuais são baseadas em local por padrão.
> - Migrar para uma rede virtual baseada em local. Veja [Migrar serviços existentes para o escopo regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> É altamente recomendado designar uma única sub-rede para um cluster.

## Criar com o Portal

Você pode consultar as [opções de configuração básica](#basic-configuration-options) e as [opções de configuração avançada](#advanced-configuration-options) para ver as explicações dos campos.

**Para criar um cluster HDInsight**

1. Entre no [Portal do Azure][azure-preview-portal].
2. Clique em **NOVO**, em **Análises de Dados** e em **HDInsight**.

    ![Criando um novo cluster no Portal do Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Criando um novo cluster no Portal do Azure")

3. Digite ou selecione os valores a seguir:

  * **Nome do Cluster**: insira um nome para o cluster. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se o nome estiver disponível.
  * **Tipo de Cluster**: selecione **Hadoop**.
  * **Sistema Operacional do Cluster**: selecione **Windows Server 2012 R2 Datacenter**.
  * **Assinatura**: selecione a assinatura do Azure que será usada para criar esse cluster.
  * **Grupo de Recursos**: selecione um grupo de recursos existente ou crie um novo. A entrada padrão será um dos seus grupos de recursos existentes, se houver algum disponível.
  * **Credenciais**: configure o nome de usuário e a senha do usuário do Hadoop (usuário HTTP). Se você habilitar a área de trabalho remota para o cluster, você precisará configurar uma data de vencimento de conta e senha e o nome de usuário do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar as alterações.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Fonte de Dados**: crie uma nova conta do Armazenamento do Azure ou selecione uma conta existente que será usada como o sistema de arquivos padrão do cluster.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Método de seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para **Tecla de Acesso** se você deseja inserir o **Nome de armazenamento** e **Tecla de Acesso** de uma conta de armazenamento existente.
  		* **Selecionar conta de armazenamento / Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar com o cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.
  		* **Escolher Contêiner Padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.
  		* **Local**: a região geográfica na qual a conta de armazenamento está localizada ou será criada. Esse local determinará o local do cluster.  O cluster e sua conta de armazenamento padrão devem estar localizados no mesmo data center do Azure.
  	
  * **Tipos de Preço do Nó**: defina o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * **Configuração Opcional**: para selecionar a versão do cluster, bem como definir outras configurações opcionais, como ingressar uma **Rede Virtual**, definir um **Metastore Externo** para armazenar dados do Hive e Oozie, usar as Ações de Script para personalizar um cluster para instalar componentes personalizados ou usar contas de armazenamento adicionais com o cluster.

  		* **Versão do HDInsight**: selecione a versão que deseja usar para o cluster. Para obter mais informações, consulte [Versões de cluster do HDInsight](hdinsight-component-versioning.md).
  		* **Rede virtual**: selecione uma rede virtual do Azure e a sub-rede, se quiser colocar o cluster em uma rede virtual.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] O cluster HDInsight baseado em Windows só pode ser implantado em uma rede virtual clássica.
  

  		
		* **Metastores Externos**: especifique um banco de dados SQL do Azure para armazenar metadados de Hive e Oozie associados ao cluster.
 
            > [AZURE.NOTE] A configuração de Metastore não está disponível para tipos de cluster HBase.

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Para **Usar um banco de dados SQL existente para metadados do Hive**, clique em **Sim**, selecione um banco de dados SQL e forneça o nome de usuário e a senha para o banco de dados. Repita essas etapas se desejar **Usar um banco de dados SQL existente para metadados de Oozie**. Clique em **Selecionar** até voltar para a folha **Configuração Opcional**.


			>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em {1>Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.
		
  		* **Ações de Script** se desejar usar um script personalizado para personalizar um cluster enquanto ele estiver sendo criado. Para obter mais informações sobre ações de script, veja [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md). Na folha Ações de Script forneça os detalhes como mostrado na captura de tela.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Chaves de Armazenamento do Azure**: especifique contas de armazenamento adicionais para associar ao cluster. Na folha **Chaves de Armazenamento do Azure**, clique em **Adicionar uma chave de armazenamento** e selecione uma conta de armazenamento existente ou crie uma nova conta
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Clique em **Criar**. A seleção de **Fixar no Quadro Inicial** adicionará um bloco para o cluster ao Quadro Inicial do Portal. O ícone indica que o cluster está sendo criado e será alterado para exibir o ícone de HDInsight após a conclusão da criação.


	| Durante a criação | Criação concluída |
	| ------------------ | --------------------- |
	| ![Indicador de provisionamento no quadro inicial](./media/hdinsight-provision-clusters/provisioning.png) | ![Bloco de cluster provisionado](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]Levará algum tempo para que o cluster seja criado, geralmente, cerca de 15 minutos. Use o bloco no Quadro Inicial ou a entrada **Notificações** à esquerda da página para verificar o processo de provisionamento.
	

5. Quando a criação for concluída, clique no bloco para o cluster no quadro inicial para iniciar a folha de cluster. A folha de cluster fornece informações essenciais sobre o cluster como o nome, o grupo de recursos ao qual ele pertence, o local, o sistema operacional, a URL para o painel do cluster, etc.


	![Folha de cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Propriedades do cluster")


	Use o seguinte para entender os ícones na parte superior desta folha e na seção **Informações gerais**:


	* **Configurações** e **Todas as Configurações**: exibem a folha **Configurações** do cluster, o que permite acessar informações de configuração detalhadas do cluster.
	* **Painel**, **Painel do Cluster** e **URL**: essas são todas as maneiras de acessar o painel do cluster, que é um portal da Web usado para executar trabalhos no cluster.
	* **Área de Trabalho Remota**: permite habilitar/desabilitar a área de trabalho remota nos nós do cluster.
	* **Cluster em Escala**: permite alterar o número de nós de trabalho deste cluster.
	* **Excluir**: exclui o cluster HDInsight.
	* **Início Rápido** (![ícone de nuvem e raio = início rápido](./media/hdinsight-provision-clusters/quickstart.png)): exibe informações que ajudarão a começar a usar o HDInsight.
	* **Usuários** (![ícone de usuários](./media/hdinsight-provision-clusters/users.png)): permite definir permissões para o _gerenciamento de portal_ deste cluster para outros usuários em sua assinatura do Azure.
	

		> [AZURE.IMPORTANT]Isso afeta _apenas_ o acesso e as permissões para este cluster no Portal e não afeta quem pode se conectar ao cluster HDInsight ou enviar trabalhos para ele.
		
	* **Marcas** (![ícone de marca](./media/hdinsight-provision-clusters/tags.png)): as marcas permitem definir pares de chave/valor para definir uma taxonomia personalizada dos seus serviços de nuvem. Por exemplo, você pode criar uma chave chamada __projeto__ e usar um valor comum para todos os serviços associados a um projeto específico.

## Criar usando o modelo ARM

O modelo do Gerenciador de Recursos do Azure (ARM) facilita a implantação e a reimplantação de cluster. O procedimento a seguir cria o cluster HDInsight baseados em Linux.

**Para implantar um cluster usando o modelo do ARM**

1. Salve o arquivo JSON encontrado no [Apêndice A](#appendix-a---arm-template) em sua estação de trabalho.
2. Crie os parâmetros, se necessário.
3. Execute o modelo usando o seguinte script do PowerShell:

		$subscriptionId = "<Azure Subscription ID"
		
		$newResourceGroupName = "<Azure Resource Group Name>"
		$Location = "EAST US 2" # for creating ARM group
				
		$armDeploymentName = "New-HDInsigt-Cluster-" + (Get-Date -Format MMdd)
		$newClusterName = "<HDInsight Cluster Name>"
		$clusterStorageAccountName = "<Default Storage Account Name>"
				
		# Connect to Azure
		#Login-AzureRmAccount
		#Select-AzureRmSubscription -SubscriptionId $subscriptionId
				
		# Create a resource group
		New-AzureRmResourceGroup -Name $newResourceGroupName -Location $Location
				
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$newClusterName";clusterStorageAccountName="$clusterStorageAccountName"}
				
		New-AzureRmResourceGroupDeployment `
			-Name $armDeploymentName `
			-ResourceGroupName $newResourceGroupName `
			-TemplateFile E:\HDITutorials-ARM\Create-clusters\hdinsight-arm-template.json `
			-TemplateParameterObject $parameters
				
		# List cluster
		Get-AzureRmHDInsightCluster -ResourceGroupName $newResourceGroupName -ClusterName $newClusterName 

	O script do PowerShell configura apenas o nome do cluster e o nome da conta de armazenamento. Você pode definir outros valores do modelo de ARM.
	
Para implantar um modelo do ARM usando outros métodos, veja [Implantar um aplicativo com um modelo do Gerenciador de Recursos do Azure](resource-group-template-deploy.md).


## Criar usando o Azure PowerShell
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Os procedimentos a seguir são necessários para criar um cluster HDInsight usando o Azure PowerShell:

- Criar um grupo de recursos do Azure
- Criar uma conta de Armazenamento do Azure
- Criar um contêiner de Blob do Azure
- Crie um cluster HDInsight

	$subscriptionId = "<Azure Subscription ID>"
	
	$newResourceGroupName = "<Azure Resource Group Name>" $location = "<Azure Location>" # por exemplo, "Leste dos EUA 2" $newDefaultStorageAccountName = "<Azure Storage Account Name>" $newClusterName = "<Azure HDInsight Cluster Name>" $clusterSizeInNodes = 1
	
	###########################################
	# Logon do Azure
	###########################################
	Login-AzureRmAccount Select-AzureRmSubscription
	SubscriptionId $subscriptionId
	
	###########################################
	# Criar o grupo de recursos
	###########################################
	New-AzureRmResourceGroup -Name $newRresourceGroupName -Location $location
	
	###########################################
	# Preparar a conta de armazenamento padrão e um contêiner
	###########################################
	New-AzureRmStorageAccount -ResourceGroupName $newResourceGroupName -Name $newDefaultStorageAccountName -Location $location
	
	$defaultStorageAccountKey = get-AzureRmStorageAccountKey - ResourceGroupName $newResourceGroupName-nome $newDefaultStorageAccountName | %{ $\_. Key1}
	$defaultStorageContext = New-AzureStorageContext - StorageAccountName $newDefaultStorageAccountName - StorageAccountKey $defaultStorageAccountKey
	AzureStorageContainer New-nome $newClusterName-contexto $defaultStorageContext #use o nome do cluster como o nome do contêiner
		
	###########################################
	# Criar o cluster
	###########################################
	$httpCredential =Get-Credential -Message "Enter the HTTP account credential:"
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $newResourceGroupName `
		-ClusterName $newClusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential



## Criar usando o SDK do .NET do HDInsight
O SDK do .NET do HDInsight fornece bibliotecas de cliente .NET que facilitam o trabalho com o HDInsight em um aplicativo .NET Framework. Siga as instruções abaixo para criar um aplicativo de console do Visual Studio e cole o código para criar um cluster.

**Para criar um aplicativo de console do Visual Studio**

1. No Visual Studio, crie um novo aplicativo de console C#.
2. Execute o comando Nuget a seguir no console de Gerenciamento de Pacotes NuGet.

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo, cole o seguinte código e forneça valores para as variáveis:

		using System;
		using System.Security;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		namespace CreateHDInsightCluster
		{
			class Program
			{
				private static HDInsightManagementClient _hdiManagementClient;
		
				private static Guid SubscriptionId = new Guid("<Azure Subscription ID");
				private const string ExistingResourceGroupName = "<Azure Resource Group Name>";
				private const string ExistingStorageName = "<Default Storage Account Name>.blob.core.windows.net";
				private const string ExistingStorageKey = "<Default Storage Account Key>";
				private const string ExistingBlobContainer = "<Default Blob Container Name>";
				private const string NewClusterName = "<HDInsight Cluster Name>";
				private const int NewClusterNumNodes = 1;
				private const string NewClusterLocation = "EAST US 2";     // Must be the same as the default Storage account
				private const OSType NewClusterOsType = OSType.Windows;
				private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
				private const string NewClusterVersion = "3.2";
				private const string NewClusterUsername = "admin";
				private const string NewClusterPassword = "<HTTP User password";
		
				static void Main(string[] args)
				{
					System.Console.WriteLine("Running");
		
					var tokenCreds = GetTokenCloudCredentials();
					var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
					_hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
				
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
				private static void CreateCluster()
				{
					var parameters = new ClusterCreateParameters
					{
						ClusterSizeInNodes = NewClusterNumNodes,
						UserName = NewClusterUsername,
						Password = NewClusterPassword,
						Location = NewClusterLocation,
						DefaultStorageAccountName = ExistingStorageName,
						DefaultStorageAccountKey = ExistingStorageKey,
						DefaultStorageContainer = ExistingBlobContainer,
						ClusterType = NewClusterType,
						OSType = NewClusterOsType
					};
		
					_hdiManagementClient.Clusters.Create(ExistingResourceGroupName, NewClusterName, parameters);
				}
		
				public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
				{
					var authFactory = new AuthenticationFactory();
		
					var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
					if (username != null && password != null)
						account.Id = username;
		
					var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
					var accessToken =
						authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
							.AccessToken;
		
					return new TokenCloudCredentials(accessToken);
				}
		
				public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
				{
					return new TokenCloudCredentials(subId.ToString(), creds.Token);
		
				}
			}
		}

7. Pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Você também será solicitado a inserir suas credenciais de conta do Azure. Pode levar vários minutos para criar um cluster HDInsight.

## Criar uso local dos Serviços de Integração do SQL Server

Você também pode usar o SQL Server Integration Services (SSIS) para criar ou excluir um cluster HDInsight. O Feature Pack do Azure para SSIS fornece os seguintes componentes que funcionam com clusters HDInsight.


- [Azure HDInsight Criar tarefa de Cluster][ssisclustercreate]
- [Azure HDInsight Excluir tarefa de Cluster][ssisclusterdelete]
- [Gerenciador de conexões de assinatura do Azure][connectionmanager]

Saiba mais sobre o Feature Pack do Azure para SSIS [aqui][ssispack].


##Próximas etapas
Neste artigo, você aprendeu várias maneiras de criar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Usar o Sqoop com o HDInsight](hdinsight-use-sqoop.md) - aprenda como copiar dados entre o HDInsight e o Banco de Dados SQL ou o SQL Server
* [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md) - aprenda como trabalhar com o HDInsight usando o PowerShell do Azure
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation] - descubra o SDK do HDInsight



##Apêndice A – Modelo do ARM

O modelo a seguir do Gerenciador de Recursos do Azure cria um cluster Hadoop com a conta de armazenamento do Azure dependente.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "username",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx

<!---HONumber=AcomDC_1203_2015-->