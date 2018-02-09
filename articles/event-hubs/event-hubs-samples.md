---
title: Exemplos de Hubs de Eventos do Azure | Microsoft Docs
description: Exemplos de Hubs de Eventos do Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2017
ms.author: sethm
ms.openlocfilehash: e037d0e291384849739825ae7ad59064a135db95
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/03/2018
---
# <a name="event-hubs-samples"></a>Exemplos de Hubs de Eventos 

O conjunto de exemplos de Hubs de Eventos do Azure demonstra os principais recursos nos [Hubs de Eventos do Azure](/azure/event-hubs/). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

No momento da redação deste artigo, exemplos de Hubs de Eventos estão localizados em vários lugares diferentes:

- [Exemplos de código do desenvolvedor do MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [Github](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Para saber mais sobre as diferentes versões do .NET Framework, veja [Estruturas e destinos](/dotnet/articles/standard/frameworks).

Mais exemplos será adicionado ao longo do tempo, então verifique novamente com frequência para atualizações.

## <a name="net-standard"></a>.NET Standard

Os exemplos a seguir demonstram como enviar e receber eventos usando o [cliente dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) para a [biblioteca do .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Enviar eventos 

O exemplo de [Introdução ao envio](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) mostra como escrever um aplicativo de console do .NET Core que envia eventos para um hub de eventos.

### <a name="receive-events"></a>Receber eventos 

O exemplo [Introdução ao recebimento com o Host do Processador de Eventos](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) é um aplicativo de console .NET Core que recebe mensagens de um hub de eventos usando o Host do Processador de Eventos.

## <a name="net-framework"></a>.NET Framework   

Estes exemplos demonstram vários outros recursos dos Hubs de Eventos do Azure, direcionados para a [biblioteca do .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Notificar os usuários de eventos recebidos

O [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) exemplo notifica os usuários de dados recebidos de sensores ou outros sistemas.

### <a name="get-started-with-event-hubs"></a>Introdução aos Hubs de Eventos 

O exemplo de [Introdução aos Hubs de Eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) demonstra os recursos básicos de Hubs de Eventos, por exemplo, como criar um hub de eventos, enviar eventos a um hub de eventos e consumir eventos usando o [Host do Processador de Eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Processamento de eventos de escala horizontal 

O exemplo de [Expandir o processamento de eventos](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) demonstra como usar o [Host do Processador de Eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) para distribuir a carga de trabalho de consumo de fluxo de Hubs de Eventos. Ele mostra como implementar a **EventProcessor** e **EventProcessorFactory** objetos para gerenciar o fluxo de eventos. 

### <a name="pull-web-data-into-an-event-hub"></a>Efetuar pull de dados da Web para um hub de eventos 

O exemplo de [Importar dados da Web](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) mostra como efetuar pull de dados de feeds públicos (como o feed de informações de tráfego do Departamento de Transportes) e enviá-los por push para um hub de eventos.

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as versões do .NET Framework visitando os links a seguir:

- [Estruturas e destinos](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 e 4.5](/dotnet/framework/index)

Você pode saber mais sobre os Hubs de Eventos nestes artigos:

- [Visão geral de Hubs de Evento](event-hubs-what-is-event-hubs.md)
- [Criar um hub de eventos](event-hubs-create.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)