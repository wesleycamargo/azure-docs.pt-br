<properties 
	pageTitle="Usar Hubs de Eventos do Azure com o Apache Spark no HDInsight para processar dados de streaming | Microsoft Azure" 
	description="Instruções passo a passo sobre como enviar dados de uma transmissão para o Hub de Eventos do Azure e receber esses eventos no Spark usando um aplicativo de escala" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/29/2015" 
	ms.author="nitinme"/>


# Streaming Spark: processar eventos de Hubs de Eventos do Azure com o Apache Spark no HDInsight (Linux)

Streaming Spark estende a API de núcleo do Spark para criar aplicativos de processamento de fluxo dimensionável, de alta taxa de transferência e com tolerância a falhas. Os dados podem ser ingeridos de várias fontes. Neste artigo, usamos Hubs de Eventos do Azure para ingerir dados. Hubs de Eventos é um sistema de ingestão altamente dimensionável, que pode receber milhões de eventos por segundo.

Neste tutorial, você aprenderá a criar um Hub de eventos do Azure, a receber mensagens em um Hub de eventos usando um aplicativo de console em Java e a recuperá-las em paralelo usando um aplicativo Spark escrito em Scala. Esse aplicativo consome os dados transmitidos por meio de Hubs de Eventos e os encaminha para saídas diferentes (Blob de Armazenamento do Azure, tabela Hive e tabela SQL).

