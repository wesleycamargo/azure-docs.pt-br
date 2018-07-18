---
title: Usando Akka Streams com Hubs de Eventos do Azure para o Ecossistema Kafka | Microsoft Docs
description: Conectando o Akka Streams a um Hub de Eventos habilitado para Kafka
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.custom: mvc
ms.date: 06/06/2018
ms.author: bahariri
ms.openlocfilehash: 9db27340a2210ea0be0564b15241952477e592ba
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35301978"
---
# <a name="using-akka-streams-with-event-hubs-for-kafka-ecosystem"></a>Usando o Akka Streams com Hubs de Eventos para o Ecossistema Kafka

Um dos principais benefícios do uso do Apache Kafka é o ecossistema de estruturas às quais ele pode se conectar. Os Hubs de Eventos habilitados para Kafka combinam a flexibilidade do Kafka com a escalabilidade, a consistência e o suporte do ecossistema do Azure.

Este tutorial mostra como conectar o Akka Streams a Hubs de Eventos habilitados para Kafka sem alterar seus clientes de protocolo ou executar seus próprios clusters. Os Hubs de Eventos do Azure para o ecossistema Kafka são compatíveis com o [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial, é necessário atender aos seguintes pré-requisitos:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [JDK (Java Development Kit) 1.8+](http://www.oracle.com/technetwork/java/javase/downloads/index.html)
    * No Ubuntu, execute `apt-get install default-jdk` para instalar o JDK.
    * Defina a variável de ambiente JAVA_HOME para apontar para a pasta onde o JDK está instalado.
* [Baixar](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
    * No Ubuntu, você pode executar `apt-get install maven` para instalar o Maven.
* [Git](https://www.git-scm.com/downloads)
    * No Ubuntu, você pode executar `sudo apt-get install git` para instalar o Git.

## <a name="create-an-event-hubs-namespace"></a>Criar um namespace de Hubs de Eventos

É necessário um namespace dos Hubs de Eventos para enviar ou receber de qualquer serviço de Hubs de Eventos. Confira [Criar Hubs de Eventos habilitados para Kafka](event-hubs-create-kafka-enabled.md) para saber mais sobre como obter um ponto de extremidade Kafka nos Hubs de Eventos. Certifique-se de copiar a cadeia de caracteres de conexão dos Hubs de Eventos para uso posterior.

## <a name="clone-the-example-project"></a>Clonar o projeto de exemplo

Agora que você tem uma cadeia de conexão de Hubs de Eventos habilitados para Kafka, clone o repositório de Hubs de Eventos do Azure e navegue até a subpasta `akka`:

```shell
git clone https://github.com/Azure/azure-event-hubs.git
cd azure-event-hubs/samples/kafka/akka
```

## <a name="akka-streams-producer"></a>Produtor do Akka Streams

Usando o exemplo do produtor do Akka Streams, envie mensagens para o serviço de Hubs de Eventos.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="producer-applicationconf"></a>Produtor application.conf

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `producer/src/main/resources/application.conf` para direcionar o produtor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
akka.kafka.producer {
    #Akka Kafka producer properties can be defined here


    # Properties defined by org.apache.kafka.clients.producer.ProducerConfig
    # can be defined in this configuration section.
    kafka-clients {
        bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
        sasl.mechanism=PLAIN
        security.protocol=SASL_SSL
        sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-producer-from-the-command-line"></a>Executar o produtor na linha de comando

Para executar o produtor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestProducer"
```

O produtor começa a enviar eventos para o hub de eventos habilitado para Kafka no tópico `test` e imprime os eventos para stdout.

## <a name="akka-streams-consumer"></a>Consumidor de fluxos do Akka

Usando o exemplo de consumidor fornecido, receba mensagens dos Hubs de Eventos habilitados para Kafka.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Fornecer um ponto de extremidade Kafka dos Hubs de Eventos

#### <a name="consumer-applicationconf"></a>Consumidor application.conf

Atualize os valores `bootstrap.servers` e `sasl.jaas.config` em `consumer/src/main/resources/application.conf` para direcionar o consumidor ao ponto de extremidade Kafka dos Hubs de Eventos com a autenticação correta.

```xml
akka.kafka.consumer {
    #Akka Kafka consumer properties defined here
    wakeup-timeout=60s

    # Properties defined by org.apache.kafka.clients.consumer.ConsumerConfig
    # defined in this configuration section.
    kafka-clients {
       request.timeout.ms=60000
       group.id=akka-example-consumer

       bootstrap.servers="{YOUR.EVENTHUBS.FQDN}:9093"
       sasl.mechanism=PLAIN
       security.protocol=SASL_SSL
       sasl.jaas.config="org.apache.kafka.common.security.plain.PlainLoginModule required username=\"$ConnectionString\" password=\"{YOUR.EVENTHUBS.CONNECTION.STRING}\";"
    }
}
```

### <a name="run-consumer-from-the-command-line"></a>Executar o consumidor na linha de comando

Para executar o consumidor na linha de comando, gere o JAR e execute no Maven (ou gere o JAR usando o Maven e execute no Java adicionando os JARs do Kafka necessários ao caminho de classe):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="AkkaTestConsumer"
```

Se o hub de eventos habilitado para Kafka tiver eventos (por exemplo, se o produtor também estiver em execução), o consumidor começará a receber eventos do tópico `test`. 

Confira o [Guia de Kafka do Akka Streams](https://doc.akka.io/docs/akka-stream-kafka/current/home.html) para encontrar informações mais detalhadas sobre o Akka Streams.

## <a name="next-steps"></a>Próximas etapas

* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o Ecossistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para Hubs de Eventos habilitados para Kafka na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [aplicativos nativos do Kafka](event-hubs-quickstart-kafka-enabled-event-hubs.md) ou o [Apache Flink](event-hubs-kafka-flink-tutorial.md)
