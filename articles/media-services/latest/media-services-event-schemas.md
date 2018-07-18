---
title: Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia
description: Descreve as propriedades que são fornecidas para eventos dos Serviços de Mídia com a Grade de Eventos do Azure
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/07/2018
ms.locfileid: "33782655"
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Esquemas de Grade de Eventos do Azure para eventos dos Serviços de Mídia

Este artigo fornece as propriedades e os esquemas para eventos de Serviços de Mídia.

## <a name="media-services-job-state-change-event"></a>Evento de alteração de estado do trabalho de Serviços de Mídia

Esta seção fornece que as propriedades e o esquema para o evento de alteração de estado do trabalho de Serviços de Mídia.  

### <a name="available-event-types"></a>Tipos de evento disponíveis

Um **Trabalho** dos Serviços de Mídia emite os seguintes tipos de evento:

| Tipo de evento | DESCRIÇÃO |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Gerado quando o estado do Trabalho muda. |

### <a name="example-event"></a>Exemplo de evento

O exemplo a seguir mostra o esquema de um evento de estado de Trabalho concluído: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Propriedades do evento

Um evento tem os seguintes dados de nível superior:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| topic | string | Grade de Eventos fornece esse valor. |
| subject | string | O caminho do recurso sob o recurso da conta dos Serviços de Mídia. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. Por exemplo, "Microsoft.Media.JobStateChange". |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados de eventos dos Serviços de Mídia. |
| dataVersion | string | A versão do esquema do objeto de dados. O fornecedor define a versão do esquema. |
| metadataVersion | string | A versão do esquema do metadados de evento. Grade de Eventos define o esquema de propriedades de nível superior. Grade de Eventos fornece esse valor. |

O objeto de dados tem as seguintes propriedades:

| Propriedade | type | DESCRIÇÃO |
| -------- | ---- | ----------- |
| previousState | string | O estado do trabalho antes do evento. |
| state | string | O novo estado do trabalho que está sendo notificado nesse evento. Por exemplo, "Na fila: o trabalho está aguardando recursos" ou "Agendado: o trabalho está pronto para começar".|

Quando o estado do Trabalho puder ser um deste valores: *Na fila*, *Agendado*, *Processando*, *Concluído*, *Erro*, *Cancelado*, *Cancelando*

## <a name="next-steps"></a>Próximas etapas

[Registre-se para eventos de alteração de estado do trabalho](job-state-events-cli-how-to.md)
