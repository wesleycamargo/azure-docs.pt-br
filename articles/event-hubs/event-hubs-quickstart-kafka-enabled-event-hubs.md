---
title: Transmitir para os Hubs de Eventos do Azure para o Apache Kafka | Microsoft Docs
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
ms.date: 08/01/2018
ms.author: bahariri
ms.openlocfilehash: 90d9f3620f954da42add08a0aebf779a95c7e7a3
ms.sourcegitcommit: 974c478174f14f8e4361a1af6656e9362a30f515
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42023190"
---
# <a name="stream-into-event-hubs-for-the-apache-kafka"></a>Transmitir para Hubs de Eventos do Apache Kafka
Este guia de início rápido mostra como transmitir para o Kafka habilitado dos Hubs de Eventos sem alterar seus clientes de protocolo ou seus próprio clusters em execução. Você aprenderá a usar seu produtores e os consumidores para se comunicar com os Hubs de Eventos habilitados com Kafka com apenas uma alteração de configuração nos seus aplicativos. Hubs de Eventos do Azure dá suporte para [Apache Kafka versão 1.0.](https://kafka.apache.org/10/documentation.html)

> [!NOTE]
> Este exemplo está disponível no [GitHub](https://github.com/Azure/azure-event-hubs)

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, você precisa atender aos seguinte pré-requisitos:

* Leia o artigo [Hubs de Eventos para o Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).
* Uma assinatura do Azure. Se você não tiver uma, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
* [Java Development Kit (JDK) 1.7+](http://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Realizar o download](http://maven.apache.org/download.cgi) e [instalar](http://maven.apache.org/install.html) um armazenamento binário Maven
* [Git](https://www.git-scm.com/)
* [Namespace de Hubs de Evento habilitado com Kafka](event-hubs-create.md)

## <a name="create-a-kafka-enabled-event-hubs-namespace"></a>Criar um Kafka habilitado com o namespace dos Hubs de Evento

1. Entre no [Azure portal][Azure portal] e clique em **Criar um recurso** na parte superior esquerda da tela.

2. Pesquisar os Hubs de eventos e selecionar as opções mostradas aqui:
    
    ![Exportar para Hubs de Eventos no portal](./media/event-hubs-create-kafka-enabled/event-hubs-create-event-hubs.png)
 
3. Forneça um nome exclusivo e habilite o Kafka no namespace. Clique em **Criar**.
    
    ![Criar um namespace](./media/event-hubs-create-kafka-enabled/create-kafka-namespace.png)
 
4. Depois que o namespace foi criado, na guia **Configurações**, clique em **Políticas de acesso compartilhado** para obter a cadeia de conexão.

    ![Clique em Políticas de acesso compartilhado](./media/event-hubs-create/create-event-hub7.png)

5. Você pode escolher o padrão **RootManageSharedAccessKey**, ou adicionar uma nova política. Clique no nome da política e copie a cadeia de conexão. 
    
    ![Selecione uma política](./media/event-hubs-create/create-event-hub8.png)
 
6. Adiciona esta cadeia de conexão à sua configuração de aplicativo Kafka.

Agora você pode transmitir eventos de aplicativos que usam o protocolo Kafka nos Hubs de eventos.

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
Neste artigo, você aprendeu como transmitir para os Hubs de Eventos com Kafka habilitado sem alterar seus clientes de protocolo ou seus próprios clusters em execução. Para saber mais, continue com o tutorial a seguir:

> [!div class="nextstepaction"]
> [Usar o MirrorMaker Kafka com Hubs de Eventos](event-hubs-kafka-mirror-maker-tutorial.md)
