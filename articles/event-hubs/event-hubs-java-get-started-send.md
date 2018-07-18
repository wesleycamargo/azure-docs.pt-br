---
title: Enviar eventos para Hubs de Eventos do Azure usando Java | Microsoft Docs
description: Começar a enviar para Hubs de Eventos usando Java
services: event-hubs
documentationcenter: ''
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.workload: core
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: sethm
ms.openlocfilehash: 5dd0c88dab9ff4b7073a9acf6872b4c3ff085586
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
---
# <a name="send-events-to-azure-event-hubs-using-java"></a>Enviar eventos para Hubs de Eventos do Azure usando Java

Os Hubs de Eventos são um sistema de ingestão altamente escalonável que pode ingerir milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em um hub de eventos, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, veja [Visão Geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial mostra como enviar eventos para um hub de eventos usando um aplicativo de console em Java. Para receber eventos usando a biblioteca do Host de processador de eventos do Java, veja [neste artigo](event-hubs-java-get-started-receive-eph.md), ou clique no idioma apropriado de recebimento no sumário à esquerda.

Para concluir este tutorial, você precisará do seguinte:

* Um ambiente de desenvolvimento Java. Para este tutorial, vamos considerar o [Eclipse](https://www.eclipse.org/).
* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.

O código neste tutorial é baseado no [exemplo Enviar exemplo do GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/Send), que é possível examinar para ver o aplicativo completo em funcionamento.

## <a name="send-events-to-event-hubs"></a>Enviar eventos para os Hubs de Eventos

A biblioteca de clientes Java para Hubs de Eventos está disponível para uso em projetos Maven no [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Você pode fazer referência a essa biblioteca usando a seguinte declaração de dependência em seu arquivo do projeto Maven. A versão atual é 1.0.0:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.0</version>
</dependency>
```

Para diferentes tipos de ambiente de build, é possível obter explicitamente os arquivos JAR liberados mais recentemente no [Repositório Central do Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Para um editor de eventos simples, importe o pacote *com.microsoft.azure.eventhubs* para as classes de cliente dos Hubs de Eventos e o pacote *com.microsoft.azure.servicebus* para as classes de utilitário como exceções comuns que são compartilhadas com o cliente de mensagens do Barramento de Serviço do Azure. 

### <a name="declare-the-send-class"></a>Declarar a classe Enviar

Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. Nome da classe `Send`:     

```java
package com.microsoft.azure.eventhubs.samples.send;

import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.PartitionSender;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.Random;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ExecutorService;

public class Send {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
```

### <a name="construct-connection-string"></a>Construir cadeia de conexão

Use a classe ConnectionStringBuilder para construir um valor de cadeia de conexão para passar para a instância do cliente de Hubs de Eventos. Substitua os espaços reservados pelos valores obtidos quando você criou o namespace e o hub de eventos:

```java
   final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
      .setNamespaceName("----NamespaceName-----")
      .setEventHubName("----EventHubName-----")
      .setSasKeyName("-----SharedAccessSignatureKeyName-----")
      .setSasKey("---SharedAccessSignatureKey----");
```

### <a name="send-events"></a>Enviar eventos

Em seguida, crie um evento singular transformando uma cadeia de caracteres em sua codificação de bytes UTF-8. Em seguida, crie uma nova instância cliente dos Hubs de Eventos usando a cadeia de conexão e envie a mensagem.   

```java 
byte[] payloadBytes = "Test AMQP message from JMS".getBytes("UTF-8");
EventData sendEvent = new EventData(payloadBytes);

final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);
ehClient.sendSync(sendEvent);
    
// close the client at the end of your program
ehClient.closeSync();

``` 

## <a name="next-steps"></a>Próximas etapas

Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Receber eventos usando o EventProcessorHost](event-hubs-java-get-started-receive-eph.md)
* [Visão Geral dos Hubs de Eventos][Event Hubs overview]
* [Criar um hub de eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-overview.md
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

