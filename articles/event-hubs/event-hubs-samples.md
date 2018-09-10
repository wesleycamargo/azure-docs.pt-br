---
title: Exemplos de Hubs de Eventos do Azure | Microsoft Docs
description: Exemplos de Hubs de Eventos do Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005327"
---
# <a name="event-hubs-samples"></a>Exemplos de Hubs de Eventos 
Você pode encontrar as amostras dos Hubs de Eventos no [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Esses exemplos demonstram os principais recursos nos [Hubs de Eventos do Azure](/azure/event-hubs/). Este tópico categoriza e descreve os exemplos disponíveis, com links para cada um.

## <a name="net-samples"></a>Amostras do .NET

| Nome da amostra | DESCRIÇÃO | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Esta amostra mostra como escrever um aplicativo do console do .NET Core que envia um conjunto de mensagens para um hub de eventos. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Esta amostra mostra como gravar um aplicativo de .NET Core que recebe mensagens de um hub de eventos usando a biblioteca do Host do Processador de Eventos.  | 

## <a name="java-samples"></a>Exemplos do Java

| Nome da amostra | DESCRIÇÃO | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Este amostra ilustra como ingerir a inclusão de lotes de eventos em seu hub de eventos. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Este amostra ilustra como ingerir a inclusão de eventos em seu hub de eventos. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Este exemplo ilustra as várias opções disponíveis com Hubs de Eventos para a ingestão de eventos. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Este exemplo ilustra como receber eventos de uma partição do hub de eventos usando um deslocamento de data e hora específico. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Este exemplo ilustra como receber eventos de uma partição do hub de eventos usando um deslocamento de data e hora específico. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Este exemplo ilustra como receber de um evento partições do hub de eventos usando um número de sequência. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Este exemplo ilustra como receber eventos de um hub de eventos usando o host de processador de eventos, que fornece a seleção de partição automático e failover entre vários destinatários simultâneos. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Este exemplo ilustra como um hub de eventos pode escalar verticalmente automaticamente em altas cargas. O exemplo enviará os eventos a uma taxa que apenas excedem a taxa configurada de um hub de eventos, fazendo com que o hub de eventos para escalar verticalmente. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Este exemplo permite medir a taxa de entrada. | 

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre os Hubs de Eventos nestes artigos:

- [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Recursos de Hubs de eventos](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)