<properties 
	pageTitle="Usar Hubs de Eventos do Azure com o Apache Spark no HDInsight para processar dados de streaming | Microsoft Azure" 
	description="Instruções passo a passo sobre como enviar dados de um fluxo para o Hub de Eventos do Azure e receber esses eventos no Spark usando um bloco de notas Zeppelin" 
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
	ms.date="12/08/2015" 
	ms.author="nitinme"/>


# Streaming Spark: processar eventos de Hubs de Eventos do Azure com o Apache Spark no HDInsight (Windows)

> [AZURE.NOTE] O HDInsight agora fornece clusters do Spark no Linux. Para obter informações sobre como executar um aplicativo de streaming em clusters do Spark no HDInsight no Linux, confira [Streaming do Spark: processar eventos de Hubs de Eventos do Azure com o Apache Spark no HDInsight (Linux)](hdinsight-apache-spark-eventhub-streaming.md).

Streaming Spark estende a API de núcleo do Spark para criar aplicativos de processamento de fluxo dimensionável, de alta taxa de transferência e com tolerância a falhas. Os dados podem ser ingeridos de várias fontes. Neste artigo, usamos Hubs de Eventos para ingerir dados. Hubs de Eventos é um sistema de ingestão altamente dimensionável, que pode receber milhões de eventos por segundo.

Neste tutorial, você aprenderá como criar um Hub de Eventos do Azure, como ingerir mensagens em um Hub de Eventos usando um aplicativo de console em C# e recuperá-los em paralelo usando um bloco de notas Zeppelin configurado para Apache Spark no HDInsight.

