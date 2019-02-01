---
title: Receber eventos usando Java – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece instruções passo a passo para a criação de um aplicativo Java que recebe eventos dos Hubs de Eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 0fc9b8b6a8bcd62aafda7c04697ab8b9c096b17e
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55296572"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Receber eventos de Hubs de Eventos do Azure usando Java

Os Hubs de Eventos são um sistema de ingestão altamente escalonável que pode ingerir milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para obter mais informações, veja [Visão Geral dos Hubs de Eventos][Event Hubs overview].

Este tutorial mostra como receber eventos em um hub de eventos usando um aplicativo de console escrito em Java.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* Um ambiente de desenvolvimento Java. Para este tutorial, vamos considerar o [Eclipse](https://www.eclipse.org/).
* Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.

O código neste tutorial se baseia no [código de EventProcessorSample no GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), que você pode examinar para ver todo o aplicativo funcional.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Receber mensagens com EventProcessorHost em Java

**EventProcessorHost** é uma classe Java que simplifica o recebimento de eventos dos Hubs de Eventos gerenciando pontos de verificação persistentes e recebimentos paralelos desses Hubs de Eventos. Usando o EventProcessorHost, você pode dividir eventos entre vários destinatários, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o EventProcessorHost para um único destinatário.

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para usar EventProcessorHost, você deve ter uma [conta de Armazenamento do Azure][Azure Storage account]:

1. Faça logon no [portal do Azure][Azure portal] e clique em **+ Criar um recurso** no lado esquerdo da tela.
2. Clique em **Armazenamento** e em **conta de Armazenamento**. Na janela **Criar conta de armazenamento**, digite um nome para a conta de armazenamento. Preencha o restante dos campos, selecione a região desejada e clique em **Criar**.
   
    ![Criar Conta de Armazenamento](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Clique na conta de armazenamento criada recentemente e, em seguida, clique em **Chaves de Acesso**:
   
    ![Obter chaves de acesso](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Copie o valor key1 para um local temporário. Você o usará posteriormente neste tutorial.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Crie um projeto Java usando o Host de EventProcessor

A biblioteca de cliente Java para os Hubs de Eventos está disponível para uso em projetos do Maven por meio do [Repositório Central do Maven][Maven Package], e pode ser referenciada usando a seguinte declaração de dependência dentro do arquivo de projeto do Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Para diferentes tipos de ambiente de build, é possível obter explicitamente os arquivos JAR liberados mais recentemente no [Repositório Central do Maven][Maven Package].  

1. Para o exemplo a seguir, primeiro crie um novo projeto do Maven para um aplicativo de console/shell em seu ambiente de desenvolvimento Java favorito. A classe é chamada `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Use o código a seguir para criar uma nova classe chamada `EventProcessorSample`. Substitua os espaços reservados pelos valores usados durante a criação do hub de eventos e da conta de armazenamento:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Crie mais uma classe chamada `EventProcessor` usando o código a seguir:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Este tutorial usa uma única instância do EventProcessorHost. Para aumentar a taxa de transferência, é recomendável executar várias instâncias de EventProcessorHost, preferencialmente em computadores separados.  Isso também fornece redundância. Nesses casos, as diversas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. Se você quiser que vários destinatários processem, cada um, *todos* os eventos, você deve usar o conceito **ConsumerGroup** . Ao receber eventos em máquinas diferentes, pode ser útil especificar nomes para instâncias de EventProcessorHost com base em máquinas (ou funções) nas quais eles foram implantados.

## <a name="publishing-messages-to-eventhub"></a>Publicar mensagens no EventHub

Antes que as mensagens sejam recuperadas pelos consumidores, elas precisam primeiro ser publicadas nas partições pelos editores. Vale observar que, quando as mensagens são publicadas no Hub de Eventos de modo síncrono usando o método sendSync() no objeto com.microsoft.azure.eventhubs.EventHubClient, a mensagem foi enviada para uma partição específica ou distribuída a todas as partições disponíveis de forma round robin, dependendo de a chave de partição ser especificada ou não.

Quando uma cadeia de caracteres que representa a chave de partição for especificada, a chave receberá um hash para determinar a qual partição enviar o evento.

Quando a chave de partição não for definida, as mensagens serão distribuídas por round robin a todas as partições disponíveis

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementar um CheckpointManager personalizado para EventProcessorHost (EPH)

A API fornece um mecanismo para implementar seu gerenciador de ponto de verificação personalizado em cenários em que a implementação padrão não é compatível com o seu caso de uso.

O gerenciador de ponto de verificação padrão usa o armazenamento de blobs. No entanto, se substituir o gerenciador de ponto de verificação usado pelo EPH por sua própria implementação, você poderá usar qualquer armazenamento que quiser para fazer a sua implementação desse gerenciador.

Crie uma classe que implementa a interface com.microsoft.azure.eventprocessorhost.ICheckpointManager

Use sua implementação personalizada do gerenciador de ponto de verificação (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

Dentro de sua implementação, você pode substituir o mecanismo de ponto de verificação padrão e implementar seus próprios pontos de verificação com base em seu próprio armazenamento de dados (SQL Server, CosmosDB, Cache do Azure para Redis etc.). É recomendável que o armazenamento usado para fazer sua implementação do gerenciador de ponto de verificação seja acessível para todas as instâncias do EPH que estejam processando eventos para o grupo de consumidores.

Você pode usar qualquer armazenamento de dados que estiver disponível em seu ambiente.

A classe com.microsoft.azure.eventprocessorhost.EventProcessorHos fornece dois construtores que permitem que você substitua o gerenciador de ponto de verificação do EventProcessorHost.

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um aplicativo Java que recebeu mensagens de um hub de eventos. Para saber como enviar eventos para um hub de eventos usando o Java, consulte [Enviar eventos do hub de eventos – Java](event-hubs-java-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
