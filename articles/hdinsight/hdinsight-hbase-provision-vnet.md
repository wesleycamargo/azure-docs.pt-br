<properties 
	pageTitle="Provisionar clusters do HBase em uma Rede Virtual | Microsoft Azure" 
	description="Introdução ao uso do HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure." 
	services="hdinsight,virtual-network" 
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
   ms.date="07/21/2015"
   ms.author="jgao"/>

# Provisionar clusters do HBase na Rede Virtual do Azure

Saiba como criar clusters do HBase do HDInsight em uma [Rede Virtual do Azure][1].

Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

- Conectividade direta do aplicativo Web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC (chamada de procedimento remoto) Java do HBase.
- Desempenho aprimorado, evitando que o tráfego percorra diversos gateways e balanceadores de carga.
- Capacidade de processar informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público.

>[AZURE.NOTE]O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade. Use o cmdlet Get-AzureVNetConfig do Azure PowerShell para verificar se uma rede virtual do Azure existente é baseada no local. Se sua rede virtual não for baseada no local, você tem as seguintes opções:
>
> - Exportar a configuração de rede virtual existente e criar uma nova rede virtual. Todas as novas redes virtuais são baseadas em local por padrão.
> - Migrar para uma rede virtual baseada em local. Consulte [Migrar serviços existentes para o escopo regional](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).



##Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). Para executar scripts do PowerShell do Azure, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Usando o cmdlet Set-ExecutionPolicy][2].

	Antes de executar scripts do PowerShell do Azure, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>


##Provisionar um cluster do HBase em uma rede virtual 

Antes de provisionar um cluster HBase, você precisa ter uma rede virtual do Azure.

**Para criar uma Rede Virtual usando o portal do Azure**

