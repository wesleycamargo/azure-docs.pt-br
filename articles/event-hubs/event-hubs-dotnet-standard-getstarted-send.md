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
ms.date: 01/30/2017
ms.author: jotaub
translationtype: Human Translation
ms.sourcegitcommit: aa7244849f6286e8ef9f9785c133b4c326193c12
ms.openlocfilehash: c549676b2126448d52ed43af509671d0b593a064

---

# <a name="get-started-sending-messages-to-event-hubs-in-net-standard"></a>Começar a enviar mensagens a Hubs de Eventos no .NET Standard

> [!NOTE]
> Este exemplo está disponível em [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender).

## <a name="what-will-be-accomplished"></a>O que será realizado

Este tutorial mostra como criar a solução existente **SampleSender** (dentro dessa pasta). Você pode executar a solução como-está, substituindo as cadeias de caracteres `EhConnectionString`, `EhEntityPath` e `StorageAccount` por seus valores de Hub de Eventos, ou siga este tutorial para criar suas próprias.

Neste tutorial, vamos escrever um aplicativo de console .NET Core para enviar mensagens para um Hub de Eventos.

## <a name="prerequisites"></a>Pré-requisitos

1. [Visual Studio 2015](http://www.visualstudio.com).

2. [Ferramentas do .NET Core do Visual Studio 2015](http://www.microsoft.com/net/core).

3. Uma assinatura do Azure.

4. Um namespace dos Hubs de Eventos.

## <a name="send-messages-to-an-event-hub"></a>Enviar mensagens a um Hub de Eventos

Para enviar mensagens para um Hub de Eventos, escreveremos um aplicativo de console em C# usando o Visual Studio.

### <a name="create-a-console-application"></a>Criar um aplicativo de console

* Inicie o Visual Studio e crie um novo aplicativo de console do .NET Core.

### <a name="add-the-event-hubs-nuget-package"></a>Adicione o pacote NuGet de Hubs de Evento

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.

2. Clique na guia **Procurar** e pesquise "Hubs de Eventos do Microsoft Azure" e selecione o item **Hubs de Eventos do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.

### <a name="write-some-code-to-send-messages-to-the-event-hub"></a>Escrever um código para enviar mensagens ao Hub de Eventos

1. Adicione a seguinte instrução `using` à parte superior do arquivo Program.cs.

    ```cs
    using Microsoft.Azure.EventHubs;
    ```

2. Adicione constantes para a classe `Program` para a cadeia de conexão de Hubs de Eventos e o caminho de entidade (nome do Hub de Eventos individual). Substitua os espaços reservados nos colchetes pelos valores adequados que foram obtidos na criação do Hub de Eventos.

    ```cs
    private static EventHubClient eventHubClient;
    private const string EhConnectionString = "{Event Hubs connection string}";
    private const string EhEntityPath = "{Event Hub path/name}";
    ```

3. Adicione um novo método chamado `MainAsync` à classe `Program` semelhante ao seguinte:

    ```cs
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

        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();
    }
    ```
    
4. Adicione um novo método chamado `SendMessagesToEventHub` à classe `Program` semelhante ao seguinte:

    ```cs
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

    ```cs
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Program.cs deve ficar assim.

    ```cs
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
    
                Console.WriteLine("Press any key to exit.");
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
  
6. Execute o programa e certifique-se de que não existam erros gerados.
  
Parabéns! Agora você enviou mensagens para um Hub de Eventos.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Receber eventos de Hubs de Eventos](event-hubs-dotnet-standard-getstarted-receive-eph.md)
* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)


<!--HONumber=Feb17_HO1-->


