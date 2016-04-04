<properties
	pageTitle="Provisionar clusters do HBase em uma Rede Virtual | Microsoft Azure"
	description="Introdução ao uso do HBase no Azure HDInsight. Saiba como criar clusters HBase do HDInsight na rede virtual do Azure."
	keywords=""
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
   ms.date="02/17/2016"
   ms.author="jgao"/>

# Criar clusters do HBase na Rede Virtual do Azure 

Saiba como criar clusters do HBase do HDInsight em uma [Rede Virtual do Azure][1].

Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

- Conectividade direta do aplicativo Web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC (chamada de procedimento remoto) Java do HBase.
- Desempenho aprimorado, evitando que o tráfego percorra diversos gateways e balanceadores de carga.
- Capacidade de processar informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público.

###Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o PowerShell do Azure**. Consulte [Instalar e usar o PowerShell do Azure](https://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Criar clusters do HBase na rede virtual

Nesta seção, você criará um cluster HBase baseado em Linux no HDInsight usando o [modelo ARM Azure](../resource-group-template-deploy.md). Não é necessário ter experiência com o modelo ARM do Azure para seguir este tutorial. Para outros métodos de criação de cluster e noções básicas sobre as configurações, confira [Criar clusters do HDInsight](hdinsight-hadoop-provision-linux-clusters.md). Para saber mais sobre como usar o modelo ARM para criar clusters Hadoop no HDInsight, confira [Criar clusters Hadoop no HDInsight usando modelos de ARM](hdinsight-hadoop-create-windows-clusters-arm-templates.md)

1. Clique na imagem a seguir para abrir um modelo ARM no Portal do Azure. O modelo ARM está localizado em um contêiner de blob público. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/pt-BR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Na folha **Parâmetros**, insira o seguinte:

    - **ClusterName**: insira um nome para o cluster Hadoop que você criará.
    - **Nome e senha de logon do cluster**: o nome de logon padrão é **admin**.
    - **Nome de usuário e senha SSH**: o nome de usuário padrão é **sshuser**. Você pode renomeá-lo. 

    Muitas propriedades foram embutidos no código do modelo. Por exemplo:
    
    - Local: Leste dos EUA
    - Contagem de nós de trabalho do cluster: 4
    - Conta de armazenamento padrão: <Cluster Name>repositório
    - Nome da rede virtual: <Cluster Name>-vnet
    - Espaço de endereço da rede virtual: 10.0.0.0/16
    - Nome da sub-rede: padrão
    - Intervalo de endereço da sub-rede: 10.0.0.0/24

3. Clique em **OK** para salvar os parâmetros.
4. Na folha **Implantação personalizada**, clique na caixa suspensa **Grupo de recursos** e clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contêiner que agrupa o cluster, a conta de armazenamento dependente e outros recursos vinculados.
5. Clique em **Termos legais** e em **Criar**.
6. Clique em **Criar**. Você verá um novo bloco intitulado **Como enviar a implantação para a implantação do modelo**. A criação de um cluster demora cerca de 20 minutos. Após a criação do cluster, você pode clicar na folha do cluster no portal para abri-la.

Depois de concluir o tutorial, talvez você queira excluir o cluster. Com o HDInsight, seus dados são armazenados no Armazenamento do Azure, assim você poderá excluir, com segurança, um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Para obter instruções sobre como excluir um cluster, confira [Gerenciar clusters Hadoop no HDInsight usando o Portal do Azure](hdinsight-administer-use-management-portal.md#delete-clusters).

Para começar a trabalhar com o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do HBase com Hadoop no HDInsight](hdinsight-hbase-tutorial-get-started.md).

##Conectar-se ao cluster HBase usando as APIs de RPC HBase Java

1.	Crie uma máquina virtual IaaS (infraestrutura como serviço) na mesma rede virtual do Azure e na mesma sub-rede. De maneira que a máquina virtual e o cluster do HBase usem o mesmo servidor DNS interno para resolver nomes de host. Para fazer isso, você deve escolher a opção **Da galeria** e selecionar a rede virtual em vez de um datacenter. Para obter instruções, consulte [Criar uma máquina virtual que executa o Windows Server](../virtual-machines/virtual-machines-windows-hero-tutorial.md). Uma imagem padrão do Windows Server 2012 com uma VM pequena é suficiente.

2.	Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determiná-lo, é preciso obter o sufixo DNS específico da conexão do cluster do HBase. Para fazer isso, use o Curl para consultar o Ambari, ou use a área de trabalho remota para conectar-se ao cluster.

	* **Curl** - use o seguinte comando:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nos dados JSON (notação de objeto JavaScript) retornados, localize a entrada "host\_name". Ela conterá o FQDN (nome de domínio totalmente qualificado) para os nós no cluster. Por exemplo:

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

	> [AZURE.NOTE] Você também pode usar a área de trabalho remota para conectar o cluster do HBase (você estará conectado ao nó principal) e executar **ipconfig** por meio de um prompt de comando para obter o sufixo DNS. Para obter instruções de como habilitar o RDP (protocolo RDP) e conectar-se ao cluster usando RDP, consulte [Gerenciar clusters do Hadoop no HDInsight usando o Portal do Azure][hdinsight-admin-portal].
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

> [AZURE.NOTE] Para obter mais informações sobre a resolução do nome em Redes Virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [Resolução do Nome (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Próximas etapas

Neste tutorial, você aprendeu a criar um cluster do HBase. Para obter mais informações, consulte:

- [Introdução ao HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Configurar a replicação do HBase no HDInsight](hdinsight-hbase-geo-replication.md)
- [Criar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md)
- [Introdução ao uso do HBase com Hadoop no HDInsight](hdinsight-hbase-tutorial-get-started.md)
- [Analisar dados de sentimento no Twitter com o HBase no HDInsight](hdinsight-hbase-analyze-twitter-sentiment.md)
- [Visão geral da Rede Virtual][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines/virtual-machines-windows-hero-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: powershell-install-configure.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-hadoop-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Detalhes de provisionamento do novo cluster HBase"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Usar a Ação de Script para personalizar um cluster HBase"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0323_2016-->