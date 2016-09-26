<properties
	pageTitle="Recursos de alta disponibilidade do HDInsight baseados em Linux (Hadoop) | Microsoft Azure"
	description="Saiba como clusters HDInsight baseados em Linux melhoram a confiabilidade e a disponibilidade usando um nó principal adicional. Você aprenderá como isso afeta os serviços do Hadoop, como o Ambari e o Hive, e também como se conectar individualmente com cada nó principal usando SSH."
	services="hdinsight"
	editor="cgronlun"
	manager="jhubbard"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/05/2016"
	ms.author="larryfr"/>

#Disponibilidade e confiabilidade dos clusters Hadoop em HDInsight

O Hadoop atinge a alta disponibilidade e confiabilidade distribuindo cópias redundantes de dados e serviços entre os nós em um cluster. No entanto, em geral, as distribuições padrão do Hadoop têm apenas um único nó de cabeçalho. Qualquer falha do único nó de cabeçalho poderá fazer com que o cluster pare de funcionar.

Para resolver esse problema potencial, os clusters HDInsight baseados em Linux no Azure fornecem dois nós de cabeçalho para aumentar a disponibilidade e confiabilidade dos serviços e trabalhos do Hadoop em execução.

> [AZURE.NOTE] As etapas deste documento são específicas de clusters HDInsight baseados em Linux. Se você estiver usando um cluster baseado no Windows, consulte [Disponibilidade e confiabilidade dos clusters Hadoop baseado em Windows no HDInsight](hdinsight-high-availability.md) para obter informações específicas do Windows.

##Compreendendo os dados

Os nós em um cluster HDInsight são implementados com o uso de Máquinas Virtuais do Azure. Em caso de falha de um nó, ele é colocado offline e um novo nó é criado para substituir o nó com falha. Enquanto o nó estiver offline, outro nó do mesmo tipo será usado até que o novo nó seja colocado online.

> [AZURE.NOTE] Se o nó estiver analisando dados no momento da falha, o andamento no trabalho será perdido. O trabalho que estava sendo executado pelo nó com falha será reenviado para outro nó.

