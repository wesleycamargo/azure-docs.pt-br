<properties
   pageTitle="Analisar dados do sensor com o Apache Storm e o HBase | Microsoft Azure"
   description="Saiba como conectar-se ao Apache Storm com uma rede virtual. Use o Storm com o HBase para processar dados de sensor de um hub de eventos e visualize-o com D3.js."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/20/2016"
   ms.author="larryfr"/>

# Analisar dados de sensor com o Apache Storm e com o HBase no HDInsight (Hadoop) 

Saiba como usar o Apache Storm no HDInsight para processar dados de sensor, armazená-los no Apache HBase no HDInsight e visualizá-los usando D3.js como um Aplicativo Web do Azure.

O modelo do Azure Resource Manager usado neste documento demonstra como criar vários recursos do Azure em um grupo de recursos. Especificamente, ele cria uma Rede Virtual do Azure, dois clusters do HDInsight (Storm e HBase) e um Aplicativo Web do Azure. Uma implementação node.js de um painel da Web em tempo real é automaticamente implantada no aplicativo Web.

> [AZURE.NOTE] As informações neste documento e o exemplo fornecido foram testados usando os versões de cluster 3.3 e 3.4 do HDInsight baseado em Linux.

## Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

    > [AZURE.IMPORTANT] Você não precisa de um cluster HDInsight existente. As etapas neste documento criarão os seguintes recursos:
    >
    > * Uma Rede virtual do Azure
    > * Um cluster Storm no HDInsight (baseado em Linux, 2 nós de trabalho)
    > * Um cluster HBase no HDInsight (baseado em Linux, 2 nós de trabalho)
    > * Um Aplicativo Web do Azure que hospeda o painel da Web

