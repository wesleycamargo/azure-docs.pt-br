---
title: 'Tutorial: Utilizar as APIs de produtor e consumidor do Apache Kafka – Azure HDInsight '
description: Saiba como utilizar as APIs de produtor e consumidor do Apache Kafka com o Kafka no HDInsight. Neste tutorial, você aprenderá como usar essas APIs com Kafka no HDInsight de um aplicativo Java.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 04/16/2018
ms.openlocfilehash: 8b20b2aa75c3872df1082ef1059000d80a2dd472
ms.sourcegitcommit: 35ceadc616f09dd3c88377a7f6f4d068e23cceec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/08/2018
ms.locfileid: "39621089"
---
# <a name="tutorial-use-the-apache-kafka-producer-and-consumer-apis"></a>Tutorial: Usar as APIs de produtor e consumidor do Apache Kafka

Saiba como utilizar as APIs de produtor e consumidor do Kafka com o Kafka no HDInsight.

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

* [Java JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/index.html) ou equivalente, como OpenJDK.

* [Apache Maven](http://maven.apache.org/)

* Um cliente SSH e o comando `scp`. Para saber mais, consulte o documento [Usar SSH com HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Um editor de texto ou Java IDE.

As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK em sua estação de trabalho de desenvolvimento. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* `JAVA_HOME` – deve apontar para o diretório em que o JDK está instalado.
* `PATH` – deve conter os seguintes caminhos:
  
    * `JAVA_HOME` (ou o caminho equivalente).
    * `JAVA_HOME\bin` (ou o caminho equivalente).
    * O diretório em que o Maven está instalado.

## <a name="set-up-your-deployment-environment"></a>Configurar o ambiente de implantação

Esse tutorial requer Kafka no HDInsight 3.6. Para saber como criar um Kafka no Cluster HDInsight, consulte o documento [Iniciar com Kafka no HDInsight](apache-kafka-get-started.md).

## <a name="understand-the-code"></a>Compreender o código

O aplicativo de exemplo está localizado em [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started), além do subdiretório `Producer-Consumer`. O aplicativo consiste principalmente em quatro arquivos:

* `pom.xml`: Esse arquivo define as dependências do projeto, versão do Java e os métodos de empacotamento.
* `Producer.java`: Este arquivo envia 1 milhão (1.000.000) de sentenças aleatórias para Kafka usando o produtor API.
* `Consumer.java`: Este arquivo usa a API de consumidor para ler dados do Kafka e emiti-los para STDOUT.
* `Run.java`: A interface de linha de comando usada para executar o código de produtor e consumidor.

### <a name="pomxml"></a>Pom.xml

As coisas importantes para entender no arquivo `pom.xml` são:

* Dependências: Este projeto depende das APIs de produtor e consumidor Kafka, que é fornecido pelo pacote `kafka-clients`. O seguinte código XML define essa dependência:

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

* Plug-ins: os plug-ins do Maven fornecem vários recursos. Neste projeto, são usados os seguintes plug-ins:

    * `maven-compiler-plugin`: Usado para definir a versão do Java usada pelo projeto para 8. Essa é a versão do Java usado pelo HDInsight 3.6.
    * `maven-shade-plugin`: Usado para gerar um jar grande que contém esse aplicativo, bem como todas as dependências. Ele também é usado para definir o ponto de entrada do aplicativo, para que você possa executar diretamente o arquivo Jar sem a necessidade de especificar a classe principal.

### <a name="producerjava"></a>Producer.java

O produtor se comunica com os hosts de broker Kafka (nós de trabalho) para armazenar dados em um tópico Kafka. O seguinte trecho de código é do arquivo `Producer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.producer.KafkaProducer;
import org.apache.kafka.clients.producer.ProducerRecord;
import java.util.Properties;
import java.util.Random;
import java.io.IOException;

public class Producer
{
    public static void produce(String brokers) throws IOException
    {

        // Set properties used to configure the producer
        Properties properties = new Properties();
        // Set the brokers (bootstrap servers)
        properties.setProperty("bootstrap.servers", brokers);
        // Set how to serialize key/value pairs
        properties.setProperty("key.serializer","org.apache.kafka.common.serialization.StringSerializer");
        properties.setProperty("value.serializer","org.apache.kafka.common.serialization.StringSerializer");
        KafkaProducer<String, String> producer = new KafkaProducer<>(properties);

        // So we can generate random sentences
        Random random = new Random();
        String[] sentences = new String[] {
                "the cow jumped over the moon",
                "an apple a day keeps the doctor away",
                "four score and seven years ago",
                "snow white and the seven dwarfs",
                "i am at two with nature"
        };

        String progressAnimation = "|/-\\";
        // Produce a bunch of records
        for(int i = 0; i < 1000000; i++) {
            // Pick a sentence at random
            String sentence = sentences[random.nextInt(sentences.length)];
            // Send the sentence to the test topic
            producer.send(new ProducerRecord<String, String>("test", sentence));
            String progressBar = "\r" + progressAnimation.charAt(i % progressAnimation.length()) + " " + i;
            System.out.write(progressBar.getBytes());
        }
    }
}
```

Esse código conecta-se aos hosts de broker Kafka (nós de trabalho) e, em seguida, envia 1.000.000 sentenças para Kafka usando a API de produtor.

### <a name="consumerjava"></a>Consumer.java

O cliente se comunica com os hosts de broker Kafka (nós de trabalho) e lê registros em um loop. O trecho de código a seguir é do arquivo `Consumer.java`:

```java
package com.microsoft.example;

import org.apache.kafka.clients.consumer.KafkaConsumer;
import org.apache.kafka.clients.consumer.ConsumerRecords;
import org.apache.kafka.clients.consumer.ConsumerRecord;
import java.util.Properties;
import java.util.Arrays;

public class Consumer {
    public static void consume(String brokers, String groupId) {
        // Create a consumer
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

        // Subscribe to the 'test' topic
        consumer.subscribe(Arrays.asList("test"));

        // Loop until ctrl + c
        int count = 0;
        while(true) {
            // Poll for records
            ConsumerRecords<String, String> records = consumer.poll(200);
            // Did we get any?
            if (records.count() == 0) {
                // timeout/nothing to read
            } else {
                // Yes, loop over records
                for(ConsumerRecord<String, String> record: records) {
                    // Display record and count
                    count += 1;
                    System.out.println( count + ": " + record.value());
                }
            }
        }
    }
}
```

Nesse código, o consumidor é configurado para ler a partir do início do tópico (`auto.offset.reset` é definido como `earliest`.)

### <a name="runjava"></a>Run.java

O arquivo `Run.java` fornece uma interface de linha de comando que executa o código de produtor ou consumidor. Você deve fornecer as informações do host de broker Kafka como um parâmetro. Opcionalmente, você pode incluir um valor de id de grupo, que é usado pelo processo de consumidor. Se você criar várias instâncias de consumidor usando a mesma id de grupo, eles balancearão a carga de leitura do tópico.

```java
package com.microsoft.example;

import java.io.IOException;
import java.util.UUID;

// Handle starting producer or consumer
public class Run {
    public static void main(String[] args) throws IOException {
        if(args.length < 2) {
            usage();
        }

        // Get the brokers
        String brokers = args[1];
        switch(args[0].toLowerCase()) {
            case "producer":
                Producer.produce(brokers);
                break;
            case "consumer":
                // Either a groupId was passed in, or we need a random one
                String groupId;
                if(args.length == 3) {
                    groupId = args[2];
                } else {
                    groupId = UUID.randomUUID().toString();
                }
                Consumer.consume(brokers, groupId);
                break;
            default:
                usage();
        }
        System.exit(0);
    }
    // Display usage
    public static void usage() {
        System.out.println("Usage:");
        System.out.println("kafka-example.jar <producer|consumer> brokerhosts [groupid]");
        System.exit(1);
    }
}
```

## <a name="build-and-deploy-the-example"></a>Compilar e implantar o exemplo

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
    
    2. Para obter os hosts de broker Kafka e os hosts Zookeeper, use os comandos a seguir. Quando solicitado, insira a senha para a conta de logon do cluster (admin).
    
        ```bash
        export KAFKAZKHOSTS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`; \
        export KAFKABROKERS=`curl -sS -u admin -G https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`; \
        ```

    3. Para criar o tópico `test`, use o seguinte comando:

        ```bash
        /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
        ```
    4. Você também pode usar o arquivo jar para criar um tópico. Por exemplo, para criar o tópico `test2`, use o seguinte comando:

        ```bash
        java -jar kafka-producer-consumer.jar create test2 $KAFKABROKERS
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

Esse comando usa `tmux` para dividir o terminal em duas colunas. Um consumidor é iniciado em cada coluna, com o mesmo valor de ID de grupo. Depois que os consumidores terminarem a leitura, observe que cada um leu apenas uma parte dos registros. Use __CTRL + C__ duas vezes para sair `tmux`.

O consumo por clientes no mesmo grupo é manipulado por meio das partições do tópico. O tópico `test` criado anteriormente tem oito partições. Se você iniciar os oito consumidores, cada consumidor lê os registros de uma única partição para o tópico.

> [!IMPORTANT]
> Não pode haver mais instâncias de consumidores do que partições em um grupo de consumidores. Neste exemplo, um grupo de consumidores pode conter até oito consumidores, já que esse é o número de partições no tópico. Ou você pode ter vários grupos de consumidores, cada um com no máximo oito consumidores.

Os registros armazenados no Kafka são armazenados na ordem em que são recebidos em uma partição. Para garantir a entrega ordenada em registros *em uma partição*, crie um grupo de consumidores em que o número de instâncias de consumidor corresponda ao número de partições. Para garantir a entrega ordenada em registros *no tópico*, crie um grupo de consumidores com apenas uma instância de consumidor.

## <a name="next-steps"></a>Próximas etapas

Neste documento, você aprendeu a usar a API de produtor e consumidor do Kafka com o Kafka no HDInsight. Confira o seguinte para obter mais informações sobre como trabalhar com o Kafka:

* [Analisar logs do Kafka](apache-kafka-log-analytics-operations-management.md)
* [Replicar dados entre clusters de Kafka](apache-kafka-mirroring.md)
* [API de Streams do Kafka com HDInsight](apache-kafka-streams-api.md)
