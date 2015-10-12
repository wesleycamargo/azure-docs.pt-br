<properties
	pageTitle="Estender o HDInsight com a Rede Virtual | Microsoft Azure"  
	description="Saiba como usar a Rede Virtual do Azure para conectar o HDInsight a outros recursos de nuvem ou recursos no seu datacenter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>


#Estender os recursos do HDInsight usando a Rede Virtual do Azure

A Rede Virtual do Azure permite que você estenda suas soluções Hadoop para incorporar recursos locais, como SQL Server, ou para criar redes privadas seguras entre recursos na nuvem.

> [AZURE.NOTE]O HDInsight não é compatível com redes virtuais do Azure baseadas em afinidade. Ao usar o HDInsight, você deve usar redes virtuais baseadas em local.
>
> No momento (25/08/2015), você só poderá provisionar um cluster HDInsight baseado em Linux em uma Rede Virtual do Azure.

##<a id="whatis"></a>O que é a Rede Virtual do Azure?

A [Rede Virtual do Azure](/documentation/services/virtual-network/) permite que você crie uma rede segura e persistente contendo os recursos necessários para sua solução. Uma rede virtual permite que você:

* Conecte recursos da nuvem juntos em uma rede privada (somente nuvem).

	![diagrama da configuração somente nuvem](media/hdinsight-extend-hadoop-virtual-network/cloud-only.png)

	Usar a Rede Virtual para vincular os serviços do Azure ao Azure HDInsight habilita os seguintes cenários:

	* **Invocar serviços ou trabalhos do HDInsight** de sites do Azure ou de serviços em execução em máquinas virtuais do Azure.

	* **Transferência direta de dados** entre o HDInsight e o Banco de Dados SQL do Azure, o SQL Server ou outra solução de armazenamento de dados em execução em uma máquina virtual.

	* **Combinar vários servidores HDInsight** em uma única solução. Um exemplo é o uso de um servidor HDInsight Storm para consumir dados de entrada para, em seguida, armazenar os dados processados em um servidor HDInsight HBase. Os dados brutos também podem ser armazenados em um servidor HDInsight Hadoop para análise futura usando MapReduce.

* Conecte seus recursos de nuvem à sua rede de datacenter local (site a site ou ponto a site), usando uma VPN (rede privada virtual).

	A configuração site a site permite conectar vários recursos do datacenter à rede virtual do Azure usando uma VPN de hardware ou o serviço de Roteamento e Acesso Remoto.

	![diagrama da configuração site a site](media/hdinsight-extend-hadoop-virtual-network/site-to-site.png)

	A configuração ponto a site permite conectar um recurso específico à rede virtual do Azure usando VPN de software.

	![diagrama da configuração ponto a site](media/hdinsight-extend-hadoop-virtual-network/point-to-site.png)

	Usar a Rede Virtual para vincular a nuvem e seu datacenter habilita cenários semelhantes à configuração somente nuvem. Porém, você não fica limitado a trabalhar com recursos na nuvem – também pode trabalhar com os recursos em seu datacenter.

	* **Transferência direta de dados** entre o HDInsight e seu datacenter. Um exemplo é usar o Sqoop para transferir dados para ou do SQL Server, ou a leitura de dados gerados por um aplicativo LOB (linha de negócios).

	* **Invocar serviços ou trabalhos do HDInsight** de um aplicativo LOB. Um exemplo é usar APIs do HBase Java para armazenar e recuperar dados de um cluster HDInsight HBase.

