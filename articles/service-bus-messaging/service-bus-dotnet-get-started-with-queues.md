---
title: "Escreva um programa que usa as filas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Como escrever um aplicativo de console em C# para a mensagem do Barramento de Serviço"
services: service-bus-messaging
documentationcenter: .net
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 03/23/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: f92909e0098a543f99baf3df3197a799bc9f1edc
ms.openlocfilehash: 83649bdad1d369cdfe4edf3c2bdaa67180db8668
ms.lasthandoff: 03/01/2017


---
# <a name="get-started-with-service-bus-queues"></a>Introdução às filas do Barramento de Serviço
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

## <a name="what-will-be-accomplished"></a>O que será realizado
Neste tutorial, faremos seguinte:

1. Crie um namespace do Barramento de Serviço usando o portal do Azure.
2. Crie uma fila de Mensagens do Barramento de Serviço usando o portal do Azure.
3. Escreva um aplicativo de console para enviar uma mensagem.
4. Escreva um aplicativo de console para enviar mensagens.

## <a name="prerequisites"></a>Pré-requisitos
1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2015.
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Criar um namespace usando o portal do Azure
Se você já tiver um namespace do Barramento de Serviço criado, vá para a seção [Criar uma fila usando o portal do Azure](#2-create-a-queue-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-queue-using-the-azure-portal"></a>2. Criar uma fila usando o portal do Azure
Se você já tiver uma fila do Barramento de Serviço criada, vá para a seção [Enviar mensagens para a fila](#3-send-messages-to-the-queue).

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="3-send-messages-to-the-queue"></a>3. Enviar mensagens para a fila
Para enviar mensagens para a fila, escreveremos um aplicativo de console em C# usando o Visual Studio.

### <a name="create-a-console-application"></a>Criar um aplicativo de console

- Inicialize o Visual Studio e crie um novo aplicativo de Console.

### <a name="add-the-service-bus-nuget-package"></a>Adicionar o pacote NuGet do Barramento de Serviço
1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, em seguida, pesquise "Barramento de Serviço do Microsoft Azure" e selecione o item **Barramento de Serviço do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### <a name="write-some-code-to-send-a-message-to-the-queue"></a>Escrever um código para enviar uma mensagem para a fila
1. Adicione a seguinte instrução using ao topo do arquivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione o seguinte código ao método `Main`, defina a variável **connectionString** como a cadeia de conexão que foi obtida ao criar o namespace e defina **queueName** como o nome da fila usado ao criar a fila.
   
    ```csharp
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);
    ```
   
    Program.cs deve ficar assim.
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "<Your connection string>";
                var queueName = "<Your queue name>";
   
                var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
                var message = new BrokeredMessage("This is a test message!");
   
                client.Send(message);
            }
        }
    }
    ```
3. Execute o programa e verifique o portal do Azure. Clique no nome da fila na folha **Visão geral** do namespace. Observe que agora o valor de **Contagem de mensagens ativas** deve ser 1.
   
      ![Contagem de mensagens][queue-message]

## <a name="4-receive-messages-from-the-queue"></a>4. Receber mensagens da fila
1. Crie um novo aplicativo de console e adicione uma referência ao pacote NuGet do Barramento de Serviço, parecido com o aplicativo de envio anterior.
2. Adicione a seguinte instrução `using` à parte superior do arquivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adicione o seguinte código ao método `Main`, defina a variável **connectionString** como a cadeia de conexão que foi obtida ao criar o namespace e defina **queueName** como o nome da fila usado ao criar a fila.
   
    ```csharp
    var connectionString = "";
    var queueName = "samplequeue";
   
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.ReadLine();
    ```
   
    O arquivo Program.cs deve ficar assim:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithQueues
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "";
          var queueName = "samplequeue";
   
          var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });
   
          Console.ReadLine();
        }
      }
    }
    ```
4. Execute o programa e verifique o portal. Observe que agora o valor **Comprimento da Fila** deve ser 0.
   
    ![Comprimento da fila][queue-message-receive]

Parabéns! Agora, você criou uma fila, enviou uma mensagem e recebeu uma mensagem.

## <a name="next-steps"></a>Próximas etapas
Confira nosso [Repositório GitHub com exemplos](https://github.com/Azure-Samples/azure-servicebus-messaging-samples) que demonstram alguns dos recursos mais avançados de mensagens do Barramento de Serviço do Azure.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