* [Node.js](http://nodejs.org/): usado para visualizar o painel da Web localmente no seu ambiente de desenvolvimento.

* [Java e JDK 1.7](http://www.oracle.com/technetwork/java/javase/downloads/index.html): usado para desenvolver a topologia do Storm.

* [Maven](http://maven.apache.org/what-is-maven.html): usado para criar e compilar o projeto.

* [Git](http://git-scm.com/): usado para baixar o projeto do GitHub.

* Um cliente __SSH__: usado para conectar-se aos clusters HDInsight baseados em Linux. Para saber mais sobre como usar SSH com o HDInsight, veja os documentos a seguir.

    * [Usar SSH com o HDInsight em um cliente Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    * [Usar SSH com o HDInsight em um cliente Linux, Unix ou Mac](hdinsight-hadoop-linux-use-ssh-unix.md)

    > [AZURE.NOTE] Você também deve ter acesso ao comando `scp`, que é usado para copiar arquivos entre o seu ambiente de desenvolvimento local e o cluster HDInsight usando o SSH.

## Arquitetura

![diagrama da arquitetura](./media/hdinsight-storm-sensor-data-analysis/devicesarchitecture.png)

Esse exemplo consiste nos seguintes componentes:

* **Hub de Eventos do Azure**: contém os dados coletados dos sensores. Para este exemplo, temos um aplicativo que gera os dados.

* **Storm no HDInsight**: fornece processamento em tempo real dos dados do Hub de Eventos.

* **HBase no HDInsight**: fornece um armazenamento de dados NoSQL persistente para dados depois que eles são processados pelo Storm.

* **Serviço de Rede Virtual do Azure**: permite a comunicação segura entre o Storm no HDInsight e o HBase em clusters HDInsight.

    > [AZURE.NOTE] Uma rede virtual é necessária para usar a API de cliente HBase Java, pois ele não é exposto pelo gateway público para clusters HBase. A instalação de clusters Storm e HBase na mesma rede virtual permite que o cluster Storm (ou qualquer outro sistema na rede virtual,) acesse diretamente o HBase usando a API de cliente.

* **Site do painel**: um painel de exemplo que traça gráficos de dados em tempo real.

	* O site é implementado no Node js; portanto, ele pode ser executado em qualquer sistema operacional cliente como teste ou pode ser implantado nos sites do Azure.

	* A [Socket.io](http://socket.io/) é usada para comunicação em tempo real entre a topologia Storm e o site.

		> [AZURE.NOTE] Isso é um detalhe de implementação. Você pode usar qualquer estrutura de comunicação, como SignalR ou WebSockets brutos.

	* A [D3.js](http://d3js.org/) é usada para representar graficamente os dados enviados para o site.

A topologia lê dados do Hub de Eventos usando a classe [org.apache.storm.eventhubs.spout.EventHubSpout](http://storm.apache.org/releases/0.10.1/javadocs/org/apache/storm/eventhubs/spout/class-use/EventHubSpout.html) e grava dados no HBase usando a classe [org.apache.storm.hbase.bolt.HBaseBolt](https://storm.apache.org/javadoc/apidocs/org/apache/storm/hbase/bolt/class-use/HBaseBolt.html). A comunicação com o site é realizada usando [socket.io-client.java](https://github.com/nkzawa/socket.io-client.java).

A seguir, há um diagrama da topologia.

![diagrama de topologia](./media/hdinsight-storm-sensor-data-analysis/sensoranalysis.png)

> [AZURE.NOTE] Isso é uma visão muito simplificada da topologia. Em tempo de execução, uma instância de cada componente é criada para cada partição para o Hub de Eventos que está sendo lido. Essas instâncias são distribuídas pelos nós do cluster e os dados são roteados entre elas da seguinte forma:
>
> * Os dados do spout para o analisador têm a carga balanceada.
> * Os dados do analisador para o Painel e para o HBase são agrupados por ID de Dispositivo para que as mensagens do mesmo dispositivo fluam sempre para o mesmo componente.

### Componentes da topologia

* **EventHub Spout**: o spout é fornecido como parte do Apache Storm versão 0.10.0 e superior.

    > [AZURE.NOTE] O spout de Hubs de Eventos usado neste exemplo exige um Storm no cluster HDInsight versão 3.3 ou 3.4. Para saber mais sobre como usar Hubs de Eventos com uma versão mais antiga do HDInsight, veja [Processar eventos dos Hubs de Eventos do Azure com o Storm no HDInsight](hdinsight-storm-develop-java-event-hub-topology.md).

* **ParserBolt.java**: os dados emitidos pelo spout são JSON brutos e, às vezes, mais de um evento é emitido por vez. Este bolt demonstra como ler os dados emitidos pelo spout e os emite para um novo fluxo como uma tupla que contém vários campos.

* **DashboardBolt.java**: demonstra como usar a biblioteca de cliente Socket.io para Java para enviar dados em tempo real para o painel da Web.

Este exemplo usa o framework do [Flux](https://storm.apache.org/releases/0.10.0/flux.html), então a definição de topologia está contida em arquivos YAML. Há dois deles:

* __no-hbase.yaml__ – use esse arquivo ao testar a topologia no ambiente de desenvolvimento. Ele não usa componentes HBase, já que você não pode acessar a API Java do HBase de fora da rede virtual na qual o cluster reside.

* __with-hbase.yaml__ – use esse arquivo ao implantar a topologia para o cluster Storm. Ele usa componentes do HBase, já que é executado na mesma rede virtual que o cluster HBase.

## Prepare o seu ambiente

Antes de usar este exemplo, você deve criar um Hub de Eventos do Azure que é lido pela topologia do Storm.

### Configurar o Hub de Eventos

O Hub de Eventos é a fonte de dados para este exemplo. Use as seguintes etapas para criar um novo Hub de Eventos.

1. Do [Portal do Azure](https://portal.azure.com), selecione **+ Novo** -> __Internet das Coisas__ -> __Hubs de Eventos__.

2. Na folha __Criar Namespace__, execute as seguintes tarefas:

    1. Insira um __Nome__ para o namespace.
    2. Selecione um tipo de preço. __Básico__ é suficiente para este exemplo.
    3. Selecione a __Assinatura__ do Azure a utilizar.
    4. Selecione um grupo de recursos existente ou crie um novo.
    5. Selecione a __Localização__ para o Hub de Eventos.
    6. Selecione __Fixar no painel__ e então clique em __Criar__.

3. Quando o processo de criação for concluído, a folha Hubs de Eventos para o namespace será exibida. Desse local, selecione __+ Adicionar Hub de Eventos__. Na folha __Criar Hub de Eventos__, insira o nome __sensordata__ e então selecione __Criar__. Mantenha os valores padrão nos outros campos.

4. Na folha Hubs de Eventos para o seu namespace, selecione __Hubs de Eventos__. Selecione a entrada __sensordata__.

5. Da folha para o Hub de Eventos sensordata, selecione __Políticas de acesso compartilhado__. Use o link __+ Adicionar__ para adicionar as políticas a seguir:


	| Nome da política | Declarações |
    | ----- | ----- |
	| dispositivos | Enviar |
	| storm | Escutar |

5. Selecione ambas as políticas e anote o valor __CHAVE PRIMÁRIA__. O valor será necessário para ambas as políticas em etapas futuras.

## Baixe e configure o projeto.

Use o seguinte comando para baixar o projeto do GitHub.

	git clone https://github.com/Blackmist/hdinsight-eventhub-example

Depois que o comando for concluído, você terá a seguinte estrutura de diretório:

	hdinsight-eventhub-example/
		TemperatureMonitor/ - this contains the topology
			resources/
                log4j2.xml - set logging to minimal
                no-hbase.yaml - topology definition for local testing
                with-hbase.yaml - topology definition that uses HBase in a virutal network
			src/ - the Java bolts
            dev.properties - contains configuration values for your environment
		dashboard/nodejs/ - this is the node.js web dashboard
		SendEvents/ - utilities to send fake sensor data

> [AZURE.NOTE] Este documento não traz todos os detalhes do código incluído nesse exemplo; no entanto, o código é totalmente comentado.

Abra o arquivo **hdinsight-eventhub-example/TemperatureMonitor/dev.properties** e adicione as informações do Hub de Eventos às seguintes linhas:

    eventhub.read.policy.name: storm
    eventhub.read.policy.key: KeyForTheStormPolicy
    eventhub.namespace: YourNamespace
    eventhub.name: sensordata

> [AZURE.NOTE] Este exemplo presume que você usou __storm__ como o nome da política que tem uma declaração __Listen__ e que o seu Hub de Eventos chama-se __sensordata__.

 Salve o arquivo depois de adicionar essas informações.

## Compile e teste localmente

Antes de testar, você deve iniciar o painel de controle para exibir a saída da topologia e gerar dados para armazenar no Hub de Eventos.

> [AZURE.IMPORTANT] O componente HBase dessa topologia não está ativo durante testes locais, pois a API do Java para o cluster HBase não pode ser acessada de fora da Rede Virtual do Azure que contém os clusters.

### Iniciar o aplicativo Web

1. Abra um novo terminal ou prompt de comando e altere os diretórios para **hdinsight-eventhub-exemplo/dashboard**.Use o seguinte comando para instalar as dependências exigidas pelo aplicativo da Web:

		npm install

2. Use o comando abaixo para iniciar o aplicativo Web:

		node server.js

	Você deve ver uma página semelhante a esta:

		Server listening at port 3000

2. Abra um navegador da Web e digite **http://localhost:3000/** como o endereço. Você verá uma página semelhante à seguinte:

	![Painel da Web](./media/hdinsight-storm-sensor-data-analysis/emptydashboard.png)

	Deixe esse prompt de comando ou terminal aberto. Após o teste, use Ctrl+C para parar o servidor Web.

### Inicie a geração de dados

> [AZURE.NOTE] As etapas desta seção usam o Node.js para que possam ser usadas em qualquer plataforma. Para obter outros exemplos de linguagem, confira o diretório **SendEvents**.

1. Abra um novo terminal, shell ou prompt de comando e altere os diretórios para **hdinsight-eventhub-example/SendEvents/nodejs**, então use o seguinte comando para instalar as dependências exigidas pelo aplicativo:

		npm install

2. Abra o arquivo **app.js** em um editor de texto e adicione as informações do Hub de Eventos obtidas anteriormente:

		// ServiceBus Namespace
		var namespace = 'YourNamespace';
		// Event Hub Name
		var hubname ='sensordata';
		// Shared access Policy name and key (from Event Hub configuration)
		var my_key_name = 'devices';
		var my_key = 'YourKey';
    
    > [AZURE.NOTE] Este exemplo presume que você usou __sensordata__ como o nome do seu Hub de Eventos e __dispositivos__ como o nome da política que tem uma declaração __Send__.

2. Use o seguinte comando para inserir novas entradas no Hub de Eventos:

		node app.js

	Você verá várias linhas de saída que contêm os dados enviados para o Hub de Eventos. Eles terão aparência semelhante a isto:

		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"0","Temperature":7}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"1","Temperature":39}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"2","Temperature":86}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"3","Temperature":29}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"4","Temperature":30}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"5","Temperature":5}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"6","Temperature":24}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"7","Temperature":40}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"8","Temperature":43}
		{"TimeStamp":"2015-02-10T14:43.05.00320Z","DeviceId":"9","Temperature":84}

### Iniciar a topologia

2. Abra um novo terminal, shell ou prompt de comando e altere os diretórios para __hdinsight-eventhub-example/TemperatureMonitor__, então use o seguinte comando para iniciar a topologia:

        mvn compile exec:java -Dexec.args="--local -R /no-hbase.yaml --filter dev.properties"
    
    Se você estiver usando o PowerShell, use o seguinte em vez disso:

        mvn compile exec:java "-Dexec.args=--local -R /no-hbase.yaml --filter dev.properties"

    > [AZURE.NOTE] Se você estiver em um sistema Unix/Linux/OS X e tiver [instalado Storm em seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), você poderá usar os seguintes comandos em vez disso:
    >
    > `mvn compile package` `storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /no-hbase.yaml`

	Isso inicia a topologia definida no arquivo __no-hbase.yaml__ em modo local. Os valores contidos no arquivo __dev.properties__ fornecem as informações de conexão para Hubs de Eventos. Uma vez iniciada, a topologia lê as entradas do Hub de Eventos e as envia para o painel em execução no computador local. Você deve ver linhas aparecendo no painel da Web semelhantes a estas:

	![Painel com dados](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

3. Enquanto o painel é executado, use o comando de `node app.js` das etapas anteriores para enviar novos dados para os Hubs de Eventos. Como os valores de temperatura são gerados aleatoriamente, o gráfico será atualizado para mostrar grandes mudanças de temperatura.

    > [AZURE.NOTE] Você deve estar no diretório __hdinsight-eventhub-example/SendEvents/Nodejs__ ao usar o comando `node app.js`.

3. Depois de verificar se funcionou, pare a topologia usando Ctrl+C. Você também pode usar Ctrl+C para parar o servidor Web local.

## Criar um cluster Storm e HBase

Para executar a topologia no HDInsight e habilitar o bolt HBase, você deve criar um novo cluster Storm e um cluster HBase. As etapas nesta seção usam um [o modelo do Azure Resource Manager](../resource-group-template-deploy.md) para criar uma nova Rede Virtual do Azure e um cluster Storm e HBase na rede virtual. O modelo também cria um aplicativo Web e implanta uma cópia do painel nele.

> [AZURE.NOTE] Uma rede virtual é usada para que a topologia em execução no cluster Storm possa se comunicar diretamente com o cluster HBase usando a API Java do HBase.

O modelo do Resource Manager usado neste documento está localizado em um contêiner de blob público em \_\_https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-hbase-storm-cluster-in-vnet.json__.

1. Clique no botão a seguir para entrar no Azure e abra o modelo do Resource Manager no portal do Azure.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-hbase-storm-cluster-in-vnet.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/pt-BR/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Na folha **Parâmetros**, insira o seguinte:

    ![Parâmetros do HDInsight](./media/hdinsight-storm-sensor-data-analysis/parameters.png)
    
    * **BASECLUSTERNAME**: esse valor será usado como o nome de base para os clusters Storm e HBase. Por exemplo, inserir __hdi__ criará um cluster Storm chamado __hdi storm__ e um cluster HBase chamado __hbase hdi__.
    * __CLUSTERLOGINUSERNAME__: o nome de usuário do administrador dos clusters Storm e HBase.
    * __CLUSTERLOGINPASSWORD__: a senha do usuário do administrador dos clusters Storm e HBase.
    * __SSHUSERNAME__: o usuário SSH a ser criado para os clusters Storm e HBase.
    * __SSHPASSWORD__: a senha para o usuário SSH dos clusters Storm e HBase.
    * __LOCATION__: a região na qual os clusters serão criados.
    
    Clique em __OK__ para salvar os parâmetros.
    
3. Use a seção __Grupo de recursos__ para criar um novo grupo de recursos ou selecione um existente.

4. No menu suspenso __Local do grupo de recursos__, selecione o mesmo local que você selecionou para o parâmetro __LOCATION__.

5. Selecione __Termos legais__ e, em seguida, selecione __Criar__.

6. Por fim, marque __Fixar no painel__ e selecione __Criar__. Levará cerca de 20 minutos para criar os clusters.

Quando os recursos tiverem sido criados, você será redirecionado para uma folha do grupo de recursos que contém os clusters e o painel da Web.

![Folha do grupo de recursos para rede virtual e clusters](./media/hdinsight-storm-sensor-data-analysis/groupblade.png)

> [AZURE.IMPORTANT] Observe que os nomes dos clusters HDInsight são __storm-BASENAME__ e __hbase-BASENAME__, onde BASENAME é o nome fornecido para o modelo. Você usará esses nomes em etapas posteriores ao se conectar aos clusters. Observe também que o nome do site do painel é __basename-dashboard__. Você usará esse nome posteriormente ao exibir o painel.

## Configurar o bolt do Painel

Para enviar dados para o painel implantado como um aplicativo Web, você deve modificar a seguinte linha no arquivo __dev.properties__:

    dashboard.uri: http://localhost:3000

Altere `http://localhost:3000` para `http://BASENAME-dashboard.azurewebsites.net` e salve o arquivo. Substitua __BASENAME__ pelo nome de base que você forneceu na etapa anterior. Você também pode usar o grupo de recursos criado anteriormente para selecionar o painel e exibir o URL.

## Criar uma tabela do HBase

Para armazenar dados no HBase, primeiro devemos criar uma tabela. Geralmente, é recomendável criar previamente os recursos que o Storm precisa gravar, pois a criação de recursos de dentro de uma topologia Storm pode resultar em várias cópias distribuídas do código que está tentando criar o mesmo recurso. Crie os recursos fora da topologia e use o Storm apenas para leitura/gravação e análise.

1. Use o SSH para se conectar ao cluster HBase usando o usuário SSH e a senha fornecidos ao modelo durante a criação do cluster. Por exemplo, caso esteja se conectando usando o comando `ssh`, você deve usar a sintaxe a seguir:

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net
    
    Neste comando, substitua __NOMEDOUSUÁRIO__ pelo nome de usuário SSH fornecido ao criar o cluster, e __NOMEDEBASE__ pelo nome de base que você forneceu. Quando solicitado, insira a senha do usuário SSH.

2. Na sessão do SSH, inicie o shell do HBase.

    	hbase shell
	
	Depois que o shell for carregado, você verá um prompt `hbase(main):001:0>`.

3. No shell do HBase, insira o seguinte comando para criar uma tabela na qual os dados do sensor serão armazenados:

    	create 'SensorData', 'cf'

4. Verifique se a tabela foi criada usando o seguinte comando:

    	scan 'SensorData'
		
	Isso retorna informações semelhantes ao exemplo a seguir, indicando que há 0 linhas na tabela.
	
		ROW                   COLUMN+CELL                                       0 row(s) in 0.1900 seconds

5. Digite o seguinte para sair do shell do HBase:

		exit

## Configurar o bolt do HBase

Para gravar no HBase usando o cluster Storm, você deve fornecer o bolt do HBase com os detalhes de configuração do cluster HBase. A maneira mais fácil de fazer isso é baixar o __hbase-site.xml__ do cluster e incluí-lo em seu projeto. Você também deve remover os comentários de várias dependências no arquivo __pom.xml__, que carrega o componente storm-hbase e as dependências necessárias.

> [AZURE.IMPORTANT] Baixe também o arquivo storm-hbase.jar fornecido em seu Storm no cluster HDInsight 3.3 ou 3.4; essa versão foi compilada para funcionar com o HBase 1.1.x, que é usado para o HBase em clusters HDInsight 3.3 e 3.4. Se você usar um componente storm-hbase de qualquer outro lugar, ele poderá ser compilado em uma versão antiga do HBase.

### Baixar o arquivo hbase-site.xml

No prompt de comando, use SCP para baixar o arquivo __hbase-site.xml__ do cluster. No exemplo a seguir, substitua __USERNAME__ pelo usuário SSH fornecido ao criar o cluster, e __BASENAME__ pelo nome de base que você forneceu anteriormente. Quando solicitado, insira a senha do usuário SSH. Substitua o `/path/to/TemperatureMonitor/resources/hbase-site.xml` pelo caminho desse arquivo no projeto TemperatureMonitor.

    scp USERNAME@hbase-BASENAME-ssh.azurehdinsight.net:/etc/hbase/conf/hbase-site.xml /path/to/TemperatureMonitor/resources/hbase-site.xml

Isso baixará o __hbase-site.xml__ para o caminho especificado.

### Baixar e instalar o componente storm-hbase

1. No um prompt de comando, use SCP para baixar o arquivo __storm-hbase.jar__ do cluster Storm. No exemplo a seguir, substitua __USERNAME__ pelo usuário SSH fornecido ao criar o cluster, e __BASENAME__ pelo nome de base que você forneceu anteriormente. Quando solicitado, insira a senha do usuário SSH.

        scp USERNAME@storm-BASENAME-ssh.azurehdinsight.net:/usr/hdp/current/storm-client/contrib/storm-hbase/storm-hbase*.jar .

    Isso baixará um arquivo chamado `storm-hbase-####.jar`, onde ### é o número da versão do Storm para esse cluster. Anote esse número, pois ele será usado posteriormente.

2. Use o seguinte comando para instalar esse componente no repositório Maven local no seu ambiente de desenvolvimento. Isso permite que o Maven localize o pacote ao compilar o projeto. Substitua __###__ pelo número da versão incluído no nome do arquivo.

        mvn install:install-file -Dfile=storm-hbase-####.jar -DgroupId=org.apache.storm -DartifactId=storm-hbase -Dversion=#### -Dpackaging=jar
    
    Se você estiver usando o PowerShell, use o seguinte comando:

        mvn install:install-file "-Dfile=storm-hbase-####.jar" "-DgroupId=org.apache.storm" "-DartifactId=storm-hbase" "-Dversion=####" "-Dpackaging=jar"

### Habilitar o componente storm-hbase no projeto

1. Abra o arquivo __TemperatureMonitor/pom.xml__ e exclua as seguintes linhas:

        <!-- uncomment this section to enable the hbase-bolt
        end comment for hbase-bolt section -->
    
    > [AZURE.IMPORTANT] Exclua apenas essas duas linhas; não exclua qualquer uma das linhas entre elas.
    
    Isso habilita vários componentes que são necessários para se comunicar com o HBase usando o bolt do hbase.

2. Localize as seguintes linhas e, em seguida, substitua __###__ pelo número da versão do arquivo storm-hbase baixado anteriormente.

        <dependency>
            <groupId>org.apache.storm</groupId>
            <artifactId>storm-hbase</artifactId>
            <version>####</version>
        </dependency>

    > [AZURE.IMPORTANT] O número da versão deve corresponder à versão usada ao instalar o componente no repositório Maven local, pois o Maven usa essas informações para carregar o componente ao criar o projeto.

2. Salve o arquivo __pom.xml__.

## Criar, empacotar e implantar a solução no HDInsight

No ambiente de desenvolvimento, use as etapas a seguir para implantar a topologia Storm no cluster storm.

1. Do diretório __TemperatureMonitor__, use o seguinte comando para executar um novo build e criar um pacote JAR do seu projeto:

		mvn clean compile package

	Isso criará um arquivo chamado **TemperatureMonitor-1.0-SNAPSHOT.jar** no diretório de **destino** do seu projeto.

2. Use o scp para carregar o arquivo __TemperatureMonitor-1.0-SNAPSHOT.jar__ no seu cluster Storm. No exemplo a seguir, substitua __USERNAME__ pelo usuário SSH fornecido ao criar o cluster, e __BASENAME__ pelo nome de base que você forneceu anteriormente. Quando solicitado, insira a senha do usuário SSH.

        scp target\TemperatureMonitor-1.0-SNAPSHOT.jar USERNAME@storm-BASENAME-ssh.azurehdinsight.net:TemperatureMonitor-1.0-SNAPSHOT.jar
    
    > [AZURE.NOTE] Pode levar vários minutos para que o arquivo seja carregado, pois ele terá um tamanho de vários megabytes.

    Use o scp para carregar o arquivo __dev.properties__, já que ele contém as informações usadas para conectar-se aos Hubs de Eventos e o painel.

        scp dev.properties USERNAME@storm-BASENAME-ssh.azurehdinsight.net:dev.properties

3. Depois que os arquivos forem carregados, conecte-se ao cluster usando SSH.

        ssh USERNAME@storm-BASENAME-ssh.azurehdinsight.net

4. Na sessão de SSH, use o seguinte comando para iniciar a topologia.

        storm jar TemperatureMonitor-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /with-hbase.yaml --filter dev.properties
    
    Isso iniciará a topologia usando a definição de topologia no arquivo __with-hbase.yaml__ e os valores de configuração no arquivo __dev.properties__.

3. Depois que a topologia for iniciada, abra um navegador no site publicado no Azure e use o comando `node app.js` para enviar dados ao Hub de Eventos. Você deve ver a atualização do painel da Web para exibir as informações.

	![painel Transações da Web](./media/hdinsight-storm-sensor-data-analysis/datadashboard.png)

## Exibir dados do HBase

Depois de enviar dados para a topologia usando `node app.js`, use as etapas a seguir para se conectar ao HBase e verificar se os dados foram gravados na tabela criada anteriormente.

1. Use SSH para conectar-se ao cluster HBase.

        ssh USERNAME@hbase-BASENAME-ssh.azurehdinsight.net

2. Na sessão do SSH, inicie o shell do HBase.

    	hbase shell
	
	Depois que o shell for carregado, você verá um prompt `hbase(main):001:0>`.

2. Exiba as linhas da tabela:

    	scan 'SensorData'
		
	Isso deve retornar informações semelhantes às seguintes, indicando que há 0 linhas na tabela.
	
		hbase(main):002:0> scan 'SensorData'
        ROW                             COLUMN+CELL
        \x00\x00\x00\x00               column=cf:temperature, timestamp=1467290788277, value=\x00\x00\x00\x04
        \x00\x00\x00\x00               column=cf:timestamp, timestamp=1467290788277, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x01               column=cf:temperature, timestamp=1467290788348, value=\x00\x00\x00M
        \x00\x00\x00\x01               column=cf:timestamp, timestamp=1467290788348, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x02               column=cf:temperature, timestamp=1467290788268, value=\x00\x00\x00R
        \x00\x00\x00\x02               column=cf:timestamp, timestamp=1467290788268, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x03               column=cf:temperature, timestamp=1467290788269, value=\x00\x00\x00#
        \x00\x00\x00\x03               column=cf:timestamp, timestamp=1467290788269, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x04               column=cf:temperature, timestamp=1467290788356, value=\x00\x00\x00>
        \x00\x00\x00\x04               column=cf:timestamp, timestamp=1467290788356, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x05               column=cf:temperature, timestamp=1467290788326, value=\x00\x00\x00\x0D
        \x00\x00\x00\x05               column=cf:timestamp, timestamp=1467290788326, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x06               column=cf:temperature, timestamp=1467290788253, value=\x00\x00\x009
        \x00\x00\x00\x06               column=cf:timestamp, timestamp=1467290788253, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x07               column=cf:temperature, timestamp=1467290788229, value=\x00\x00\x00\x12
        \x00\x00\x00\x07               column=cf:timestamp, timestamp=1467290788229, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x08               column=cf:temperature, timestamp=1467290788336, value=\x00\x00\x00\x16
        \x00\x00\x00\x08               column=cf:timestamp, timestamp=1467290788336, value=2015-02-10T14:43.05.00320Z
        \x00\x00\x00\x09               column=cf:temperature, timestamp=1467290788246, value=\x00\x00\x001
        \x00\x00\x00\x09               column=cf:timestamp, timestamp=1467290788246, value=2015-02-10T14:43.05.00320Z
        10 row(s) in 0.1800 seconds

    > [AZURE.NOTE] Essa operação de varredura retornará apenas um máximo de 10 linhas da tabela.

## Excluir os clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para excluir os clusters, o repositório e o aplicativo Web de uma vez, exclua o grupo de recursos que os contém.

## Próximas etapas

Agora, você aprendeu como usar o Storm para ler dados de Hubs de Eventos, armazená-los no HBase e visualizar as informações em um painel externo usando o Socket.io e o D3.js.

* Para obter mais exemplos de topologias Storm com o HDInsight, confira:

    * [Topologias de exemplo para Storm no HDInsight](hdinsight-storm-example-topology.md)

* Para saber mais sobre o Apache Storm, confira o site [Apache Storm](https://storm.incubator.apache.org/).

* Para saber mais sobre HBase com HDInsight, confira a [Visão geral do HBase com HDInsight](hdinsight-hbase-overview.md)

* Para saber mais sobre o Socket.IO, acesse o site [socket.io](http://socket.io/).

* Para saber mais sobre D3.js, confira [D3.js - Documentos controlados por dados](http://d3js.org/)

* Para saber mais sobre como criar topologias em Java, confira [Desenvolver topologias Java para Apache Storm no HDInsight](hdinsight-storm-develop-java-topology.md).

* Para saber mais sobre como se conectar ao cluster Storm, confira [Desenvolver topologias C# para Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md).

[azure-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0921_2016-->