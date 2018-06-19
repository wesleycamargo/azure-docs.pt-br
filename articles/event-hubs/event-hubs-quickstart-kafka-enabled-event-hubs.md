---
title: Transmitir para os Hubs de Eventos do Azure para o Ecossistema Kafka | Microsoft Docs
description: Transmitir para Hubs de Eventos usando o protocolo de Kafka e APIs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/08/2018
ms.author: bahariri
ms.openlocfilehash: 8ef6240d19ce1ac1b891c95ce525a8bd211a2900
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2018
ms.locfileid: "35297216"
---
# <a name="stream-into-event-hubs-for-the-kafka-ecosystem"></a>Transmissão nos Hubs de Eventos para o Ecossistema Kafka

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs)

Este guia de início rápido mostra como transmitir para o Kafka habilitado dos Hubs de Eventos sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Você aprenderá a usar seu produtores e os consumidores para se comunicar com os Hubs de Eventos habilitados com Kafka com apenas uma alteração de configuração nos seus aplicativos. O Hubs de Eventos do Azure para o Ecossistema Kakfa é compatível com [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>pré-requisitos

Para concluir este início rápido, você precisa atender aos seguinte pré-requisitos:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Realizar o download](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
* [Git](https://www.git-scm.com/)
* [Namespace de Hubs de Evento habilitado com Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Enviar e receber mensagens com Kafka no Hubs de Evento

1. Clone o [repositório de Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs).

2. Navegue até `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Atualize os detalhes de configuração para o produtor no `src/main/resources/producer.config` da seguinte maneira:

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Execute o código de produtor e fluxo nos Hubs de Eventos habilitado com Kafka:
   
    ```shell
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Navegue até `azure-event-hubs/samples/kafka/quickstart/consumer`.

6. Atualize os detalhes de configuração para o consumidor no `src/main/resources/consumer.config` da seguinte maneira:
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Execute o código do consumidor e processo a partir do Hubs de Eventos habilitados com Kafka usando seus clientes Kafka:

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se seu cluster Kafka de Hubs de Eventos tiver eventos, inicie agora recebendo-os do consumidor.

## <a name="next-steps"></a>Próximas etapas

* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* [Saiba mais sobre os Hubs de Eventos para o Ecossistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* Use [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para [transmitir eventos do Kafka local para Hubs de Eventos habilitados para Kafka na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
* Saiba como transmitir em Hubs de Eventos habilitados para Kafka usando [Apache Flink](event-hubs-kafka-flink-tutorial.md) ou [Akka Streams](event-hubs-kafka-akka-streams-tutorial.md).
