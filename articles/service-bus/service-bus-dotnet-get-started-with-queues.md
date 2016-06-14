<properties
   pageTitle="Introdução às filas do Barramento de Serviço | Microsoft Azure"
   description="Como escrever um aplicativo de console em C# para a mensagem do Barramento de Serviço"
   services="service-bus"
   documentationCenter=".net"
   authors="jtaubensee"
   manager="timlt"
   editor="sethmanheim"/>

<tags
   ms.service="service-bus"
   ms.devlang="tbd"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="dotnet"
   ms.workload="na"
   ms.date="06/07/2016"
   ms.author="jotaub@microsoft.com"/>

#Introdução às Filas do Barramento de Serviço
[AZURE.INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

##O que será realizado
Neste tutorial, faremos seguinte:

1. Crie um namespace do Barramento de Serviço usando o portal do Azure.

2. Crie uma fila de Mensagens do Barramento de Serviço usando o portal do Azure.

3. Escreva um aplicativo de console para enviar uma mensagem.

4. Escreva um aplicativo de console para enviar mensagens.

##Pré-requisitos
1. [Visual Studio 2013 / Visual Studio 2015](http://www.visualstudio.com)

2. Uma assinatura do Azure

##1\. Criar um namespace usando o portal do Azure
Se você já tiver um namespace do Barramento de Serviço criado, vá para a [seção Criar uma fila usando o portal do Azure](#2-create-a-queue-using-the-azure-portal).

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

##2\. Criar uma fila usando o portal do Azure
Se você já tiver uma fila do Barramento de Serviço criada, vá para a [seção Enviar mensagens para a fila](#3-sending-messages-to-the-queue).

[AZURE.INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

##3\. Enviar mensagens para a fila
Para enviar mensagens para a fila, escreveremos um aplicativo de console em C# usando o Visual Studio.

###Criar um aplicativo de console
1. Inicialize o Visual Studio e crie um novo aplicativo de Console.

###Adicionar o pacote NuGet do Barramento de Serviço
1. Clique com o botão direito no projeto recém-criado e selecione **Gerenciar Pacotes NuGet**.

2. Procure "Barramento de Serviço do Microsoft Azure" e selecione o item **Barramento de Serviço do Microsoft Azure**. Clique em **Instalar** para concluir a instalação e feche essa caixa de diálogo.

    ![Selecionar um pacote NuGet][1]

###Escrever um código para enviar uma mensagem para a fila
1. Adicione a seguinte instrução using ao topo do arquivo Program.cs.

    ```
    using Microsoft.ServiceBus.Messaging;
    ```
    
2. Adicione o seguinte código ao método Main e defina a variável **connectionString** como a cadeia de conexão que foi obtida ao criar o namespace e **queueName** como o nome da fila usado ao criar a fila.

    ```
    var connectionString = "<Your connection string>";
    var queueName = "<Your queue name>";
  
    var client = QueueClient.CreateFromConnectionString(connectionString, queueName);
    var message = new BrokeredMessage("This is a test message!");
    client.Send(message);

    ```

    Program.cs deve ficar assim.

    ```
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
  
3. Execute o programa e verifique o portal do Azure. Observe que agora o **Comprimento da Fila** deve ser 1.
    
      ![Comprimento da fila][2]
    
##4\. Recebendo mensagens da fila
1. Crie um novo aplicativo de Console e adicione uma referência ao pacote NuGet do Barramento de Serviço que foi feito para o aplicativo de envio acima.

2. Adicione a seguinte instrução using ao topo do arquivo Program.cs.
  
    ```
    using Microsoft.ServiceBus.Messaging;
    ```
  
3. Adicione o seguinte código ao método Main e defina a variável **connectionString** como a cadeia de conexão que foi obtida ao criar o namespace e **queueName** como o nome da fila usado ao criar a fila.

    ```
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

    ```
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
  
4. Execute o programa e verifique o portal do Azure. Observe que agora o **Comprimento da Fila** deve ser 0.

    ![Comprimento da fila][3]
  
Parabéns! Agora, você criou uma fila, enviou uma mensagem e recebeu uma mensagem.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
##Próximas etapas

Verifique nosso repositório GitHub com exemplos que mostram alguns dos recursos mais avançados das Mensagens do Barramento de Serviço do Azure. [https://github.com/Azure-Samples/azure-servicebus-messaging-samples][github-samples]

<!--Image references-->

[1]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[2]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-send.png
[3]: ./media/service-bus-dotnet-get-started-with-queues/queue-length-receive.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples

<!---HONumber=AcomDC_0608_2016-->