1. Entre no [Portal do Azure][azure-portal].
2. Clique em **NOVO** no canto inferior esquerdo, clique em **SERVIÇOS DE REDE**, clique em **REDE VIRTUAL** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.
3. Digite ou selecione os valores a seguir:

	- **Nome** - o nome da sua rede virtual.
	- **Espaço de endereço** - escolha um nome de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará. Para percorrer este tutorial, você pode escolher qualquer uma das três opções. 
	- **Contagem máxima de VMs** - escolha uma das contagens máximas da máquina virtual. Esse valor determina o número de possíveis hosts (VMs) que podem ser criadas sob o espaço de endereço. Para percorrer este tutorial, **4096 [CIDR: /20]** é suficiente. 
	- **Local** - o local deve ser o mesmo que o do cluster do HBase que você criará.
	- **Servidor DNS** - este tutorial usa um servidor interno de sistema de nome de domínio (DNS) fornecido pelo Azure, portanto, você possa escolher **Nenhum**. Configurações de rede mais avançadas com servidores DNS personalizados também têm suporte. Para obter orientações detalhadas, consulte [Resolução de Nomes (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Clique em **CRIAR UMA REDE VIRTUAL** no canto inferior direito. O nome da nova rede virtual aparecerá na lista. Aguarde até que a coluna de Status exiba **Criada**.
5. No painel principal, clique na rede virtual que você acabou de criar.
6. Clique em **PAINEL** na parte superior da página.
7. Em **visão rápida**, anote a ID da rede virtual. Você precisará dela para provisionar o cluster do HBase.
8. Clique em **CONFIGURAR** na parte superior da página.
9. No final da página, o nome da sub-rede padrão é **Subnet-1**. Você também pode renomear a sub-rede ou adicionar uma nova sub-rede ao cluster do HBase. Anote o nome da sub-rede, você precisará dele para provisionar o cluster.
10. Verifique o **CIDR (CONTAGEM DE ENDEREÇOS)** para a sub-rede que será usada para o cluster. A contagem de endereços deve ser maior que o número de nós de trabalho mais sete (gateway: 2, nó principal: 2, Zookeeper: 3). Por exemplo, se você precisa de um cluster do HBase de 10 nós, a contagem de endereços da sub-rede deve ser maior que 17 (10+7). Caso contrário, a implantação falhará.
11. Clique em **Salvar**, no final da página, se tiver atualizado os valores de sub-rede.


**Para adicionar uma máquina virtual do servidor DNS à rede virtual**

Um servidor DNS é opcional, mas necessário em alguns casos. O procedimento foi documentado em [Configurar DNS entre duas redes virtuais do Azure][hdinsight-hbase-replication-dns]. Basicamente, você precisará executar estas etapas:

1. adicionar uma máquina virtual do Azure à rede virtual
2. definir um endereço IP estático para a máquina virtual
3. adicionar a função de servidor DNS à máquina virtual
4. Atribuir o servidor DNS à rede virtual 


**Para criar uma conta de Armazenamento do Azure e um contêiner de armazenamento de Blob a ser usado pelo cluster**

> [AZURE.NOTE]Os clusters do HDInsight usam o armazenamento de blob do Azure para armazenar dados. Para obter mais informações, consulte [Usar o armazenamento de blob do Azure com o Hadoop no HDInsight](../hdinsight-use-blob-storage.md). Você precisará de uma conta de armazenamento e um contêiner de armazenamento de blob. O local da conta de armazenamento deve corresponder ao local da rede virtual e ao local do cluster.

Como outros clusters do HDInsight, o cluster HBase requer uma conta de armazenamento do Azure e um contêiner de armazenamento de Blob como o sistema de arquivos padrão. O local da conta de armazenamento deve corresponder ao local da rede virtual e ao local do cluster. Para obter mais informações, consulte [Usar o armazenamento de blob do Azure com o Hadoop no HDInsight][hdinsight-storage]. Quando você provisiona um cluster HBase, você tem as opções de criar um novo ou usar os existentes. Este procedimento mostra como criar uma conta de armazenamento e um contêiner de armazenamento de Blob usando o portal do Azure.

1. Entre no [Portal do Azure][azure-portal].
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e clique em **CRIAÇÃO RÁPIDA**.

3. Digite ou selecione os valores a seguir:

	- **URL** - o nome da conta de armazenamento.
	- **LOCAL** - o local da conta de armazenamento. Certifique-se de que ele corresponda ao local da rede virtual. Não há suporte para grupos de afinidade.
	- **REPLICAÇÃO** - para fins de teste, use **Localmente redundante** para reduzir o custo.

4. Clique em **CRIAR CONTA DE ARMAZENAMENTO**. Você verá a nova conta do Armazenamento na lista de armazenamento.
5. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
6. Clique na nova conta de armazenamento da lista para selecioná-la.
7. Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
8. Tome nota do nome da conta de armazenamento e da chave de acesso primária (ou a chave de acesso secundária; qualquer uma das chaves funciona). Você precisará deles mais tarde no tutorial.
9. No topo da página, clique em **CONTÊINER**.
10. No final da página, clique em **ADICIONAR**.
11. Insira o nome do contêiner. Este contêiner será usado como contêiner padrão para este cluster do HBase. Por padrão, o nome do contêiner padrão corresponde ao nome do cluster. Mantenha o campo **ACESSO** como **Privado**.  
12. Clique na marca de seleção para criar o contêiner.

**Para provisionar um cluster HBase usando o Portal do Azure**

> [AZURE.NOTE]Para obter informações sobre como provisionar um novo cluster do HBase usando o PowerShell do Azure, consulte [Provisionar um cluster HBase usando o PowerShell do Azure](#powershell).

1. Entre no [Portal do Azure][azure-portal].

2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.

3. Insira um nome de cluster, selecione HBase como o tipo de cluster, selecione o sistema operacional Windows Server 2012, selecione a versão do HDInsight e, em seguida, clique no botão direito.

	![Fornecer detalhes para o cluster HBase][img-provision-cluster-page1]


	> [AZURE.NOTE]Para um cluster HBase, o Windows Server é a opção de sistema operacional disponível.

4. Na página **Configurar Cluster**, digite ou selecione os valores a seguir:

	![Fornecer detalhes para o cluster HBase](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nós de dados</td><td>Selecione o número dos nós de dados que você deseja implantar. Para fins de teste, crie um cluster de nó único. <br />O limite de tamanho do cluster varia para as assinaturas do Azure. Contate o suporte de cobrança do Azure para aumentar o limite.</td></tr>
		<tr><td>Região/Rede virtual</td><td><p>Selecione uma região ou rede virtual do Azure, se você já tiver criado uma. Para este tutorial, selecione a rede que você criou anteriormente e, em seguida, selecione uma sub-rede correspondente. O nome padrão é <b>Sub-rede-1</b>.</p></td></tr>
		<tr><td>Tamanho do nó principal</td><td><p>Selecione um tamanho de VM para o nó principal.</p></td></tr>
		<tr><td>Tamanho do nó de dados</td><td><p>Selecione um tamanho de VM para os nós de dados.</p></td></tr>
		<tr><td>Tamanho do Zookeeper</td><td><p>Selecione um tamanho de VM para o nó Zookeper.</p></td></tr>
	</table>

	>[AZURE.NOTE]O custo pode variar de acordo com sua escolha de VMs. O HDInsight usa todas as VMs de camada padrão para nós de cluster. Para obter informações sobre como os tamanhos de VM afetam os preços, consulte <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">Preços do HDInsight</a>.

	Clique no botão direito.

5. Insira o nome de usuário e senha do Hadoop a utilizar para este cluster e, em seguida, clique no botão à direita.

	![Fornecer conta de armazenamento para o cluster Hadoop do HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome de usuário HTTP</td>
			<td>Especifique o nome do usuário do cluster HDInsight.</td></tr>
		<tr><td>Senha/Confirmar senha HTTP</td>
			<td>Especifique a senha do usuário do cluster HDInsight.</td></tr>
		<tr><td>Habilitar área de trabalho remota para o cluster</td>
			<td>Marque esta caixa de seleção para especificar uma data de expiração, senha e nome de usuário para um usuário de área de trabalho remota que possa acessar remotamente os nós do cluster após seu provisionamento. Você também pode habilitar área de trabalho remota mais tarde, depois que o cluster for provisionado. Para instruções, consulte <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Conectar-se a clusters HDInsight usando RDP</a>.</td></tr>
	</table>

6. Na página **Conta de Armazenamento**, forneça os seguintes valores:

    ![Fornecer conta de armazenamento para o cluster Hadoop do HDInsight](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Conta de armazenamento</td>
			<td>Especifique a conta de Armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster HDInsight. Você pode escolher uma destas três opções:
			<ul>
				<li><strong>Usar Armazenamento Existente</strong></li>
				<li><strong>Criar Novo Armazenamento</strong></li>
				<li><strong>Usar Armazenamento de Outra Assinatura</strong></li>
			</ul>
			</td></tr>
		<tr><td>Nome da conta</td>
			<td><ul>
				<li>Se você optar por usar um armazenamento existente, para <strong>Nome da conta</strong>, selecione uma conta de armazenamento existente. A lista suspensa exibe somente as contas de armazenamento localizadas no mesmo datacenter onde você optou por provisionar o cluster.</li>
				<li>Se você selecionar uma das opções <strong>Criar novo armazenamento</strong> ou <strong>Usar armazenamento de outra assinatura</strong>, deverá fornecer o nome da conta de armazenamento.</li>
			</ul></td></tr>
		<tr><td>Chave de conta</td>
			<td>Se você escolheu a opção <strong>Usar Armazenamento de Outra Assinatura</strong>, especifique a chave dessa conta do Armazenamento.</td></tr>
		<tr><td>Contêiner padrão</td>
			<td><p>Especifique o contêiner padrão da conta do Armazenamento que é utilizado como sistema de arquivos padrão para o cluster HDInsight. Se você tiver escolhido <strong>Usar Armazenamento Existente</strong> no campo <strong>Conta de Armazenamento</strong> e não existir nenhum contêiner nessa conta, por padrão, o contêiner será criado com o mesmo nome que o nome do cluster. Se um contêiner com o nome do cluster já existir, será acrescentado um número de sequência ao nome do contêiner. Por exemplo, meucontêiner1, meucontêiner2 e assim por diante. No entanto, se a conta do Armazenamento tiver um contêiner com um nome diferente do nome do cluster especificado, você poderá usar esse contêiner também.</p>
        	<p>Se você optou por criar um novo armazenamento ou usar o armazenamento de outra assinatura do Azure, especifique o nome do contêiner padrão.</p>
    	</td></tr>
		<tr><td>Contas de armazenamento adicionais</td>
			<td>Se necessário, especifique contas de armazenamento adicionais para o cluster. O HDInsight dá suporte a várias contas do Armazenamento. Não há nenhum limite quanto ao número de contas do Armazenamento que um cluster pode usar. No entanto, se criar um cluster usando o Portal do Azure, você terá um limite de sete contas, devido a restrições da interface do usuário. Cada conta de armazenamento adicional que você especificar adiciona uma página extra de <strong>Conta de Armazenamento</strong> ao assistente, em que você pode especificar as informações da conta. Por exemplo, na captura de tela acima, nenhuma conta de armazenamento adicional está selecionada, e portanto, não ocorre adição de uma página extra ao assistente.</td></tr>
	</table>

	Clique na seta à direita.

7. Na página **Ações de Script** selecione a marca de seleção no canto inferior direito. Não clique no botão **Adicionar ação de script**, já que este tutorial não requer uma configuração de cluster personalizado.
	
	![Configurar a Ação de Script para personalizar um cluster HBase do HDInsight][img-provision-cluster-page5]

	> [AZURE.NOTE]Esta página pode ser usada para personalizar o cluster durante a instalação. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script](hdinsight-hadoop-customize-cluster.md).
	
Para começar a trabalhar com o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do HBase com Hadoop no HDInsight](../hdinsight-hbase-get-started.md).

##Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase

1.	Provisione uma máquina virtual IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. De maneira que a máquina virtual e o cluster do HBase usem o mesmo servidor DNS interno para resolver nomes de host. Para fazer isso, você deve escolher a opção **Da galeria** e selecionar a rede virtual em vez de um datacenter. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server](../virtual-machines-windows-tutorial.md). Uma imagem padrão do Windows Server 2012 com uma VM pequena é suficiente.
	
2.	Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determiná-lo, é preciso obter o sufixo DNS específico da conexão do cluster do HBase. Para fazer isso, use o Curl para consultar o Ambari, ou use a área de trabalho remota para conectar-se ao cluster.

	* **Curl** - use o seguinte comando:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nos dados JSON (notação de objeto JavaScript) retornados, localize a entrada "host_name". Ela conterá o FQDN (nome de domínio totalmente qualificado) para os nós no cluster. Por exemplo:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		A parte do nome do domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.

	* **PowerShell do Azure** - use o seguinte script do PowerShell do Azure para registrar a função **Get-ClusterDetail**, que pode ser usada para retornar o sufixo DNS:

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS 
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
			#>
			
				$DnsSuffix = ".azurehdinsight.net"
				
				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
			
				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
					
					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

		Após executar o script do PowerShell do Azure, use o seguinte comando para retornar o sufixo DNS usando a função **Get-ClusterDetail**. Especifique o nome do cluster do HBase do HDInsight, o nome e a senha do administrador ao usar esse comando.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Isso retornará o sufixo DNS. Por exemplo, **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Você também pode usar a área de trabalho remota para conectar o cluster do HBase (você estará conectado ao nó principal) e executar **ipconfig** por meio de um prompt de comando para obter o sufixo DNS. Para obter instruções de como habilitar o RDP (protocolo RDP) e conectar-se ao cluster usando RDP, consulte [Gerenciar clusters do Hadoop no HDInsight usando o Portal do Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster. 

	To make the configuration change:

	1. RDP into the virtual machine. 
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**. 
	- Set **Primary DNS Suffix** to the value obtained in step 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**. 
	5. Reboot the virtual machine.
-->

Para verificar se a máquina virtual pode se comunicar com o cluster do HBase, use o seguinte comando `ping headnode0.<dns suffix>` por meio da máquina virtual. Por exemplo, envie ping a headnode0.mycluster.b1.cloudapp.net.

Para usar essa informação em um aplicativo Java, você pode seguir as etapas em [Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)](hdinsight-hbase-build-java-maven.md) para criar um aplicativo. Para que o aplicativo se conecte a um servidor HBase remoto, modifique o arquivo **hbase-site.xml** nesse exemplo para usar o FQDN para ZooKeeper. Por exemplo:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Para obter mais informações sobre a resolução do nome em Redes Virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [Resolução do Nome (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##Provisionar um cluster do HBase usando o PowerShell do Azure

**Para provisionar um cluster do HBase usando o PowerShell do Azure**

1. Abra o ISE (Ambiente de Scripting Integrado) do PowerShell do Azure.
2. Cope e cole o seguinte no painel de script:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Clique em **Executar Script** ou pressione **F5**.
4. Para validar o cluster, você pode verificar o cluster no portal do Azure ou executar o seguinte cmdlet do PowerShell do Azure no painel inferior:

	Get-AzureHDInsightCluster

##Próximas etapas

Neste tutorial, você aprendeu como provisionar um cluster do HBase. Para obter mais informações, consulte:

- [Introdução ao HDInsight](../hdinsight-get-started.md)
- [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-geo-replication.md) 
- [Provisionar clusters do Hadoop no HDInsight](hdinsight-provision-clusters.md) 
- [Introdução ao uso do HBase com Hadoop no HDInsight](../hdinsight-hbase-get-started.md)
- [Analisar dados de sentimento no Twitter com o HBase no HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Visão geral da Rede Virtual][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalhes de provisionamento do novo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usar a Ação de Script para personalizar um cluster HBase"
 

<!-----HONumber=July15_HO5-->