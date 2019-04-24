---
title: Exemplos – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece uma lista de exemplos para Hubs de Eventos do Azure que estão no GitHub.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 1c1198733fb56303d328ee97152442d25dbe945a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60343470"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Repositórios Git com exemplos para os Hubs de Eventos do Azure 
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
Você pode encontrar exemplos de Node. js para Hubs de eventos do Azure na [azure-sdk-para-js](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/eventhub/event-hubs/samples) repositório do GitHub.

## <a name="go-samples"></a>Exemplos da linguagem Go
Você pode encontrar exemplos de Go para os Hubs de Eventos do Azure no repositório do GitHub [azure-eventos-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="azure-cli-samples"></a>Exemplos da CLI do Azure
Você pode encontrar exemplos de CLI do Azure para Hubs de Eventos do Azure no repositório do GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI).

## <a name="azure-powershell-samples"></a>Exemplos do Azure PowerShell
Você pode encontrar exemplos do Azure PowerShell para Hubs de Eventos do Azure no repositório do GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell).
 
## <a name="apache-kafka-samples"></a>Exemplos do Apache Kafka
Você pode encontrar exemplos para os Hubs de Eventos para o recurso Apache Kafka no repositório [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka) do GitHub.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre os Hubs de Eventos nestes artigos:

- [Visão geral de Hubs de Eventos](event-hubs-what-is-event-hubs.md)
- [Recursos de Hubs de eventos](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)