---
title: "Introdução a tópicos e assinaturas do Barramento de Serviço do Azure | Microsoft Docs"
description: "Escreva um aplicativo de console em C# que usa tópicos e assinaturas de mensagens do Barramento de Serviço."
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
ms.date: 06/30/2017
ms.author: sethm
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 9401ada519f600b0d2817f06a396e16607a24129
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017


---
# Introdução aos tópicos do Barramento de Serviço
<a id="get-started-with-service-bus-topics" class="xliff"></a>

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

## O que será realizado
<a id="what-will-be-accomplished" class="xliff"></a>

Este tutorial cobre as seguintes etapas:

1. Crie um namespace do Barramento de Serviço usando o portal do Azure.
2. Crie um tópico do Barramento de Serviço usando o portal do Azure.
3. Crie uma assinatura do Barramento de Serviço para esse tópico usando o portal do Azure.
4. Escreva um aplicativo de console para enviar uma mensagem ao tópico.
5. Escreva um aplicativo de console para receber essa mensagem da assinatura.

## Pré-requisitos
<a id="prerequisites" class="xliff"></a>

1. [Visual Studio 2015 ou superior](http://www.visualstudio.com). Os exemplos neste tutorial usam o Visual Studio 2017.
2. Uma assinatura do Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 1. Criar um namespace usando o portal do Azure
<a id="1-create-a-namespace-using-the-azure-portal" class="xliff"></a>

Se você já criou um namespace de Mensagens do Barramento de Serviço, vá para a seção [Criar um tópico usando o portal do Azure](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## 2. Criar um tópico usando o portal do Azure
<a id="2-create-a-topic-using-the-azure-portal" class="xliff"></a>

1. Faça logon no [Portal do Azure][azure-portal].
2. No painel de navegação à esquerda do portal, clique em **Barramento de Serviço** (se a opção **Barramento de Serviço** não estiver visível, clique em **Mais serviços**).
3. Clique no namespace no qual você gostaria de criar o tópico. Será exibida a folha Visão geral do namespace:
   
    ![Criar um tópico][createtopic1]
4. No **namespace do Barramento de Serviço**, clique em **Tópicos** e clique em **Adicionar tópico**.
   
    ![Selecionar tópicos][createtopic2]
5. Insira um nome para o tópico e desmarque a opção **Ativar particionamento**. Deixe as outras opções com os valores padrão.
   
    ![Selecionar Nova][createtopic3]
6. Na parte inferior da folha, clique em **Criar**.

## 3. Criar uma assinatura para o tópico
<a id="3-create-a-subscription-to-the-topic" class="xliff"></a>

1. No painel de recursos de portal, clique no namespace que você criou na etapa 1 e clique no nome do tópico que criou na etapa 2.
2. Na parte superior do painel Visão geral, clique no sinal de adição ao lado de **Assinatura** para adicionar uma assinatura ao tópico.

    ![Criar assinatura][createtopic4]

3. Insira um nome para a assinatura. Deixe as outras opções com os valores padrão.

## 4. Enviar mensagens para o tópico
<a id="4-send-messages-to-the-topic" class="xliff"></a>

Para enviar mensagens para o tópico, escreveremos um aplicativo de console em C# usando o Visual Studio.

### Criar um aplicativo de console
<a id="create-a-console-application" class="xliff"></a>

Inicie o Visual Studio e crie um novo projeto de **Aplicativo de console (.NET Framework)**.

### Adicionar o pacote NuGet do Barramento de Serviço
<a id="add-the-service-bus-nuget-package" class="xliff"></a>

1. Clique com o botão direito do mouse no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.
2. Clique na guia **Procurar**, pesquise **Barramento de Serviço do Microsoft Azure** e selecione o item **WindowsAzure.ServiceBus**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.
   
    ![Selecionar um pacote NuGet][nuget-pkg]

### Escrever um código para enviar um tópico para a fila
<a id="write-some-code-to-send-a-message-to-the-topic" class="xliff"></a>

1. Adicione a seguinte instrução `using` à parte superior do arquivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
2. Adicione o seguinte código ao `Main` método. Defina a variável `connectionString` para a cadeia de conexão que você obteve ao criar o namespace e defina `topicName` como o nome que você usou ao criar o tópico.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
    var message = new BrokeredMessage("This is a test message!");

    Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
    Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

    client.Send(message);

    Console.WriteLine("Message successfully sent! Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    Seu arquivo Program.cs deve ficar assim.
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.ServiceBus.Messaging;

    namespace tsend
    {
        class Program
        {
            static void Main(string[] args)
            {
                var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";
                var topicName = "<your topic name>";

                var client = TopicClient.CreateFromConnectionString(connectionString, topicName);
                var message = new BrokeredMessage("This is a test message!");

                Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
                Console.WriteLine(String.Format("Message id: {0}", message.MessageId));

                client.Send(message);

                Console.WriteLine("Message successfully sent! Press ENTER to exit program");
                Console.ReadLine();
            }
        }
    }
    ```
3. Execute o programa e verifique o portal do Azure: clique no nome do tópico na folha **Visão geral** do namespace. A folha **Essentials** do tópico é exibida. Nas assinaturas listadas na parte inferior da folha, observe que o valor **Contagem de Mensagens** para cada assinatura agora deve ser 1. Cada vez que você executa o aplicativo de remetente sem recuperar mensagens (conforme descrito na próxima seção), esse valor aumenta em 1. Observe também que o tamanho atual do tópico aumenta o valor **Atual** na folha **Essentials** cada vez que o aplicativo adiciona uma mensagem para o tópico/a assinatura.
   
      ![Tamanho da mensagem][topic-message]

## 5. Receber mensagens da assinatura
<a id="5-receive-messages-from-the-subscription" class="xliff"></a>

1. Para receber as mensagens recém-enviadas, crie um novo aplicativo de console e adicione uma referência ao pacote NuGet do Barramento de Serviço, parecido com o aplicativo de envio anterior.
2. Adicione a seguinte instrução `using` à parte superior do arquivo Program.cs.
   
    ```csharp
    using Microsoft.ServiceBus.Messaging;
    ```
3. Adicione o seguinte código ao `Main` método. Defina a variável `connectionString` para a cadeia de conexão que você obteve ao criar o namespace e defina `topicName` como o nome que você usou ao criar o tópico.
   
    ```csharp
    var connectionString = "<your connection string>";
    var topicName = "<your topic name>";
   
    var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
    client.OnMessage(message =>
    {
      Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
      Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
    });
   
    Console.WriteLine("Press ENTER to exit program");
    Console.ReadLine();
    ```
   
    O arquivo Program.cs deve ficar assim:
   
    ```csharp
    using System;
    using Microsoft.ServiceBus.Messaging;
   
    namespace GettingStartedWithTopics
    {
      class Program
      {
        static void Main(string[] args)
        {
          var connectionString = "Endpoint=sb://<your namespace>.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=<your key>";;
          var topicName = "<your topic name>";
   
          var client = SubscriptionClient.CreateFromConnectionString(connectionString, topicName, "<your subscription name>");
   
          client.OnMessage(message =>
          {
            Console.WriteLine(String.Format("Message body: {0}", message.GetBody<String>()));
            Console.WriteLine(String.Format("Message id: {0}", message.MessageId));
          });

          Console.WriteLine("Press ENTER to exit program");   
          Console.ReadLine();
        }
      }
    }
    ```
4. Execute o programa e verifique o portal novamente. Observe que os valores **Contagem de Mensagens** e **Atuais** agora são 0.
   
    ![Tamanho do tópico][topic-message-receive]

Parabéns! Agora você criou um tópico e uma assinatura, enviou uma mensagem e recebeu essa mensagem.

## Próximas etapas
<a id="next-steps" class="xliff"></a>

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

