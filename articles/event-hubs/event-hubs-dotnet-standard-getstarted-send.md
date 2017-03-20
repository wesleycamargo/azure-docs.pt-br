---
title: Enviar eventos para Hubs de Eventos do Azure usando .NET Standard | Microsoft Docs
description: "Começar a enviar eventos para Hubs de Eventos no .NET Standard"
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 6a6fe5e2e706fd8ab4ee6c51cde5b54fa703688b
ms.lasthandoff: 03/06/2017

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Começar a enviar mensagens a Hubs de Eventos no .NET Standard

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender).

Este tutorial mostra como escrever um aplicativo do console .NET Core que envia um conjunto de mensagens para um Hub de Eventos. Você pode executar a solução [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleSender) como está, substituindo as cadeias de caracteres `EhConnectionString` e `EhEntityPath` pelos valores do Hub de Eventos ou então seguir as etapas neste tutorial para criar as suas próprias.

## <a name="prerequisites"></a>Pré-requisitos

1. [Microsoft Visual Studio 2015 ou 2017](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2015, mas também há suporte para o Visual Studio 2017.
2. [Ferramentas do .NET Core do Visual Studio 2015 ou 2017](http://www.microsoft.com/net/core).
3. Uma assinatura do Azure.
4. Um namespace dos Hubs de Eventos.

Para enviar mensagens para um Hub de Eventos, escreveremos um aplicativo de console em C# usando o Visual Studio.

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um Hub de Eventos

A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o Hub de Eventos. Para criar um namespace e um Hub de eventos, execute o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir.

## <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio. No menu Arquivo, clique em **Novo** e em **Projeto**. Crie um aplicativo de console do .NET Core.

![][1]

## <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Evento

Adicione o pacote NuGet [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) ao seu projeto.

## <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escrever um código para enviar mensagens ao Hub de Eventos

1. Adicione as instruções `using` a seguir à parte superior do arquivo Program.cs.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    ```

2. Adicione constantes para a classe `Program` para a cadeia de conexão de Hubs de Eventos e o caminho de entidade (nome do Hub de Eventos individual). Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação do Hub de Eventos.

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Adicione um novo método chamado `MainAsync` à classe `Program`, conforme demonstrado a seguir:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
        // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
        // we are using the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
        {
            EntityPath = EhEntityPath
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```
    
4. Adicione um novo método chamado `SendMessagesToEventHub` à classe `Program` semelhante ao seguinte:

    ```csharp
    // Creates an Event Hub client and sends 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Adicione o código a seguir ao método `Main` na classe `Program`.

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Program.cs deve ficar assim.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;
       
        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EhConnectionString = "{Event Hubs connection string}";
            private const string EhEntityPath = "{Event Hub path/name}";
        
            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }
        
            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from a the connection string, and sets the EntityPath.
                // Typically the connection string should have the Entity Path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EhConnectionString)
                {
                    EntityPath = EhEntityPath
                };
        
                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
        
                await SendMessagesToEventHub(100);
        
                await eventHubClient.CloseAsync();
        
                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }
        
            // Creates an Event Hub client and sends 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }
        
                    await Task.Delay(10);
                }
        
                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```
  
6. Execute o programa e certifique-se de que não existem erros.
  
Parabéns! Agora você enviou mensagens para um Hub de Eventos.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Receber eventos de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

[1]: ./media/event-hubs-dotnet-standard-getstarted-send/netcore.png
