<properties
   pageTitle="Estender HDInsight com a Rede Virtual do Azure | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Saiba como usar a Rede Virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos no seu datacenter"
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


#Estender os recursos do HDInsight usando a Rede Virtual do Azure

A Rede Virtual do Azure permite que você estenda suas soluções Hadoop para incorporar recursos locais como o SQL Server ou para criar redes privadas seguras entre recursos na nuvem.

> [AZURE.NOTE] Atualmente o HDInsight não oferece suporte à Redes Virtuais do Azure baseadas em afinidade. Ao usar o HDInsight, você deve usar redes virtuais baseadas em local.

##<a id="whatis"></a>O que é a Rede Virtual do Azure?

[A Rede Virtual do Azure](/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	Usar a Rede Virtual para vincular serviços do Azure com o HDInsight permite os cenários a seguir.

	* **Invocando serviços ou trabalhos HDInsight** de sites do Azure ou serviços em execução em Máquinas Virtuais do Azure.

	* **Transferência de dados diretamente** entre HDInsight e Banco de Dados SQL ou SQL Server ou outra solução de armazenamento de dados em execução em uma máquina virtual.

	* **Combinando vários servidores HDInsight** em uma única solução. Como usar um servidor do HDInsight Storm para consumir dados de entrada e armazenar os dados processados em um servidor HDInsight HBase. Os dados brutos também podem ser armazenados em um servidor HDInsight Hadoop para análise futura usando MapReduce.

* Conecte seus recursos de nuvem à sua rede do datacenter local (site a site ou ponto a site) usando uma Rede Virtual Privada (VPN)

	A configuração site a site permite que você conecte vários recursos de seu datacenter à Rede Virtual do Azure usando uma VPN de hardware ou o Serviço de Roteamento e Acesso Remoto

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	A configuração ponto a site permite que você conecte um recurso específico à Rede Virtual do Azure usando a VPN do software

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	Usando a Rede Virtual para vincular a nuvem e seus dados centers permite cenários semelhantes à configuração somente em nuvem, mas em vez de estar limitado a trabalhar com recursos na nuvem, você também pode trabalhar com os recursos em seu data center.

	* **Transferência de dados diretamente** entre HDInsight e seu data center. Por exemplo, usando o Sqoop para transferir dados para ou do SQL Server ou a leitura de dados gerados por um aplicativo de linha de negócios (LOB).

	* **Invocando serviços ou trabalhos HDInsight** de um aplicativo de LOB. Por exemplo, usando APIs do HBase Java para armazenar e recuperar dados de um cluster HDInsight HBase.

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Você deve criar a Rede Virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> O Azure HDInsight oferece suporte somente a redes virtuais baseadas no local, e não trabalha atualmente com redes virtuais baseadas em grupos de afinidade.
>
> É altamente recomendável designar uma única subrede para cada cluster.

Para obter mais informações sobre como provisionar um cluster HDInsight em uma Rede Virtual, consulte [Provisionando clusters Hadoop no HDInsight](hdinsight-provision-clusters.md).

##<a id="tasks"></a>Tarefas e informações

Esta seção contém informações sobre tarefas comuns e informações que talvez sejam necessárias ao usar HDInsight com uma Rede Virtual.

###Determinar o nome de domínio totalmente qualificado

O cluster HDInsight será atribuído um FQDN específico da interface da Rede Virtual. Este é o endereço que você deve usar ao se conectar ao cluster de outros recursos na Rede Virtual. Para determinar o FQDN, use a seguinte URL para consultar o serviço de gerenciamento do Ambari.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Para obter mais informações sobre como usar o Ambari com HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Você deve especificar o nome do cluster e um serviço e componente em execução no cluster, como o gerenciador de recursos YARN.

> [AZURE.NOTE] Os dados retornados são um documento JSON que contêm muitas informações sobre o componente. Para extrair apenas o FQDN, você deve usar um JSON parser para recuperar o valor  `host_components[0].HostRoles.host_name`.

Por exemplo, para retornar o FQDN de um cluster HDInsight Hadoop, você pode usar um dos métodos a seguir para recuperar os dados para o gerenciador de recursos YARN.

* [PowerShell do Azure](install-configure-powershell.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix
		
		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
		
		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [Curl](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Conectando-se ao HBase

Para se conectar ao HBase remotamente usando a API do Java, você deve determinar os endereços de quorum Zookeeper para o cluster HBase e especificar isso no seu aplicativo.

Para obter o endereço de quorum do zookeeper, use um dos seguintes métodos para consultar o serviço de gerenciamento do Ambari.

* [PowerShell do Azure](install-configure-powershell.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [Curl](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Para obter mais informações sobre como usar o Ambari com HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Uma vez que as informações estejam reunidas, use-as em seu aplicativo cliente.

Por exemplo, um aplicativo Java que usa a API do HBase, você adicionaria um arquivo **hbase-site.xml** para o projeto e especificaria as informações de quorum no arquivo da seguinte maneira.

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

###Verificar a conectividade de rede

Alguns serviços, como o SQL Server, podem limitar conexões de rede. Isso impedirá que HDInsight trabalhe com êxito com esses serviços.

Se você encontrar problemas ao acessar um serviço do HDInsight, consulte a documentação para o serviço para garantir que você habilitou o acesso à rede. Você também pode verificar o acesso à rede, criando uma máquina Virtual do Azure na mesma Rede Virtual e usar utilitários de cliente para verificar se a VM pode se conectar ao serviço através da Rede Virtual.

##<a id="nextsteps"></a>Próximas etapas

Os exemplos a seguir demonstram como usar HDInsight com Rede Virtual do Azure.

* [Analisar dados de sensor com Storm e HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md) - Demonstra como configurar um cluster Storm e HBase em uma Rede Virtual, bem como gravar dados remotamente para o HBase pelo Storm.

* [Provisionar os clusters HBase na Rede Virtual Azure](hdinsight-hbase-provision-vnet.md) - Fornece informações sobre como provisionar um cluster HBase em uma Rede Virtual do Azure

* [Provisionar clusters de Hadoop no HDInsight](hdinsight-provision-clusters.md) - Fornece informações sobre o provisionamento de clusters Hadoop, incluindo informações sobre como usar a Rede Virtual do Azure

* [Usar o Sqoop com Hadoop no HDInsight](hdinsight-use-sqoop.md) - Fornece informações sobre como usar o Sqoop para transferir dados com o SQL Server por uma Rede Virtual

Para saber mais sobre Redes Virtuais do Azure, consulte [Visão geral da Rede Virtual do Azure](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=47-->
