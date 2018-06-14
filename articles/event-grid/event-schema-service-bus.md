---
title: Esquema de eventos do Barramento de Serviço da Grade de Eventos do Azure
description: Descreve as propriedades que são fornecidas para eventos do Barramento de Serviço com a Grade de Eventos do Azure
services: event-grid
author: banisadr
manager: darosa
ms.service: event-grid
ms.topic: reference
ms.date: 02/21/2018
ms.author: babanisa
ms.openlocfilehash: 991679eeb0f7c98606133750b193a5895f39178f
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/18/2018
ms.locfileid: "34303310"
---
# <a name="azure-event-grid-event-schema-for-service-bus"></a>Esquema de eventos da Grade de Eventos do Azure para Barramento de Serviço

Este artigo fornece as propriedades e o esquema para eventos do Barramento de Serviço. Para obter uma introdução a esquemas de evento, consulte [esquema de grade de eventos do Azure](event-schema.md).

## <a name="available-event-types"></a>Tipos de evento disponíveis

Barramento de Serviço emite os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners | Gerado quando há mensagens ativas em uma fila ou assinatura e nenhum receptor escutando. |
| Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener | Gerado quando há mensagens ativas em uma fila de mensagens mortas e nenhum listener ativo. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de mensagens ativas com nenhum evento de listener:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

O esquema para um evento de fila de mensagens mortas é semelhante:

```json
[{
  "topic": "/subscriptions/{subscription-id}/resourcegroups/{your-rg}/providers/Microsoft.ServiceBus/namespaces/{your-service-bus-namespace}",
  "subject": "topics/{your-service-bus-topic}/subscriptions/{your-service-bus-subscription}",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://{your-service-bus-namespace}.servicebus.windows.net/{your-topic}/subscriptions/{your-service-bus-subscription}/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. Grade de Eventos fornece esse valor. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos do armazenamento de blob. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| nameSpaceName | string | O namespace do Barramento de Serviço onde está o recurso existente. |
| requestUri | string | O URI para a fila específica ou a assinatura que emite o evento. |
| entityType | string | O tipo de entidade de Barramento de Serviço que emite eventos (fila ou assinatura). |
| queueName | string | A fila de mensagens ativas se inscreve-se em uma fila. Valor nulo se usar tópicos / assinaturas. |
| topicName | string | O tópico da assinatura do Barramento de Serviço ao qual as mensagens ativas pertencem. Valor nulo se usar uma fila. |
| subscriptionName | string | A assinatura do Barramento de Serviço com as mensagens ativas. Valor nulo se usar uma fila. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
* Para obter detalhes sobre como usar a grade de eventos do Azure com o Barramento de Serviço, consulte a [visão geral de integração do Barramento de Serviço à Grade de Eventos](../service-bus-messaging/service-bus-to-event-grid-integration-concept.md).
* Tente [receber eventos do Barramento de Serviço com Funções e Aplicativos Lógicos](../service-bus-messaging/service-bus-to-event-grid-integration-example.md?toc=%2fazure%2fevent-grid%2ftoc.json).
