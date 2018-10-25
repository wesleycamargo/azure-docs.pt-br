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
ms.openlocfilehash: ed337689360428d66657e5391ee52bb0ae39dc14
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365638"
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

## <a name="python-samples"></a>Exemplos em Python
Você pode encontrar exemplos de Python para Hubs de Eventos do Azure no repositório do GitHub [azure-eventos-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples).

## <a name="nodejs-samples"></a>Exemplos do Node.js
Você pode encontrar exemplos de Node. js do Azure para Hubs de Eventos no repositório GitHub [azure-eventos-hubs-node](https://github.com/Azure/azure-event-hubs-node).

## <a name="go-samples"></a>Exemplos da linguagem Go
Você pode encontrar exemplos de Go para os Hubs de Eventos do Azure no repositório do GitHub [azure-eventos-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="azure-cli-samples"></a>Exemplos da CLI do Azure
Você pode encontrar exemplos de CLI do Azure para Hubs de Eventos do Azure no repositório do GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI).

## <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell
Você pode encontrar exemplos do Azure PowerShell para Hubs de Eventos do Azure no repositório do GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell).
 


## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre os Hubs de Eventos nestes artigos:

- [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Recursos de Hubs de eventos](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)