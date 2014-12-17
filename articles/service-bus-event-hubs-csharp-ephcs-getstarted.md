<properties pageTitle="Introdução aos Hubs de eventos" metaKeywords="barramento de serviço do Azure, hub de eventos, introdução a hubs de eventos" description="Follow this tutorial to get started using Azure Event Hubs with C# using EventProcessorHost" metaCanonical="" services="" documentationCenter="" title="Get Started with Event Hubs" authors="elioda" solutions="" manager="timlt" editor="" />

<tags ms.service="service-bus" ms.workload="core" ms.tgt_pltfrm="csharp" ms.devlang="csharp" ms.topic="hero-article" ms.date="10/27/2014" ms.author="elioda" />

# <a name="getting-started"> </a>Introdução aos Hubs de Eventos

[WACOM.INCLUDE [service-bus-selector-get-started](../includes/service-bus-selector-get-started.md)]

Hubs de Eventos são um sistema de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por aplicativos e dispositivos conectados. Depois de coletados em Hubs de Eventos, você pode transformar e armazenar os dados usando qualquer provedor de análise em tempo real ou cluster de armazenamento.

Para mais informações, consulte [Visão geral de Hubs de Eventos].

Neste tutorial, você aprenderá a receber mensagens em um Hub de Eventos usando um aplicativo de console em C#, e recuperá-los em paralelo usando a biblioteca do [Host do Processador de Eventos] em C#.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio Express 2013 para Windows

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte <a href="http://www.windowsazure.com/pt-br/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fpt-br%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Avaliação gratuita do Azure</a>.

## Criar um Hub de Eventos

1. Faça logon no [Portal de Gerenciamento do Azure] e clique em **+NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em **Barramento de Serviço**, em **Hub de Eventos** e, em seguida, **Criação Rápida**.

   	![][1]

3. Digite um nome para seu Hub de Eventos, selecione a região desejada e clique em **Criar novo Hub de Eventos**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente ***event hub name*-ns**).

   	![][3]

5. Clique na guia **Hubs de Eventos** na parte superior da página e depois clique no Hub de Eventos recém-criado.

   	![][4]

6. Clique na guia **Configurar** na parte superior, adicione uma regra chamada **SendRule** com direitos *Send*, adicione outra regra chamada **ReceiveRule** com direitos de *Manage, Send, Listen* e clique em **Salvar**.

   	![][5]

7. Clique na guia **Painel** na parte superior da página e clique em **Informações de Conexão**. Anote as duas cadeias de conexão.

   	![][6]

Seu Hub de Eventos agora está criado e você tem as cadeias de conexão necessárias para enviar e receber eventos.

[WACOM.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[WACOM.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1. No Visual Studio, execute o projeto **Receptor**, então aguarde ele iniciar os receptores para todas as partições.

   	![][21]

2.	Execute o projeto **Remetente**, pressione **Enter** nas janelas do console e veja os eventos aparecerem na janela do receptor.

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
