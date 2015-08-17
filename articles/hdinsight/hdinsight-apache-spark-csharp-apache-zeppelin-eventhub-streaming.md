<properties 
	pageTitle="Usar Hubs de Eventos do Azure com o Apache Spark no HDInsight para processar dados de streaming | Azure" 
	description="Instruções passo a passo sobre como enviar dados de um fluxo para o Hub de Eventos do Azure e receber esses eventos no Spark usando um bloco de notas Zeppelin" 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/10/2015" 
	ms.author="nitinme"/>


# Streaming Spark: processar eventos de Hubs de Eventos do Azure com o Apache Spark no HDInsight

Streaming Spark estende a API de núcleo do Spark para criar aplicativos de processamento de fluxo dimensionável, de alta taxa de transferência e com tolerância a falhas. Os dados podem ser ingeridos de várias fontes. Neste artigo, usamos Hubs de Eventos para ingerir dados. Hubs de Eventos é um sistema de ingestão altamente dimensionável, que pode receber milhões de eventos por segundo.

Neste tutorial, você aprenderá como criar um Hub de Eventos do Azure, como ingerir mensagens em um Hub de Eventos usando um aplicativo de console em C# e recuperá-los em paralelo usando um bloco de notas Zeppelin configurado para Apache Spark no HDInsight.

**Pré-requisitos:**

Você deve ter o seguinte:

- Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster do Apache Spark. Para obter instruções, consulte [Provisionar clusters do Apache Spark no Azure HDInsight](hdinsight-apache-spark-provision-clusters.md).
- Um [Hub de Eventos do Azure](service-bus-event-hubs-csharp-ephcs-getstarted.md).
- Uma estação de trabalho com o Microsoft Visual Studio 2013. Para obter instruções, consulte [Instalar o Visual Studio](https://msdn.microsoft.com/library/e2h7fzkw.aspx).

##<a name="createeventhub"></a>Criar Hub de Eventos do Azure

1. No [portal do Azure](https://manage.windowsazure.com), selecione **NOVO** > **Barramento de Serviço** > **Hub de Eventos** | **Criação Personalizada**.

2. Na tela **Adicionar um novo Hub de Eventos**, insira um **Nome do Hub de Eventos**, selecione a **Região** na qual criar o hub e crie um novo namespace ou selecione um existente. Clique na **Seta** para continuar.

	![página 1 do assistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub.png "Criar um Hub de Eventos do Azure")

	> [AZURE.NOTE]Você deve selecionar o mesmo **Local** do cluster Apache Spark no HDInsight para reduzir latência e custos.

3. Na tela **Configurar Hub de Eventos**, insira os valores de **Contagem de Partição** e **Retenção de Mensagem** e clique na marca de seleção. Para este exemplo, use uma contagem de partições de 10 e uma retenção de mensagens de 1. Observe a contagem de partições, pois você precisará desse valor posteriormente.

	![página 2 do assistente](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Create.Event.Hub2.png "Especificar dias de retenção e o tamanho da partição para o Hub de Eventos")

4. Clique no Hub de Eventos que você criou, clique em **Configurar** e crie duas políticas de acesso para o hub de eventos.

	<table>
<tr><th>Nome</th><th>Permissões</th></tr>
<tr><td>mysendpolicy</td><td>Enviar</td></tr>
<tr><td>myreceivepolicy</td><td>Escutar</td></tr>
</table>Depois de criar permissões, selecione o ícone **Salvar** na parte inferior da página. Isso cria políticas de acesso compartilhado que serão usadas para enviar (**mysendpolicy**) e escutar (**myreceivepolicy**) para esse Hub de Eventos.

	![políticas](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policies.png "Criar políticas de Hub de Eventos")

	
5. Na mesma página, anote as chaves da política geradas para as duas políticas. Salve-as, pois serão usadas mais tarde.

	![chaves de política](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Keys.png "Salvar chaves de política")

6. Na página **Painel**, clique em **Informações de Conexão** do botão para recuperar e salvar as cadeias de conexão para o Hub de Eventos usando as duas políticas.

	![chaves de política](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Policy.Connection.Strings.png "Salvar cadeias de conexão de política")

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

##<a name="receivezeppelin"></a>Receber mensagens no Spark no HDInsight usando Zeppelin

Nesta seção, você criará um bloco de notas [Zeppelin](https://zeppelin.incubator.apache.org) para receber mensagens do Hub de Eventos no cluster Spark no HDInsight.

1. Inicie o bloco de anotações do Zeppelin. Selecione o cluster Spark no portal do Azure e na barra de tarefas do portal, na parte inferior, clique em **Bloco de Notas Zeppelin**. Quando solicitado, insira as credenciais de administrador para o cluster Spark. Siga as instruções na página que é aberta para iniciar o bloco de anotações.

2. Crie um novo bloco de anotações. No painel de cabeçalho, clique em **Bloco de Notas** e na lista suspensa, clique em **Criar Nova Nota**.

	![Criar um novo bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.CreateNewNote.png "Criar um novo bloco de anotações do Zeppelin")

	Na mesma página, sob o título **Notebook**, você verá um novo bloco de anotações com nomes começando com **Anotação XXXXXXXXX**. Clique no novo bloco de anotações.

3. Na página da Web para o novo bloco de anotações, clique no título e altere o nome do bloco de anotações, se desejar. Pressione ENTER para salvar a alteração do nome. Além disso, verifique se o cabeçalho do bloco de anotações mostra um status **Conectado** no canto superior direito.

	![Status do bloco de anotações do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.NewNote.Connected.png "Status do bloco de anotações do Zeppelin")

4. No parágrafo vazio criado por padrão no novo bloco de notas, cole o trecho a seguir e substitua os espaços reservados para usar a configuração do hub de eventos. Neste trecho, você recebe o fluxo do Hub de Eventos e o registra em uma tabela temporária, chamada **mytemptable**. Na próxima seção, iniciaremos o aplicativo do remetente. Em seguida, você pode ler os dados diretamente da tabela.

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

	![Saída do trecho](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Code.Output.png "Saída de trecho")

2. Execute o projeto do **Remetente** e pressione **Enter** na janela do console para começar a enviar mensagens para o Hub de Eventos.

3. No bloco de notas Zeppelin, em um novo parágrafo, insira o trecho a seguir para ler as mensagens recebidas no Spark.

		%sql select * from mytemptable limit 10

	A captura de tela a seguir mostra as mensagens recebidas em **mytemptable**.

	![Receber as mensagens no Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Streaming.Event.Hub.Zeppelin.Output.png "Receber mensagens no bloco de notas Zeppelin")

4. Reinicie o interpretador de SQL do Spark para sair do aplicativo. Clique na guia **Interpretador** na parte superior e, para o interpretador do Spark, clique em **Reiniciar**.

	![Reiniciar o interpretador do Zeppelin](./media/hdinsight-apache-spark-csharp-apache-zeppelin-eventhub-streaming/HDI.Spark.Zeppelin.Restart.Interpreter.png "Reiniciar o interpretador do Zeppelin")

##<a name="sparkstreamingha"></a>Execute o aplicativo de streaming com alta disponibilidade

O uso do Zeppelin para receber dados de streaming no cluster Spark no HDInsight é uma boa abordagem para criar um protótipo de seu aplicativo. No entanto, para executar o aplicativo de streaming em uma instalação de produção com alta disponibilidade e resiliência, você precisa fazer o seguinte:

1. Copiar o jar de dependência na conta de armazenamento associada ao cluster.
2. Criar um aplicativo de streaming.
3. RDP no cluster e copiar o jar do aplicativo no nó de cabeçalho do cluster.
3. RDP no cluster e executar o aplicativo no nó de cluster.

Instruções sobre como executar essas etapas e um exemplo de aplicativo de streaming podem ser baixados do GitHub em [https://github.com/hdinsight/hdinsight-spark-examples](https://github.com/hdinsight/hdinsight-spark-examples).


##<a name="seealso"></a>Consulte também


* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)
* [Início rápido: provisionar o Apache Spark no HDInsight e executar consultas interativas usando SQL do Spark](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md)
* [Usar o Spark no HDInsight para criar aplicativos de aprendizado de máquina](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)


[hdinsight-versions]: ../hdinsight-component-versioning/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: ../storage-create-storage-account/

<!---HONumber=06-->