<properties
	pageTitle="Disponibilidade dos clusters Hadoop no HDInsight | Microsoft Azure"
	description="Clusters de HDInsight baseados em Linux melhoram a confiabilidade e disponibilidade usando um nó principal adicional."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/02/2015"
	ms.author="larryfr"/>

#Disponibilidade e confiabilidade dos clusters Hadoop em HDInsight

Um segundo nó principal é usado pelos clusters Hadoop baseados em Linux implantados pelo HDInsight do Azure. Isso aumenta a disponibilidade e confiabilidade de serviços do Hadoop e trabalhos em execução no Azure.

> [AZURE.NOTE]As etapas deste documento são específicas de clusters HDInsight baseados em Linux. Se você estiver usando um cluster baseado no Windows, consulte [Disponibilidade e confiabilidade dos clusters Hadoop baseado em Windows no HDInsight](hdinsight-high-availability.md) para obter informações específicas do Windows.

##Noções básicas sobre os nós de cabeçalho

Algumas implementações de Hadoop têm um único nó de cabeçalho que hospeda serviços e componentes que gerenciam falhas de nós de dados (trabalho) sem problemas. Mas qualquer interrupção dos serviços principais em execução no nó de cabeçalho faria com que o cluster deixasse de funcionar.

Os clusters HDInsight fornecem um nó de cabeçalho secundário, que permite que os serviços e componentes principais continuem a executar nó secundário no caso de uma falha do principal.

> [AZURE.IMPORTANT]Ambos os nós de cabeçalho estão ativos e em execução no cluster simultaneamente. Alguns serviços, como HDFS ou YARN, são apenas “active” em um nó de cabeçalho a qualquer momento (e fica de “standby” no outro nó de cabeçalho). Outros serviços, como HiveServer2 ou MetaStore Hive estão ativos em ambos os nós de cabeçalho ao mesmo tempo.

