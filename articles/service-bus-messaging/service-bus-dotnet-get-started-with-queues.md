---
title: "Introdução às filas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Escreva um aplicativo de console em C# que usa filas de mensagens do Barramento de Serviço."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/7/2017
ms.author: sethm
ms.openlocfilehash: 6af7e4d238c10c0fed3443db58644e3557525993
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="get-started-with-service-bus-queues"></a>Introdução às filas do Barramento de Serviço

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este tutorial cobre as seguintes etapas:

1. Crie um namespace do Barramento de Serviço usando o portal do Azure.
2. Crie uma fila do Barramento de Serviço usando o portal do Azure.
3. Escreva um aplicativo de console .NET Core para enviar um conjunto de mensagens à fila.
4. Escreva um aplicativo de console .NET Core para receber essas mensagens da fila.

## <a name="prerequisites"></a>Pré-requisitos

1. [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](http://www.visualstudio.com/vs) ou posterior.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure

> [!NOTE] 
> Você também pode criar um namespace de Barramento de Serviço e entidades de mensagens usando o [PowerShell](/powershell/azure/get-started-azureps). Para obter mais informações, consulte [Usar o PowerShell para gerenciar recursos do Barramento de Serviço](service-bus-manage-with-ps.md).

Se você já criou um namespace de Mensagens do Barramento de Serviço, vá para a seção [Criar uma fila usando o portal do Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Criar uma fila usando o portal do Azure

Se você já criou uma fila do Barramento de Serviço, vá para a seção [Enviar mensagens para a fila](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Enviar mensagens para a fila

Para enviar mensagens para a fila, escreva um aplicativo de console em C# usando o Visual Studio.

### <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio e crie um novo projeto de **Aplicativo de console (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** e depois selecione o item **Microsoft.Azure.ServiceBus**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Escreva o código para enviar mensagens para a fila

1. Em Program.cs, adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dentro de classe `Program`, declare as variáveis a seguir. Defina a variável `ServiceBusConnectionString` para a cadeia de conexão que você obteve ao criar o namespace e defina `QueueName` como o nome que você usou ao criar a fila:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ``` 

3. Substitua o conteúdo padrão de `Main()` com a seguinte linha de código:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Diretamente após `Main()`, adicione o método `MainAsync()` assíncrono a seguir que chama o método de envio de mensagens:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Diretamente após o método `MainAsync()`, adicione o método `SendMessagesAsync()` a seguir que realiza o trabalho de enviar o número de mensagens especificado pelo método `numberOfMessagesToSend` (atualmente definido como 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. Seu arquivo Program.cs deve ficar assim.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

7. Execute o programa e verifique o portal do Azure: clique no nome da fila na janela **Visão geral** do namespace. A tela **Essentials** da fila é exibida. Observe que agora o valor de **Contagem de Mensagens Ativas** da fila é **10**. Cada vez que você executa o aplicativo do remetente sem recuperar mensagens (conforme descrito na próxima seção), esse valor aumenta em 10. Observe também que o tamanho atual da fila aumenta o valor **Atual** na janela **Essentials** cada vez que o aplicativo adiciona mensagens à fila.
   
      ![Tamanho da mensagem][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Receber mensagens da fila

Para receber as mensagens recém-enviadas, crie outro aplicativo de console .NET Core e instale o pacote NuGet **Microsoft.Azure.ServiceBus**, parecido com o aplicativo do remetente anterior.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Escrever código para receber mensagens da fila

1. Em Program.cs, adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dentro de classe `Program`, declare as variáveis a seguir. Defina a variável `ServiceBusConnectionString` para a cadeia de conexão que você obteve ao criar o namespace e defina `QueueName` como o nome que você usou ao criar a fila:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Substitua o conteúdo padrão de `Main()` com a seguinte linha de código:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Diretamente após `Main()`, adicione o método `MainAsync()` assíncrono a seguir que chama o método `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
    }
    ```

5. Diretamente após o método `MainAsync()`, adicione o método a seguir que registra o manipulador de mensagens e recebe as mensagens enviadas pelo aplicativo do remetente:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Diretamente após o método anterior, adicione o método `ProcessMessagesAsync()` a seguir para processar as mensagens recebidas:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Por fim, adicione o seguinte método para lidar com qualquer exceção que possa ocorrer:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    
   
8. O arquivo Program.cs deve ficar assim:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
               // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
4. Execute o programa e verifique o portal novamente. Observe que os valores **Contagem de Mensagens Ativas** e **Atuais** agora são **0**.
   
    ![Comprimento da fila][queue-message-receive]

Parabéns! Agora você criou uma fila, enviou um conjunto de mensagens para ela e recebeu essas mensagens da mesma fila.

## <a name="next-steps"></a>Próximas etapas

Confira nosso [Repositório GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) que demonstram alguns dos recursos mais avançados de mensagens do Barramento de Serviço.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

