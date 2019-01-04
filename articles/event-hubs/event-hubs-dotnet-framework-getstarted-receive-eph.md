---
title: Receber eventos usando o .NET Framework - Hubs de Eventos do Azure | Microsoft Docs
description: Siga este tutorial para receber eventos de Hubs de Eventos do Azure usando o .NET Framework.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: c4974bd3-2a79-48a1-aa3b-8ee2d6655b28
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 8052b797707f7913fdd678f4dd51822754623104
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53077239"
---
# <a name="receive-events-from-azure-event-hubs-using-the-net-framework"></a>Receber eventos de Hubs de Eventos do Azure usando o .NET Framework

## <a name="introduction"></a>Introdução

Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas). Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial mostra como escrever um aplicativo de console do .NET Framework que recebe mensagens de um hub de eventos usando o [Host do Processador de Eventos](event-hubs-event-processor-host.md). O [Host do Processador de Eventos](event-hubs-event-processor-host.md) é uma classe do .NET que simplifica o recebimento de eventos de hubs de eventos ao gerenciar pontos de verificação persistentes e recebimentos paralelos desses hubs de eventos. Ao usar o Host do Processador de Eventos, você pode dividir eventos através de vários receptores, mesmo quando hospedados em nós diferentes. Este exemplo mostra como usar o Host do Processador de Eventos para um único destinatário. O exemplo de [Processamento de eventos de escala horizontal][Scale out Event Processing with Event Hubs] mostra como usar o Host do Processador de Eventos com vários destinatários.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

* [Microsoft Visual Studio 2017 ou superior](https://visualstudio.com).

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

## <a name="create-a-console-application"></a>Criar um aplicativo de console

No Visual Studio, crie um novo projeto de aplicativo de área de trabalho do Visual C# usando o modelo de projeto de **Aplicativo de Console** . Nomeie o projeto como **Destinatário**.
   
![Criar um aplicativo de console](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Eventos

1. No Gerenciador de Soluções, clique com o botão direito no projeto **Destinatário** e clique em **Gerenciar Pacotes NuGet para Solução**.
2. Clique na guia **Procurar** e procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Clique em **Instalar**e aceite os termos de uso.
   
    ![Pesquisar pacote NuGet do Host do Processador de Eventos](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    O Visual Studio baixa, instala e adiciona uma referência ao [Hub de Eventos do Barramento de Serviço do Azure - pacote NuGet do EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com todas as suas dependências.

## <a name="implement-the-ieventprocessor-interface"></a>Implementar a interface IEventProcessor

1. Clique com o botão direito do mouse no projeto do **Receptor**, clique em **Adicionar** e clique em **Classe**. Nomeie a nova classe **SimpleEventProcessor** e clique em **Adicionar** para criar a classe.
   
    ![Adicionar classe SimpleEventProcessor](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Adicione as seguintes instruções na parte superior do arquivo SimpleEventProcessor.cs file:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Substitua o seguinte código no corpo da classe:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Essa classe é chamada pelo **EventProcessorHost** para processar eventos recebidos do hub de eventos. A classe `SimpleEventProcessor` usa um cronômetro para chamar o método de ponto de verificação periodicamente no contexto do **EventProcessorHost** . Esse processamento garante que, se o destinatário for reiniciado, ele perderá, no máximo, cinco minutos de trabalho de processamento.

## <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Atualizar o método Main para usar o SimpleEventProcessor

1. Na classe **Program**, adicione a seguinte instrução `using` na parte superior do arquivo:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Substitua o método `Main` na classe `Program` pelo código a seguir, substituindo o nome do hub de eventos e a cadeia de conexão no nível de namespace que você salvou anteriormente, e a conta de armazenamento e a chave que você copiou nas seções anteriores. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Agora, você recebeu mensagens de um hub de eventos usando o host do processador de eventos.


> [!NOTE]
> Este tutorial usa uma única instância do [EventProcessorHost](event-hubs-event-processor-host.md). Para aumentar o rendimento, recomendamos que você execute várias instâncias de [EventProcessorHost](event-hubs-event-processor-host.md), conforme mostrado na amostra de [Processamento de evento com escala](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3). Nesses casos, as múltiplas instâncias são coordenadas automaticamente umas com as outras para balancear a carga de eventos recebidos. 

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um aplicativo .NET Framework, que recebeu mensagens de um hub de eventos. Para saber como enviar eventos para um hub de eventos usando o .NET Framework, confira [Enviar eventos de hub de eventos – .NET Framework](event-hubs-dotnet-framework-getstarted-send.md).

<!-- Images. -->
[19]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj1.png
[20]: ./media/event-hubs-csharp-ephcs-getstarted/create-eh-proj2.png
[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Scale out Event Processing with Event Hubs]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