Os nós [ZooKeeper](http://zookeeper.apache.org/) (ZKs) são usados para eleição de líder de serviços principais em nós de cabeçalho e para garantir que os serviços, nós de dados (trabalho) e gateways saibam em qual nó de cabeçalho um serviço principal está ativo.

## Acessando os nós de cabeçalho

Em geral, todo o acesso ao cluster por meio de gateways públicos (Ambari Web e APIs REST,) não é afetado por ter vários nós de cabeçalho. A solicitação é encaminhada para o nó de cabeçalho ativo e atendida conforme apropriada.

Ao acessar o cluster usando o SSH, a conexão pela porta 22 (o padrão para o SSH) irá se conectar ao headnode0; conectando por meio da porta 23 irá se conectar ao headnode1.

### Nomes internos de domínio totalmente qualificado (FQDN)

Os nós em um cluster HDInsight possuem um endereço IP interno e o FQDN que só pode ser acessado do cluster (por exemplo, uma sessão SSH para o nó de cabeçalho ou um trabalho em execução no cluster.) Ao acessar serviços em cluster usando o endereço IP ou FQDN interno, você deve usar Ambari para verificar o IP ou FQDN para usar quando acessar o serviço.

Por exemplo, o serviço de Oozie pode ser executado somente em um nó de cabeçalho e usar o comando `oozie` de uma sessão SSH requer a URL para o serviço. Isso pode ser recuperado do Ambari usando o seguinte comando:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Isso retornará um valor semelhante ao seguinte, que contém a URL interna para usar com o comando `oozie`:

	"oozie.base.url": "http://headnode0.CLUSTERNAME-ssh.d9.internal.cloudapp.net:11000/oozie"

## Como verificar o status do serviço

A interface do usuário da Ambari Web ou a API REST do Ambari pode ser usada para verificar o status dos serviços que são executados no nó de cabeçalho.

###API REST do Ambari

Você pode usar o seguinte comando para verificar o estado de um serviço por meio da API de REST do Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Substitua a **SENHA** pela senha da conta do usuário HTTP (administrador)

* Substitua o **CLUSTERNAME** pelo nome do cluster

* Substitua o **SERVICENAME** pelo nome do serviço para verificar o status de

Por exemplo, para verificar o status do serviço **HDFS** em um cluster chamado **mycluster**, por uma senha de **senha**, você usaria o seguinte:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

A resposta será semelhante ao seguinte:

	{
	  "href" : "http://headnode0.mycluster-ssh.j7.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

A URL indica que o serviço está sendo executado no **headnode0**.

O estado indica que o serviço está sendo executado ou **INICIADO**.

Se você não souber quais serviços estão instalados no cluster, você pode usar o seguinte para recuperar uma lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componentes do serviço

Serviços podem conter componentes os quais você deseja verificar o status de individualmente. Por exemplo, o HDFS contém o componente NameNode. Para exibir informações sobre um componente, o comando seria:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se você não souber quais componentes estão instalados no cluster, você pode usar o seguinte para recuperar uma lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Interface do usuário da Ambari Web

A interface do usuário da Ambari Web é visível em https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira as credenciais do usuário HTTP para o cluster. O nome de usuário padrão HTTP é **admin** e a senha é a senha que você digitou ao criar o cluster.

Ao chegar na página Ambari, os serviços instalados serão listados à esquerda da página.

![Serviços instalados](./media/hdinsight-high-availability-linux/services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o status. Todos os alertas relacionados a um serviço podem ser visualizados usando o link **Alertas** na parte superior da página. Você pode selecionar cada serviço para exibir mais informações sobre ele.

A página de serviço fornece informações sobre o status e a configuração de cada serviço, mas não fornece informações sobre o nó de cabeçalho no qual o serviço está em execução. Para exibir essas informações, use o link **Hosts** na parte superior da página. Isso exibirá os hosts do cluster, incluindo os nós de cabeçalho.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Selecionar o link para um dos nós de cabeçalho exibirá os serviços e componentes em execução nesse nó.

![Status do componente](./media/hdinsight-high-availability-linux/nodeservices.png)

## Como acessar arquivos de log no nó principal secundário

###SSH

Enquanto estiver conectado a um nó de cabeçalho por meio do SSH, os arquivos de log podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-yarn/yarn** contêm logs para YARN.

Cada nó de cabeçalho pode ter entradas de log exclusivo, portanto você deve verificar os logs em ambos.

###Ambari

> [AZURE.NOTE]Acessar arquivos de log por meio de Ambari requer um túnel SSH, já que os sites da Web para os serviços individuais não são expostos publicamente na Internet. Para obter mais informações sobre o uso de um túnel SSH, consulte [Usar túnel SSH para acessar a IU da Ambari Web, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).

Na IU da Ambari Web, selecione o serviço que você deseja para ver os logs (por exemplo, YARN,) e, em seguida, use **Links Rápidos** para selecionar qual nó principal para ver os logs.

![Usando links rápidos para exibir logs](./media/hdinsight-high-availability-linux/viewlogs.png)

## Como configurar o tamanho do nó principal ##

O tamanho do nó principal só pode ser selecionado durante a criação do cluster. O tamanho padrão dos nós principais é **A3**, que fornece 4 núcleos, 7GB de memória e 285GB de armazenamento local. Você pode encontrar uma lista de diferentes tamanhos de VM disponíveis para o HDInsight, incluindo o núcleo, memória e armazenamento local para cada um, na [página de preços do HDInsight](http://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um novo cluster, você pode especificar o tamanho dos nós. A seguir são apresentada as informações sobre como especificar o tamanho usando o [Portal de visualização do Azure][preview-portal], o [Azure PowerShell][azure-powershell] e o [CLI do Azure][azure-cli]\:

* **Portal de visualização do azure**: ao criar um novo cluster, você terá a opção de definir o tamanho (tipo de preço) do nó principal e do nó de dados (trabalho) para o cluster:

	![Imagem do Assistente de criação de cluster com a seleção de tamanho do nó](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI do azure**: ao usar o comando `azure hdinsight cluster create`, você pode definir o tamanho do nó principal usando o parâmetro `--headNodeSize`.

* **Azure PowerShell**: ao usar o cmdlet `New-AzureHDInsightCluster`, você pode definir o tamanho do nó principal usando o parâmetro `-HeadNodeVMSize`.

##Próximas etapas

Neste documento, você aprendeu como o Azure HDInsight fornece alta disponibilidade para Hadoop. Use o seguinte para saber mais sobre os tópicos mencionados neste documento.

- [Referência REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Instalar e configurar a CLI do Azure](../xplat-cli-install.md).

- [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

- [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

- [Provisionar os clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=Oct15_HO3-->