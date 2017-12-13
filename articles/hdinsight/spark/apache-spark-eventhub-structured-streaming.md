---
title: Usar o streaming estruturado do Apache Spark com Hubs de Eventos no Azure HDInsight | Microsoft Docs
description: "Compile um exemplo de streaming do Apache Spark sobre como enviar dados de uma transmissão para o Hub de Eventos do Azure e receber esses eventos no cluster HDInsight Spark usando um aplicativo de escala."
keywords: streaming do apache spark, streaming do spark, exemplo de spark, exemplo de streaming do apache spark, exemplo do hub de evento do azure, exemplo de spark
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: a542295e91a641289fa4261920a08eddbad6a217
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2017
---
# <a name="apache-spark-structured-streaming-on-hdinsight-to-process-events-from-event-hubs"></a>Streaming Estruturado do Apache Spark no HDInsight para processar eventos do Hubs de Eventos

Neste artigo, você aprenderá a processar telemetria em tempo real usando o Streaming Estruturado do Spark. Para fazer isso, execute as seguintes etapas de alto nível:

1. Compilar e executar na sua estação de trabalho local um aplicativo de exemplo de Produtor de Eventos que gera eventos que serão enviados para o Hubs de Eventos.
2. Usar o [shell do Spark](apache-spark-shell.md) para definir e executar um aplicativo simples do Streaming Estruturado do Spark.

## <a name="prerequisites"></a>Pré-requisitos

