---
title: Esquema de assinatura de Grade de Eventos do Azure
description: Descreve as propriedades para assinar um evento com a grade de eventos do Azure.
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 08/17/2017
ms.author: babanisa
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: cce058e75ebf49f53d11e3b5a87ea4625994db0f
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---

# <a name="event-grid-subscription-schema"></a>Esquema de assinatura de Grade de Eventos

Para criar uma assinatura de grade de eventos, você envia uma solicitação para a operação de assinatura Create Event. Use o seguinte formato:

```
PUT /subscriptions/{subscription-id}/resourceGroups/{group-name}/providers/{resource-provider}/{resource-type}/{resource-name}/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

Por exemplo, para criar uma inscrição de evento para uma conta de armazenamento denominada `examplestorage` em um grupo de recursos denominado `examplegroup`, use o seguinte formato:

```
PUT /subscriptions/{subscription-id}/resourceGroups/examplegroup/providers/Microsoft.Storage/storageaccounts/examplestorage/Microsoft.EventGrid/eventSubscriptions/{event-type-definitions}?api-version=2017-06-15-preview
``` 

O artigo descreve as propriedades e o esquema para o corpo da solicitação.
 
## <a name="event-subscription-properties"></a>Propriedades da assinatura do evento

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| destino | objeto | O objeto que define o ponto de extremidade. |
| filtro | objeto | Um campo opcional para filtrar os tipos de eventos. |

### <a name="destination-object"></a>objeto de destino

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| endpointType | string | O tipo de ponto de extremidade para a assinatura (webhook/HTTP, o Hub de evento ou fila). | 
| endpointUrl | string |  | 

### <a name="filter-object"></a>objeto filter

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| includedEventTypes | array | Correspondência quando o tipo de evento na mensagem de evento é uma correspondência exata para esses nomes de tipo de evento. Gera um erro quando o nome do evento não coincide com os nomes de tipo de evento registrados para a origem do evento. O padrão corresponde a todos os tipos de evento. |
| subjectBeginsWith | string | Uma correspondência de prefixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. | 
| subjectEndsWith | string | Uma correspondência de sufixo de filtro para o campo de assunto no evento mensagem. A cadeia de caracteres padrão ou vazia corresponde a tudo. |
| subjectIsCaseSensitive | string | Controla a correspondência que diferencia maiúsculas e minúsculas para filtros. |


## <a name="example-subscription-schema"></a>Esquema de assinatura de exemplo

```json
{
  "properties": {
    "destination": {
      "endpointType": "webhook",
      "properties": {
          "endpointUrl": "https://example.azurewebsites.net/api/HttpTriggerCSharp1?code=VXbGWce53l48Mt8wuotr0GPmyJ/nDT4hgdFj9DpBiRt38qqnnm5OFg=="
      }
    },
    "filter": {
      "includedEventTypes": [ "blobCreated", "blobDeleted" ],
      "subjectBeginsWith": "blobServices/default/containers/mycontainer/log",
      "subjectEndsWith": ".jpg",
      "subjectIsCaseSensitive": "true"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à Grade de Eventos, confira [O que é a Grade de Eventos?](overview.md)
