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
	ms.date="08/12/2015"
	ms.author="jgao"/>

# Provisionar clusters do HBase na Rede Virtual do Azure

Saiba como criar clusters do HBase do HDInsight em uma [Rede Virtual do Azure][1].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Provisionar os clusters do HBase na rede Virtual do Azure](hdinsight-hbase-provision-vnet-v1.md)

Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

- Conectividade direta do aplicativo Web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC (chamada de procedimento remoto) Java do HBase.
- Desempenho aprimorado, evitando que o tráfego percorra diversos gateways e balanceadores de carga.
- Capacidade de processar informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público.

##Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure](../install-configure-powershell.md). Para executar scripts do PowerShell do Azure, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Usando o cmdlet Set-ExecutionPolicy][2].

	Antes de executar scripts do PowerShell do Azure, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>


## Provisionar um cluster do HBase em uma rede virtual

Aplicativos normalmente são compostos de vários componentes. Neste tutorial, você terá:

- uma rede virtual do Azure
- uma conta de armazenamento do Azure
- um Cluster do Azure HDInsight HBase
- (opcional) uma máquina virtual do Azure, servindo como um servidor DNS

O Gerenciador de Recursos do Azure permite trabalhar com os recursos do seu aplicativo como um grupo. Você pode implantar, atualizar ou excluir todos os recursos para seu aplicativo em uma única operação coordenada. Usar um modelo para a implantação e esse modelo pode ser útil para ambientes diferentes, como teste, preparação e produção. Você pode esclarecer a cobrança para sua organização exibindo os custos acumulados para todo o grupo.

**Para criar um grupo de recursos**

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Gerenciamento**, e, em seguida, clique em **Grupo de recursos**.
3. Digite ou selecione os valores a seguir:

	- **Nome do grupo de recursos**: insira um nome para o grupo de recursos.
	- **Assinatura**: selecione a assinatura do Azure usada para este grupo de recursos.
	- **Local do grupo de recursos**: selecione um data center do Azure. Esse local não precisa corresponder ao local do cluster HDInsight.

4. Clique em **Criar**.

Antes de provisionar um cluster HBase, você precisa ter uma rede virtual do Azure.

**Para criar uma Rede Virtual usando o portal do Azure**

1. Entre no [portal de visualização](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Rede**, e, em seguida, clique em **Rede Virtual**.
3. Em **Selecionar um modelo de implantação**, selecione **Clássico**, e, em seguida, clique em **Criar**.

	>[AZURE.NOTE]O cluster HDInsight baseado em Windows só pode ser implantado em uma rede virtual clássica.

4. Digite ou selecione os valores a seguir:

	- **Nome**: o nome da sua rede virtual.
	- **Espaço de endereço**: escolha um espaço de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará. Para este tutorial você pode usar os valores padrões. Clique em **OK** para salvar as alterações.
	- **Grupo de recursos**: selecione o grupo de recursos que você criou anteriormente no tutorial.
	- **Assinatura**: selecione a assinatura do Azure que você deseja usar para essa rede virtual.
	- **Local** - o local deve ser o mesmo que o do cluster do HBase que você criará.

5. Clique em **Criar**.

Por padrão, a rede virtual usa um servidor interno do Sistema de Nome de Domínio (DNS) fornecido pelo Azure. Configurações de rede mais avançadas com servidores DNS personalizados também têm suporte. Para obter orientações detalhadas, consulte [Resolução de Nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**(Opcional) Para adicionar uma máquina virtual do servidor DNS à rede virtual**

Um servidor DNS é opcional, mas necessário em alguns casos. O procedimento foi documentado em [Configurar DNS entre duas redes virtuais do Azure][hdinsight-hbase-replication-dns]. Basicamente, você precisará executar estas etapas:

1. adicionar uma máquina virtual do Azure à rede virtual
2. definir um endereço IP estático para a máquina virtual
3. adicionar a função de servidor DNS à máquina virtual
4. Atribuir o servidor DNS à rede virtual 

**Para provisionar um cluster HBase usando o Portal do Azure**

> [AZURE.NOTE]Para obter informações sobre como provisionar um novo cluster do HBase usando o PowerShell do Azure, consulte [Provisionar um cluster HBase usando o PowerShell do Azure](#powershell).


**Para criar um cluster HDInsight**

1. Entre no [Portal de Visualização do Azure](https://portal.azure.com).
2. Clique em **NOVO**, clique em **Análises de dados**, e, em seguida, clique em **HDInsight**.

    ![Criar um novo cluster no Portal de Visualização do Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Criar um novo cluster no Portal de Visualização do Azure")

3. Digite ou selecione os valores a seguir:

  - **Nome do cluster**: insira um nome para o cluster. Uma marca de seleção verde aparecerá ao lado do nome do cluster, se o nome estiver disponível.
  - **Tipo de cluster**: selecione **HBase**
  - **Sistema operacional do cluster**: selecione **Windows Server 2012 R2 Datacenter**.
  - **Assinatura**: selecione a assinatura do Azure que será usada para provisionar esse cluster.
  - **Grupo de recursos**: selecione o grupo de recursos que você criou anteriormente no tutorial.
  - **Credenciais**: configurar o nome de usuário e a senha do usuário do Hadoop (usuário HTTP). Se você habilitar a área de trabalho remota para o cluster, você precisará configurar uma data de vencimento de conta e senha e o nome de usuário do usuário de área de trabalho remota. Clique em **Selecionar** na parte inferior para salvar as alterações.
  - **Fonte de dados**: selecione uma existente ou crie uma nova conta de armazenamento do Azure que será usada como o sistema de arquivos padrão para o cluster. O nome padrão para o contêiner padrão é o nome do cluster. O local da conta de armazenamento também determina o local do cluster.
  - **Camada de preços de nó**: para fins de aprendizado ou avaliação, selecione 1 nó de região para minimizar o custo.

  	- **Método de seleção**: defina essa opção para **De todas as assinaturas** para habilitar a procura de contas de armazenamento de todas as suas assinaturas. Defina essa opção para **Tecla de Acesso** se você deseja inserir o **Nome de armazenamento** e **Tecla de Acesso** de uma conta de armazenamento existente.
  	- **Selecionar conta de armazenamento / Criar nova**: clique em **Selecionar conta de armazenamento** para procurar e selecionar uma conta de armazenamento existente que você deseja associar com o cluster. Ou, clique em **Criar nova** para criar uma nova conta de armazenamento. Use o campo exibido para inserir o nome da conta de armazenamento. Uma marca de seleção verde será exibida se o nome estiver disponível.
    - **Escolher contêiner padrão**: use essa opção para inserir o nome do contêiner padrão a ser usado para o cluster. Embora você possa inserir qualquer nome aqui, é recomendável usar o mesmo nome que o cluster para que você possa reconhecer facilmente que o contêiner é usado para este cluster específico.
  	- **Local**: a região geográfica na qual a conta de armazenamento está ou será criada. Esse local determinará o local do cluster. O cluster e sua conta de armazenamento padrão devem estar localizados no mesmo data center do Azure.

  - **Camadas de preços do nó**: definir o número de nós de trabalho que você precisa para o cluster. O custo estimado do cluster será mostrado na folha.
	- **Configuração opcional**: para este tutorial, você só precisa configurar a **Rede Virtual**. Selecione a rede virtual que você criou anteriormente no tutorial. Verifique se você também selecionou uma sub-rede.

4. Clique em **Criar**.


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
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


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

<!---HONumber=August15_HO9-->