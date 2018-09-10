---
title: 'Tutorial: Processar dados do Hubs de Eventos do Azure com o Apache Spark no Azure HDInsight '
description: Conectar-se ao Apache Spark no HDInsight do Azure para Hubs de Eventos do Azure e processar dados de fluxo.
services: hdinsight
ms.service: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.custom: hdinsightactive,mvc
ms.topic: conceptual
ms.date: 06/14/2018
ms.openlocfilehash: 9cdb5ae31e2743b5ebe877ddd8d6680423e3d9b2
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046245"
---
# <a name="tutorial-process-tweets-using-azure-event-hubs-and-spark-in-hdinsight"></a>Tutorial: Processar tweets usando Hubs de Eventos do Azure e o Spark no HDInsight

Neste tutorial, você aprenderá como criar um aplicativo de streaming do Apache Spark para enviar tweets para um hub de eventos do Azure e criar outro aplicativo para ler os tweets do hub de eventos. Para obter uma explicação detalhada do streaming Spark, veja [Visão geral do streaming do Apache Spark](http://spark.apache.org/docs/latest/streaming-programming-guide.html#overview). O HDInsight traz os mesmos recursos de streaming para um cluster Spark no Azure.

Neste tutorial, você aprenderá como:
> [!div class="checklist"]
> * Enviar mensagens para o Hub de Eventos do Azure
> * Ler mensagens para o Hub de Eventos do Azure

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

* **Concluir o artigo [Tutorial: Carregar dados e executar consultas em um cluster do Apache Spark no Azure HDInsight](./apache-spark-load-data-run-query.md)**.

## <a name="create-a-twitter-application"></a>Criar um aplicativo do Twitter

Para receber uma transmissão de tweets, crie um aplicativo no Twitter. Siga as instruções para criar um aplicativo do Twitter e escrever os valores necessários para concluir este tutorial.

1. Navegue até o [Gerenciamento de Aplicativo do Twitter](https://apps.twitter.com/).
2. Selecione **Criar novo aplicativo**.
3. Forneça os seguintes valores:

    - Nome: forneça o nome do aplicativo. O valor usado para este tutorial é **HDISparkStreamApp0423**. Esse nome deve ser um nome exclusivo.
    - Descrição: forneça uma breve descrição do aplicativo. O valor usado para este tutorial é **Um aplicativo de streaming simples do HDInsight Spark**.
    - Site: forneça o site do aplicativo. Ele não precisa ser um site válido.  O valor usado para este tutorial é **http://www.contoso.com**.
    - URL de retorno de chamada: você pode deixar em branco.

4. Selecione **Sim, eu li e concordo com o Contrato de Desenvolvedor do Twitter** e, em seguida, selecione **Criar seu aplicativo Twitter**.
5. Selecione a guia **Chaves e Tokens de Acesso** .
6. Selecione **Criar o token de acesso** no final da página.
7. Anote os seguintes valores da página.  Você precisará desses valores mais tarde neste tutorial:

    - **Chave do consumidor (chave de API)**    
    - **Segredo do consumidor (segredo de API)**  
    - **Token de acesso**
    - **Segredo do token de acesso**   

## <a name="create-an-azure-event-hub"></a>Criar um Hub de Eventos do Azure

Você pode usar esse hub de eventos para armazenar tweets.

1. Entre no [Portal do Azure](https://ms.portal.azure.com).
2. Selecione **Criar um recurso** na parte superior esquerda da tela.
3. Selecione **Internet das Coisas** e, em seguida, selecione **Hubs de Eventos**.

    ![Criar hub de eventos para exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-create-event-hub-for-spark-streaming.png "Criar hub de eventos para exemplo de streaming do Spark")
4. Insira os seguintes valores para o novo namespace de hub de evento:

    - **Nome**: Insira um nome para o hub de evento.  O valor usado para este tutorial é **myeventhubns20180403**.
    - **Camada de preço**: selecione **Standard**.
    - **Grupo de recursos**: você tem a opção de criar um novo ou selecionar o grupo de recursos para o cluster do Spark. 
    - **Local**: selecione o mesmo **Local** como o seu cluster Apache Spark no HDInsight para reduzir latência e custos.

    ![Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-name-for-spark-streaming.png "Fornecer um nome ao hub de eventos para o exemplo de streaming do Spark")
5. Selecione **Criar** para criar o namespace.

6. Abra o namespace de hub de eventos usando as instruções a seguir:

    1. No portal do Azure, selecione **Todos os serviços**.
    2. Na caixa de filtro, digite **hubs de evento**.
    3. Clique duas vezes no namespace que você criou.
    4. Selecione **+ Hub de Eventos**.

6. Na lista de namespaces dos Hubs de Eventos, selecione o namespace recém-criado.      
5. Selecione **Hubs de Eventos** e, em seguida, selecione **+ Hub de eventos** para criar um novo Hub de Eventos.
  

6. Insira os valores a seguir:

    - Nome: dê um nome ao seu Hub de Evento.
    - Contagem de partição: 10
    - Retenção de mensagem: 1. 
   
    ![Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-provide-event-hub-details-for-spark-streaming-example.png "Fornecer detalhes do hub de eventos para o exemplo de streaming do Spark")

7. Selecione **Criar**.
8. Selecione **Políticas de acesso compartilhado** para o namespace (Observe que não são as políticas de acesso compartilhado de hub de evento) e, em seguida, selecione **RootManageSharedAccessKey**.
    
     ![Definir políticas de Hub de Eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-set-event-hub-policies-for-spark-streaming-example.png "Definir políticas de Hub de Eventos para o exemplo de streaming do Spark")

9. Salve os valores de **Chave primária** e **Chave primária de cadeia de caracteres de conexão** para usar posteriormente no tutorial.

     ![Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark](./media/apache-spark-eventhub-streaming/hdinsight-view-event-hub-policy-keys.png "Ver chaves de política do Hub de Eventos para o exemplo de streaming do Spark")


## <a name="send-tweets-to-the-event-hub"></a>Enviar tweets para o hub de eventos

Você precisa criar um bloco de anotações do Jupyter e nomeá-lo **SendTweetsToEventHub**. 

1. Execute o código a seguir para adicionar as bibliotecas Maven externas:

    ```
    %%configure
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0,org.twitter4j:twitter4j-core:4.0.6"}}
    ```

2. Execute o código a seguir para enviar tweets para o hub de eventos:

    ```
    import java.util._
    import scala.collection.JavaConverters._
    import java.util.concurrent._
    
    import org.apache.spark._
    import org.apache.spark.streaming._
    import org.apache.spark.eventhubs.ConnectionStringBuilder

    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    import com.microsoft.azure.eventhubs._
    val pool = Executors.newFixedThreadPool(1)
    val eventHubClient = EventHubClient.create(connStr.toString(), pool)
    
    def sendEvent(message: String) = {
          val messageData = EventData.create(message.getBytes("UTF-8"))
          eventHubClient.get().send(messageData)
          println("Sent event: " + message + "\n")
    }
    
    import twitter4j._
    import twitter4j.TwitterFactory
    import twitter4j.Twitter
    import twitter4j.conf.ConfigurationBuilder

    // Twitter application configurations
    // Replace values below with yours   
    val twitterConsumerKey = "<CONSUMER KEY>"
    val twitterConsumerSecret = "<CONSUMER SECRET>"
    val twitterOauthAccessToken = "<ACCESS TOKEN>"
    val twitterOauthTokenSecret = "<TOKEN SECRET>"
    
    val cb = new ConfigurationBuilder()
    cb.setDebugEnabled(true).setOAuthConsumerKey(twitterConsumerKey).setOAuthConsumerSecret(twitterConsumerSecret).setOAuthAccessToken(twitterOauthAccessToken).setOAuthAccessTokenSecret(twitterOauthTokenSecret)
    
    val twitterFactory = new TwitterFactory(cb.build())
    val twitter = twitterFactory.getInstance()

    // Getting tweets with keyword "Azure" and sending them to the Event Hub in realtime!
    
    val query = new Query(" #Azure ")
    query.setCount(100)
    query.lang("en")
    var finished = false
    while (!finished) {
      val result = twitter.search(query)
      val statuses = result.getTweets()
      var lowestStatusId = Long.MaxValue
      for (status <- statuses.asScala) {
        if(!status.isRetweet()){
          sendEvent(status.getText())
        }
        lowestStatusId = Math.min(status.getId(), lowestStatusId)
        Thread.sleep(2000)
      }
      query.setMaxId(lowestStatusId - 1)
    }
    
    // Closing connection to the Event Hub
    eventHubClient.get().close()
    ```

3. Abra o hub de eventos no portal do Azure.  Em **Visão geral**, você deverá ver alguns gráficos mostrando as mensagens enviadas para o hub de eventos.

## <a name="read-tweets-from-the-event-hub"></a>Ler tweets do hub de eventos

Você precisa criar outro bloco de anotações do Jupyter e nomeá-lo **ReadTweetsFromEventHub**. 

1. Execute o código a seguir para adicionar uma biblioteca Maven externa:

    ```
    %%configure -f
    {"conf":{"spark.jars.packages":"com.microsoft.azure:azure-eventhubs-spark_2.11:2.2.0"}}
    ```
2. Execute o código a seguir para ler tweets do hub de eventos:

    ```
    import org.apache.spark.eventhubs._
    // Event hub configurations
    // Replace values below with yours        
    val eventHubName = "<Event hub name>"
    val eventHubNSConnStr = "<Event hub namespace connection string>"
    val connStr = ConnectionStringBuilder(eventHubNSConnStr).setEventHubName(eventHubName).build 
    
    val customEventhubParameters = EventHubsConf(connStr).setMaxEventsPerTrigger(5)
    val incomingStream = spark.readStream.format("eventhubs").options(customEventhubParameters.toMap).load()
    //incomingStream.printSchema    
    
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._
    
    // Event Hub message format is JSON and contains "body" field
    // Body is binary, so you cast it to string to see the actual content of the message
    val messages = incomingStream.withColumn("Offset", $"offset".cast(LongType)).withColumn("Time (readable)", $"enqueuedTime".cast(TimestampType)).withColumn("Timestamp", $"enqueuedTime".cast(LongType)).withColumn("Body", $"body".cast(StringType)).select("Offset", "Time (readable)", "Timestamp", "Body")
    
    messages.printSchema
    
    messages.writeStream.outputMode("append").format("console").option("truncate", false).start().awaitTermination()
    ```

## <a name="clean-up-resources"></a>Limpar recursos

Com o HDInsight, seus dados são armazenados no Armazenamento do Azure ou no Azure Data Lake Store, assim você poderá excluir um cluster quando ele não estiver em uso. Você também é cobrado por um cluster HDInsight, mesmo quando ele não está em uso. Como os encargos para o cluster são muitas vezes maiores do que os encargos para armazenamento, faz sentido, do ponto de vista econômico, excluir os clusters quando não estiverem em uso. Se você planeja trabalhar no próximo tutorial imediatamente, convém manter o cluster.

Abra o cluster no portal do Azure e selecione **Excluir**.

![Excluir o cluster HDInsight](./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png "Excluir o cluster HDInsight")

Também é possível selecionar o nome do grupo de recursos para abrir a página do grupo de recursos, e depois selecionar **Excluir grupo de recursos**. Ao excluir o grupo de recursos, você exclui o cluster Spark do HDInsight e a conta de armazenamento padrão.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

* Ler mensagem de um hub de eventos.
Avance para o próximo artigo para ver que você pode criar uma aplicativo de aprendizado de máquina. 

> [!div class="nextstepaction"]
> [Criar um aplicativo de aprendizado de máquina](./apache-spark-ipython-notebook-machine-learning.md)


