---
title: "Introdução a tópicos e assinaturas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Escreva um aplicativo de console em C# .NET Core que usa tópicos e assinaturas de mensagens do Barramento de Serviço."
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: 5391951c34498b3bd684cd2e223b8288d4040247
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="get-started-with-service-bus-topics"></a>Introdução aos tópicos do Barramento de Serviço

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Este tutorial cobre as seguintes etapas:

1. Crie um namespace do Barramento de Serviço usando o portal do Azure.
2. Crie um tópico do Barramento de Serviço usando o portal do Azure.
3. Crie uma assinatura do Barramento de Serviço para esse tópico usando o portal do Azure.
4. Escreva um aplicativo de console .NET Core para enviar um conjunto de mensagens ao tópico.
5. Escreva um aplicativo de console .NET Core para receber essas mensagens da assinatura.

## <a name="prerequisites"></a>pré-requisitos

1. [Atualização 3 do Visual Studio 2017 (versão 15.3, 26730.01)](http://www.visualstudio.com/vs) ou posterior.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure

> [!NOTE] 
> Você também pode criar um namespace de Barramento de Serviço e entidades de mensagens usando o [PowerShell](/powershell/azure/get-started-azureps). Para obter mais informações, consulte [Usar o PowerShell para gerenciar recursos do Barramento de Serviço](service-bus-manage-with-ps.md).

Se você já criou um namespace de Mensagens do Barramento de Serviço, vá para a seção [Criar um tópico usando o portal do Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Criar um tópico usando o portal do Azure

1. Faça logon no [Portal do Azure][azure-portal].
2. No painel de navegação à esquerda do portal, clique em **Barramento de Serviço** (se a opção **Barramento de Serviço** não estiver visível, clique em **Mais serviços** ou clique em **Todos os Recursos**). Clique no namespace no qual você gostaria de criar o tópico. 
3. A janela de visão geral do namespace é aberta. Clique em **Tópicos**:
   
    ![Criar um tópico][createtopic1]
4. Clique em **+ Tópico**.
   
    ![Selecionar tópicos][createtopic2]
5. Insira um nome para o tópico. Deixe as outras opções com os valores padrão.
   
    ![Selecionar Nova][createtopic3]
6. Na parte inferior do diálogo, clique em **Criar**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Criar uma assinatura para o tópico

1. No painel de recursos de portal, clique no namespace que você criou na etapa 1, depois clique em **Tópicos** e no nome do tópico que criou na etapa 2.
2. Na parte superior do painel de visão geral, clique em **+ Assinatura** para adicionar uma assinatura ao tópico.

    ![Criar assinatura][createtopic4]

3. Insira um nome para a assinatura. Deixe as outras opções com os valores padrão.

## <a name="4-send-messages-to-the-topic"></a>4. Enviar mensagens para o tópico

Para enviar mensagens para o tópico, escreva um aplicativo de console C# usando o Visual Studio.

### <a name="create-a-console-application"></a>Criar um aplicativo de console

Inicie o Visual Studio e crie um novo projeto de **Aplicativo de console (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise por **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** e depois selecione o item **Microsoft.Azure.ServiceBus**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Escrever o código para enviar mensagens para o tópico

1. Em Program.cs, adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dentro de classe `Program`, declare as variáveis a seguir. Defina a variável `ServiceBusConnectionString` para a cadeia de conexão que você obteve ao criar o namespace e defina `TopicName` como o nome que você usou ao criar o tópico:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
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
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
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
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. O arquivo Program.cs do remetente deve ficar assim.
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
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

3. Execute o programa e verifique o portal do Azure: clique no nome do tópico na janela **Visão geral** do namespace. A tela **Essentials** do tópico é exibida. Na assinatura listadas próxima à parte inferior da janela, observe que o valor **Contagem de Mensagens** da assinatura agora é **10**. Cada vez que você executa o aplicativo do remetente sem recuperar mensagens (conforme descrito na próxima seção), esse valor aumenta em 10. Observe também que o tamanho atual do tópico aumenta o valor **Atual** na janela **Essentials** cada vez que o aplicativo adiciona mensagens ao tópico.
   
      ![Tamanho da mensagem][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Receber mensagens da assinatura

Para receber as mensagens recém-enviadas, crie outro aplicativo de console .NET Core e instale o pacote NuGet **Microsoft.Azure.ServiceBus**, parecido com o aplicativo do remetente anterior.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Escrever código para receber mensagens da assinatura

1. Em Program.cs, adicione as seguintes instruções `using` na parte superior da definição do namespace, antes da declaração de classe:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. Dentro de classe `Program`, declare as variáveis a seguir. Defina a variável `ServiceBusConnectionString` para a cadeia de conexão obtida ao criar o namespace, defina `TopicName` como o nome usado ao criar o tópico e defina `SubscriptionName` como o nome usado ao criar a assinatura para o tópico:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Substitua o conteúdo padrão de `Main()` com a seguinte linha de código:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Diretamente após `Main()`, adicione o método `MainAsync()` assíncrono a seguir que chama o método `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
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
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Diretamente após o método anterior, adicione o método `ProcessMessagesAsync()` a seguir para processar as mensagens recebidas:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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

8. O arquivo Program.cs do destinatário deve ficar assim:
   
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
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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
9. Execute o programa e verifique o portal novamente. Observe que os valores **Contagem de Mensagens** e **Atuais** agora são **0**.
   
    ![Tamanho do tópico][topic-message-receive]

Parabéns! Usando a biblioteca .NET padrão, você acabou de criar um tópico e uma assinatura, enviar 10 mensagens e receber essas mensagens.

## <a name="next-steps"></a>Próximas etapas

Confira nosso [Repositório GitHub com exemplos](https://github.com/Azure/azure-service-bus/tree/master/samples) que demonstram alguns dos recursos mais avançados de mensagens do Barramento de Serviço.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
