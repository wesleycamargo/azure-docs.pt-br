---
title: Esquema de evento de assinatura de Grade de Eventos do Azure
description: "Descreve as propriedades que são fornecidas para eventos de assinatura com a Grade de Eventos do Azure"
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/08/2017
ms.author: tomfitz
ms.openlocfilehash: 7dc10d0cc73960fac4759a0cebec8d294cf1b463
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema-for-subscriptions"></a>Esquema de eventos para assinatura da Grade de Eventos do Azure para hubs de eventos

Este artigo fornece as propriedades e o esquema para eventos de assinatura do Azure. Para obter uma introdução a esquemas de evento, consulte [esquema de evento da Grade de Eventos do Azure](event-schema.md).

Grupos de recursos e as assinaturas do Azure emitem os mesmos tipos de evento. Os tipos de eventos relacionados às alterações em recursos. A principal diferença é que grupos de recursos de emissão de eventos para os recursos no grupo de recursos e as assinaturas do Azure emitem eventos de recursos entre a assinatura.

## <a name="available-event-types"></a>Tipos de evento disponíveis

As assinaturas do Azure agora podem emitir eventos de gerenciamento do Azure Resource Manager, como quando uma VM é criada ou uma conta de armazenamento é excluída.

| Tipo de evento | Descrição |
| ---------- | ----------- |
| Microsoft.Resources.ResourceWriteSuccess | Gerado quando um recurso cria ou operação de atualização será bem-sucedida. |
| Microsoft.Resources.ResourceWriteFailure | Gerado quando um recurso cria ou operação de atualização será falha. |
| Microsoft.Resources.ResourceWriteCancel | Gerado quando um recurso cria ou operação de atualização é cancelada. |
| Microsoft.Resources.ResourceDeleteSuccess | Gerado quando um recurso cria ou operação de atualização é bem-sucedida. |
| Microsoft.Resources.ResourceDeleteFailure | Gerado quando um recurso cria ou operação de atualização é falha. |
| Microsoft.Resources.ResourceDeleteCancel | Gerado quando um recurso cria ou operação de atualização é cancelada. Esse evento ocorre quando a implantação de um modelo é cancelada. |

## <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um recurso criado de evento: 

```json
[
    {
    "topic":"/subscriptions/{subscription-id}",
    "subject":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "eventType":"Microsoft.Resources.ResourceWriteSuccess",
    "eventTime":"2017-08-16T03:54:38.2696833Z",
    "id":"25b3b0d0-d79b-44d5-9963-440d4e6a9bba",
    "data": {
        "authorization":"{azure_resource_manager_authorizations}",
        "claims":"{azure_resource_manager_claims}",
        "correlationId":"54ef1e39-6a82-44b3-abc1-bdeb6ce4d3c6",
        "httpRequest":"{request-operation}",
        "resourceProvider":"Microsoft.EventGrid",
        "resourceUri":"/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
        "operationName":"Microsoft.EventGrid/eventSubscriptions/write",
        "status":"Succeeded",
        "subscriptionId":"{subscription-id}",
        "tenantId":"72f988bf-86f1-41af-91ab-2d7cd011db47"
        },
    }
]
```

O esquema para um evento de recurso excluído é semelhante:

```json
[{
  "topic":"/subscriptions/{subscription-id}",
  "subject": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicApp0ecd6c02-2296-4d7c-9865-01532dc99c93",
  "eventType": "Microsoft.Resources.ResourceDeleteSuccess",
  "eventTime": "2017-11-07T21:24:19.6959483Z",
  "id": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
  "data": {
    "authorization": "{azure_resource_manager_authorizations}",
    "claims": "{azure_resource_manager_claims}",
    "correlationId": "7995ecce-39d4-4851-b9d7-a7ef87a06bf5",
    "httpRequest": "{request-operation}",
    "resourceProvider": "Microsoft.EventGrid",
    "resourceUri": "/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.EventGrid/eventSubscriptions/LogicAppdd584bdf-8347-49c9-b9a9-d1f980783501",
    "operationName": "Microsoft.EventGrid/eventSubscriptions/delete",
    "status": "Succeeded",
    "subscriptionId": "{subscription-id}",
    "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47"
  }
}]
```

## <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de evento de assinatura. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| autorização | string | A autorização solicitada para a operação. |
| declarações | string | As propriedades da declaração. |
| correlationId | string | Uma ID de operação para solução de problemas. |
| httpRequest | string | Os detalhes da operação. |
| ResourceProvider | string | O provedor de recursos executando a operação. |
| resourceUri | string | O URI do recurso na operação. |
| operationName | string | A operação que foi executada. |
| status | string | O status da operação. |
| subscriptionId | string | A ID da assinatura do recurso. |
| tenantId | string | A ID do locatário do recurso. |

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md).
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
