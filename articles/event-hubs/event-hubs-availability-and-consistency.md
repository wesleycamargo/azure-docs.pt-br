---
title: "Disponibilidade e consistência em Hubs de eventos do Azure | Microsoft Docs"
description: "Como fornecer o máximo de disponibilidade e consistência com os Hubs de Eventos do Azure usando partições."
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 8f3637a1-bbd7-481e-be49-b3adf9510ba1
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/21/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: 7587b1bed2f809fa2c4bab78c54396eed778b9ef
ms.openlocfilehash: df2d79fdb4a26509f3c7c1f8f3a8adcaa6b24f9d
ms.lasthandoff: 02/21/2017

---

# <a name="availability-and-consistency-in-event-hubs"></a>Disponibilidade e consistência nos Hubs de Eventos

## <a name="overview"></a>Visão geral
Os Hubs de Eventos do Azure usam um [modelo de particionamento](event-hubs-what-is-event-hubs.md#partitions) para melhorar a disponibilidade e a paralelização dentro de um único Hub de Eventos. Por exemplo, se um Hub de eventos tem quatro partições e uma delas está sendo movida de um servidor para outro em uma operação de balanceamento de carga, você ainda pode enviar e receber de três outras partições. Além disso, ter mais partições permite que você tenha mais leitores simultâneos processando seus dados, melhorando assim sua taxa de transferência agregada. Noções básicas sobre as implicações de particionamento e ordenação em um sistema distribuído é um aspecto fundamental do design de soluções.

Para ajudar a explicar a compensação entre ordenação e disponibilidade, podemos observar o [teorema CAP](https://en.wikipedia.org/wiki/CAP_theorem), também conhecido como Teorema de Brewer. O teorema afirma que uma pessoa deve escolher entre consistência, disponibilidade e tolerância a partição.

O teorema define consistência e disponibilidade conforme a seguir:
* Tolerância a partição – a capacidade de um sistema de processamento de dados de continuar o processamento de dados mesmo se ocorrer uma falha de partição.
* Disponibilidade – um nó sem falha retorna uma resposta razoável dentro de um período razoável de tempo (sem nenhum erro ou tempo limite atingido).
* Consistência – há garantia de que uma leitura retorne a gravação mais recente para um determinado cliente.

## <a name="partition-tolerance"></a>Tolerância a partição
Os Hubs de Eventos são criados sobre um modelo particionado. Você pode configurar o número de partições no seu Hub de Eventos durante a instalação, mas você não pode alterar este valor posteriormente. Já que você deve usar partições com Hubs de eventos, você precisa tomar uma decisão quanto à disponibilidade e a consistência de seu aplicativo.

## <a name="availability"></a>Disponibilidade
A maneira mais simples de começar a usar os Hubs de Eventos é o comportamento padrão. Se você criar um novo `EventHubClient` e usar a função send, os eventos serão distribuídos automaticamente entre partições em seu Hub de Eventos. Esse comportamento permite a maior quantidade possível de tempo de atividade.

Para casos de uso que exigem o máximo tempo de atividade, esse modelo é preferencial.

## <a name="consistency"></a>Consistência
Em cenários específicos, a ordenação de eventos pode ser importante. Por exemplo, você pode desejar que seu sistema de back-end processe um comando update antes de processar um comando delete. Neste exemplo, você pode definir a chave de partição em um evento ou usar um `PartitionSender` para enviar eventos apenas para uma determinada partição. Isso assegura que, quando esses eventos forem lidos da partição, eles serão lidos em ordem.

Com esse tipo de configuração, você deve ter em mente que, se a partição específica para a qual você está enviando não estiver disponível, você receberá uma resposta de erro. Como um ponto de comparação, se você não tivesse uma afinidade com uma única partição, o serviço de Hubs de Eventos enviaria seu evento para a próxima partição disponível.

Uma solução possível para assegurar a ordenação e também maximizar a atividade seria agregar eventos como parte do seu aplicativo de processamento de eventos. A maneira mais fácil de fazer isso seria carimbar seu evento com uma propriedade de número de sequência personalizada. Veja a seguir um exemplo disso:

```csharp
// Get the latest sequence number from your application
var sequenceNumber = GetNextSequenceNumber();
// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));
// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);
// Send single message async
await eventHubClient.SendAsync(data);
```

O exemplo anterior enviaria seu evento para uma das partições disponíveis no seu Hub de Eventos e definiria o número de sequência correspondente de seu aplicativo. Essa solução requer que o estado seja mantido pelo seu aplicativo de processamento, mas forneceria a seus remetentes um ponto de extremidade com maior probabilidade de estar disponível.

## <a name="next-steps"></a>Próximas etapas
Você pode saber mais sobre Hubs de Eventos visitando os links abaixo:

* [Visão Geral dos Hubs de Eventos](event-hubs-what-is-event-hubs.md)
* [Criar um Hub de Eventos](event-hubs-create.md)

