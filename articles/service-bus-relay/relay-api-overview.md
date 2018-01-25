---
title: "Visão geral da API de Retransmissão do Azure | Microsoft Docs"
description: "Visão geral das APIs de Retransmissão do Azure disponíveis"
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: sethm
ms.openlocfilehash: fc6db8aba887b186961da9b12e7c5f32afa4355b
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="available-relay-apis"></a>APIs de Retransmissão disponíveis

## <a name="runtime-apis"></a>APIs de tempo de execução

A tabela a seguir lista todos os clientes de tempo de execução de Retransmissão disponíveis no momento.

A seção [informações adicionais](#additional-information) contém mais informações sobre o status de cada biblioteca de tempo de execução.

| Linguagem/plataforma | Recurso disponível | Pacote de cliente | Repositório |
| --- | --- | --- | --- |
| .NET Standard | Conexões Híbridas | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [Github](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Retransmissão de WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nó | Conexões Híbridas | [`hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[`hyco-websocket`](https://www.npmjs.com/package/hyco-websocket) | [Github](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informações adicionais

#### <a name="net"></a>.NET

O ecossistema do .NET tem vários tempos de execução, portanto, há várias bibliotecas .NET de Hubs de Eventos. A biblioteca .NET Standard pode ser executada usando o .NET Core ou o .NET Framework, enquanto a biblioteca do .NET Framework só pode ser executada em um ambiente do .NET Framework. Para saber mais sobre o .NET Framework, veja [versões da estrutura](/dotnet/articles/standard/frameworks#framework-versions).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre a Retransmissão do Azure, visite estes links:
* [O que é Retransmissão do Azure?](relay-what-is-it.md)
* [Perguntas frequentes sobre retransmissão](relay-faq.md)