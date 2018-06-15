---
title: Hubs de notificação - Arquitetura de Push Corporativo
description: Orientação sobre como usar os Hubs de Notificação do Azure em um ambiente corporativo
services: notification-hubs
documentationcenter: ''
author: dimazaid
manager: kpiteira
editor: spelluru
ms.assetid: 903023e9-9347-442a-924b-663af85e05c6
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: article
ms.date: 04/14/2018
ms.author: dimazaid
ms.openlocfilehash: d7066b58330d35e5dba66cfe6ed5cfaddff4b68a
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33778055"
---
# <a name="enterprise-push-architectural-guidance"></a>Orientação arquitetural do push corporativo
As empresas hoje estão gradualmente migrando para a criação de aplicativos móveis para os usuários finais (externos) ou para os funcionários (internos). Eles têm sistemas de back-end no local como mainframes ou alguns aplicativos LoB que devem ser integrados na arquitetura de aplicativos móveis. Este guia fala sobre a melhor maneira de fazer esta integração recomendando a melhor solução para cenários comuns.

Um requisito frequente é enviar notificação por push para os usuários através de seus aplicativos móveis quando ocorre um evento de interesse nos sistemas de back-end. por exemplo, um cliente bancário que tenha o aplicativo do banco em seu iPhone deseja ser notificado quando um débito fica acima de um determinado valor de sua conta ou um cenário de intranet em que um funcionário do departamento financeiro com um aplicativo de aprovação de orçamento em um Windows Phone deseja ser notificado quando a solicitação de aprovação for recebida.

A conta bancária ou o processamento de aprovação provavelmente pode ser feito em algum sistema back-end que deve iniciar um envio por push para o usuário. Poderá haver vários sistemas de back-end e todos deverão compilar o mesmo tipo de lógica para efetuar push quando um evento disparar uma notificação. A complexidade aqui reside na integração de vários sistemas de back-end com sistemas individuais de envio por push, nos quais os usuários finais podem se inscrever para diferentes notificações e pode até mesmo haver vários aplicativos móveis. Por exemplo, no caso de aplicativos móveis de intranet nos quais um aplicativo móvel talvez queira receber notificações de vários sistemas de back-end. Os sistemas de back-end não sabem nem precisam saber de tecnologia/semântica de push. Assim, uma solução comum tem sido tradicionalmente introduzir um componente que controla os sistemas de back-end para todos os eventos de interesse e é responsável por enviar as mensagens por push para o cliente.

Uma solução ainda melhor é usar o Barramento de Serviço do Azure — modelo de Tópico/Assinatura que reduzirá a complexidade tornando a solução escalonável.

Esta é a arquitetura geral da solução (generalizado com vários aplicativos móveis, mas igualmente aplicável quando há apenas um aplicativo móvel)

## <a name="architecture"></a>Arquitetura
![][1]

A parte mais importante neste diagrama de arquitetura é o Barramento de Serviço do Azure, que fornece um modelo de programação de tópicos/assinaturas (falaremos mais sobre isso em [Programação Pub/Sub do Barramento de Serviço]). O receptor, que nesse caso, é o back-end móvel (normalmente, [Serviço Móvel do Azure], que iniciará um envio por push para os aplicativos móveis) não recebe mensagens diretamente dos sistemas de back-end, mas em vez disso, temos uma camada de abstração intermediária fornecida pelo [Barramento de Serviço do Azure, que permite que o back-end móvel receba mensagens de um ou mais sistemas de back-end. Um Tópico do Barramento de Serviço precisa ser criado para cada um dos sistemas de back-end, por exemplo, Conta, RH, Finanças, que são basicamente "tópicos" de interesse que iniciam o envio de mensagens como notificação por push. Os sistemas de back-end enviam mensagens para esses tópicos. Um Back-end Móvel pode assinar um ou mais tópicos criando uma assinatura do Barramento de Serviço. Isso permite que o back-end móvel receba uma notificação do sistema de back-end correspondente. O back-end móvel continua a escutar mensagens em suas assinaturas e, assim que uma mensagem chega, ela volta e é enviada como notificação para seu hub de notificação. Por fim, os hubs de notificação entregam a mensagem para o aplicativo móvel. Confira a lista de componentes principais:

1. Sistemas de back-end (sistemas de LoB/herdados)
   * Cria um tópico do barramento de serviço
   * Envia mensagem
2. Back-end móvel
   * Cria a assinatura do serviço
   * Recebe uma mensagem (do sistema de back-end)
   * Envia uma notificação para os clientes (via Hub de Notificação do Azure)
3. Aplicativo Móvel
   * Recebe e exibe a notificação

### <a name="benefits"></a>Benefícios:
1. A separação entre o receptor (aplicativo/serviço móvel via Hub de Notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicionais sejam integrados com alterações mínimas.
2. Isso também cria o cenário de vários aplicativos móveis, podendo receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo:
### <a name="prerequisites"></a>pré-requisitos
Conclua os tutoriais a seguir para se familiarizar com os conceitos, bem como as etapas de criação e configuração comuns:

1. [Programação Pub/Sub do Barramento de Serviço] — Este tutorial explica os detalhes de como trabalhar com Tópicos/Assinaturas do Barramento de Serviço, como criar um namespace para conter tópicos/assinaturas e como enviar e receber mensagens deles.
2. [Hubs de Notificação — Tutorial Universal do Windows] — Este tutorial explica como configurar um aplicativo da Windows Store e como usar Hubs de Notificação para se registrar e receber notificações.

### <a name="sample-code"></a>Exemplo de código
O código de exemplo completo está disponível em [Exemplos do Hub de Notificação]. Ele é dividido em três componentes:

1. **EnterprisePushBackendSystem**
   
    a. Este projeto usa o pacote *WindowsAzure.ServiceBus* do Nuget e é baseado na [Programação Pub/Sub do Barramento de Serviço].
   
    b. Este aplicativo é um aplicativo de console em C# simples para simular um sistema LoB que inicia a mensagem a ser entregue ao aplicativo móvel.
   
        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the topic
            CreateTopic(connectionString);
   
            // Send message
            SendMessage(connectionString);
        }
   
    c. `CreateTopic` é usado para criar o tópico do Barramento de Serviço.
   
        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already
   
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }
   
    d. `SendMessage` é usado para enviar as mensagens para esse Tópico do Barramento de Serviço. Este código simplesmente envia, periodicamente, um conjunto de mensagens aleatórias para o tópico para fins de exemplo. Normalmente há um sistema de back-end que envia mensagens quando ocorre um evento.
   
        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);
   
            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };
   
            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);
   
                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);
   
                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);
   
                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }
