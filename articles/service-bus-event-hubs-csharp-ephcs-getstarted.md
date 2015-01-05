<properties pageTitle="Introdução aos Hubs de Evento" metaKeywords="Azure Service Bus, Event Hub, getting started Event Hubs" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introdução aos Hubs de Evento

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Hubs de Evento é um sistema de ingestão altamente escalável que pode processar milhões de eventos por segundo, permitindo que seu aplicativo processe e analise quantidades maciças de dados produzidos pelos dispositivos e aplicativos conectados. Depois de coletados em Hubs de Evento, você pode transformar e armazenar dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento. Para mais informações sobre Hubs de Evento, consulte o [Guia do desenvolvedor de Hubs de Evento]. 

Para obter mais informações, consulte[Visão geral de Hubs de Evento].

Neste tutorial, você vai aprender a ingerir mensagens em um Hub de Evento usando um aplicativo de console em C#, e a recuperá-las em paralelo usando a biblioteca C#[Host de processador de eventos].

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio Express 2013 para Windows

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter mais informações, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.

## Criar um Hub de Evento

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **NOVO** na parte inferior da tela.

2.  Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Evento** e, em seguida, **Criação Rápida**.

   	![][1]

3. Digite um nome para o Hub de Evento, selecione a região desejada e clique em **Criar um novo Hub de Evento**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente o***event hub name*-ns**).

   	![][3]

5. Clique na guia **Hubs de Evento** na parte superior e clique no Hub de Evento que acabou de criar.

   	![][4]

6. Clique na guia **Configurar** na parte superior, adicione uma regra denominada**SendRule** com direito de *Enviar* e adicione outra regra denominada**ReceiveRule** com direitos de*Gerenciar, Enviar, Escutar* e clique em **Salvar**.

   	![][5]

7. Clique na guia **Painel** na parte superior e clique em **Informações de Conexão**.Anote as duas cadeias de conexão.

   	![][6]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, execute o projeto **Receptor** e aguarde que ele inicie os receptores de todas as partições.

   	![][21]

2.	Execute o projeto **Remetente** pressione **Enter** nas janelas do console e veja os eventos aparecem na janela do receptor.

   	![][22]

<!-- Images. -->
[1]: ./media/service-bus-event-hubs-getstarted/create-event-hub1.png
[2]: ./media/service-bus-event-hubs-getstarted/create-event-hub2.png
[3]: ./media/service-bus-event-hubs-getstarted/create-event-hub3.png
[4]: ./media/service-bus-event-hubs-getstarted/create-event-hub4.png
[5]: ./media/service-bus-event-hubs-getstarted/create-event-hub5.png
[6]: ./media/service-bus-event-hubs-getstarted/create-event-hub6.png

[21]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/service-bus-event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[Portal de Gerenciamento do Azure]: https://manage.windowsazure.com/
[Host do processador de eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de Evento]: http://msdn.microsoft.com/pt-br/library/azure/dn836025.aspx

<!--HONumber=35.1-->
