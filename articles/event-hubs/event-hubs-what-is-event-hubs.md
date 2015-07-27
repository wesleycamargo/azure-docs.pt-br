<properties
	pageTitle="O que é Hub de Eventos do Azure?"
	description="Visão geral do Hub de Eventos do Azure."
	services="event-hubs"
	documentationCenter=".net"
	authors="nberdy"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="07/15/2015"
	ms.author="sethm"/>

# O que é Hub de Eventos do Azure?

Hub de Eventos do Azure é um serviço de entrada de dados altamente dimensionável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Hub de Eventos age como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um hub de eventos, ele pode ser transformado e armazenado usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma.

## Recursos de Hub de Eventos

Hub de Eventos é um serviço de processamento de eventos que fornece entrada a telemetria e eventos na nuvem em grande escala, com baixa latência e alta confiabilidade. Esse serviço é especialmente útil para:

* Instrumentação de aplicativos
* Experiência do usuário ou o processamento de fluxo de trabalho
* Cenários de IoT (Internet das coisas)

Outros principais recursos de Hub de Eventos são acompanhamento de aplicativo em dispositivos móveis, informações de tráfego de farms da web, captura de eventos em jogos de console ou dados de telemetria coletados de máquinas industriais ou veículos conectados.

Ao contrário de [tópicos e filas do Barramento de Serviço](../service-bus/service-bus-messaging-overview.md), o Hub de Eventos concentra-se em fornecer processamento de fluxo em escala. Os recursos do Hub de Eventos são diferentes de tópicos, no sentido de que têm uma forte tendência para alta produtividade e cenários de processamento de eventos. Como resultado, o Hub de Eventos não implementa alguns dos recursos de mensagens que estão disponíveis para [tópicos](service-bus/fundamentals-service-bus-hybrid-solutions.md#topics). Se você precisar desses recursos, os tópicos continuam sendo a melhor opção.

## Próximas etapas

Para obter informações detalhadas sobre Hub de Eventos, consulte os tópicos a seguir.

- [Visão geral de Hubs de Evento](event-hubs-overview.md)
- [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
- [Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos](event-hubs-availability-and-support-faq.md)
- Introdução a um [Tutorial de Hub de Eventos]
- Um [aplicativo de exemplo completo que usa Hub de Eventos].

[Tutorial de Hub de Eventos]: service-bus-event-hubs-csharp-ephcs-getstarted.md
[aplicativo de exemplo completo que usa Hub de Eventos]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-286fd097

<!---HONumber=July15_HO3-->