<properties 
   pageTitle="Provisionar clusters do Hadoop de forma personalizada no HDInsight | Microsoft Azure" 
   description="Saiba como provisionar de forma personalizada clusters para o Azure HDInsight usando o Portal Clássico do Azure, o Azure PowerShell, uma linha de comando ou o SDK do .NET." 
   services="hdinsight" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="01/28/2016"
   ms.author="jgao"/>

#Provisionar clusters do Hadoop no HDInsight

Aprenda a planejar para provisionar clusters do HDInsight.

> [AZURE.WARNING] As etapas neste documento usam o portal clássico do Azure, juntamente com versões mais antigas do Azure PowerShell e a CLI do Azure. A Microsoft não recomenda o uso destas etapas. Em vez disso, é necessário usar o portal do Azure e as versões mais recentes do Azure PowerShell e a CLI do Azure. Para obter uma versão deste documento que usa o Portal do Azure e as versões mais recentes do Azure PowerShell e a CLI do Azure, veja [Provision Hadoop clusters in HDInsight](hdinsight-provision-clusters.md) (Provisionar clusters Hadoop no HDInsight)
>
> Para obter uma explicação das vantagens do portal do Azure, veja [Portal do Microsoft Azure](https://azure.microsoft.com/features/azure-portal/).

**Pré-requisitos:**

Antes de começar a seguir as instruções neste artigo, você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Opções de configuração básicas


- **Nome do cluster**

	O nome do cluster é usado para identificar um cluster. O nome do cluster deve seguir as diretrizes seguintes:
	
	- O campo deve ser uma cadeia com 3 a 63 caracteres
	- O campo pode conter apenas letras, números e hifens.

- **Nome da assinatura**

	Um cluster HDInsight é vinculado a uma assinatura do Azure.
 
- **Sistema operacional**

	Você pode provisionar clusters HDInsight em um dos dois sistemas operacionais a seguir:
	- **HDInsight no Windows (Windows Server 2012 R2 Datacenter)**:
	- **HDInsight no Linux (Ubuntu 12.04 LTS para Linux)**: o HDInsight fornece a opção de configuração de clusters do Linux no Azure. Configure um cluster do Linux, se você estiver familiarizado com o Linux ou Unix, migre de uma solução Hadoop baseada em Linux existente ou deseja fácil integração com componentes do ecossistema do Hadoop criados para o Linux. Para obter mais informações, consulte [Introdução ao Hadoop no Linux em HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md). 


- **Versão do HDInsight**

	É usada para determinar a versão do HDInsight a ser usada para este cluster. Para obter mais informações, veja [Versões e componentes do cluster Hadoop no HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409)

- **Tipo de cluster** e **tamanho do cluster (também conhecidos como nós de dados)**

	O HDInsight permite que os clientes implantem uma variedade de tipos de cluster, para diferentes cargas de trabalho de análise de dados. Os tipos do cluster oferecidos atualmente são:
	
	- Clusters do Hadoop: para cargas de trabalho de consulta e análise
	- Clusters do HBase: para cargas de trabalho NoSQL
	- Clusters do Storm: para cargas de trabalho de processamento de eventos em tempo real
	- Clusters do Spark: para cargas de trabalho de processamento na memória, consultas interativas, fluxo e aprendizado de máquina.

	![Clusters do HDInsight](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE] O *cluster Azure HDInsight* também é chamado de *clusters Hadoop no HDInsight* ou *cluster HDInsight*. Às vezes, ele é usado alternadamente com o *cluster Hadoop*. Todos eles denotam clusters do Hadoop hospedados no ambiente do Microsoft Azure.

	Em um determinado tipo cluster, há diferentes funções para os diferentes nós, que permitem que um cliente dimensione os nós em uma determinada função apropriada para os detalhes de sua carga de trabalho. Por exemplo, um cluster do Hadoop pode ter seus nós de trabalho configurados com uma grande quantidade de memória se o tipo de análise executada tiver um uso muito intensivo de memória.

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	Os clusters Hadoop para HDInsight são implantados com duas funções:

	- Nó principal (2 nós)
	- Nó de dados (pelo menos 1 nó)

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	Clusters HBase para HDInsight são implantados com três funções:
	- Servidores de cabeça (2 nós)
	- Servidores de região (pelo menos 1 nó)
	- Nós mestre/Zookeeper (3 nós)
	
	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	Clusters Storm para HDInsight são implantados com três funções:
	- Nós Nimbus (2 nós)
	- Servidores de Supervisor (pelo menos 1 nó)
	- Nós Zookeeper (3 nós)
	

	![Funções de cluster Hadoop do HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	Os clusters do Spark para HDInsight são implantados com três funções:
	- Nó principal (2 nós)
	- Nó de trabalho (pelo menos 1 nó)
	- Nós do Zookeeper (3 nós) (Gratuito para A1 Zookeepers)

	Os clientes são cobrados pelo uso desses nós pela duração da vida útil do cluster. A cobrança é iniciada quando um cluster é criado e para quando o cluster é excluído (clusters não podem ser desalocados ou colocados em espera). O tamanho do cluster afeta seu preço. Para fins de aprendizado, é recomendável usar 1 nó de dados. Para obter mais informações sobre os preços do HDInsight, confira [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] O limite de tamanho do cluster varia segundo as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.
	
- **Rede virtual/região (ou seja, o local)**

	![Regiões do Azure](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	Para ver uma lista das regiões com suporte, clique na lista suspensa **Região** em [Preços do HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Tamanho do nó**

	![tamanhos de nó de vm do hdinsight](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	Selecione o tamanho da VM para os nós. Para obter mais informações, confira [Tamanhos para Serviços de Nuvem](cloud-services-sizes-specs.md)

	O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.


- **Usuários do HDInsight**

	Os clusters do HDInsight permitem que você configure duas contas de usuário durante o provisionamento:

	- Usuário HTTP. O nome de usuário padrão é admin, com a configuração básica do Portal Clássico do Azure.
	- Usuário RDP (clusters do Windows): é usado para conectar-se ao cluster usando o RDP. Quando cria a conta, você deve definir uma data de validade que esteja dentro de 90 dias a partir do dia atual. 
	- Usuário SSH (clusters do Linux): é usado para conectar-se ao cluster usando SSH. Você pode criar contas de usuário SSH adicionais depois que o cluster for criado ao seguir as etapas em [Usar SSH com Hadoop baseado em Linux no HDInsight do Linux, do Unix ou do OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
  
 

- **Conta de armazenamento do Azure**


	O HDFS original usa um dos muitos discos locais no cluster. O HDInsight usa o armazenamento de Blobs do Azure em vez do armazenamento de dados. O armazenamento de blobs do Azure é uma solução de armazenamento de uso geral que se integra perfeitamente com o HDInsight. Através de uma interface HDFS (Sistema de Arquivos Distribuído Hadoop), o conjunto completo de componentes em HDInsight pode operar diretamente sobre os dados não estruturados do armazenamento de Blobs. O armazenamento de dados no armazenamento de blobs permite que os clusters HDInsight usados para cálculo sejam excluídos com segurança sem que ocorra perda de dados do usuário.
	
	Durante a configuração, você deve especificar uma conta de armazenamento do Azure e um contêiner de armazenamento de Blob do Azure na conta de armazenamento do Azure. Alguns processos de provisionamento requerem que a conta de armazenamento do Azure e o contêiner de armazenamento de Blob sejam criados anteriormente. O contêiner de armazenamento de Blob é usado como o local de armazenamento padrão pelo cluster. Ou você pode especificar contas de armazenamento do Azure adicionais (armazenamento vinculado) que poderão ser acessadas pelo cluster. Além disso, o cluster também pode acessar qualquer contêiner de Blob configurado com acesso de leitura público completo ou acesso de leitura para blobs somente. Para obter mais informações sobre o acesso restrito, confira [Gerenciar o acesso aos recursos do Armazenamento do Azure](storage-manage-access-to-resources.md).

	![Armazenamento do HDInsight](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE] Um contêiner de armazenamento de Blobs oferece o agrupamento de um conjunto de blobs, conforme mostrado na imagem:
	
	![Armazenamento do blobs do Azure](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING] Não compartilhe um contêiner de armazenamento de Blobs entre múltiplos clusters. Não há suporte para isso.
	
	Para obter mais informações sobre o uso de repositórios de blobs secundários, consulte [Usando o Armazenamento de Blobs do Azure com o HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Metastore do Hive/Oozie**

	O metastore contém metadados de Hive e Oozie, como tabelas Hive, partições, esquemas e colunas. Usar o metastore ajuda a manter seus metadados de Hive e Oozie, para que você não precise recriar tabelas Hive ou trabalhos do Oozie ao provisionar um novo cluster. Por padrão, o Hive usa um banco de dados SQL do Azure integrado para armazenar essas informações. O banco de dados integrado não é capaz de preservar os metadados quando o cluster é excluído. Por exemplo, você tem um cluster provisionado com um metastore do Hive. Você criou algumas tabelas do Hive. Depois de excluir o cluster e recriá-lo usando o mesmo metastore do Hive, você poderá ver as tabelas do Hive criadas no cluster original.

## Opções de configuração avançada

>[AZURE.NOTE] Esta seção atualmente só se aplica a clusters do HDInsight baseados do Windows.

### Personalizar clusters usando a personalização de clusters do HDInsight

Às vezes, você deseja configurar os arquivos de configuração. Estes são alguns deles.

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Os clusters não podem reter as alterações devido à recriação das imagens. Para obter mais informações, consulte [A instância da função é reinicializada devido as atualizações do sistema operacional (a página pode estar em inglês)](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Para manter as alterações durante o tempo de vida dos clusters, você pode usar a personalização de clusters do HDInsight durante o processo de provisionamento.

A seguir, há um exemplo de script do Azure PowerShell para personalização de uma configuração do Hive:

	# hive-site.xml configuration 
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60
	
	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues 
	
	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Alguns outros exemplos de personalização de outros arquivos de configuração:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1
	
	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50
	
	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000
	
	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120
	
Para obter mais informações, confira o blog de Azim Uddin chamado [Personalizando o provisionamento de clusters HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Personalizar clusters usando a ação de Script

Você pode instalar componentes adicionais ou personalizar a configuração de cluster por meio de scripts durante o provisionamento. Esses scripts são chamados usando a **Ação de Script**, que é uma opção de configuração que pode ser usada no Portal Clássico do Azure, nos cmdlets do Windows PowerShell do HDInsight ou no SDK do .NET do HDInsight. Para obter mais informações, consulte [Personalizar cluster HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).


### Usar redes virtuais do Azure

A [Rede Virtual do Azure](https://azure.microsoft.com/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN).

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando uma VPN do software.

	![diagrama da configuração ponto a site](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

Para obter informações sobre como usar o HDInsight com uma Rede Virtual, incluindo requisitos de configuração específicos para a Rede Virtual, veja [Estender os recursos do HDInsight usando a Rede Virtual do Azure](hdinsight-extend-hadoop-virtual-network.md).

## Ferramentas de provisionamento

- O Portal Clássico do Azure
- PowerShell do Azure
- SDK .NET
- CLI

### Use o Portal Clássico do Azure

Você pode consultar as [opções de configuração básica] e as [opções de configuração avançada] para ver as explicações dos campos.

**Para criar um cluster HDInsight usando a opção Criação Personalizada**

1. Entre no [Portal Clássico do Azure][azure-management-portal].
2. No final da página, clique em **+ NOVO**, em **SERVIÇOS DE DADOS**, em **HDINSIGHT** e em **CRIAÇÃO PERSONALIZADA**.
3. Na página **Detalhes do Cluster**, digite ou escolha os valores a seguir:

	- Nome do cluster
	- Nome da assinatura
	- Tipo de cluster
	- Sistema operacional
	- Versão do HDInsight

4. Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	- Nós de dados
	- Região/Rede virtual
	- Tamanho do nó principal
	- Tamanho do nó de dados

5. Na página **Configurar Usuário de Cluster**, forneça os seguintes valores:

	- Nome de usuário HTTP
	- Senha/Confirmar senha HTTP
	- Habilitar área de trabalho remota para o cluster
	- Inserir como metastore do Hive/Oozie

6. Se você optar por inserir o metastore do Hive/Oozie na tela anterior, na página **Configurar Metastore do Hive/Oozie**, forneça os seguintes valores:

	- Banco de dados do metastore do Hive
	- Usuário do banco de dados
	- Senha do usuário do banco de dados
	- Banco de dados do metastore do Oozie
	- Usuário do banco de dados
	- Senha do usuário do banco de dados

7. Na página **Conta de Armazenamento**, forneça os seguintes valores:

	- Conta de armazenamento
	- Nome da conta
	- Contêiner padrão
	- Contas de armazenamento adicionais

8. Na página **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre o script personalizado que você deseja executar para personalizar um cluster, enquanto o cluster está sendo criado. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).

	- Nome: especifique um nome para a ação de script
	- URI do script: especifique o URI (Uniform Resource Identifier) do script invocado para personalizar o cluster.
	- Tipo de nó: especifique os nós em que o script de personalização deve ser executado. Você pode escolher <b>Todos os Nós</b>, <b>Somente nós de cabeçalho</b> ou <b>Somente nós de dados</b>.
	- Parâmetros: especifique os parâmetros, se exigido pelo script.</td></tr>

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.

### Usar PowerShell do Azure
O PowerShell do Azure é um ambiente de script poderoso que você pode usar para controlar e automatizar a implantação e o gerenciamento de suas cargas de trabalho no Azure. Esta seção fornece instruções sobre como provisionar um cluster HDInsight usando o PowerShell do Azure. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md). Para obter mais informações sobre como usar o PowerShell do Azure com o HDInsight, consulte [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md). Para obter a lista de cmdlets do Windows PowerShell do HDInsight, consulte [Referência de cmdlets do HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE] Embora os scripts nesta seção possam ser usados para configurar um cluster HDInsight em uma Rede Virtual do Azure, eles não criarão uma Rede Virtual do Azure. Para obter informações sobre como criar uma Rede Virtual do Azure, consulte [Tarefas de configuração de rede virtual](../services/virtual-machines/).

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

> [AZURE.NOTE] Os cmdlets do PowerShell do Azure são a única maneira recomendada de alterar as variáveis de configuração em um cluster do HDInsight. As alterações feitas aos arquivos de configuração do Hadoop durante uma conexão ao cluster por meio da área de trabalho remota podem ser sobrescritas em caso de aplicação de patch no cluster. Os valores da configuração definidos por meio do PowerShell do Azure serão preservados se ocorrer uma aplicação de patch no cluster.

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

	>[AZURE.NOTE] Os comandos $hadoopUserName e $hadoopUserPassword são usados para criar a conta de usuário do Hadoop para o cluster. Você usará essa conta para conectar-se ao cluster e executar trabalhos. Se você usar a opção Criação Rápida no Portal Clássico do Azure para provisionar um cluster, o nome de usuário padrão do Hadoop será “admin”. Não confunda essa conta com a conta de usuário do protocolo RDP. A conta de usuário do RDP deve ser diferente da conta de usuário do Hadoop. Para obter mais informações, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal Clássico do Azure][hdinsight-admin-portal].

	Pode levar vários minutos para que o provisionamento do cluster seja concluído.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Para provisionar um cluster HDInsight usando as opções de configuração personalizadas**

Enquanto provisiona um cluster, você pode usar as outras opções de configuração, como conectar-se a mais de um contêiner de armazenamento de Blob do Azure ou usar uma rede virtual ou banco de dados SQL do Azure para metastores do Hive e do Oozie. Isso permite que você separe o tempo de vida de seus dados e metadados do tempo de vida do cluster.

> [AZURE.NOTE] Os cmdlets do Windows PowerShell são a única maneira recomendada de alterar as variáveis de configuração em um cluster HDInsight. As alterações feitas aos arquivos de configuração do Hadoop durante uma conexão ao cluster por meio da área de trabalho remota podem ser sobrescritas em caso de aplicação de patch no cluster. Os valores da configuração definidos por meio do PowerShell do Azure serão preservados se ocorrer uma aplicação de patch no cluster.

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

	>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.

**Para listar os clusters HDInsight**

- Execute o seguinte comando em uma janela de console do PowerShell do Azure para listar o cluster HDInsight e verificar se ele foi provisionado com êxito:

		Get-AzureHDInsightCluster -Name <ClusterName>


### Usar a CLI do Azure

> [AZURE.NOTE] A partir de 29/8/2014, a CLI do Azure não pode ser usada para associar um cluster a uma rede virtual do Azure.

Outra opção para o provisionamento de um cluster HDInsight é a CLI do Azure. A CLI do Azure é implementada em Node.js. Ela pode ser usada em qualquer plataforma que dá suporte a Node. js, incluindo Windows, Mac e Linux.

Para obter um guia geral sobre como usar a CLI do Azure, veja [CLI do Azure](../xplat-cli-install.md).

As instruções a seguir orientam sobre como instalar a CLI do Azure no Linux e no Windows e como usar a linha de comando para provisionar um cluster.

- [Configurar a CLI do Azure para Linux](#clilin)
- [Configurar a CLI do Azure para Windows](#cliwin)
- [Provisionar clusters HDInsight usando a CLI do Azure](#cliprovision)

#### <a id="clilin"></a>Configurar a CLI do Azure para Linux

Execute os seguintes procedimentos para configurar seu computador Linux para usar as ferramentas de linha de comando do Azure (CLI do Azure):

- Instale a CLI do Azure usando o NPM (Gerenciador de Pacotes Node.js)
- Conecte-se à sua assinatura do Azure

**Para instalar a CLI do Azure usando o NPM**

1.	Abra uma janela de terminal no seu computador Linux e execute o seguinte comando:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para conectar-se à sua assinatura do Azure**

Antes de usar a CLI do Azure, você deve configurar a conectividade entre sua estação de trabalho e o Azure. As informações da assinatura do Azure são utilizadas pela CLI do Azure para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a CLI do Azure usará para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.

> [AZURE.NOTE] O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use Criptografia de Unidade de Disco BitLocker.


1.	Abra uma janela de terminal.
2.	Execute o seguinte comando para fazer logon em sua assinatura do Azure:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	O comando inicia a página da Web na qual se baixa o arquivo de configurações de publicação. Se a página da Web não se abrir, clique no link na janela do terminal para abrir a página do navegador e fazer logon no portal.

3.	Baixe o arquivo de configurações de publicação para o computador.
5.	Na janela do prompt de comando, execute o seguinte comando para importar o arquivo de configurações da publicação:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Configurar a CLI do Azure para Windows

Execute os seguintes procedimentos para configurar seu computador Windows para usar a CLI do Azure:

- Instale a CLI do Azure (usando o NPM ou do Windows Installer)
- Baixar e importar as configurações de publicação da conta do Azure


A CLI do Azure pode ser instalada usando o NPM ou o Instalador do Windows. A Microsoft recomenda instalar usando apenas uma das duas opções.

**Para instalar a CLI do Azure usando o NPM**

1.	Procure o **www.nodejs.org**.
2.	Clique em **INSTALAR** e siga as instruções usando as configurações padrão.
3.	Abra o **Prompt de Comando** (ou o *Prompt de Comando do Azure* ou o *Prompt de Comando do Desenvolvedor para VS2012*) em sua estação de trabalho.
4.	Execute o seguinte comando na janela do prompt de comando:

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE] Se você receber um erro informando que o comando NPM não foi encontrado, verifique se os caminhos a seguir estão na variável de ambiente PATH: <i>C:\\Arquivos de Programas (x86)\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i> ou <i>C:\\Arquivos de Programas\\nodejs;C:\\Usuários[nomedeusuário]\\AppData\\Roaming\\npm</i>

5.	Execute o comando a seguir para verificar a instalação:

		azure hdinsight -h

	Você pode usar a opção *-h* em diferentes níveis para exibir as informações da Ajuda. Por exemplo:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Para instalar a CLI do Azure usando o Windows Installer**

1.	Navegue até **http://azure.microsoft.com/downloads/**.
2.	Role para baixo até a seção **Ferramentas de linha de comando** e, em seguida, clique em **Interface de Linha de Comando do Azure** e siga o assistente do Web Platform Installer.

**Para baixar e importar as configurações de publicação**

Antes de usar a CLI do Azure, você deve configurar a conectividade entre sua estação de trabalho e o Azure. As informações da assinatura do Azure são utilizadas pela CLI do Azure para se conectar à sua conta. Essas informações podem ser obtidas no Azure em um arquivo de configurações de publicação. O arquivo de configurações de publicação pode ser importado como uma definição de configuração local persistente que a CLI do Azure usará para operações subsequentes. Você precisa importar suas configurações de publicação apenas uma vez.

> [AZURE.NOTE] O arquivo de configurações de publicação contém informações confidenciais. A Microsoft recomenda excluir o arquivo ou executar etapas adicionais para criptografar a pasta do usuário que contém o arquivo. No Windows, modifique as propriedades da pasta ou use o BitLocker.


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

Para obter informações sobre como criar uma conta do Armazenamento do Azure usando o Portal Clássico do Azure, veja [Criar, gerenciar ou excluir uma conta de armazenamento](../storage/storage-create-storage-account.md).

Se você já tem uma conta do Armazenamento, mas não se lembra do nome da conta e da chave de conta, você pode usar os seguintes comandos para recuperar as informações:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Para obter detalhes sobre como obter as informações usando o Portal Clássico do Azure, veja a seção *Como: exibir, copiar e regenerar chaves de acesso de armazenamento* de [Criar, gerenciar ou excluir uma conta de armazenamento](../storage/storage-create-storage-account.md).

Um cluster HDInsight também requer um contêiner em uma conta do Armazenamento. Se a conta de armazenamento fornecida ainda não tiver um contêiner, o comando *azure hdinsight cluster create* solicitará um nome de contêiner e também o criará. No entanto, se você desejar criar o contêiner com antecedência, poderá usar o seguinte comando:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Depois de preparar a conta de armazenamento e o contêiner de Blob, você estará pronto para criar um cluster.

**Para provisionar um cluster HDInsight**

- Execute o seguinte comando na janela do prompt de comando:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Para provisionar um cluster HDInsight usando um arquivo de configuração**

Normalmente, você provisiona um cluster HDInsight, executa os trabalhos e exclui o cluster para reduzir o custo. A CLI oferece a opção de salvar as configurações em um arquivo, de modo que você possa reutilizá-lo sempre que provisionar um cluster.

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

	>[AZURE.NOTE] O banco de dados SQL do Azure usado para o metastore deve permitir a conectividade com outros serviços do Azure, incluindo o Azure HDInsight. No painel do banco de dados SQL do Azure, no lado direito, clique no nome do servidor. Esse é o servidor no qual a instância do banco de dados SQL está sendo executada. Quando você estiver na exibição do servidor, clique em **Configurar** e depois em **Serviços do Azure**; clique em **Sim** e depois em **Salvar**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Para listar e mostrar detalhes do cluster**

- Use os seguintes comandos para listar e mostrar os detalhes do cluster:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Para excluir um cluster**

- Use o seguinte comando para excluir um cluster:

		azure hdinsight cluster delete <ClusterName>



### Use o SDK .NET do HDInsight
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

1. Abra o Visual Studio 2013 ou 2015.

2. No menu **Arquivo**, clique em **Novo** e em **Projeto**.

3. Em **Novo Projeto**, digite ou selecione os seguintes valores:

	|Propriedade|Valor|
	|--------|-----|
	|Modelo|Templates/Visual C#/Windows/Console Application|
	|Nome|CreateHDICluster|

4. Clique em **OK** para criar o projeto.

5. No menu **Ferramentas**, clique em **Gerenciador de Pacotes NuGet** e em **Console do Gerenciador de Pacotes**.

6. Execute o seguinte comando no console para instalar os pacotes:

		Install-Package Microsoft.Azure.Common.Authentication -Pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Management.Resources -Pre

	Esses comandos adicionam bibliotecas .NET e referências a elas no projeto atual do Visual Studio.

7. No Gerenciador de Soluções, clique duas vezes em **Program.cs** para abri-lo.
8. Substitua o código pelo seguinte código:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Microsoft.Azure.Management.Resources;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		            
		            var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
		            resourceManagementClient.Providers.Register("Microsoft.HDInsight");
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
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
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Substitua os valores de membro de classe.

**Para executar o aplicativo**

Enquanto o aplicativo estiver aberto no Visual Studio, pressione **F5** para executar o aplicativo. Uma janela de console deve ser aberta e exibir o status do aplicativo. Pode levar vários minutos para criar um cluster HDInsight.


##<a id="nextsteps"></a> Próximas etapas
Neste artigo, você aprendeu várias maneiras de provisionar um cluster HDInsight. Para saber mais, consulte os seguintes artigos:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - aprenda como começar a trabalhar com seu cluster HDInsight
* [Usar o Sqoop com o HDInsight](hdinsight-use-sqoop.md) - aprenda como copiar dados entre o HDInsight e o Banco de Dados SQL ou o SQL Server
* [Administrar o HDInsight usando o PowerShell](hdinsight-administer-use-powershell.md) - aprenda como trabalhar com o HDInsight usando o PowerShell do Azure
* [Enviar trabalhos do Hadoop de forma programática](hdinsight-submit-hadoop-jobs-programmatically.md) - aprenda como enviar trabalhos ao HDInsight de forma programática
* [Documentação do SDK do Azure HDInsight][hdinsight-sdk-documentation] - descubra o SDK do HDInsight


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0420_2016-->