2. **ReceiveAndSendNotification**
   
    a. Este projeto usa os pacotes *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* do Nuget e se baseia na [Programação Pub/Sub do Barramento de Serviço].
   
    b. O aplicativo de console a seguir é executado como um [Trabalho Web do Azure] porque ele precisa ser executado continuamente para ouvir mensagens dos sistemas LoB/back-end. Este aplicativo faz parte do seu back-end móvel.
   
        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");
   
            // Create the subscription that receives messages
            CreateSubscription(connectionString);
   
            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }
   
    c. `CreateSubscription` é usado para criar uma assinatura do Barramento de Serviço para o tópico onde o sistema de back-end envia mensagens. Dependendo do cenário de negócios, esse componente cria uma ou mais assinaturas para tópicos correspondentes (por exemplo, alguns podem estar recebendo mensagens do sistema de RH, outros do sistema de Finanças, e assim por diante)
   
        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);
   
            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }
   
    d. ReceiveMessageAndSendNotification é usado para ler a mensagem do tópico usando sua assinatura e se a leitura for bem-sucedida, em seguida, criar uma notificação (no cenário de exemplo uma notificação nativa do Windows) para ser enviado para o aplicativo móvel usando os Hubs de Notificação do Azure.
   
        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");
   
            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);
   
            Client.Receive();
   
            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";
   
                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");
   
                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);
   
                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }
   
    e. Para publicar este aplicativo como um **Trabalho Web**, clique com o botão direito do mouse na solução no Visual Studio e selecione **Publicar como Trabalho Web**
   
    ![][2]
   
    f. Selecione o perfil de publicação e crie um novo site do Azure, se ele ainda não existir, que hospeda esse Trabalho Web e, quando tiver o site, clique em **Publicar**.
   
    ![][3]
   
    g. Configure o trabalho para ser “Executado Continuamente” para que, quando fizer logon no [Portal do Azure], você veja algo semelhante ao seguinte:
   
    ![][4]
3. **EnterprisePushMobileApp**
   
    a. Este é um aplicativo da Windows Store que recebe notificações do WebJob em execução como parte do back-end móvel e as exibe. Este código se baseia em [Hubs de Notificação — Tutorial Universal do Windows].  
   
    b. Certifique-se de que seu aplicativo está habilitado para receber notificações do sistema.
   
    c. Verifique se o seguinte código de registro de Hubs de Notificação está sendo chamado no aplicativo de inicialização (depois de substituir *HubName* e *DefaultListenSharedAccessSignature*:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemplo de execução:
1. Confirme se seu WebJob está em execução e se está programado para executar continuamente.
2. Execute o **EnterprisePushMobileApp que inicia o aplicativo da Windows Store.
3. Execute o aplicativo de console **EnterprisePushBackendSystem** que simula o back-end do LoB e começa a enviar mensagens. Você deverá ver as notificações do sistema da seguinte forma:
   
    ![][5]
4. Originalmente, as mensagens foram enviadas para os tópicos do Barramento de Serviço que estava sendo monitorado por assinaturas do Barramento de Serviço em seu WebJob. Depois que uma mensagem foi recebida, uma notificação foi criada e enviada ao aplicativo móvel. Você pode verificar os logs do WebJob para confirmar o processamento quando for para o link Logs no [Portal do Azure] para seu WebJob:
   
    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos do Hub de Notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço Móvel do Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Azure Service Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação Pub/Sub do Barramento de Serviço]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Trabalho Web do Azure]: ../app-service/web-sites-create-web-jobs.md
[Hubs de Notificação — Tutorial Universal do Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Portal do Azure]: https://portal.azure.com/
