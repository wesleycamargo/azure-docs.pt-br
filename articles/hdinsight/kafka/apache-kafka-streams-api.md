---
title: Usar API de Streams do Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Saiba como usar a API de Streams do Apache Kafka com o Kafka no HDInsight. Essa API permite realizar processamento de fluxo entre tópicos no Kafka."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: larryfr
ms.openlocfilehash: 1ea20eceb28fead003c7279632b1e75ae1fd3553
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-streams-api"></a>API de streams do Apache Kafka

Saiba como criar um aplicativo que usa a API de Streams do Kafka e executá-lo com o Kafka no HDInsight.

Ao trabalhar com o Apache Kafka, o processamento de fluxo normalmente é feito usando o Apache Spark ou Storm. A versão Kafka 0.10.0 (no HDInsight 3.5 e 3.6) introduziu a API de Streams do Kafka. Essa API permite transformar fluxos de dados entre tópicos de entrada e saída, usando um aplicativo executado no Kafka. Em alguns casos, isso pode ser uma alternativa para criar uma solução de streaming Spark ou Storm. Para obter mais informações sobre Streams do Kafka, consulte a documentação[Introdução a Streams](https://kafka.apache.org/10/documentation/streams/) em Apache.org.

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Você deve ter os seguintes componentes instalados no ambiente de desenvolvimento:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Um cliente SSH e o comando `scp`. Para saber mais, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurar o ambiente de implantação

Esse exemplo requer Kafka no HDInsight 3.6. Para saber como criar um Kafka no Cluster HDInsight, consulte o documento [Iniciar com Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

Utilize as seguintes etapas para criar e implantar o projeto para o Kafka no Cluster HDInsight.

1. Baixe os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Altere os diretórios para o diretório `Streaming` e utilize o seguinte comando para criar um pacote Jar:

    ```bash
    mvn clean package
    ```

    Esse comando cria um diretório chamado `target`, que contém um arquivo chamado `kafka-streaming-1.0-SNAPSHOT.jar`.

3. Utilize o seguinte comando para copiar o arquivo `kafka-streaming-1.0-SNAPSHOT.jar` para o Cluster HDInsight:
   
    ```bash
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-streaming.jar
    ```
   
    Substitua **SSHUSER** pelo usuário do SSH do cluster e substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira a senha para a conta de usuário SSH. Para saber mais sobre como usar o `scp` com HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run-the-example"></a>Executar o exemplo

1. Para abrir uma conexão SSH para o cluster, use o seguinte comando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua **SSHUSER** pelo usuário do SSH do cluster e substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira a senha para a conta de usuário SSH. Para saber mais sobre como usar o `scp` com HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

4. Para criar os tópicos do Kafka usados por esse exemplo, utilize os seguintes comandos:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    ```

    Substitua __o nome do seu cluster__ pelo nome do Cluster HDInsight. Quando solicitado, insira a senha da conta de logon do Cluster HDInsight.

    > [!NOTE]
    > Se o seu logon do cluster for diferente do valor padrão de `admin`, substitua o valor `admin`nos comandos anteriores pelo nome de logon do cluster.

5. Para executar esse exemplo, será necessário realizar três etapas:

    * Iniciar a solução de Streams contida em`kafka-streaming.jar`.
    * Inicie um produtor que faça gravação para o tópico `test`.
    * Iniciar um consumidor de modo que seja possível visualizar a saída gravada para o tópico `wordcounts`

    É possível realizar essas operações, abrindo três sessões SSH. Porém, em seguida, você deverá definir `$KAFKABROKERS` e `$KAFKAZKHOSTS` para cada um executando a etapa 4 dessa seção em cada sessão SSH. Uma solução mais fácil é usar o utilitário `tmux` que pode dividir a exibição SSH atual em várias seções. Para iniciar o fluxo, produtor e consumidor usando `tmux`, use o seguinte comando:

    ```bash
    tmux new-session '/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer' \; split-window -h 'java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS' \; split-window -v '/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test' \; attach
    ```

    Esse comando divide a exibição SSH em três seções:

    * A seção esquerda executa um consumidor de console que lê mensagens do `wordcounts` tópico: `/usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer`

        > [!NOTE]
        > Os parâmetros `--property` informam ao consumidor do console para imprimir a chave (palavra) juntamente com a contagem (valor). Esses parâmetros também configuram o desserializador a ser usado ao fazer a leitura desses valores do Kafka.

    * A seção superior direita executa a solução da API de Streams: `java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS`

    * A seção inferior direita executa o produtor do console e aguarda a inserção de mensagens a serem enviadas para o `test` tópico: `/usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic test`
 
6. Após o comando `tmux` dividir a exibição, o cursor estará na seção inferior direita. Comece a inserir frases. Após cada frase, o painel esquerdo será atualizado para mostrar uma contagem de palavras exclusivas. A saída é semelhante ao texto a seguir:
   
        dwarfs  13635
        ago     13664
        snow    13636
        dwarfs  13636
        ago     13665
        a       13803
        ago     13666
        a       13804
        ago     13667
        ago     13668
        jumped  13640
        jumped  13641
   
    > [!NOTE]
    > A contagem é incrementada sempre que uma palavra é encontrada.

7. Use __Ctrl + C__ para sair do produtor. Continue usando __Ctrl + C__ para sair do aplicativo e do consumidor.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de Streams do Kafka com Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar logs do Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters de Kafka](apache-kafka-mirroring.md)
* [API do Produtor e Consumidor Kafka com HDInsight](apache-kafka-producer-consumer-api.md)
* [Use streaming do Apache Spark (DStream) com o Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use fluxo estruturado do Apache Spark com o Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Usar o streaming estruturado do Apache Spark para mover dados do Kafka no HDInsight para o Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Usar Apache Storm com Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conectar-se ao Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)