<properties 
	pageTitle="Provisionar clusters do HBase na Rede Virtual do Azure | Azure" 
	description="Saiba como criar clusters do HDInsight na Rede Virtual do Azure." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Provisionar clusters do HBase na Rede Virtual do Azure

Saiba como criar clusters do HDInsight na [rede Virtual do Azure][1]. 

Com a integração da rede virtual, os clusters do HBase podem ser implantados na mesma rede virtual que seus aplicativos, de modo que os aplicativos possam se comunicar diretamente com o HBase. Os benefícios incluem:

- Conectividade direta do aplicativo da web com os nós do cluster do HBase, que permite a comunicação usando APIs RPC Java do HBase.
- Aprimore o desempenho evitando que o tráfego percorra diversos gateways e balanceadores de carga. 
- Processe informações confidenciais de maneira mais segura, sem expor um ponto de extremidade público


##Neste artigo

- [Pré-requisitos](#prerequisites)
- [Provisionar clusters do HBase em uma rede virtual](#hbaseprovision)
- [Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase](#connect)
- [Provisionar um cluster do HBase usando o PowerShell](#powershell)
- [Próximas etapas](#nextsteps)

##<a id="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. O Azure é uma plataforma baseada em assinatura. Para obter mais informações sobre como adquirir uma assinatura, consulte [Opções de compra][azure-purchase-options], [Ofertas para membros][azure-member-offers] ou [Avaliação gratuita][azure-free-trial].

- **Uma estação de trabalho com o PowerShell do Azure instalado e configurado**. Para obter instruções, consulte [Instalar e configurar o PowerShell do Azure][powershell-install]. Para executar scripts do PowerShell, você deve executar o PowerShell do Azure como administrador e configurar a política de execução como *RemoteSigned*. Consulte [Usando o cmdlet Set-ExecutionPolicy][2].

	Antes de executar scripts do PowerShell, verifique se você está conectado à sua assinatura do Azure usando o seguinte cmdlet:

		Add-AzureAccount

	Se você tiver várias assinaturas do Azure, use o seguinte cmdlet para definir a assinatura atual:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Provisionar um cluster do HBase em uma rede virtual. 

**Para criar uma Rede Virtual usando o portal de gerenciamento:**

1. Entre no [Portal de Gerenciamento do Azure][azure-portal].
2. Clique em **NOVO** no canto inferior esquerdo, clique em **SERVIÇOS DE REDE**, clique em **REDE VIRTUAL** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.
3. Digite ou selecione os valores a seguir:

	- **Nome**: O nome da sua rede virtual.
	- **Espaço de endereço**: Escolha um nome de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará. Para percorrer este tutorial, você pode escolher qualquer uma das três opções. 
	- **Contagem máxima de VMs**: Escolha uma das contagens máximas de VMs. Esse valor determina o número de possíveis hosts (VMs) que podem ser criadas sob o espaço de endereço. Para percorrer este tutorial, **4096 [CIDR: /20]** é suficiente. 
	- **Local**: O local deve ser o mesmo que o do cluster do HBase que você criará.
	- **Servidor DNS**: Este artigo usa o servidor DNS interno fornecido pelo Azure, então, você pode selecionar **Nenhum**. Configurações de rede mais avançadas com servidores DNS personalizados também têm suporte. Para obter orientações detalhadas, consulte [resolução de nomes (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Clique em **CRIAR UMA REDE VIRTUAL**. O nome da nova rede virtual aparecerá na lista. Aguarde até que a coluna de Status exiba **Criado**.
5. No painel principal, clique na rede virtual que você acabou de criar.
6. Clique em **PAINEL** na parte superior da página.
7. Em **visão rápida**, anote a **ID DA REDE VIRTUAL**. Você precisará dela para provisionar o cluster do HBase.
8. Clique em **CONFIGURAR** na parte superior da página.
9. No final da página, o nome da sub-rede padrão é **Subnet-1**. Você também pode renomear a sub-rede ou adicionar uma nova sub-rede ao cluster do HBase. Anote o nome da sub-rede, você precisará dele para provisionar o cluster
10. Verifique o **CIDR (CONTAGEM DE ENDEREÇOS)** para a sub-rede que será usada para o cluster. A contagem de endereços deve ser maior que o número de nós de trabalho mais sete (Gateway: 2, Headnode: 2, Zookeeper: 3). Por exemplo, se você precisa de um cluster do HBase de 10 nós, a contagem de endereços da sub-rede deve ser maior que 17 (10+7). Caso contrário, a implantação falhará.

	> [AZURE.NOTE] É altamente recomendado designar uma única sub-rede para um cluster. 

11. Clique em **Salvar**, no final da página, se tiver atualizado os valores de sub-rede.



> [AZURE.NOTE] Os clusters do HDInsight usam o armazenamento de blob do Azure para armazenar dados. Para obter mais informações, consulte [Usar o armazenamento de blob do Azure com o Hadoop no HDInsight][hdinsight-storage]. Você precisará de uma conta de armazenamento e um contêiner de armazenamento de blob. O local da conta de armazenamento deve corresponder ao local da rede virtual e ao local do cluster.

**Para criar uma conta de Armazenamento do Azure e um contêiner de armazenamento Blob:**

1. Entre no [Portal de Gerenciamento do Azure][azure-portal].
2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **ARMAZENAMENTO** e, em seguida, clique em **CRIAÇÃO RÁPIDA**.

3. Digite ou selecione os valores a seguir:

	- **URL**: O nome da conta de armazenamento
	- **LOCAL**: O local da conta de armazenamento. Certifique-se de que ele corresponda ao local da rede virtual. Não há suporte para grupos de afinidade.
	- **REPLICAÇÃO**: Para fins de teste, use **Localmente Redundante** para reduzir o custo.

4. Clique em **CRIAR CONTA DE ARMAZENAMENTO**.  Você verá a nova conta de armazenamento na lista de armazenamento. 
5. Aguarde até que o **STATUS** da nova conta de armazenamento seja alterado para **Online**.
6. Clique na nova conta de armazenamento da lista para selecioná-la.
7. Clique em **GERENCIAR CHAVES DE ACESSO** na parte inferior da página.
8. Tome nota do **NOME DA CONTA DE ARMAZENAMENTO** e da **CHAVE DE ACESSO PRIMÁRIA** (ou a **CHAVE DE ACESSO SECUNDÁRIA**.  As duas chaves funcionam).  Você precisará deles mais tarde no tutorial.
9. Na parte superior da página, clique em **CONTÊINER**.
10. No final da página, clique em **ADICIONAR**.
11. Insira o nome do contêiner.  Este contêiner será usado como contêiner padrão para este cluster do HBase. Por padrão, o nome do contêiner padrão corresponde ao nome do cluster. Mantenha o campo **ACESSO** como **Privado**.  
12. Clique no ícone de seleção para criar o contêiner.

**Para provisionar um cluster HBase usando o Portal do Azure:**

> [AZURE.NOTE] Para obter informações sobre como provisionar um novo cluster do HBase usando o PowerShell, consulte [Provisionar um cluster do HBase usando o PowerShell](#powershell).

1. Entre no [Portal de Gerenciamento do Azure][azure-portal].

2. Clique em **NOVO** no canto inferior esquerdo, aponte para **SERVIÇOS DE DADOS**, aponte para **HDINSIGHT** e, em seguida, clique em **CRIAÇÃO PERSONALIZADA**.

3. Insira um NOME DE CLUSTER, selecione o TIPO DE CLUSTER como HBase e selecione a versão HDINSIGHT.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Clique no botão direito.

4. Digite ou selecione o seguinte:

	- **NÓS DE DADOS**: Insira o número de nós de dados para o cluster HBase. 
	- **REGIÃO/REDE VIRTUAL**: Selecione a rede virtual do Azure que você criou.
	- **SUB-REDES DE REDE VIRTUAL**: Selecione uma sub-rede. O nome padrão é **Sub-rede-1**.

	Clique no botão direito.

5. Insira o **NOME DE USUÁRIO** e **SENHA** do usuário do Hadoop para este cluster e, em seguida, clique no botão à direita.
6. Selecione se deseja usar uma nova conta de armazenamento ou uma conta de armazenamento existente como conta de armazenamento padrão para o cluster e, em seguida, clique no botão à direita.

7. Na página **Ações de Script**, clique em **adicionar ação de script** para fornecer detalhes sobre o script personalizado que você deseja executar para personalizar um cluster, enquanto o cluster está sendo criado. Por exemplo, você pode usar a Ação de Script para personalizar um cluster para instalar o <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>. Para obter mais informações, consulte [Personalizar clusters HDInsight usando a Ação de Script][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Propriedade</th><th>Valor</th></tr>
		<tr><td>Nome</td>
			<td>Especifique um nome para a ação de script.</td></tr>
		<tr><td>URI do script</td>
			<td>Especifique o URI para o script que é chamado para personalizar o cluster.</td></tr>
		<tr><td>Tipo de nó</td>
			<td>Especifica os nós em que o script de personalização é executado. Você pode escolher <b>Todos os nós</b>, <b>Somente nós headnode</b> ou <b>Somente nós de trabalho</b>.
		<tr><td>Parâmetros</td>
			<td>Especifique os parâmetros, se exigido pelo script.</td></tr>
	</table>

	Você pode adicionar mais de uma ação de script para instalar vários componentes no cluster. Depois de adicionar os scripts, clique na marca de seleção para iniciar o provisionamento do cluster.


Para começar a trabalhar com o novo cluster do HBase, você pode usar os procedimentos encontrados em [Introdução ao uso do HBase com Hadoop no HDInsight][hbase-get-started].

##<a id="connect"></a>Conectar-se ao cluster do HBase provisionado em uma rede virtual usando as APIs RPC Java do HBase

1.	Provisione uma máquina virtual IaaS na mesma rede virtual do Azure e na mesma sub-rede. De maneira que a máquina virtual e o cluster do HBase usem o mesmo servidor DNS interno para resolver nomes de host. Para fazer isso, você deve escolher a opção Da galeria e selecionar a rede virtual em vez de um datacenter. Para obter as instruções, consulte [Criar uma máquina virtual executando o Windows Server][vm-create]. Uma imagem padrão do Windows Server 2012 com uma VM pequena é suficiente.
	
2.	Ao usar um aplicativo Java para se conectar ao HBase remotamente, você deve usar o nome de domínio totalmente qualificado (FQDN). Para determiná-lo, é preciso obter o sufixo DNS específico da conexão do cluster do HBase. Para fazer isso, use o Curl para consultar o Ambari ou a área de trabalho remota para se conectar ao cluster.

	* **Curl** - use o seguinte comando:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Nos dados JSON retornados, localize a entrada "host_name". Ela conterá o nome de domínio totalmente qualificado (FQDN) para os nós no cluster. Por exemplo:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		A parte do nome do domínio que começa com o nome do cluster é o sufixo DNS. Por exemplo, mycluster.b1.cloudapp.net.

	* **PowerShell** - use o seguinte script do PowerShell para registrar a função **Get-ClusterDetail**, que pode ser usada para retornar o sufixo DNS.

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
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
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

		Após executar o script do PowerShell, use o seguinte comando para retornar o sufixo DNS usando a função Get-ClusterDetail. Especifique o nome do cluster do HBase do HDInsight, o nome e a senha do administrador ao usar esse comando.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Isso retornará o sufixo DNS. Por exemplo, **nome_do_seu_cluster.b4.internal.cloudapp.net**.

	> [AZURE.NOTE] Você também pode usar a área de trabalho remota para conectar o cluster do HBase (você estará conectado ao nó de cabeça) e executar **ipconfig** por meio de um prompt de comando para obter o sufixo DNS. Para obter instruções de como habilitar o RDP e se conectar ao cluster usando RDP, consulte [Gerenciar clusters do Hadoop no HDInsight usando o Portal de Gerenciamento do Azure][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Altere a configuração de sufixo DNS primário da máquina virtual. Isto permite que a máquina virtual resolva automaticamente o nome de host do cluster HBase sem a especificação explícita do sufixo. Por exemplo, o nome de host *workernode0* será resolvido corretamente no workernode0 do cluster do HBase. 

	Para alterar a configuração:

	1. Realize um RDP na máquina virtual. 
	2. Abra o **Editor de Políticas de Grupo Local**. O executável é gpedit.msc.
	3. Expanda a **Configuração do Computador**, expanda os **Modelos Administrativos**, expanda **Rede** e clique em **Cliente DNS**. 
	- Defina **Sufixo DNS Primário** como o valor obtido na etapa 2: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Clique em **OK**. 
	5. Reinicie a máquina virtual.
-->

Para verificar se a máquina virtual pode se comunicar com o cluster do HBase, use o seguinte comando `ping headnode0.<dns suffix>` por meio da máquina virtual. Por exemplo, ping headnode0.mycluster.b1.cloudapp.net

Para usar essa informação em um aplicativo Java, você pode seguir as etapas em [Utilizar o Maven para criar aplicativos Java que usam o HBase com o HDInsight (Hadoop)](azure.microsoft.com/pt-br/documentation/articles/hdinsight-hbase-build-java-maven/) para criar um aplicativo. Para que o aplicativo se conecte a um servidor HBase remoto, modifique o arquivo **hbase-site.xml** nesse exemplo para usar o FQDN para ZooKeeper. Por exemplo:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Para obter mais informações sobre a resolução do nome em Redes Virtuais do Azure, incluindo como usar seu próprio servidor DNS, consulte [Resolução do Nome (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##<a id="powershell"></a>Provisionar um cluster do HBase usando o PowerShell do Azure

**Para provisionar um cluster do HBase usando o PowerShell do Azure**

1. Abra o ISE do PowerShell.
2. Cope e cole o seguinte no painel de script.

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
4. Para validar o cluster, você pode verificar o cluster no portal de gerenciamento ou executar o seguinte cmdlet do PowerShell no painel inferior:

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Próximas etapas

Neste tutorial, aprendemos como provisionar um cluster do HBase. Para obter mais informações, consulte:

- [Introdução ao HDInsight][hdinsight-get-started]
- [Provisionar clusters do Hadoop no HDInsight][hdinsight-provision] 
- [Introdução ao uso do HBase com o Hadoop no HDInsight][hbase-get-started]
- [Analisar dados de sentimento no Twitter com o HBase no HDInsight][hbase-twitter-sentiment]
- [Visão geral da Rede Virtual][vnet-overview].


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"
<!--HONumber=42-->
