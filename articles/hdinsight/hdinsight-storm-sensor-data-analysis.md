<properties
   pageTitle="Analisar dados do sensor com o Apache Storm e o HBase | Microsoft Azure"
   description="Saiba como conectar-se ao Apache Storm com uma rede virtual. Use o Storm com o HBase para processar dados de sensor de um hub de eventos e visualize-o com D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

# Analisar dados de sensor com o Apache Storm e com o HBase no HDInsight (Hadoop)

Saiba como usar o Apache Storm no HDInsight para processar dados de sensor a partir de Hubs de Eventos do Azure e visualizá-los usando D3.js. Este documento também descreve como usar uma rede virtual do Azure para conectar o Storm no HDInsight com o HBase no HDInsight e armazenar dados da topologia no HBase.

> [AZURE.NOTE]As informações neste documento são baseadas na utilização do Storm baseado em Windows no cluster HDInsight. Para obter informações sobre como trabalhar com o Hub de Eventos do Azure por meio do Storm baseado em Linux, consulte [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight](hdinsight-storm-develop-java-event-hub-topology.md)

## Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um [Apache Storm no cluster HDInsight](../hdinsight-storm-getting-started.md)

* [Node.js](http://nodejs.org/): usado para o painel da Web e para enviar dados de sensor ao Hub de Eventos.

* [Java e o JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

* [Maven](http://maven.apache.org/what-is-maven.html)

* [Git](http://git-scm.com/)

> [AZURE.NOTE]Java, JDK, Maven e Git também estão disponíveis por meio do gerenciador de pacotes [Chocolatey NuGet](http://chocolatey.org/).

## Arquitetura

![diagrama da arquitetura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Esse exemplo consiste nos seguintes componentes:

* **Hub de Eventos do Azure**: fornece dados coletados dos sensores. Para este exemplo, temos um aplicativo que gera dados falsos.

* **Storm no HDInsight**: fornece processamento em tempo real dos dados do Hub de Eventos.

* **HBase no HDInsight** (opcional): fornece armazenamento persistente de dados NoSQL.

* **Serviço de Rede Virtual do Azure** (opcional; necessário com o uso do HBase): permite a comunicação segura entre o Storm no HDInsight e o HBase em clusters HDInsight.

* **Site do painel**: um painel de exemplo que traça gráficos de dados em tempo real.

	* O site é implementado no Node js; portanto, ele pode ser executado em qualquer sistema operacional cliente como teste ou pode ser implantado nos sites do Azure.

	* A [Socket.io](http://socket.io/) é usada para comunicação em tempo real entre a topologia Storm e o site.

		> [AZURE.NOTE]Isso é um detalhe de implementação. Você pode usar qualquer estrutura de comunicação, como SignalR ou WebSockets brutos.

	* A [D3.js](http://d3js.org/) é usada para representar graficamente os dados enviados para o site.

A topologia lê dados do Hub de Eventos usando a classe **com.microsoft.eventhubs.spout.EventHubSpout**, que vem no cluster Storm no HDInsight. A comunicação com o site é realizada usando [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

Opcionalmente, a comunicação com o HBase é realizada usando a classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html), que vem como parte do Storm.

A seguir, há um diagrama da topologia.

![diagrama de topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE]Isso é uma visão muito simplificada da topologia. Em tempo de execução, uma instância de cada componente é criada para cada partição para o Hub de Eventos que está sendo lido. Essas instâncias são distribuídas pelos nós do cluster e os dados são roteados entre elas da seguinte forma:
>
> * Os dados do spout para o analisador têm a carga balanceada.
> * Os dados do analisador para o Painel de controle e para o HBase (se for usado) são agrupados por identificação de dispositivo para que as mensagens no mesmo dispositivo fluam sempre para o mesmo componente.

### Componentes

* **EventHub Spout**: o spout é fornecido como parte dos [Exemplos do Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples) no GitHub.

* **ParserBolt.java**: os dados emitidos pelo spout são JSON brutos e, às vezes, mais de um evento é emitido por vez. Este bolt demonstra como ler os dados emitidos pelo spout e os emite para um novo fluxo como uma tupla que contém vários campos.

* **DashboardBolt.java**: demonstra como usar a biblioteca cliente Socket.io para Java a fim de enviar dados em tempo real para o painel da Web.

## Prepare o seu ambiente

Antes de usar esse exemplo, você deve criar um Hub de Eventos do Azure que é lido pela topologia do Storm. Você também deve criar uma topologia Storm no HDInsight, já que o componente usado para ler dados do Hub de Eventos só fica disponível no cluster.

> [AZURE.NOTE]Ao final, o spout do Hub de Eventos estará disponível do Maven.

### Configurar o Hub de Eventos

O Hub de Eventos é a fonte de dados para este exemplo. Use as seguintes etapas para criar um novo Hub de Eventos.

1. No [Portal Clássico do Azure](https://manage.windowsazure.com), selecione **NOVO | Barramento de Serviço | Hub de Eventos | Criação Personalizada**.

2. Na caixa de diálogo **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na seta para continuar.

2. Na caixa de diálogo **Configurar o Hub de Eventos**, insira os valores de **Contagem de partições** e **Retenção de mensagem**. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1.

3. Depois que o Hub de Eventos tiver sido criado, selecione o namespace e selecione **Hubs de Eventos**. Por fim, selecione o Hub de Eventos criado anteriormente.

4. Selecione **Configurar** e crie duas novas políticas de acesso usando as informações a seguir.

	<table>
<tr><th>Nome</th><th>Permissões</th></tr>
<tr><td>Dispositivos</td><td>Enviar</td></tr>
<tr><td>Storm</td><td>Escutar</td></tr>
</table>Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria as políticas de acesso compartilhado que serão usadas para enviar e ler mensagens desse hub.

5. Depois de salvar as políticas, use o **Gerador de chave de acesso compartilhado** na parte inferior da página para recuperar a chave para as políticas de **dispositivos** e do **Storm**. Salve-os, pois precisará deles mais tarde.

### Criar o Storm no cluster HDInsight

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).

2. Clique em **HDInsight** no painel à esquerda e depois em **+NOVO** no canto inferior esquerdo da página.

3. Clique no ícone HDInsight na segunda coluna e selecione **Personalizar**.

4. Na página **Detalhes do Cluster**, insira o nome do novo cluster e selecione **Storm** como o **Tipo de Cluster**. Clique na seta para continuar.

5. Insira 1 para o número de **Nós de dados** a usar para esse cluster.

	> [AZURE.NOTE]Para minimizar o custo do cluster usado neste artigo, reduza o **Tamanho do Cluster** para 1 e exclua o cluster após ter terminado de usá-lo.

6. Insira o **Nome de Usuário**, a **Senha** do administrador e clique na seta para continuar.

4. Para **Conta de Armazenamento**, selecione **Criar Novo Armazenamento** ou selecione uma conta de armazenamento existente. Selecione ou insira o **Nome da conta** e o **Contêiner padrão** a serem utilizados. Clique no ícone de marca de seleção na parte inferior esquerda para criar o cluster Storm.

## Baixe e instale o EventHubSpout

1. Baixe o [projeto de Exemplos do Storm no HDInsight](https://github.com/hdinsight/hdinsight-storm-examples/). Após o download, localize o arquivo **lib/eventhubs/eventhubs-storm-spout-0.9-jar-with-dependencies.jar**.

2. No prompt de comando, use o seguinte comando para instalar o arquivo **eventhubs-storm-spout-0.9-jar-with-dependencies.jar** no repositório local do Maven. Isso permite a você adicioná-lo facilmente como referência no projeto Storm em uma etapa posterior.

		mvn install:install-file -Dfile=target/eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar

## Baixe e configure o projeto.

Use o seguinte comando para baixar o projeto do GitHub.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Depois que o comando for concluído, você terá a seguinte estrutura de diretório:

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this is the Java topology
			conf/
				Config.properties
				hbase-site.xml
			src/
			test/
			dashboard/ - this is the node.js web dashboard
			SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE]Este documento não traz todos os detalhes do código incluído nesse exemplo; no entanto, o código é totalmente comentado.

Abra o arquivo **Config.properties** e adicione as informações que você usou ao criar o Hub de Eventos. Salve o arquivo depois de adicionar essas informações.

	eventhubspout.username = storm

	eventhubspout.password = <the key of the 'storm' policy>

	eventhubspout.namespace = <the event hub namespace>

	eventhubspout.entitypath = <the event hub name>

	eventhubspout.partitions.count = <the number of partitions for the event hub>

	## if not provided, will use storm's zookeeper settings
	## zookeeper.connectionstring=localhost:2181

	eventhubspout.checkpoint.interval = 10

	eventhub.receiver.credits = 1024

## Compile e teste localmente

Antes de testar, você deve iniciar o painel de controle para exibir a saída da topologia e gerar dados para armazenar no Hub de Eventos.

### Iniciar o aplicativo Web

1. Abra um novo terminal ou prompt de comando e altere os diretórios para **hdinsight-eventhub-exemplo/dashboard**.Use o seguinte comando para instalar as dependências exigidas pelo aplicativo da Web:

		npm install

2. Use o comando abaixo para iniciar o aplicativo Web:

		node server.js

	Você deve ver uma página semelhante a esta:

		Server listening at port 3000

2. Abra um navegador da Web e digite ****http://localhost:3000/** como o endereço. Você verá uma página semelhante à seguinte:

	![Painel da Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Deixe esse prompt de comando ou terminal aberto. Após o teste, use Ctrl+C para parar o servidor Web.

### Inicie a geração de dados

> [AZURE.NOTE]As etapas desta seção usam o Node.js para que possam ser executados em qualquer plataforma. Para obter outros exemplos de linguagem, confira o diretório **SendEvents**.


1. Abra um novo terminal ou prompt de comando e altere os diretórios para **hdinsight-eventhub-example/SendEvents/nodejs**.Use o seguinte comando para instalar as dependências exigidas pelo aplicativo:

		npm install

2. Abra o arquivo **app.js** em um editor de texto e adicione as informações do Hub de Eventos obtidas anteriormente:

		// ServiceBus Namespace
		var namespace = 'servicebusnamespace';
		// Event Hub Name
		var hubname ='eventhubname';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'key';

2. Use o seguinte comando para inserir novas entradas no Hub de Eventos:

		node app.js

	Você verá várias linhas de saída que contêm os dados enviados para o Hub de Eventos. Eles terão aparência semelhante a isto:

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":0,"Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":1,"Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":2,"Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":3,"Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":4,"Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":5,"Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":6,"Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":7,"Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":8,"Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":9,"Temperature":84}

### Iniciar a topologia

2. Inicie a topologia localmente usando o seguinte comando:

	mvn compile exec:java -Dstorm.topology=com.microsoft.examples.Temperature

	Isso iniciará a topologia, lerá arquivos do Hub de Eventos e os enviará ao painel em execução nos sites da Websites do Azure. Você deve ver linhas aparecendo no painel da Web semelhantes a estas:

	![Painel com dados](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Enquanto o painel é executado, use o comando de `node app.js` das etapas anteriores para enviar novos dados para o painel. Como os valores de temperatura são gerados aleatoriamente, o gráfico será atualizado para mostrar os novos valores.

3. Depois de verificar se funcionou, pare a topologia digitando Ctrl+C. Para interromper o aplicativo SendEvent, selecione a janela e pressione qualquer tecla. Você também pode usar Ctrl+C para parar o servidor Web.

## Empacotar e implantar a topologia no HDInsight

No seu ambiente de desenvolvimento, use as etapas a seguir para executar a topologia Temperature no cluster Storm do HDInsight.

### Publique o painel do site.

1. Para implantá-lo em um site do Azure, siga as etapas em [Criar e implantar um site do Node.js no Azure](../web-sites-nodejs-develop-deploy-mac.md). Anote a URL do site, que será semelhante a **mywebsite.azurewebsites.net**.

2. Quando o site for criado, vá para o site no Portal Clássico do Azure e selecione a guia **Configurar**. Habilite o **Web Sockets** e clique em **Salvar** na parte inferior da página.

2. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts\\DashboardBolt.java** e altere a linha abaixo para apontar para a URL do painel publicado:

		socket = IO.socket("http://mywebsite.azurewebsites.net");

3. Salve o arquivo **DashboardBolt.java**.

### Empacotar e implantar a topologia

1. Use o seguinte comando para criar um pacote JAR do seu projeto:

		mvn package

	Isso criará um arquivo chamado **TemperatureMonitor-1.0-SNAPSHOT.jar** no diretório de **destino** do seu projeto.

2. Siga as etapas em [Implantar e gerenciar topologias do Storm](hdinsight-storm-deploy-monitor-topology.md) para carregar e iniciar a topologia em seu cluster Storm no HDInsight usando o **Painel Storm**.

3. Depois que a topologia for iniciada, abra um navegador no site publicado no Azure e use o comando `node app.js` para enviar dados ao Hub de Eventos. Você deve ver a atualização do painel da Web para exibir as informações.

	![painel Transações da Web](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Opcional: usar o HBase

Para usar o Storm e o HBase juntos, você deve criar uma rede virtual do Azure e um cluster Storm e HBase dentro da rede.

### Criar uma rede virtual do Azure (opcional)

Se você planeja usar o HBase com este exemplo, você deve criar uma rede virtual do Azure que contenha um cluster Storm no HDInsight e um cluster HBase no HDInsight.

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com).

2. No final da página, clique em **+NOVO** > **Serviços de Rede** > **Rede Virtual** > **Criação Rápida**.

3. Digite ou selecione os valores a seguir:

	- **Nome**: o nome da sua rede virtual.

	- **Espaço de endereço**: escolha um espaço de endereço para a rede virtual que seja grande o suficiente para fornecer endereços para todos os nós do cluster. Caso contrário, a provisão falhará.

	- **Contagem máxima de VMs**: escolha uma das contagens máximas da máquina virtual.

	- **Local**:O local deve ser o mesmo que o do cluster HBase que você criará.

	- **Servidor DNS**: este artigo usa o servidor DNS interno fornecido pelo Azure; portanto, você pode selecionar **Nenhum**. Configurações de rede mais avançadas com servidores DNS personalizados também têm suporte. Para obter orientações detalhadas, confira [Resolução de nomes (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

4. Clique em **Criar uma Rede Virtual**. O nome da nova rede virtual aparecerá na lista. Aguarde até que a coluna de Status exiba **Criada**.

5. No painel principal, clique na rede virtual que você acabou de criar.

6. No topo da página, clique em **PAINEL**.

7. Em **visão rápida**, anote a **ID DA REDE VIRTUAL**. Ele será necessário ao provisionar os clusters Storm e HBase.

8. No topo da página, clique em **CONFIGURAR**.

9. No final da página, o nome da sub-rede padrão é **Subnet-1**. Use o botão **adicionar sub-rede** para adicionar a **Sub-rede-2**. Essas sub-redes alojarão os clusters do Storm e do HBase.

	> [AZURE.NOTE]Neste artigo, usaremos clusters com apenas um nó. Se estiver criando clusters de vários nós, verifique o **CIDR (CONTAGEM DE ENDEREÇOS)** para a sub-rede que será usada para o cluster. A contagem de endereços deve ser maior que o número de nós de trabalho mais sete (Gateway: 2, Headnode: 2, Zookeeper: 3). Por exemplo, se você precisa de um cluster do HBase de 10 nós, a contagem de endereços da sub-rede deve ser maior que 17 (10+7). Caso contrário, a implantação falhará.
	>
	> É altamente recomendado designar uma única sub-rede para um cluster.

11. Na parte inferior da página, clique em **Salvar**.

### Criar um cluster Storm e HBase na rede virtual

1. Entre no [Portal Clássico do Azure](https://manage.windowsazure.com/).

2. Clique em **HDInsight** no painel à esquerda e depois em **+NOVO** no canto inferior esquerdo da página.

3. Clique no ícone HDInsight na segunda coluna e selecione **Personalizar**.

4. Na página **Detalhes do Cluster**, insira o nome do novo cluster e selecione **Storm** como o **Tipo de Cluster**. Clique na seta para continuar.

5. Insira 1 para o número de **Nós de dados** a usar para esse cluster. Para **Região/Rede virtual**, selecione a Rede virtual do Azure criada anteriormente. Para **Sub-redes da Rede Virtual**, selecione **Sub-rede-1**.

	> [AZURE.NOTE]Para minimizar o custo do cluster usado neste artigo, reduza o **Tamanho do Cluster** para 1 e exclua o cluster após ter terminado de usá-lo.

6. Insira o **Nome de Usuário**, a **Senha** do administrador e clique na seta para continuar.

4. Para **Conta de Armazenamento**, selecione **Criar Novo Armazenamento** ou selecione uma conta de armazenamento existente. Selecione ou insira o **Nome da conta** e o **Contêiner padrão** a serem utilizados. Clique no ícone de marca de seleção na parte inferior esquerda para criar o cluster Storm.

5. Repita essas etapas para criar um novo cluster **HBase**. Abaixo estão as principais diferenças:

	* **Tipo de cluster**: selecione **HBase**

	* **Sub-redes da Rede Virtual**: selecione **Sub-rede-2**.

	* **Conta de armazenamento**: você deve usar um contêiner diferente do usado para o cluster Storm.

### Descobrir o sufixo DNS do HBase

Para gravar no HBase usando o cluster Storm, você deve usar o FQDN (nome de domínio totalmente qualificado) do cluster HBase. Use o seguinte comando para descobrir essas informações:

	curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

Nos dados JSON retornados, localize a entrada **"host\_name"**. Ela conterá o FQDN para os nós no cluster. Por exemplo:

	...
	"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
	...

A parte do nome do domínio que começa com o nome do cluster é o sufixo DNS, como **mycluster.b1.cloudapp.net**.

### Habilitar o bolt do HBase

1. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\conf\\hbase-site.xml** e substitua as entradas `suffix` na linha a seguir com o sufixo DNS obtido anteriormente para o cluster HBase. Depois de fazer essas alterações, salve o arquivo.

		<value>zookeeper0.suffix,zookeeper1.suffix,zookeeper2.suffix</value>

	Isso será usado pelo bolt HBase para se comunicar com o cluster HBase.

1. Abra **hdinsight-eventhub-example\\TemperatureMonitor\\src\\main\\java\\com\\microsoft\\examples\\bolts * * em um editor de texto e remova os comentários das linhas a seguir removendo o `//` desde o início. Salve o arquivo depois de fazer essa alteração.

		topologyBuilder.setBolt("HBase", new HBaseBolt("SensorData", mapper).withConfigKey("hbase.conf"), spoutConfig.getPartitionCount())
    	  .fieldsGrouping("Parser", "hbasestream", new Fields("deviceid")).setNumTasks(spoutConfig.getPartitionCount());

	Isso habilita o bolt do HBase.

	> [AZURE.NOTE]Você somente deve ativar o bolt HBase quando estiver implantando em cluster Storm, não quando estiver testando localmente.

### Dados de Storm e HBase

Antes de executar a topologia, você deve preparar o HBase para aceitar os dados.

1. Use a Área de Trabalho Remota para se conectar ao cluster HBase.

2. Na área de trabalho, inicie a linha de comando do HDInsight e insira os seguintes comandos:

    cd %HBASE\_HOME% bin\\hbase shell

3. No shell do HBase, insira o seguinte comando para criar uma tabela na qual os dados do sensor serão armazenados:

    create 'SensorData', 'cf'

4. Verifique se a tabela não contém dados inserindo o seguinte comando:

    scan 'SensorData'

Quando você tiver iniciado a topologia no cluster Storm e nos dados processados, pode usar o comando `scan 'SensorData'` novamente para verificar se os dados foram inseridos no HBase.


## Próximas etapas

Você agora aprendeu a usar o Storm para ler dados do Hub de Eventos, armazenar dados no HBase e exibir informações do Storm em um painel externo usando SignalR e D3.js. Se você usou as etapas opcionais, também aprendeu a configurar o HDInsight em uma rede virtual e a comunicar-se entre uma topologia Storm e o HBase usando o bolt HBase.

* Para obter mais exemplos de topologias Storm com o HDinsight, confira:

    * [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

* Para saber mais sobre o Apache Storm, confira o site [Apache Storm](https://storm.incubator.apache.org/).

* Para saber mais sobre HBase com HDInsight, confira a [Visão geral do HBase com HDInsight](hdinsight-hbase-overview.md)

* Para saber mais sobre o Socket.IO, acesse o site [socket.io](http://socket.io/).

* Para saber mais sobre D3.js, confira [D3.js - Documentos controlados por dados](http://d3js.org/)

* Para saber mais sobre como criar topologias em Java, confira [Desenvolver topologias Java para Apache Storm no HDInsight](hdinsight-storm-develop-java-topology.md).

* Para saber mais sobre como se conectar ao cluster Storm, confira [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_1203_2015-->