* Uma assinatura do Azure. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster do Apache Spark no HDInsight. Para obter instruções, consulte o artigo sobre como [Criar clusters do Apache Spark no Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Um namespace dos Hubs de Eventos do Azure. Para saber mais, consulte [Criar um namespace dos Hubs de Eventos do Azure](apache-spark-eventhub-streaming.md#create-an-azure-event-hub).

* Kit de desenvolvimento Oracle Java. Você pode instalá-lo clicando [aqui](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).

* Apache Maven. Você pode baixá-lo [aqui](https://maven.apache.org/download.cgi). As instruções para instalar o Maven estão disponíveis [aqui](https://maven.apache.org/install.html).

## <a name="build-configure-and-run-the-event-producer"></a>Criar, configurar e executar o Produtor de Eventos
Nesta tarefa, você clona um aplicativo de exemplo que cria eventos aleatórios e os envia para um Hub de Eventos configurado. Esse aplicativo de exemplo está disponível no GitHub em [https://github.com/hdinsight/eventhubs-sample-event-producer](https://github.com/hdinsight/eventhubs-sample-event-producer).

1. Verifique se as ferramentas git estão instaladas. É possível baixá-las em [GIT Downloads](http://www.git-scm.com/downloads) (Downloads do GIT). 
2. Abra um prompt de comando ou shell de terminal e execute o seguinte comando em um diretório de sua escolha para clonar o projeto.
        
        git clone https://github.com/hdinsight/eventhubs-sample-event-producer.git eventhubs-client-sample

3. Isso cria uma nova pasta chamada eventhubs-client-sample. No shell ou no prompt de comando, navegue para essa pasta.
4. Execute o Maven para criar o aplicativo usando o seguinte comando:

          mvn package

5. No shell ou prompt de comando, navegue até o diretório de destino criado e que contém o arquivo ``com-microsoft-azure-eventhubs-client-example-0.2.0.jar``.
6. Em seguida, será necessário compilar na linha de comando para executar o Produtor de Eventos no seu Hub de Eventos. Para fazer isso, substitua os valores no comando da seguinte maneira:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "<namespace>" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "<policyKey>" --message-length 32 --thread-count 1 --message-count -1

7. Por exemplo, um comando completo seria semelhante ao seguinte:

        java -cp com-microsoft-azure-eventhubs-client-example-0.2.0.jar com.microsoft.azure.eventhubs.client.example.EventhubsClientDriver --eventhubs-namespace "sparkstreaming" --eventhubs-name "hub1" --policy-name "RootManageSharedAccessKey" --policy-key "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU=" --message-length 32 --thread-count 1 --message-count -1

8. O comando é iniciado e, se a configuração estiver correta, a saída relacionada com os eventos que estão sendo enviados para o seu Hub de Eventos será mostrada em alguns instantes, desta maneira:

        Map('policyKey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, 'eventhubsName -> hub1, 'policyName -> RootManageSharedAccessKey, 'eventhubsNamespace -> sparkstreaming, 'messageCount -> -1, 'messageLength -> 32, 'threadCount -> 1)
        Events per thread: -1 (-1 for unlimited)
        10 > Sun Jun 18 11:32:58 PDT 2017 > 1024 > 1024 > Sending event: ZZ93958saG5BUKbvUI9wHVmpuA2TrebS
        10 > Sun Jun 18 11:33:46 PDT 2017 > 2048 > 2048 > Sending event: RQorGRbTPp6U2wYzRSnZUlWEltRvTZ7R
        10 > Sun Jun 18 11:34:33 PDT 2017 > 3072 > 3072 > Sending event: 36Eoy2r8ptqibdlfCYSGgXe6ct4AyOX3
        10 > Sun Jun 18 11:35:19 PDT 2017 > 4096 > 4096 > Sending event: bPZma9V0CqOn6Hj9bhrrJT0bX2rbPSn3
        10 > Sun Jun 18 11:36:06 PDT 2017 > 5120 > 5120 > Sending event: H2TVD77HNTVyGsVcj76g0daVnYxN4Sqs

9. Deixe o Produtor de Eventos em execução enquanto você continua seguindo as etapas.

## <a name="run-spark-shell-on-your-hdinsight-cluster"></a>Executar o shell do Spark no cluster do HDInsight
Nesta tarefa, você se conectará ao nó de cabeçalho do cluster do HDInsight por meio do SSH, inicializará o shell do Spark e executará um aplicativo Spark Streaming que recupera e processa os eventos do Hubs de Eventos. 

Neste momento, seu cluster do HDInsight deve estar pronto. Caso contrário, será necessário esperar até que ele conclua o provisionamento. Quando estiver pronto, continue com as seguintes etapas:

1. Conecte-se ao cluster HDInsight usando SSH. Para obter instruções, consulte [Conectar ao HDInsight usando o SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

5. O aplicativo que você está criando exige o pacote Spark Streaming do Hubs de Eventos. Para executar o shell do Spark de forma que ele recupere automaticamente essa dependência da [Central do Maven](https://search.maven.org), verifique se o suprimento alternado entre os pacotes e as coordenadas do Maven são assim:

        spark-shell --packages "com.microsoft.azure:spark-streaming-eventhubs_2.11:2.1.0"

6. Quando o shell do Spark finalizar o carregamento, você verá:

        Welcome to
            ____              __
            / __/__  ___ _____/ /__
            _\ \/ _ \/ _ `/ __/  '_/
        /___/ .__/\_,_/_/ /_/\_\   version 2.1.0.2.6.0.10-29
            /_/
                
        Using Scala version 2.11.8 (OpenJDK 64-Bit Server VM, Java 1.8.0_131)
        Type in expressions to have them evaluated.
        Type :help for more information.

        scala> 

7. Copie o trecho de código a seguir para um editor de texto e modifique-o, para que ele contenha a Chave da Política e o Namespace definido, conforme apropriado para o seu Hub de Eventos.

        val eventhubParameters = Map[String, String] (
            "eventhubs.policyname" -> "RootManageSharedAccessKey",
            "eventhubs.policykey" -> "<policyKey>",
            "eventhubs.namespace" -> "<namespace>",
            "eventhubs.name" -> "hub1",
            "eventhubs.partition.count" -> "2",
            "eventhubs.consumergroup" -> "$Default",
            "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            "eventhubs.sql.containsProperties" -> "true"
            )

8. Cole o trecho modificado no prompt scala> em espera e pressione "Retornar". Você deve ver uma saída semelhante a:

        scala> val eventhubParameters = Map[String, String] (
            |       "eventhubs.policyname" -> "RootManageSharedAccessKey",
            |       "eventhubs.policykey" -> "2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU",
            |       "eventhubs.namespace" -> "sparkstreaming",
            |       "eventhubs.name" -> "hub1",
            |       "eventhubs.partition.count" -> "2",
            |       "eventhubs.consumergroup" -> "$Default",
            |       "eventhubs.progressTrackingDir" -> "/eventhubs/progress",
            |       "eventhubs.sql.containsProperties" -> "true"
            |     )
        eventhubParameters: scala.collection.immutable.Map[String,String] = Map(eventhubs.sql.containsProperties -> true, eventhubs.name -> hub1, eventhubs.consumergroup -> $Default, eventhubs.partition.count -> 2, eventhubs.progressTrackingDir -> /eventhubs/progress, eventhubs.policykey -> 2P1Q17Wd1rdLP1OZQYn6dD2S13Bb3nF3h2XZD9hvyyU, eventhubs.namespace -> hdiz-docs-eventhubs, eventhubs.policyname -> RootManageSharedAccessKey)

9. Em seguida, comece a criar uma consulta do Streaming Estruturado do Spark especificando a fonte. Cole o seguinte no shell do Spark e pressione "Retornar".

        val inputStream = spark.readStream.
        format("eventhubs").
        options(eventhubParameters).
        load()

10. Você deve ver uma saída semelhante a:

        inputStream: org.apache.spark.sql.DataFrame = [body: binary, offset: bigint ... 5 more fields]

11. Em seguida, crie a consulta de forma que ela grave sua saída no Console. Basta colar o seguinte no shell do Spark e pressionar "Retornar".

        val streamingQuery1 = inputStream.writeStream.
        outputMode("append").
        format("console").start().awaitTermination()

12. Você verá alguns lotes iniciarem com saídas semelhantes a estas:

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        [Stage 0:>                                                          (0 + 2) / 2]

13. Depois, os resultados da saída do processamento de cada microlote de eventos. 

        -------------------------------------------
        Batch: 0
        -------------------------------------------
        17/06/18 18:57:39 WARN TaskSetManager: Stage 1 contains a task of very large size (419 KB). The maximum recommended task size is 100 KB.
        +--------------------+------+---------+------------+---------+------------+----------+
        |                body|offset|seqNumber|enqueuedTime|publisher|partitionKey|properties|
        +--------------------+------+---------+------------+---------+------------+----------+
        |[7B 22 74 65 6D 7...|     0|        0|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   112|        1|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   224|        2|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   336|        3|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   448|        4|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   560|        5|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   672|        6|  1497734887|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   784|        7|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|   896|        8|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1008|        9|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1120|       10|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1232|       11|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1344|       12|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1456|       13|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1568|       14|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1680|       15|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1792|       16|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  1904|       17|  1497734888|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2016|       18|  1497734889|     null|        null|     Map()|
        |[7B 22 74 65 6D 7...|  2128|       19|  1497734889|     null|        null|     Map()|
        +--------------------+------+---------+------------+---------+------------+----------+
        only showing top 20 rows

14. À medida que novos eventos chegam do Produtor de Eventos, eles são processados por essa consulta do Streaming Estruturado.
15. Lembre-se de excluir o cluster do HDInsight quando terminar de executar este exemplo.



## <a name="see-also"></a>Consulte também

* [Visão geral do Spark Streaming](apache-spark-streaming-overview.md)













