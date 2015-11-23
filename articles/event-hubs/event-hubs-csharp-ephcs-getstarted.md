<properties
	pageTitle="Introdução aos Hubs de eventos em C# | Microsoft Azure"
	description="Siga este tutorial para começar a usar os Hubs de Eventos do Azure com C# e a usar o EventProcessorHost."
	services="event-hubs"
	documentationCenter=""
	authors="fsautomata"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/05/2015"
	ms.author="sethm"/>

# Introdução aos Hubs de Evento

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introdução

Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o portal do Azure para criar um Hub de Eventos. Ele também mostra como coletar mensagens em um Hub de Eventos usando um aplicativo de console escrito em C#, e como recuperá-los em paralelo usando a biblioteca do [Host do Processador de Eventos] em C#.

Para concluir este tutorial, você precisará do seguinte:

+ Microsoft Visual Studio 2013 ou Microsoft Visual Studio Express 2013 para Windows.

+ Uma conta ativa do Azure. <br/>Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos Para obter detalhes, consulte [Avaliação Gratuita do Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

## Criar um Hub de Evento

1. Entre no [portal do Azure] e clique em **NOVO** na parte inferior da tela.

2. Clique em **Serviços de Aplicativos**, em seguida, **Barramento de Serviço**, em seguida, **Hub de eventos** e, em seguida, **Criação rápida**.

   	![][1]

3. Digite um nome para o Hub de Evento, selecione a região desejada e clique em **Criar um novo Hub de Evento**.

   	![][2]

4. Clique no namespace que você acabou de criar (geralmente ***nome do hub de evento*-ns**).

   	![][3]

5. Clique na guia **Hubs de Eventos** no início da página e clique no Hub de Evento que acabou de criar.

   	![][4]

6. Clique na guia **Configurar** na parte superior, adicione uma regra denominada **SendRule** com direitos *Enviar*, adicione outra regra chamada **ReceiveRule** com direitos *Gerenciar, Enviar, Escutar* e clique em **Salvar**.

   	![][5]

7. Clique na guia **Painel** parte superior da página e clique em **Informações de Conexão**. Anote as duas cadeias de conexão ou copie-as em algum lugar para usar mais tarde neste tutorial.

   	![][6]

Seu Hub de Evento foi criado, e você tem as cadeias de conexão que precisa para enviar e receber eventos.

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]


[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephcs](../../includes/service-bus-event-hubs-get-started-receive-ephcs.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	No Visual Studio, execute o projeto **Receptor** e aguarde que ele inicie os receptores de todas as partições.

   	![][21]

2.	Execute o projeto **Remetente** pressione **Enter** nas janelas do console e veja os eventos aparecem na janela do receptor.

   	![][22]

## Próximas etapas

Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].
- O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][].
- Uma [solução de mensagens na fila][] usando filas do Barramento de Serviço.
- [Visão geral de Hubs de Evento][]

<!-- Images. -->
[1]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub1.png
[2]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub2.png
[3]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub3.png
[4]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub4.png
[5]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub5.png
[6]: ./media/event-hubs-csharp-ephcs-getstarted/create-event-hub6.png

[21]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs1.png
[22]: ./media/event-hubs-csharp-ephcs-getstarted/run-csharp-ephcs2.png

<!-- Links -->
[portal do Azure]: https://manage.windowsazure.com/
[Host do Processador de Eventos]: https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost
[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solução de mensagens na fila]: ../service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=Nov15_HO3-->