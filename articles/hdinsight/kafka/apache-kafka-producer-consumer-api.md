---
title: 'Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka – Azure HDInsight '
description: Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Neste tutorial, você aprenderá como usar essas APIs com Kafka no HDInsight de um aplicativo Java.
services: hdinsight
author: dhgoelmsft
ms.author: dhgoel
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 11/06/2018
ms.openlocfilehash: 556fc1f04cc6a1d1b594bdd3787ed43d30f607c6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091164"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka

Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight.

A API de produtor do Kafka permite que os aplicativos enviam fluxos de dados para o cluster Kafka. A API de consumidor do Kafka permite que os aplicativos leiam fluxos de dados do cluster.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar seu ambiente de desenvolvimento
> * Configurar o ambiente de implantação
> * Compreender o código
> * Compilar e implantar o aplicativo
> * Executar o aplicativo no cluster

Para obter mais informações sobre as APIs, consulte a documentação do Apache sobre [API de Produtor](https://kafka.apache.org/documentation/#producerapi) e [API de Consumidor](https://kafka.apache.org/documentation/#consumerapi).

## <a name="set-up-your-development-environment"></a>Configurar seu ambiente de desenvolvimento

Você deve ter os seguintes componentes instalados no ambiente de desenvolvimento:

* [Java JDK 8](https://aka.ms/azure-jdks) ou equivalente, como OpenJDK.

* [Apache Maven](https://maven.apache.org/)

* Um cliente SSH e o comando `scp`. Para saber mais, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um editor de texto ou Java IDE.

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório em que o JDK está instalado.
* `PATH` – deve conter os seguintes caminhos:

    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório em que o Maven está instalado.

## <a name="set-up-your-deployment-environment"></a>Configurar o ambiente de implantação

Esse tutorial requer Apache Kafka no HDInsight 3.6. Para saber como criar um Kafka no cluster HDInsight, consulte o documento [Iniciar com Apache Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Compreender o código

O aplicativo de exemplo está localizado em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), além do subdiretório `Producer-Consumer`. O aplicativo consiste principalmente em quatro arquivos:

* `pom.xml`: este arquivo define as dependências do projeto, versão do Java e os métodos de empacotamento.
* `Producer.java`: este arquivo envia sentenças aleatórias para o Kafka usando a API de produtor.
* `Consumer.java`: este arquivo usa a API de consumidor para ler dados do Kafka e emiti-los para STDOUT.
* `Run.java`: a interface de linha de comando usada para executar o código de produtor e consumidor.

### <a name="pomxml"></a>Pom.xml

As coisas importantes para entender no arquivo `pom.xml` são:

* Dependências: este projeto depende das APIs de produtor e consumidor do Kafka, fornecidas pelo pacote `kafka-clients`. O seguinte código XML define essa dependência:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
      <groupId>org.apache.kafka</groupId>
      <artifactId>kafka-clients</artifactId>
      <version>${kafka.version}</version>
    </dependency>
    ```

    > [!NOTE]  
    > A entrada `${kafka.version}` é declarada na seção `<properties>..</properties>` de `pom.xml`, e está configurada para a versão Kafka do cluster HDInsight.

* Plug-ins: os plug-ins do Maven oferecem várias funcionalidades. Neste projeto, são usados os seguintes plug-ins:

    * `maven-compiler-plugin`: usado para definir a versão do Java usada pelo projeto como 8. Essa é a versão do Java usado pelo HDInsight 3.6.
    * `maven-shade-plugin`: usado para gerar um uber jar que contém esse aplicativo, bem como todas as dependências. Ele também é usado para definir o ponto de entrada do aplicativo, para que você possa executar diretamente o arquivo Jar sem a necessidade de especificar a classe principal.

### <a name="producerjava"></a>Producer.java

O produtor se comunica com os hosts de agente de Kafka (nós de trabalho) e envia dados a um tópico Kafka. O seguinte snippet de código é do arquivo [Producer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Producer.java) do [repositório GitHub](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) e mostra como definir as propriedades de produtor:

```java
Properties properties = new Properties();
// Set the brokers (bootstrap servers)
properties.setProperty("bootstrap.servers", brokers);
// Set how to serialize key/value pairs
properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
KafkaProducer<String, String> producer = new KafkaProducer<>(properties);
```

### <a name="consumerjava"></a>Consumer.java

O cliente se comunica com os hosts de broker Kafka (nós de trabalho) e lê registros em um loop. O seguinte trecho de código do arquivo [Consumer.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Consumer.java) define as propriedades do consumidor:

```java
KafkaConsumer<String, String> consumer;
// Configure the consumer
Properties properties = new Properties();
// Point it to the brokers
properties.setProperty("bootstrap.servers", brokers);
// Set the consumer group (all consumers must belong to a group).
properties.setProperty("group.id", groupId);
// Set how to serialize key/value pairs
properties.setProperty("key.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
properties.setProperty("value.deserializer","org.apache.kafka.common.serialization.StringDeserializer");
// When a group is first created, it has no offset stored to start reading from. This tells it to start
// with the earliest record in the stream.
properties.setProperty("auto.offset.reset","earliest");

consumer = new KafkaConsumer<>(properties);
```

Nesse código, o consumidor é configurado para ler a partir do início do tópico (`auto.offset.reset` é definido como `earliest`.)

### <a name="runjava"></a>Run.java

O arquivo [Run.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Producer-Consumer/src/main/java/com/microsoft/example/Run.java) fornece uma interface de linha de comando que executa o código de produtor ou consumidor. Você deve fornecer as informações do host de broker Kafka como um parâmetro. Opcionalmente, você pode incluir um valor de id de grupo, que é usado pelo processo de consumidor. Se você criar várias instâncias de consumidor usando a mesma id de grupo, eles balancearão a carga de leitura do tópico.

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

Você pode ignorar as etapas 1 e 2 para o build e baixar os jars (kafka-producer-consumer.jar) pré-compilados de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/tree/master/Prebuilt-Jars). Em seguida, você pode copiar esse jar ao seu cluster HDInsight.

1. Baixe os exemplo de [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started).

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

2. Para criar os tópicos do Kafka usados por esse exemplo, utilize as seguintes etapas:

    1. Para salvar o nome do cluster a uma variável e instalar um utilitário de análise de JSON (`jq`), use os seguintes comandos. Ao receber a solicitação, insira o nome do cluster Kafka:
    
        ```bash
        sudo apt -y install jq
        read -p 'Enter your Kafka cluster name:' CLUSTERNAME
        ```
    
    2. Para obter os hosts de broker Kafka e os hosts Apache Zookeeper, use os comandos a seguir. Quando solicitado, insira a senha para a conta de logon do cluster (admin).
    
        ```bash
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Para criar o tópico `test`, use o seguinte comando:

        ```bash
        java -jar kafka-producer-consumer.jar create test $KAFKABROKERS
        ```

3. Para executar o produtor e fazer gravação de dados para o tópico, utilize o seguinte comando:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

4. Quando produtor concluir, use o seguinte comando para a leitura do tópico:

    ```bash
    java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS
    ```

    São exibidos os registros lidos, juntamente com uma contagem de registros.

5. Use __Ctrl + C__ para sair do consumidor.

### <a name="multiple-consumers"></a>Vários consumidores

Os consumidores do Kafka usam um grupo de consumidores ao ler os registros. Usar o mesmo grupo com vários consumidores resulta em leituras de balanceamento de carga de um tópico. Cada consumidor no grupo recebe uma parte dos registros.

O aplicativo do consumidor aceita um parâmetro que é utilizado como a ID do grupo. Por exemplo, o seguinte comando inicia um consumidor usando uma ID do grupo `mygroup`:

```bash
java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup
```

Para ver esse processo em ação, use o seguinte comando:

```bash
tmux new-session 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; split-w
indow -h 'java -jar kafka-producer-consumer.jar consumer test $KAFKABROKERS mygroup' \; attach
```

Esse comando usa `tmux` para dividir o terminal em duas colunas. Um consumidor é iniciado em cada coluna, com o mesmo valor de ID de grupo. Depois que os consumidores terminarem a leitura, observe que cada um leu apenas uma parte dos registros. Use __Ctrl + C__ duas vezes para sair do `tmux`.

O consumo por clientes no mesmo grupo é manipulado por meio das partições do tópico. Neste exemplo de código, o tópico `test` criado anteriormente tem oito partições. Se você iniciar os oito consumidores, cada consumidor lê os registros de uma única partição para o tópico.

> [!IMPORTANT]  
> Não pode haver mais instâncias de consumidores do que partições em um grupo de consumidores. Neste exemplo, um grupo de consumidores pode conter até oito consumidores, já que esse é o número de partições no tópico. Ou você pode ter vários grupos de consumidores, cada um com no máximo oito consumidores.

Os registros armazenados no Kafka são armazenados na ordem em que são recebidos em uma partição. Para garantir a entrega ordenada em registros *em uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponda ao número de partições. Para garantir a entrega ordenada em registros *no tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar logs do Apache Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters do Apache Kafka](apache-kafka-mirroring.md)
* [API de Streams do Apache Kafka com HDInsight](apache-kafka-streams-api.md)
