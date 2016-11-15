---
title: "O que é Hub de Eventos do Azure? | Microsoft Docs"
description: "Visão geral e descrição dos Hubs de Eventos do Azure"
services: event-hubs
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 4391d750-5bbe-456d-9091-b416127bc754
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/17/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fcc3e2cb76a06607d0bfbbad810968b70d67e4f4


---
# <a name="what-is-azure-event-hubs"></a>O que é Hub de Eventos do Azure?
Hub de Eventos do Azure é um serviço de entrada de dados altamente escalonável que pode incluir milhões de eventos por segundo, para que você possa processar e analisar grandes quantidades de dados produzidos por seus aplicativos e dispositivos conectados. Os Hub de Eventos agem como a "porta de entrada” para um pipeline de eventos e depois que os dados são coletados em um Hub de Eventos, eles podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Hub de Eventos separa a produção de um fluxo de eventos do consumo desses eventos, para que os consumidores de eventos possam acessar os eventos em seu próprio cronograma. Para obter mais informações e detalhes técnicos, confira a [Visão geral de Hubs de Eventos](event-hubs-overview.md).

## <a name="event-hubs-capabilities"></a>Recursos de Hub de Eventos
Hubs de Eventos é um serviço de processamento de eventos que fornece processamento de telemetria e eventos na nuvem em grande escala, com baixa latência e alta confiabilidade. Esse serviço é especialmente útil para:

* Instrumentação de aplicativos
* Experiência do usuário ou o processamento de fluxo de trabalho
* Cenários de IoT (Internet das coisas)

Alguns outros recursos dos Hubs de Eventos importantes incluem o comportamento de rastreamento de aplicativos móveis, informações do tráfego de web farms, captura de eventos de jogos em jogos de console ou telemetria coletada de máquinas industriais ou veículos conectados.

## <a name="next-steps"></a>Próximas etapas
Para obter informações detalhadas sobre Hubs de Eventos, consulte os tópicos a seguir.

* [Visão geral de Hubs de Evento](event-hubs-overview.md)
* [Guia de programação dos Hubs de Eventos](event-hubs-programming-guide.md)
* [Perguntas frequentes sobre disponibilidade e suporte dos Hubs de Eventos](event-hubs-availability-and-support-faq.md)
* Introdução a um [Tutorial dos Hubs de Eventos][Tutorial dos Hubs de Eventos]
* Um [aplicativo de exemplo que usa os Hubs de Eventos completo][aplicativo de exemplo que usa os Hubs de Eventos]

[Tutorial de Hubs de Evento]: event-hubs-csharp-ephcs-getstarted.md
[aplicativo de exemplo completo que usa os Hubs de Evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097



<!--HONumber=Nov16_HO2-->