Para obter mais informações sobre os recursos, benefícios e capacidades das redes virtuais, consulte a [Visão geral da rede virtual do Azure](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Você deve criar a Rede Virtual do Azure antes de provisionar um cluster HDInsight. Para obter mais informações, consulte [Tarefas de configuração de rede virtual](/documentation/services/virtual-network/).
>
> O Azure HDInsight dá suporte apenas a redes virtuais baseadas em local e atualmente não funciona com redes virtuais baseadas em grupo de afinidade.
>
> É altamente recomendável designar uma única subrede para cada cluster.
>
> Não é possível usar uma Rede Virtual v1 (Clássica) do Azure com o HDInsight baseado em Linux. A Rede Virtual deve ser v2 (Gerenciador de Recursos do Azure) para que seja listada como uma opção durante o processo de criação de cluster HDInsight no portal de visualização do Azure ou para poder ser usada durante a criação de um cluster por meio da CLI do Azure ou do Azure PowerShell.
>
> Se você tiver recursos em uma rede v1 e desejar disponibilizar o HDInsight diretamente a esses recursos por meio de uma rede virtual, veja [Conectando VNets clássicas a novas VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) para obter informações sobre como conectar uma Rede Virtual v2 a uma Rede Virtual v1. Quando essa conexão for estabelecida, você poderá criar o cluster HDInsight na Rede Virtual v2.

Para obter mais informações sobre como provisionar um cluster HDInsight em uma rede virtual, consulte [Provisionando clusters Hadoop no HDInsight](hdinsight-provision-clusters.md).

##<a id="tasks"></a>Tarefas e informações

Esta seção contém informações sobre tarefas comuns e informações que podem ser necessárias ao usar o HDInsight com uma rede virtual.

###Determinar o FQDN

Um FQDN (nome de domínio totalmente qualificado) será atribuído ao cluster HDInsight para a interface da Rede Virtual. Esse é o endereço que você deve usar ao se conectar ao cluster por meio de outros recursos na rede virtual. Para determinar o FQDN, use a seguinte URL para consultar o serviço de gerenciamento Ambari:

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE]Para obter mais informações sobre como usar o Ambari com o HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Você deve especificar o nome do cluster e um serviço e componente em execução no cluster, como o gerenciador de recursos YARN.

> [AZURE.NOTE]Os dados retornados são um documento JSON (JavaScript Object Notation) que contém muitas informações sobre o componente. Para extrair apenas o FQDN, você deve usar um analisador JSON para recuperar o valor `host_components[0].HostRoles.host_name`.

Por exemplo, para retornar o FQDN de um cluster Hadoop no HDInsight, você pode usar um dos métodos a seguir para recuperar os dados para o gerenciador de recursos YARN:

* [PowerShell do Azure](../powershell-install-configure.md)

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

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

###Conectando-se ao HBase

Para se conectar ao HBase remotamente usando a API do Java, você deve determinar os endereços de quorum ZooKeeper para o cluster HBase e especificá-los no seu aplicativo.

Para obter o endereço de quorum ZooKeeper, use um dos seguintes métodos para consultar o serviço de gerenciamento Ambari:

* [PowerShell do Azure](../powershell-install-configure.md)

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

* [cURL](http://curl.haxx.se/) e [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE]Para obter mais informações sobre como usar o Ambari com o HDInsight, consulte [Monitorar clusters Hadoop no HDInsight usando a API do Ambari](hdinsight-monitor-use-ambari-api.md).

Uma vez que as informações estejam reunidas, use-as em seu aplicativo cliente.

Por exemplo, para um aplicativo Java que usa a API do HBase, você adicionaria um arquivo **hbase-site.htm** ao projeto e especificaria as informações de quorum no arquivo da seguinte maneira:

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

Se você encontrar problemas ao acessar um serviço do HDInsight, consulte a documentação para o serviço para garantir que você habilitou o acesso à rede. Você também pode verificar o acesso à rede criando uma máquina virtual do Azure na mesma rede virtual e usar utilitários de cliente para verificar se a máquina virtual consegue se conectar ao serviço através da rede virtual.

##<a id="nextsteps"></a>Próximas etapas

Os exemplos a seguir demonstram como usar o HDInsight com a Rede Virtual do Azure:

* [Analisar dados de sensor com o Storm e o HBase no HDInsight](hdinsight-storm-sensor-data-analysis.md): demonstra como configurar um cluster Storm e HBase em uma rede virtual e como gravar dados remotamente no HBase pelo Storm.

* [Provisionar clusters Hadoop no HDInsight](hdinsight-hadoop-provision-linux-clusters.md): fornece informações sobre como provisionar clusters Hadoop, inclusive informações sobre como usar a Rede Virtual do Azure.

* [Usar o Sqoop com o Hadoop no HDInsight](hdinsight-use-sqoop-mac-linux.md): fornece informações sobre como usar o Sqoop para transferir dados com o SQL Server em uma rede virtual.

Para saber mais sobre redes virtuais do Azure, consulte [Visão geral da Rede Virtual do Azure](../virtual-network/virtual-networks-overview.md).

<!---HONumber=Oct15_HO1-->