> [AZURE.NOTE]Para seguir as instruções neste artigo, você terá que usar as duas versões do portal do Azure. Para criar um Hub de Eventos, você usará o [Portal do Azure](https://manage.windowsazure.com). Para trabalhar com o cluster HDInsight Spark, você usará o [Portal de Visualização do Azure](https://ms.portal.azure.com/).

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).
- Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Um Java IDE. Este artigo usa IntelliJ IDEA 15.0.1. Você pode instalá-lo clicando [aqui](https://www.jetbrains.com/idea/download/).
- Driver do Microsoft JDBC para SQL Server, v4.1 ou posterior. Isso é necessário para gravar os dados de evento em um banco de dados do SQL Server. Você pode instalá-lo clicando [aqui](https://msdn.microsoft.com/sqlserver/aa937724.aspx).
- Um banco de dados SQL do Azure. Para obter instruções, consulte [criar um banco de dados SQL em minutos](sql-database/sql-database-get-started.md)

## O que essa solução faz?

É assim que a solução de transmissão flui:

1. Crie um Hub de Eventos do Azure que vá receber uma transmissão de eventos.

2. Execute um aplicativo local autônomo que gere eventos e envie-os por push ao Hub de Eventos do Azure. O aplicativo de exemplo que faz isso é publicado em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples).

2. Execute um aplicativo de transmissão remotamente em um cluster Spark que leia os eventos de fluxo do Hub de Evento do Azure e envia-os para diferentes locais (Blob do Azure, tabela Hive e tabela do banco de dados SQL).

## Criar Hub de Eventos do Azure

1. No [portal do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Barramento de Serviço** > **Hub de Eventos** | **Criação Personalizada**.

2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na **Seta** para continuar.

	![página 1 do assistente](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub.png "Criar um Hub de Eventos do Azure")

	> [AZURE.NOTE]Você deve selecionar o mesmo **Local** do cluster Apache Spark no HDInsight para reduzir latência e custos.

3. Na tela **Configurar Hub de Eventos**, insira os valores de **Contagem de Partição** e **Retenção de Mensagem** e clique na marca de seleção. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.

	![página 2 do assistente](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Especificar dias de retenção e o tamanho da partição para o Hub de Eventos")

4. Clique no Hub de Eventos que você criou, clique em **Configurar** e crie duas políticas de acesso para o hub de eventos.

	<table>
<tr><th>Nome</th><th>Permissões</th></tr>
<tr><td>mysendpolicy</td><td>Enviar</td></tr>
<tr><td>myreceivepolicy</td><td>Escutar</td></tr>
</table>Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (**mysendpolicy**) e escutar (**myreceivepolicy**) para esse Hub de Eventos.

	![políticas](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Criar políticas de Hub de Eventos")

	
5. Na mesma página, anote as chaves da política geradas para as duas políticas. Salve-as, pois serão usadas mais tarde.

	![chaves de política](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Salvar chaves de política")

6. Na página **Painel**, clique em **Informações de Conexão** na parte inferior para recuperar e salvar as cadeias de conexão para o Hub de Eventos usando as duas políticas.

	![chaves de política](./media/hdinsight-apache-spark-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Salvar cadeias de conexão de política")

## Usar um aplicativo Scala para enviar mensagens ao Hub de Eventos

Nesta seção, você pode usar um aplicativo Scala autônomo local para enviar uma transmissão de eventos para o Hub de Eventos do Azure criado na etapa anterior. Este aplicativo está disponível no GitHub em [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer). As etapas aqui supõem que você já tenha bifurcado esse repositório GitHub.

1. Abra o aplicativo **EventhubsSampleEventProducer** no IntelliJ IDEA.
	
2. Compile o projeto. No menu **Criar**, clique em **Criar Projeto**. O jar de saída é criado em **\\out\\artifacts**.

>[AZURE.TIP]Você também pode usar uma opção disponível no IntelliJ IDEA para criar o projeto diretamente de um repositório GitHub. Para entender como usar essa abordagem, siga as instruções na próxima seção para obter orientação. Observe que muitas etapas descritas na próxima seção não serão aplicável ao aplicativo Scala que você cria nessa etapa. Por exemplo:

> * Você não terá de atualizar o POM para incluir a versão do Spark. Isso acontece porque não há nenhuma dependência no Spark para criar esse aplicativo
> * Você não precisará adicionar alguns jars de dependência à biblioteca de projeto. Isso ocorre porque os jars não são necessários para esse projeto.

## Atualizar o aplicativo de transmissão Scala para receber os eventos

Um aplicativo Scala de exemplo para receber o evento e encaminhá-lo para diferentes destinos está disponível em [https://github.com/hdinsight/spark-streaming-data-persistence-examples](https://github.com/hdinsight/spark-streaming-data-persistence-examples). Siga as etapas abaixo para atualizar o aplicativo e criar o jar de saída.

1. Inicie o IntelliJ IDEA e, na tela de inicialização, selecione **Fazer check-out do controle de versão** e, em seguida, clique em **Git**.
		
	![Obter fontes do Git](./media/hdinsight-apache-spark-eventhub-streaming/get-source-from-git.png)

2. Na caixa de diálogo **Clonar Repositório**, forneça a URL para o repositório Git do qual clonar, especifique o diretório para o qual clonar e clique em **Clonar**.

	![Clonar de Git](./media/hdinsight-apache-spark-eventhub-streaming/clone-from-git.png)

	
3. Siga as instruções até que o projeto estar completamente clonado. Pressione **Alt + 1** para abrir a **Exibição do Projeto**. O resultado deve ser parecido com o seguinte:

	![Exibição de projeto](./media/hdinsight-apache-spark-eventhub-streaming/project-view.png)
	
4. Abra o pom.xml e verifique se que a versão do Spark está correta. No nó <properties>, procure o trecho a seguir e verifique a versão do Spark.

		<scala.version>2.10.4</scala.version>
    	<scala.compat.version>2.10.4</scala.compat.version>
    	<scala.binary.version>2.10</scala.binary.version>
    	<spark.version>1.5.1</spark.version>

	Verifique se o valor **spark.version** está definido para **1.5.1**.

5. O aplicativo requer dois jars de dependência:

	* **Jar receptor EventHub**. Ele é necessário para o Spark receber as mensagens do hub de eventos. Este jar está disponível em seu cluster Spark Linux em `/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.1.2.3.2.1-12-jar-with-dependencies.jar`. Você pode usar pscp para copiar o jar para seu computador local.

			pscp sshuser@mysparkcluster-ssh.azurehdinsight.net/usr/hdp/current/spark-client/lib/spark-streaming-eventhubs-example-1.5.1.2.3.2.1-12-jar-with-dependencies.jar C:/eventhubjar

		Isso copiará o arquivo jar do cluster Spark para seu computador local.

	* **Jar do driver JDBC**. Isso é necessário para gravar as mensagens recebidas do hub de eventos em um Banco de Dados SQL do Azure. Você pode baixar v 4.1 ou posterior desse arquivo jar [daqui](https://msdn.microsoft.com/pt-BR/sqlserver/aa937724.aspx).
	

		Adicione referência a esses jars na biblioteca do projeto. Execute as seguintes etapas:

		1. Na janela IntelliJ IDEA em que o aplicativo abrir, clique em **Arquivo**, clique em **Estrutura do Projeto** e, em seguida, clique em **Bibliotecas**. 

			![adicionar dependências ausentes](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Adicionar jars de dependência ausente")

			Clique no ícone de adicionar (![adicionar ícone](./media/hdinsight-apache-spark-eventhub-streaming/add-icon.png)), clique em **Java** e, em seguida, navegue até o local onde você baixou o jar do receptor EventHub. Siga os prompts para adicionar o arquivo jar à biblioteca do projeto.

		1. Repita a etapa anterior para adicionar o jar JDBC também à biblioteca do projeto.
	
			![adicionar dependências ausentes](./media/hdinsight-apache-spark-eventhub-streaming/add-missing-dependency-jars.png "Adicionar jars de dependência ausente")

		1. Clique em **Aplicar**.

6. Crie o arquivo jar de saída. Execute as seguintes etapas.
	1. Na caixa de diálogo **Estrutura do Projeto**, clique em **Artefatos** e, em seguida, clique no sinal de mais. Na caixa de diálogo pop-up, clique em **JAR** e, em seguida, clique em **Dos módulos com dependências**.

		![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-1.png)

	1. Na caixa de diálogo **Criar JAR de Módulos**, clique no botão de reticências (![reticências](./media/hdinsight-apache-spark-eventhub-streaming/ellipsis.png)) em relação à **Classe Principal**.

	1. Na caixa de diálogo **Selecionar Classe Principal**, selecione qualquer uma das classes disponíveis e clique em **OK**.

		![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-2.png)

	1. Na caixa de diálogo **Criar JAR de Módulos**, certifique-se de que a opção **extrair para o JAR de destino** esteja selecionada e, em seguida, clique em **OK**. Isso cria um JAR único com todas as dependências.

		![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/create-jar-3.png)

	1. A guia **Layout de Saída** lista todos os jars incluídos como parte do projeto Maven. Você pode selecionar e excluir aqueles dos quais o aplicativo Scala não tem qualquer dependência direta. Para o aplicativo que estamos criando aqui, você pode remover tudo, exceto o último (**microsoft-spark-streaming-examples compile output**). Selecione os jars para excluir e, em seguida, clique no ícone **Excluir** (![excluir ícone](./media/hdinsight-apache-spark-eventhub-streaming/delete-icon.png)).

		![Criar JAR](./media/hdinsight-apache-spark-eventhub-streaming/delete-output-jars.png)

		Certifique-se de que a caixa **Compilar à criação** esteja marcada, o que garante que o jar seja criado sempre que o projeto for criado ou atualizado. Clique em **Aplicar** e em **OK**.

	1. Na guia **Layout de Saída**, à direita na parte inferior da caixa Elementos Disponíveis, você tem os dois jars de dependência adicionados anteriormente à biblioteca do projeto. Você deve adicioná-los à guia Layout de Saída. Clique com o botão direito em cada arquivo jar e, em seguida, clique em **Extrair para Raiz de Saída**.

		![Extrair o jar de dependência](./media/hdinsight-apache-spark-eventhub-streaming/extract-dependency-jar.png)

		Repita essa etapa para o outro jar de dependência também. A guia **Layout de Saída** deve ter esta aparência.

		![Guia de saída final](./media/hdinsight-apache-spark-eventhub-streaming/final-output-tab.png)

		Na caixa de diálogo **Estrutura do Projeto**, clique em **Aplicar** e, em seguida, clique em **OK**.

	1. Na barra de menus, clique em **Compilar** e, em seguida, clique em **Criar Projeto**. Você também pode clicar em **Compilar Artefatos** para criar o jar. O jar de saída é criado em **\\out\\artifacts**.

		![Criar JAR](./media/hdinsight-apache-spark-create-standalone-application/output.png)

## Executar os aplicativos remotamente em um cluster do Spark usando Livy

Usaremos Livy para executar o aplicativo de transmissão remotamente em um cluster do Spark. Para uma discussão detalhada de como usar Livy com cluster HDInsight Spark, consulte [Enviar trabalhos remotamente para um cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-livy-rest-interface.md). Antes de começar a executar os trabalhos remotos para transmitir eventos usando Spark, há algumas ações que você deve executar:

1. Inicie o aplicativo autônomo local para gerar eventos e enviar ao hub de eventos. Use o seguinte comando para fazer isso:

		java -cp EventhubsSampleEventProducer.jar com.microsoft.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "mysbnamespace" --eventhubs-name "myeventhub" --policy-name "mysendpolicy" --policy-key "<policy key>" --message-length 32 --thread-count 32 --message-count -1

2. Copiar o jar de transmissão (**microsoft-spark-streaming-Examples.jar**) para o armazenamento de Blobs do Azure associado ao cluster. Isso torna o jar acessível ao Livy. Você pode usar [**AzCopy**](storage/storage-use-azcopy.md), um utilitário de linha de comando, para fazer isso. Há muitos outros clientes que podem ser usados para carregar dados. É possível saber mais sobre eles em [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

3. Instale CURL no computador do qual você está executando a esses aplicativos. Usamos CURL para invocar os pontos de extremidade do Livy para executar os trabalhos remotamente.

### Execute os aplicativos para receber os eventos em um Blob de armazenamento do Azure como texto

Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputBlob.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputBlob.txt** são definidos da seguinte maneira:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsEventCount", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Vamos entender quais são os parâmetros no arquivo de entrada:

* **arquivo** é o caminho para o arquivo de jar do aplicativo na conta de armazenamento do Azure associada ao cluster.
* **className** é o nome da classe no jar.
* **args** é a lista de argumentos exigidos pela classe
* **numExecutors** é o número de núcleos usados pelo Spark para executar o aplicativo de transmissão. Isso sempre deve ser pelo menos duas vezes o número de partições do hub de eventos.
* **executorMemory**, **executorCores**, **driverMemory** são parâmetros usados para atribuir os recursos necessários para o aplicativo de transmissão.

>[AZURE.NOTE]Você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.
	
Quando você executar o comando, deve ver uma saída semelhante à seguinte:

	< HTTP/1.1 201 Created
	< Content-Type: application/json; charset=UTF-8
	< Location: /18
	< Server: Microsoft-IIS/8.5
	< X-Powered-By: ARR/2.5
	< X-Powered-By: ASP.NET
	< Date: Tue, 01 Dec 2015 05:39:10 GMT
	< Content-Length: 37
	<
	{"id":1,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

Anote a ID do lote na última linha da saída (no exemplo, é '1'). Para verificar se o aplicativo é executado com sucesso, você pode examinar sua conta de armazenamento do Azure associada ao cluster e deverá ver a pasta **/EventCount/EventCount10** criada ali. Essa pasta deve conter blobs que capturam o número de eventos processados dentro do período de tempo especificado para o parâmetro **batch-interval-in-seconds**.

O aplicativo continuará a ser executado até você eliminá-lo. Para fazer isso, use o seguinte comando:

	curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/1"

### Execute os aplicativos para receber os eventos em um Blob de armazenamento do Azure como JSON

Abra um prompt de comando, navegue até o diretório em que você instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e cluster nome):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputJSON.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputJSON.txt** são definidos da seguinte maneira:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureBlobAsJSON", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-store-folder", "/EventStore10"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto na etapa anterior. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) que são usadas como parâmetros. O aplicativo de streaming cria para você.

 Depois de executar o comando, você pode examinar a sua conta de armazenamento do Azure associada ao cluster, e deverá ver a pasta **/EventStore10** criada ali. Abra qualquer arquivo prefixado com **part-** e você deverá ver os eventos processados em um formato JSON.

### Executar os aplicativos para receber os eventos em uma tabela Hive

Para executar o aplicativo que transmite eventos em uma tabela Hive, são necessários alguns componentes adicionais. Estes são:

* datanucleus-api-jdo-3.2.6.jar
* datanucleus-rdbms-3.2.9.jar
* datanucleus-core-3.2.10.jar
* hive-site.xml

Os arquivos **.jar** estão disponíveis no seu cluster HDInsight Spark em `/usr/hdp/current/spark-client/lib`. O **hive-site.xml** está disponível em `/usr/hdp/current/spark-client/conf`.

Você pode usar [WinScp](http://winscp.net/eng/download.php) para copiar esses arquivos do cluster para seu computador local. Em seguida, você pode usar ferramentas para copiar esses arquivos para a sua conta de armazenamento associada ao cluster. Para obter mais informações sobre como carregar arquivos para a conta de armazenamento, consulte [Carregar dados para trabalhos do Hadoop no HDInsight](hdinsight-upload-data.md).

Depois de copiar os arquivos da sua conta de armazenamento do Azure, abra um prompt de comando, navegue até o diretório em que instalou CURL e execute o seguinte comando (substitua nome de usuário/senha e nome do cluster):

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputHive.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputHive.txt** são definidos da seguinte maneira:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToHiveTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--event-hive-table", "EventHiveTable10" ], "jars":["wasb:///example/jars/datanucleus-api-jdo-3.2.6.jar", "wasb:///example/jars/datanucleus-rdbms-3.2.9.jar", "wasb:///example/jars/datanucleus-core-3.2.10.jar"], "files":["wasb:///example/jars/hive-site.xml"], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Os parâmetros são semelhantes aos especificados para a saída de texto nas etapas anteriores. Novamente, você não precisa criar as pastas de saída (EventCheckpoint, EventCount/EventCount10) ou a tabela do Hive de saída (EventHiveTable10) usadas como parâmetros. O aplicativo de streaming cria para você. Observe que a opção **jars** e **arquivos** inclui caminhos para os arquivos .jar e o hive-site.xml copiados para a conta de armazenamento.

Para verificar se a tabela de hive foi criada com êxito, é possível executar SSH no cluster e executar consultas de Hive. Para obter instruções, consulte [usar o Hive com Hadoop no HDInsight com SSH](hdinsight-hadoop-use-hive-ssh.md). Quando você estiver conectado usando SSH, poderá executar o comando a seguir para verificar se a tabela de Hive, **EventHiveTable10**, é criada.

	show tables;

Você deverá ver um resultado semelhante ao seguinte:

	OK
	eventhivetable10
	hivesampletable

Você também pode executar uma consulta SELECT para exibir o conteúdo da tabela.

	SELECT * FROM eventhivetable10 LIMIT 10;

Você verá algo semelhante ao mostrado a seguir:

	ZN90apUSQODDTx7n6Toh6jDbuPngqT4c
	sor2M7xsFwmaRW8W8NDwMneFNMrOVkW1
	o2HcsU735ejSi2bGEcbUSB4btCFmI1lW
	TLuibq4rbj0T9st9eEzIWJwNGtMWYoYS
	HKCpPlWFWAJILwR69MAq863nCWYzDEw6
	Mvx0GQOPYvPR7ezBEpIHYKTKiEhYammQ
	85dRppSBSbZgThLr1s0GMgKqynDUqudr
	5LAWkNqorLj3ZN9a2mfWr9rZqeXKN4pF
	ulf9wSFNjD7BZXCyunozecov9QpEIYmJ
	vWzM3nvOja8DhYcwn0n5eTfOItZ966pa
	Time taken: 4.434 seconds, Fetched: 10 row(s)


### Executar os aplicativos para receber os eventos em uma tabela de Banco de Dados SQL do Azure

Antes de executar essa etapa, verifique se que você tem um banco de dados SQL Azure criado. Você precisará de valores para nome do banco de dados, nome do servidor de banco de dados e credenciais de administrador de banco de dados como parâmetros. Porém, você não precisa criar a tabela de banco de dados. O aplicativo de transmissão a cria para você.

Abra um prompt de comando, navegue até o diretório no qual você instalou CURL e execute o seguinte comando:

	curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\inputSQL.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

Os parâmetros no arquivo **inputSQL.txt** são definidos da seguinte maneira:

	{ "file":"wasb:///example/jars/microsoft-spark-streaming-examples.jar", "className":"com.microsoft.spark.streaming.examples.workloads.EventhubsToAzureSQLTable", "args":["--eventhubs-namespace", "mysbnamespace", "--eventhubs-name", "myeventhub", "--policy-name", "myreceivepolicy", "--policy-key", "<put-your-key-here>", "--consumer-group", "$default", "--partition-count", 10, "--batch-interval-in-seconds", 20, "--checkpoint-directory", "/EventCheckpoint", "--event-count-folder", "/EventCount/EventCount10", "--sql-server-fqdn", "<database-server-name>.database.windows.net", "--sql-database-name", "mysparkdatabase", "--database-username", "sparkdbadmin", "--database-password", "<put-password-here>", "--event-sql-table", "EventContent" ], "numExecutors":20, "executorMemory":"1G", "executorCores":1, "driverMemory":"2G" }

Para verificar se o aplicativo é executado com êxito, você pode se conectar ao Banco de Dados SQL do Azure usando o SQL Server Management Studio. Para obter instruções sobre como fazer isso, consulte [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio](sql-database/sql-database-connect-query-ssms). Quando você estiver conectado ao banco de dados, pode navegar para a tabela **EventContent** criada pelo aplicativo de transmissão. Você pode executar uma consulta rápida para obter os dados da tabela. Execute a consulta a seguir:

	SELECT * FROM EventCount

Você deverá ver uma saída semelhante ao seguinte:

	00046b0f-2552-4980-9c3f-8bba5647c8ee
	000b7530-12f9-4081-8e19-90acd26f9c0c
	000bc521-9c1b-4a42-ab08-dc1893b83f3b
	00123a2a-e00d-496a-9104-108920955718
	0017c68f-7a4e-452d-97ad-5cb1fe5ba81b
	001KsmqL2gfu5ZcuQuTqTxQvVyGCqPp9
	001vIZgOStka4DXtud0e3tX7XbfMnZrN
	00220586-3e1a-4d2d-a89b-05c5892e541a
	0029e309-9e54-4e1b-84be-cd04e6fce5ec
	003333cf-874f-4045-9da3-9f98c2b4ea49
	0043c07e-8d73-420a-9af7-1fcb94575356
	004a11a9-0c2c-4bc0-a7d5-2e0ebd947ab9

	
## <a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### Cenários

* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Spark com aprendizado de máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### Criar e executar aplicativos

* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### Extensões

* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

### Gerenciar recursos

* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=AcomDC_0107_2016-->