---
title: Esquema de eventos da Grade de Eventos do Azure
description: "Descreve as propriedades que são fornecidas para eventos com a Grade de Eventos do Azure"
services: event-grid
author: banisadr
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 11/07/2017
ms.author: babanisa
ms.openlocfilehash: caa709fdc2a59472ee812bde91f7300396aa5755
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/11/2017
---
# <a name="azure-event-grid-event-schema"></a>Esquema de eventos da Grade de Eventos do Azure

Este artigo descreve as propriedades e esquema que estão presentes para todos os eventos. Os eventos consistem em um conjunto de cinco propriedades de cadeia de caracteres obrigatórias e um objeto data obrigatório. As propriedades são comuns a todos os eventos de qualquer fornecedor. O objeto data contém propriedades que são específicas a cada fornecedor. Para tópicos do sistema, essas propriedades são específicas ao provedor de recursos, como Armazenamento do Azure ou Hub de Eventos do Azure.

Os eventos são enviados à Grade de Eventos do Azure em uma matriz, que pode conter vários objetos de evento. Se houver apenas um único evento, o comprimento da matriz será 1. A matriz pode ter um tamanho total de até 1 MB. Cada evento na matriz é limitado a 64 KB.

## <a name="event-schema"></a>Esquema do evento

O exemplo a seguir mostra as propriedades que são usadas por todos os editores de eventos:

```json
[
  {
    "topic": string,
    "subject": string,    
    "id": string,
    "eventType": string,
    "eventTime": string,
    "data":{
      object-unique-to-each-publisher
    }
  }
]
```

Por exemplo, o esquema publicado para um evento de armazenamento de Blob do Azure é:

```json
[
  {
    "topic": "/subscriptions/{subscription-id}/resourceGroups/Storage/providers/Microsoft.Storage/storageAccounts/xstoretestaccount",
    "subject": "/blobServices/default/containers/oc2d2817345i200097container/blobs/oc2d2817345i20002296blob",
    "eventType": "Microsoft.Storage.BlobCreated",
    "eventTime": "2017-06-26T18:41:00.9584103Z",
    "id": "831e1650-001e-001b-66ab-eeb76e069631",
    "data": {
      "api": "PutBlockList",
      "clientRequestId": "6d79dbfb-0e37-4fc4-981f-442c9ca65760",
      "requestId": "831e1650-001e-001b-66ab-eeb76e000000",
      "eTag": "0x8D4BCC2E4835CD0",
      "contentType": "application/octet-stream",
      "contentLength": 524288,
      "blobType": "BlockBlob",
      "url": "https://oc2d2817345i60006.blob.core.windows.net/oc2d2817345i200097container/oc2d2817345i20002296blob",
      "sequencer": "00000000000004420000000000028963",
      "storageDiagnostics": {
        "batchId": "b68529f3-68cd-4744-baa4-3c0498ec19f0"
      }
    }
  }
]
```
 
## <a name="event-properties"></a>Propriedades do evento

Todos os eventos conterão os mesmos dados de nível superior a seguir:

| Propriedade | Tipo | Descrição |
| -------- | ---- | ----------- |
| topic | string | Caminho de recurso completo para a origem do evento. Esse campo não é gravável. |
| subject | string | Caminho definido pelo fornecedor para o assunto do evento. |
| eventType | string | Um dos tipos de evento registrados para a origem do evento. |
| eventTime | string | A hora em que o evento é gerado com base na hora UTC do provedor. |
| ID | string | Identificador exclusivo do evento. |
| data | objeto | Dados do evento específicos ao provedor de recursos. |

Para saber mais sobre as propriedades no objeto de dados, consulte a origem do evento:

* [Assinaturas do Azure (operações de gerenciamento)](event-schema-subscriptions.md)
* [Armazenamento de Blobs](event-schema-blob-storage.md)
* [Hubs de Eventos](event-schema-event-hubs.md)
* [Grupos de recursos (operações de gerenciamento)](event-schema-resource-groups.md)

Para tópicos personalizados, o publicador do evento determina o objeto de dados. Os dados de nível superior devem conter os mesmos campos do que os eventos definidos pelo recurso padrão. Ao publicar eventos em tópicos personalizados, você deve considerar a modelagem do assunto dos eventos para auxiliar no roteamento e na filtragem.

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos do Azure, confira [O que é uma Grade de eventos?](overview.md)
* Para obter mais informações sobre como criar uma assinatura da Grade de Eventos do Azure, confira [Event Grid subscription schema](subscription-creation-schema.md) (Esquema de assinatura da Grade de Eventos).