> [AZURE.NOTE] Para seguir as instruções neste artigo, você terá que usar as duas versões do portal do Azure. Para criar um Hub de Eventos, você usará o [portal Clássico do Azure](https://manage.windowsazure.com). Para trabalhar com o cluster HDInsight Spark, você usará o [portal do Azure](https://ms.portal.azure.com/).

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark. Para obter instruções, confira [Criar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Um [Hub de Eventos do Azure](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Uma estação de trabalho com o Microsoft Visual Studio 2013. Para obter instruções, confira [Instalar Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Criar Hub de Eventos do Azure

1. No [portal do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Barramento de Serviço** > **Hub de Eventos** | **Criação Personalizada**.

2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na **Seta** para continuar.

	![página 1 do assistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub.png "Criar um Hub de Eventos do Azure")

	> [AZURE.NOTE] Você deve selecionar o mesmo **Local** do cluster Apache Spark no HDInsight para reduzir latência e custos.

3. Na tela **Configurar Hub de Eventos**, insira os valores de **Contagem de Partição** e **Retenção de Mensagem** e clique na marca de seleção. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.

	![página 2 do assistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.create.event.hub2.png "Especificar dias de retenção e o tamanho da partição para o Hub de Eventos")

4. Clique no Hub de Eventos que você criou, clique em **Configurar** e crie duas políticas de acesso para o hub de eventos.

	<table>
	<tr><th>Nome</th><th>Permissões</th></tr>
	<tr><td>mysendpolicy</td><td>Enviar</td></tr>
	<tr><td>myreceivepolicy</td><td>Escutar</td></tr>
	</table>

	Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (**mysendpolicy**) e escutar (**myreceivepolicy**) para esse Hub de Eventos.

	![políticas](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policies.png "Criar políticas de Hub de Eventos")

	
5. Na mesma página, anote as chaves da política geradas para as duas políticas. Salve-as, pois serão usadas mais tarde.

	![chaves de política](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.keys.png "Salvar chaves de política")

6. Na página **Painel**, clique em **Informações de Conexão** na parte inferior para recuperar e salvar as cadeias de conexão para o Hub de Eventos usando as duas políticas.

	![chaves de política](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.policy.connection.strings.png "Salvar cadeias de conexão de política")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Receber mensagens no Spark no HDInsight usando Zeppelin

Nesta seção, você criará um bloco de anotações [Zeppelin](https://zeppelin.incubator.apache.org) para receber mensagens do Hub de Eventos no cluster do Spark no HDInsight.

### Alocando recursos para Zeppelin para aplicativo de streaming

Você deve fazer as seguintes considerações ao criar um aplicativo de streaming usando Zeppelin:

* **Partições do Hub de Eventos e núcleos alocados para o Zeppelin**. Nas etapas anteriores, você criou um Hub de Eventos com algumas partições. No aplicativo de streaming de Zeppelin que você criar abaixo, você especificará novamente o mesmo número de partições. Para transmitir com êxito os dados do Hub de Eventos usando o Zeppelin, o número de núcleos que você alocar para o Zeppelin deve ser duas vezes maior que o número de partições no Hub de Eventos.
* **O número mínimo de núcleos a alocar para o Zeppelin**. No aplicativo de streaming que você criar abaixo, você cria uma tabela temporária na qual armazena as mensagens que são transmitidas por seu aplicativo. Você, em seguida, usa uma instrução Spark SQL para ler mensagens desta tabela temporária. Para poder executar a instrução do Spark SQL, certifique-se de ter pelo menos dois núcleos alocados para o Zeppelin.

Se você combinar os dois requisitos anteriores, eis o que você obtém:

* O número mínimo de núcleos que você deve alocar para o Zeppelin é 2.
* O número de núcleos alocados sempre deve ser duas vezes o número de partições no Hub de Eventos. 

Para obter instruções sobre como alocar recursos em um cluster do Spark, confira [Gerenciar os recursos de cluster do Apache Spark no HDInsight](hdinsight-apache-spark-resource-manager-v1.md).

### Criar um aplicativo de streaming usando o Zeppelin

1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   

2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha do **Painel de Cluster**, clique em **Bloco de Anotações Zeppelin**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.

	> [AZURE.NOTE] Você também pode acessar o Bloco de Notas Zeppelin de seu cluster abrindo a seguinte URL no navegador. Substitua __CLUSTERNAME__ pelo nome do cluster:
	>
	> `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Bloco de Anotações** e, na lista suspensa, clique em **Criar Nova Anotação**.

	![Criar um novo bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.createnewnote.png "Criar um novo bloco de anotações do Zeppelin")

	Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com o nome começando por **Note XXXXXXXXX**. Clique no novo bloco de anotações.

3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações, se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

	![Status do bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.newnote.connected.png "Status do bloco de anotações do Zeppelin")

4. No parágrafo vazio criado por padrão no novo bloco de notas, cole o trecho a seguir e substitua os espaços reservados para usar a configuração do hub de eventos. Neste trecho, você recebe o fluxo do Hub de Eventos e o registra em uma tabela temporária, chamada **mytemptable**. Na próxima seção, iniciaremos o aplicativo do remetente. Em seguida, você pode ler os dados diretamente da tabela.

	> [AZURE.NOTE] No trecho de código a seguir, **eventhubs.checkpoint.dir** deve ser definido como um diretório em seu contêiner de armazenamento padrão. Se o diretório não existe, o aplicativo streaming o cria. Você pode especificar o caminho completo para o diretório, como "**wasb://container@storageaccount.blob.core.windows.net/mycheckpointdir/**", ou o caminho relativo para o diretório, como "**/mycheckpointdir**".

		import org.apache.spark.streaming.{Seconds, StreamingContext}
		import org.apache.spark.streaming.eventhubs.EventHubsUtils
		import sqlContext.implicits._
		
		val ehParams = Map[String, String](
		  "eventhubs.policyname" -> "<name of policy with listen permissions>",
		  "eventhubs.policykey" -> "<key of policy with listen permissions>",
		  "eventhubs.namespace" -> "<service bus namespace>",
		  "eventhubs.name" -> "<event hub in the service bus namespace>",
		  "eventhubs.partition.count" -> "1",
		  "eventhubs.consumergroup" -> "$default",
		  "eventhubs.checkpoint.dir" -> "/<check point directory in your storage account>",
		  "eventhubs.checkpoint.interval" -> "10"
		)
		
		val ssc =  new StreamingContext(sc, Seconds(10))
		val stream = EventHubsUtils.createUnionStream(ssc, ehParams)
		
		case class Message(msg: String)
		stream.map(msg=>Message(new String(msg))).foreachRDD(rdd=>rdd.toDF().registerTempTable("mytemptable"))

		stream.print
		ssc.start


##<a name="runapps"></a>Executar os aplicativos

1. No bloco de notas Zeppelin, execute o parágrafo com o trecho de código. Pressione **SHIFT + ENTER** ou o botão **Reproduzir** no canto superior direito.

	O status no canto direito do parágrafo deve progredir de PRONTO, PENDENTE, EM EXECUÇÃO para CONCLUÍDO. A saída será exibida na parte inferior do mesmo parágrafo. A captura de tela é semelhante ao seguinte:

	![Saída do trecho](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.code.output.png "Saída de trecho")

2. Execute o projeto do **Remetente** e pressione **Enter** na janela do console para começar a enviar mensagens para o Hub de Eventos.

3. No bloco de notas Zeppelin, em um novo parágrafo, insira o trecho a seguir para ler as mensagens recebidas no Spark.

		%sql 
		select * from mytemptable limit 10

	A captura de tela a seguir mostra as mensagens recebidas em **mytemptable**.

	![Receber as mensagens no Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.streaming.event.hub.zeppelin.output.png "Receber mensagens no bloco de notas Zeppelin")

4. Reinicie o interpretador de SQL do Spark para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

	![Reiniciar o interpretador do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/hdispark.zeppelin.restart.interpreter.png "Reiniciar o interpretador do Zeppelin")

##<a name="sparkstreamingha"></a>Execute o aplicativo de streaming com alta disponibilidade

O uso do Zeppelin para receber dados de streaming no cluster Spark no HDInsight é uma boa abordagem para criar um protótipo de seu aplicativo. No entanto, para executar o aplicativo de streaming em uma instalação de produção com alta disponibilidade e resiliência, você precisa fazer o seguinte:

1. Copiar o jar de dependência na conta de armazenamento associada ao cluster.
2. Criar um aplicativo de streaming.
3. RDP no cluster e copiar o jar do aplicativo no nó de cabeçalho do cluster.
3. RDP no cluster e executar o aplicativo no nó de cluster.

Instruções sobre como executar essas etapas e um exemplo de aplicativo de streaming podem ser baixados do GitHub em [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview-v1.md)
* [Início rápido: criar o Apache Spark no HDInsight e executar consultas interativas usando o Spark SQL](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning-v1.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools-v1.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager-v1.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md

<!---HONumber=AcomDC_0330_2016-->