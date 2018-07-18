---
title: Transmitir para os Hubs de Eventos do Azure para o Ecossistema Kafka | Microsoft Docs
description: Transmitir para Hubs de Eventos usando o protocolo de Kafka e APIs.
services: event-hubs
documentationcenter: ''
author: basilhariri
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2018
ms.author: bahariri
ms.openlocfilehash: cabbb7ed6157a6c68530ab6b5f405aa67b31a1b2
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/10/2018
---
# <a name="stream-into-event-hubs-for-kafka-ecosystem"></a>Transmissão nos Hubs de Eventos para o Ecossistema Kafka

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs).

Este guia de início rápido mostra como transmitir para o Kafka habilitado dos Hubs de Eventos sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Você aprenderá a usar seu produtores e os consumidores podem se comunicar com os Hubs de Eventos habilitos com Kafka com apenas uma alteraão de configuração nos seus aplicativos. O Hubs de Eventos do Azure para o Ecossistema Kakfa é compatível com [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

## <a name="prerequisites"></a>pré-requisitos

Para concluir este início rápido, certifique-se de que:

* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Realizar o download](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
* [Git](https://www.git-scm.com/)
* [Namespace de Hubs de Evento habilitado com Kafka](event-hubs-create.md)

## <a name="send-and-receive-messages-with-kafka-in-event-hubs"></a>Enviar e receber mensagens com Kafka no Hubs de Evento

1. Clone o [repositório de Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs).

2. Navegue até `azure-event-hubs/samples/kafka/quickstart/producer`.

3. Atualize os detalhes de configuração para o produtor em src/main/resources/producer.config conforme mostrado aqui.

    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```
4. Execute o código de produtor e fluxo em no Hubs de Evento habilitado com Kafka.
   
    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestProducer"                                    
    ```
5. Navegue até o azure-event-hubs/samples/kafka/quickstart/consumer.

6. Atualize os detalhes de configuração para o produtor em src/main/resources/consumer.config conforme mostrado aqui.
   
    ```xml
    bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
    security.protocol=SASL_SSL
    sasl.mechanism=PLAIN
    sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
    ```

7. Execute o código do consumidor e processo a partir do Hubs de Evento habilitado com Kafa usando seus clientes Kafka.

    ```java
    mvn clean package
    mvn exec:java -Dexec.mainClass="TestConsumer"                                    
    ```

Se seu cluster Kafka de Hubs de Evento tiver eventos na fila do produtor, inicie agora recebendo-os do consumidor.

## <a name="next-steps"></a>Próximas etapas

* [Saiba mais sobre os Hubs de Eventos para o Ecossistema Kafka](event-hubs-for-kafka-ecosystem-overview.md)
* [Saiba sobre os Hubs de Evento](event-hubs-what-is-event-hubs.md)
* Use o [MirrorMaker](https://cwiki.apache.org/confluence/pages/viewpage.action?pageId=27846330) para eventos de fluxo de Kafka local para o Hubs de Evento habilitado Kafa na nuvem.](event-hubs-kafka-mirror-maker-tutorial.md)
