---
title: O que são Hubs de Eventos do Azure? | Microsoft Docs
description: Visão geral e descrição dos Hubs de Eventos do Azure
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm

---
# O que é Hub de Eventos do Azure?
Hub de Eventos do Azure é um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Os Hub de Eventos agem como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um Hub de Eventos, eles podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma. Para obter mais informações e detalhes técnicos, confira a [Visão geral de Hubs de Eventos](event-hubs-overview.md).

## Recursos de Hub de Eventos
Hubs de Eventos é um serviço de processamento de eventos que fornece processamento de telemetria e eventos na nuvem em grande escala, com baixa latência e alta confiabilidade. Esse serviço é especialmente útil para:

* Instrumentação de aplicativos
* Experiência do usuário ou o processamento de fluxo de trabalho
* Cenários de IoT (Internet das coisas)

Alguns outros recursos dos Hubs de Eventos importantes incluem o comportamento de rastreamento de aplicativos móveis, informações do tráfego de web farms, captura de eventos de jogos em jogos de console ou telemetria coletada de máquinas industriais ou veículos conectados.

## Próximas etapas
Para obter informações detalhadas sobre Hubs de Eventos, consulte os tópicos a seguir.

* [Visão geral de Hubs de Evento](event-hubs-overview.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos](event-hubs-availability-and-support-faq.md)
* Introdução a um [Tutorial de Hub de Eventos][Tutorial de Hub de Eventos]
* Um [aplicativo de exemplo completo que usa Hub de Eventos][aplicativo de exemplo completo que usa Hub de Eventos].

[Tutorial de Hub de Eventos]: event-hubs-csharp-ephcs-getstarted.md
[aplicativo de exemplo completo que usa Hub de Eventos]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097

<!---HONumber=AcomDC_0907_2016-->