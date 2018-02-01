---
title: Utilizar as APIs de produtor e consumidor do Apache Kafka - Azure HDInsight | Microsoft Docs
description: "Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Essas APIs permitem desenvolve aplicativos que fazem gravação e leitura a partir do Apache Kafka."
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
ms.date: 01/18/2018
ms.author: larryfr
ms.openlocfilehash: b57745d6bd993a993e923c964327d9071e745413
ms.sourcegitcommit: 817c3db817348ad088711494e97fc84c9b32f19d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/20/2018
---
# <a name="apache-kafka-producer-and-consumer-apis"></a>APIs de produtor e consumidor do Apache Kafka

Saiba como criar um aplicativo que usa as APIs de produtor e consumidor do Kafka com o Kafka no HDInsight.

Para obter a documentação sobre as APIs, consulte [API de Produtor](https://kafka.apache.org/documentation/#producerapi) e [API de Consumidor](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Você deve ter os seguintes componentes instalados no ambiente de desenvolvimento:

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Um cliente SSH e o comando `scp`. Para saber mais, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="set-up-your-deployment-environment"></a>Configurar o ambiente de implantação

Esse exemplo requer Kafka no HDInsight 3.6. Para saber como criar um Kafka no Cluster HDInsight, consulte o documento [Iniciar com Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

1. Baixe os exemplos de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

2. Altere os diretórios para o local do `Producer-Consumer` diretório e use o seguinte comando:

    ```
    mvn clean package
    ```

    Esse comando cria um diretório chamado `target`, que contém um arquivo chamado `kafka-producer-consumer-1.0-SNAPSHOT.jar`.

3. Use os seguintes comandos para copiar o arquivo `kafka-producer-consumer-1.0-SNAPSHOT.jar` para o cluster HDInsight:
   
    ```bash
    scp ./target/kafka-producer-consumer-1.0-SNAPSHOT.jar SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net:kafka-producer-consumer.jar
    ```
   
    Substitua **SSHUSER** pelo usuário do SSH do cluster e substitua **CLUSTERNAME** pelo nome do cluster. Quando solicitado, insira a senha do usuário do SSH.

## <a id="run"></a> Executar o exemplo

1. Para abrir uma conexão SSH para o cluster, use o seguinte comando:

    ```bash
    ssh SSHUSER@CLUSTERNAME-ssh.azurehdinsight.net
    ```

    Substitua **SSHUSER** pelo usuário do SSH do cluster e substitua **CLUSTERNAME** pelo nome do cluster. Se solicitado, insira a senha para a conta de usuário SSH. Para saber mais sobre como usar o `scp` com HDInsight, confira [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Para criar os tópicos do Kafka usados por esse exemplo, utilize os seguintes comandos:

    ```bash
    sudo apt -y install jq

    CLUSTERNAME='your cluster name'

    export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`

    export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    ```

    Substitua __o nome do seu cluster__ pelo nome do Cluster HDInsight. Quando solicitado, insira a senha da conta de logon do Cluster HDInsight.

    > [!NOTE]
    > Se o seu logon do cluster for diferente do valor padrão de `admin`, substitua o valor `admin`nos comandos anteriores pelo nome de logon do cluster.

3. Para executar o produtor e fazer gravação de dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer $KAFKABROKERS
    ```

4. Quando produtor concluir, use o seguinte comando para a leitura do tópico:
   
    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS
    ```
   
    São exibidos os registros lidos, juntamente com uma contagem de registros.

5. Use __Ctrl + C__ para sair do consumidor.

### <a name="multiple-consumers"></a>Vários consumidores

Os consumidores do Kafka usam um grupo de consumidores ao ler os registros. Usar o mesmo grupo com vários consumidores resulta em leituras de balanceamento de carga de um tópico. Cada consumidor no grupo recebe uma parte dos registros.

O aplicativo do consumidor aceita um parâmetro que é utilizado como a ID do grupo. Por exemplo, o seguinte comando inicia um consumidor usando uma ID do grupo `mygroup`:
   
```bash
java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
```

Para ver esse processo em ação, use as seguintes etapas

1. Repita as etapas 1 e 2 na seção [Executar o exemplo](#run) para abrir uma nova sessão SSH.

2. Em ambas as sessões SSH, digite o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar consumer $KAFKABROKERS mygroup
    ```
    
    > [!IMPORTANT]
    > Digite ambos os comandos simultaneamente para que sejam executados ao mesmo tempo.

    Observe que a contagem de mensagens lidas não é a mesma que a seção anterior, onde havia apenas um consumidor. Em vez disso, as mensagens lidas são divididas entre as instâncias.

O consumo por clientes no mesmo grupo é manipulado por meio das partições do tópico. O tópico `test` criado anteriormente tem oito partições. Se você abrir oito sessões do SSH e iniciar um consumidor em todas elas, cada consumidor lerá os registros de uma única partição do tópico.

> [!IMPORTANT]
> Não pode haver mais instâncias de consumidores do que partições em um grupo de consumidores. Neste exemplo, um grupo de consumidores pode conter até oito consumidores, já que esse é o número de partições no tópico. Ou você pode ter vários grupos de consumidores, cada um com no máximo oito consumidores.

Os registros armazenados no Kafka são armazenados na ordem em que são recebidos em uma partição. Para garantir a entrega ordenada em registros *em uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponda ao número de partições. Para garantir a entrega ordenada em registros *no tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de produtor e consumidor do Kafka com o Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar logs do Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters de Kafka](apache-kafka-mirroring.md)
* [API de Streams do Kafka com HDInsight](apache-kafka-streams-api.md)
* [Use streaming do Apache Spark (DStream) com o Kafka no HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Use fluxo estruturado do Apache Spark com o Kafka no HDInsight](../hdinsight-apache-kafka-spark-structured-streaming.md)
* [Usar o streaming estruturado do Apache Spark para mover dados do Kafka no HDInsight para o Cosmos DB](../apache-kafka-spark-structured-streaming-cosmosdb.md)
* [Usar Apache Storm com Kafka no HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Conectar-se ao Kafka por meio de uma Rede Virtual do Azure](apache-kafka-connect-vpn-gateway.md)