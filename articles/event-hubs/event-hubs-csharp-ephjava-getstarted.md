<properties
	pageTitle="Introdução aos Hubs de Eventos em C# | Microsoft Azure"
	description="Siga este tutorial para começar a usar os Hubs de Eventos do Azure; enviar eventos em C; e receber eventos em Java usando o EventProcessorHost."
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
	ms.topic="hero-article"
	ms.date="06/16/2016"
	ms.author="jotaub"/>

# Introdução aos Hubs de Eventos

[AZURE.INCLUDE [service-bus-selector-get-started](../../includes/service-bus-selector-get-started.md)]

## Introdução

Os Hubs de Eventos são um serviço que processa grandes quantidades de dados de eventos (telemetria) a partir de aplicativos e dispositivos conectados. Depois de coletar dados para Hubs de Eventos, você pode armazenar os dados usando um cluster de armazenamento ou transformá-los usando um provedor de análise em tempo real. Essa funcionalidade de coleta e processamento de eventos em grande escala é um componente fundamental de arquiteturas de aplicativos modernas, incluindo a IoT (Internet das Coisas).

Este tutorial mostra como usar o portal clássico do Azure para criar um Hub de Eventos. Ele também mostra como coletar mensagens em um Hub de Eventos usando um aplicativo de console escrito em C#, e como recuperá-los em paralelo usando a biblioteca do Host do Processador de Eventos em Java.

Para concluir esse tutorial, você precisará do seguinte:

+ [Microsoft Visual Studio](http://visualstudio.com)

+ Uma conta ativa do Azure. <br/>Se você não tiver uma, poderá criar uma conta gratuita em poucos minutos. Para obter detalhes, consulte [Avaliação Gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F target="\_blank").

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-send-csharp](../../includes/service-bus-event-hubs-get-started-send-csharp.md)]

[AZURE.INCLUDE [service-bus-event-hubs-get-started-receive-ephjava](../../includes/service-bus-event-hubs-get-started-receive-ephjava.md)]

## Executar os aplicativos

Agora você está pronto para executar os aplicativos.

1.	Execute o projeto **Receptor**.

	![][21]

2.	Execute o projeto **Remetente**.

	![][22]

## Próximas etapas

Agora que criou um aplicativo funcional que cria um Hub de Eventos e envia e recebe dados, você pode passar para os seguintes cenários:

- Um [aplicativo de exemplo completo que usa os Hubs de Evento][].
- O exemplo de [Escala horizontal do processamento de eventos com Hubs de Eventos][].
- Uma [solução de mensagens na fila][] usando filas do Barramento de Serviço.
- [Visão geral de Hubs de Evento][]

<!-- Images. -->
[21]: ./media/event-hubs-csharp-ephjava-getstarted/ephjava.png
[22]: ./media/event-hubs-csharp-ephjava-getstarted/cs-send.png

<!-- Links -->
[Azure classic portal]: https://manage.windowsazure.com/
[Visão geral de Hubs de Evento]: event-hubs-overview.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Escala horizontal do processamento de eventos com Hubs de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[solução de mensagens na fila]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
 

<!---HONumber=AcomDC_0824_2016-->