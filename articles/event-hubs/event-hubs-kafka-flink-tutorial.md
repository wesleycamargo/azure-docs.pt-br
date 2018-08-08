---
title: Usar o Apache Flink com Hubs de Eventos do Azure para o Apache Kafka | Microsoft Docs
description: Conectar o Apache Flink a um hub de eventos habilitado para Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: ce1665c3cfd58d0d5aa8e253b5db317505b1959e
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39284570"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Usar o Apache Flink com Hubs de Eventos do Azure para o Apache Kafka

Um dos principais benefícios do uso do Apache Kafka é o ecossistema de estruturas às quais ele pode se conectar. Os Hubs de Eventos habilitados para Kafka combinam a flexibilidade do Kafka com a escalabilidade, a consistência e o suporte do ecossistema do Azure.

Este tutorial mostra como conectar o Apache Flink aos Hubs de Eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Hubs de Eventos do Azure dá suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário atender aos seguintes pré-requisitos:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7 +](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace dos Hubs de Eventos para enviar ou receber de qualquer serviço de Hubs de Eventos. Confira [Criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md) para saber mais sobre como obter um ponto de extremidade Kafka nos Hubs de Eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de Hubs de Eventos habilitados para Kafka, clone o repositório de Hubs de Eventos do Azure e navegue até a subpasta `flink`:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/flink
```

## <a name="flink-producer"></a>Produtor do Flink

Seguindo o exemplo do produtor do Flink, envie mensagens para o serviço do Hubs de Eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="producerconfig"></a>producer.config

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `producer/src/main/resources/producer.config` para direcionar o produtor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-producer-from-the-command-line"></a>Executar o produtor na linha de comando

Para executar o produtor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

O produtor começará a enviar eventos para o hub de eventos habilitado para Kafka no tópico `test` e a imprimir os eventos para stdout.

## <a name="flink-consumer"></a>Consumidor Flink

Usando o exemplo de consumidor fornecido, receba mensagens dos Hubs de Eventos habilitados para Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="consumerconfig"></a>consumer.config

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `consumer/src/main/resources/consumer.config` para direcionar o consumidor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor na linha de comando

Para executar o consumidor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Se o hub de eventos habilitado para Kafka tiver eventos (por exemplo, se o produtor também estiver em execução), o consumidor receberá eventos do tópico `test`.

Confira [Guia de conector Kafka do Flink](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) para saber informações mais detalhadas sobre como conectar o Flink ao Kafka.

## <a name="next-steps"></a>Próximas etapas

* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para Hubs de Eventos habilitados para Kafka na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md).