As seções a seguir abordam os tipos de nós individuais usados com o HDInsight. Nem todos os tipos de nó são usados para um tipo de cluster. Por exemplo, um tipo de cluster Hadoop não terá nenhum nó Nimbus. Para obter mais informações sobre os nós usados pelos tipos de cluster HDInsight, veja a seção “Tipos de cluster” de [Criar clusters Hadoop baseados em Linux no HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

###Nós de cabeçalho

Algumas implementações do Hadoop têm um único nó de cabeçalho que hospeda serviços e componentes que gerenciam falhas de nós de trabalho sem problemas. Mas qualquer interrupção dos serviços principais em execução no nó de cabeçalho faria com que o cluster deixasse de funcionar.

Os clusters HDInsight fornecem um nó de cabeçalho secundário, que permite que os serviços e componentes principais continuem a executar nó secundário no caso de uma falha do principal.

> [AZURE.IMPORTANT] Ambos os nós de cabeçalho estão ativos e em execução no cluster simultaneamente. Alguns serviços, como HDFS ou YARN, são apenas “active” em um nó de cabeçalho a qualquer momento (e fica de “standby” no outro nó de cabeçalho). Outros serviços, como HiveServer2 ou MetaStore Hive estão ativos em ambos os nós de cabeçalho ao mesmo tempo.

###Nós Nimbus

Para clusters Storm, os nós Nimbus fornecem funcionalidade semelhante ao JobTracker do Hadoop, distribuindo e monitorando o processamento nos nós de trabalho. O HDInsight fornece dois nós Nimbus para o tipo de cluster Storm.

###Nós do Zookeeper

Os nós [ZooKeeper](http://zookeeper.apache.org/) (ZKs) são usados para eleição de líder de serviços principais em nós de cabeçalho e para garantir que os serviços, nós de dados (trabalho) e gateways saibam em qual nó de cabeçalho um serviço principal está ativo. Por padrão, o HDInsight fornece três nós do ZooKeeper.

###Nós de trabalho

Os nós de trabalho executam a análise de dados real quando um trabalho é enviado para o cluster. Se um nó de trabalho falhar, a tarefa que ele estava executando será enviada para outro nó de trabalho. Por padrão, o HDInsight criará quatro nós de trabalho; no entanto, é possível alterar esse número para atender às suas necessidades, durante e após a criação do cluster.

###Nó de borda

Um nó de borda não participa ativamente na análise de dados dentro do cluster, mas é usado por desenvolvedores ou cientistas de dados ao trabalhar com o Hadoop. O nó de borda reside na mesma Rede Virtual do Azure que os outros nós no cluster e pode acessar diretamente todos os outros nós. Já que ele não está envolvido na análise de dados do cluster, ele pode ser usado sem a preocupação com a retirada de recursos de serviços críticos ou de trabalhos de análise do Hadoop.

Atualmente, o Servidor R no HDInsight é o único tipo de cluster que fornece um nó de borda por padrão. Para o Servidor R no HDInsight, o nó de borda é usado para testar o código R localmente no nó antes de enviá-lo ao cluster para o processamento distribuído.

[Criar um cluster HDInsight baseado em Linux com o Hue em um Nó de Borda](https://azure.microsoft.com/documentation/templates/hdinsight-linux-with-hue-on-edge-node/) é um modelo de exemplo que pode ser usado para criar um tipo de cluster Hadoop que tem um nó de Borda.


## Acessando os nós

O acesso ao cluster pela Internet é fornecido por meio de um gateway público e é limitado à conexão com os nós de cabeçalho e – no caso de um Servidor R no cluster HDInsight –, o nó de borda. O acesso aos serviços em execução nos nós de cabeçalho não é afetado pelo fato de ter vários nós de cabeçalhos, já que o gateway público encaminha solicitações para o nó de cabeçalho que hospeda o serviço solicitado. Por exemplo, se, no momento, o Ambari estiver hospedado no nó de cabeçalho 1, o gateway encaminhará solicitações de entrada para o Ambari nesse nó.

Ao acessar o cluster usando o SSH, a conexão pela porta 22 (o padrão de SSH) vai conectar ao nó de cabeçalho 0; a conexão pela porta 23 vai conectar ao nó de cabeçalho 1. Por exemplo, `ssh username@mycluster-ssh.azurehdinsight.net` se conectará ao nó de cabeçalho 0 do cluster chamado __mycluster__.

> [AZURE.NOTE] Isso também se aplica aos protocolos baseados no SSH, como o SFTP (protocolo FTP do SSH).

O nó de borda fornecido com o Servidor R nos clusters HDInsight também pode ser diretamente acessado usando o SSH por meio da porta 22. Por exemplo, `ssh username@RServer.mycluster.ssh.azurehdinsight.net` se conectará ao nó de borda de um Servidor R no cluster HDInsight chamado __mycluster__.

### Nomes internos de domínio totalmente qualificado (FQDN)

Os nós em um cluster HDInsight possuem um endereço IP interno e o FQDN que só pode ser acessado do cluster (por exemplo, uma sessão SSH para o nó de cabeçalho ou um trabalho em execução no cluster.) Ao acessar serviços em cluster usando o endereço IP ou FQDN interno, você deve usar Ambari para verificar o IP ou FQDN para usar quando acessar o serviço.

Por exemplo, o serviço de Oozie pode ser executado somente em um nó de cabeçalho e usar o comando `oozie` de uma sessão SSH requer a URL para o serviço. Isso pode ser recuperado do Ambari usando o seguinte comando:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Isso retornará um valor semelhante ao seguinte, que contém a URL interna para usar com o comando `oozie`:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

### Acessando outros tipos de nó

Você pode se conectar a nós que não estão diretamente acessíveis pela Internet usando os métodos a seguir.

* __SSH__: assim que estiver conectado a um nó de cabeçalho usando o SSH, você poderá usar o SSH por meio do nó de cabeçalho para se conectar aos outros nós no cluster.
* __Túnel SSH__: caso precise acessar um serviço Web hospedado em um dos nós que não esteja exposto à Internet, será necessário [usar um túnel SSH](hdinsight-linux-ambari-ssh-tunnel.md).
* __Rede Virtual do Azure__: caso o cluster HDInsight faça parte de uma Rede Virtual do Azure, qualquer recurso contido na mesma Rede Virtual poderá acessar diretamente todos os nós no cluster.

## Como verificar o status do serviço

A interface do usuário do Ambari Web ou a API REST do Ambari pode ser usada para verificar o status dos serviços executados nos nós de cabeçalho.

###Interface do usuário da Ambari Web

A interface do usuário da Ambari Web é visível em https://CLUSTERNAME.azurehdinsight.net. Substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira as credenciais do usuário HTTP para o cluster. O nome de usuário padrão HTTP é **admin** e a senha é a senha que você digitou ao criar o cluster.

Ao chegar na página Ambari, os serviços instalados serão listados à esquerda da página.

![Serviços instalados](./media/hdinsight-high-availability-linux/services.png)

Há uma série de ícones que podem aparecer ao lado de um serviço para indicar o status. Todos os alertas relacionados a um serviço podem ser visualizados usando o link **Alertas** na parte superior da página. Você pode selecionar cada serviço para exibir mais informações sobre ele.

A página de serviço fornece informações sobre o status e a configuração de cada serviço, mas não fornece informações sobre o nó de cabeçalho no qual o serviço está em execução. Para exibir essas informações, use o link **Hosts** na parte superior da página. Isso exibirá os hosts do cluster, incluindo os nós de cabeçalho.

![lista de hosts](./media/hdinsight-high-availability-linux/hosts.png)

Selecionar o link para um dos nós de cabeçalho exibirá os serviços e componentes em execução nesse nó.

![Status do componente](./media/hdinsight-high-availability-linux/nodeservices.png)

###API REST do Ambari

A API REST do Ambari está disponível pela Internet, e o gateway público manipula as solicitações de roteamento para o nó de cabeçalho que hospeda a API REST.

Você pode usar o seguinte comando para verificar o estado de um serviço por meio da API de REST do Ambari:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Substitua a **SENHA** pela senha da conta do usuário HTTP (administrador)

* Substitua o **CLUSTERNAME** pelo nome do cluster

* Substitua o **SERVICENAME** pelo nome do serviço para verificar o status de

Por exemplo, para verificar o status do serviço **HDFS** em um cluster chamado **mycluster**, por uma senha de **senha**, você usaria o seguinte:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

A resposta será semelhante ao seguinte:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

A URL indica que o serviço está sendo executado no **nó de cabeçalho 0**.

O estado indica que o serviço está sendo executado ou **INICIADO**.

Se você não souber quais serviços estão instalados no cluster, você pode usar o seguinte para recuperar uma lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Componentes do serviço

Serviços podem conter componentes os quais você deseja verificar o status de individualmente. Por exemplo, o HDFS contém o componente NameNode. Para exibir informações sobre um componente, o comando seria:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Se você não souber quais componentes estão instalados no cluster, você pode usar o seguinte para recuperar uma lista:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component
    
## Como acessar arquivos de log nos nós de cabeçalho

###SSH

Enquanto estiver conectado a um nó de cabeçalho por meio do SSH, os arquivos de log podem ser encontrados em **/var/log**. Por exemplo, **/var/log/hadoop-yarn/yarn** contêm logs para YARN.

Cada nó de cabeçalho pode ter entradas de log exclusivo, portanto você deve verificar os logs em ambos.

###SFTP

Também é possível se conectar ao nó de cabeçalho usando o Protocolo FTP do SSH ou SFTP e baixar os arquivos de log diretamente.

Semelhante ao uso de um cliente SSH, ao se conectar com o cluster, é necessário fornecer o nome de conta de usuário SSH e o endereço SSH do cluster. Por exemplo: `sftp username@mycluster-ssh.azurehdinsight.net`. Também é necessário fornecer a senha da conta quando solicitado ou uma chave pública usando o parâmetro `-i`.

Depois de conectado, você verá um prompt `sftp>`. Neste prompt, é possível alterar os diretórios, além de carregar e baixar arquivos. Por exemplo, os seguintes comandos alteram os diretórios para o diretório **/var/log/hadoop/hdfs** e baixam todos os arquivos no diretório em seguida.

    cd /var/log/hadoop/hdfs
    get *

Para obter uma lista dos comandos disponíveis, insira `help` no prompt `sftp>`.

> [AZURE.NOTE] Há também interfaces gráficas que permitem visualizar o sistema de arquivos quando você estiver conectado usando SFTP. Por exemplo, [MobaXTerm](http://mobaxterm.mobatek.net/) permite que você procure o sistema de arquivos usando uma interface semelhante à do Windows Explorer.


###Ambari

> [AZURE.NOTE] Acessar arquivos de log por meio de Ambari requer um túnel SSH, já que os sites da Web para os serviços individuais não são expostos publicamente na Internet. Para obter mais informações sobre o uso de um túnel SSH, consulte [Usar túnel SSH para acessar a IU da Ambari Web, ResourceManager, JobHistory, NameNode, Oozie e outras IUs da Web](hdinsight-linux-ambari-ssh-tunnel.md).

Na IU da Ambari Web, selecione o serviço que você deseja para ver os logs (por exemplo, YARN,) e, em seguida, use **Links Rápidos** para selecionar qual nó principal para ver os logs.

![Usando links rápidos para exibir logs](./media/hdinsight-high-availability-linux/viewlogs.png)

## Como configurar o tamanho do nó ##

O tamanho do nó só pode ser selecionado durante a criação do cluster. Você pode encontrar uma lista de diferentes tamanhos de VM disponíveis para o HDInsight, incluindo o núcleo, memória e armazenamento local para cada um, na [página de preços do HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

Ao criar um novo cluster, você pode especificar o tamanho dos nós. A seguir, apresentamos informações sobre como especificar o tamanho usando o [Portal do Azure][preview-portal], o [Azure PowerShell][azure-powershell] e a [CLI do Azure][azure-cli]\:

* **Portal do Azure**: ao criar um novo cluster, você terá a opção de definir o tamanho (tipo de preço) do nó de cabeçalho, de trabalho (se usado pelo tipo de cluster) e nós do ZooKeeper para o cluster:

	![Imagem do Assistente de criação de cluster com a seleção de tamanho do nó](./media/hdinsight-high-availability-linux/headnodesize.png)

* **CLI do Azure**: ao usar o comando `azure hdinsight cluster create`, é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `--headNodeSize`, `--workerNodeSize` e `--zookeeperNodeSize`.

* **Azure PowerShell**: ao usar o cmdlet `New-AzureRmHDInsightCluster`, é possível definir o tamanho dos nós de cabeçalho, de trabalho e do ZooKeeper usando os parâmetros `-HeadNodeVMSize`, `-WorkerNodeSize` e `-ZookeeperNodeSize`.

##Próximas etapas

Neste documento, você aprendeu como o Azure HDInsight fornece alta disponibilidade para Hadoop. Use o seguinte para saber mais sobre os tópicos mencionados neste documento.

- [Referência REST do Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Instalar e configurar a CLI do Azure.](../xplat-cli-install.md)

- [Instalar e configurar o PowerShell do Azure](../powershell-install-configure.md)

- [Gerenciar clusters HDInsight usando o Ambari](hdinsight-hadoop-manage-ambari.md)

- [Provisionar os clusters HDInsight baseados em Linux](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=AcomDC_0914_2016-->