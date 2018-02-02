---
title: Assinatura de grade de eventos do Azure com modelo
description: Crie uma assinatura de grade de eventos com um modelo do Resource Manager.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 01/30/2018
ms.author: tomfitz
ms.openlocfilehash: ee0b2c228ae4ea53c0ee9794529aa190334ceed9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="use-resource-manager-template-for-event-grid-subscription"></a>Use o modelo do Resource Manager para a assinatura de Grade de Eventos

Este artigo mostra como usar um modelo do Azure Resource Manager para criar assinaturas de Grade de Eventos. O formato usado vai variar se você estiver assinando eventos do grupo de recursos ou eventos para um tipo de recurso específico. Os dois formatos são mostrados neste artigo.

## <a name="subscribe-to-resource-group-events"></a>Assinar eventos de grupo de recursos

Ao assinar eventos de grupo de recursos, use `Microsoft.EventGrid/eventSubscriptions` como o tipo de recurso. Para o tipo pontual de evento, use `WebHook` ou `EventHub`.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "resources": [
        {
            "type": "Microsoft.EventGrid/eventSubscriptions",
            "name": "mySubscription",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false,
                    "includedEventTypes": ["All"]
                }
            }
        }
    ]
}
```

Quando você implanta esse modelo em um grupo de recursos, você assina eventos para esse grupo de recursos.

## <a name="subscribe-to-resource-events"></a>Assinar eventos de recurso

Ao assinar eventos de recursos, você associa a assinatura ao recurso correto, incluindo o nome e o tipo de recurso na definição da assinatura. Para o tipo de recurso, use `<provider-namespace>/<resource-type>/providers/eventSubscriptions`. Para o nome, use `<resource-name>/Microsoft.EventGrid/<subscription-name>`. Para o tipo pontual de evento, use `WebHook` ou `EventHub`.

O exemplo a seguir mostra como assinar eventos de armazenamento de Blobs.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storageName": {
            "type": "string"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
            "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/myStorageSubscription')]",
            "apiVersion": "2018-01-01",
            "properties": {
                "destination": {
                    "endpointType": "WebHook",
                    "properties": {
                        "endpointUrl": "https://requestb.in/ynboxiyn"
                    }
                },
                "filter": {
                    "subjectBeginsWith": "",
                    "subjectEndsWith": "",
                    "isSubjectCaseSensitive": false
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>Próximas etapas

* Para ver uma introdução à Grade de Eventos, confira [About Event Grid](overview.md) (Sobre a Grade de Eventos).
* Para obter uma introdução ao Resource Manager, consulte [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
* Para começar a usar a Grade de Eventos, confira [Criar e rotear eventos personalizados com a Grade de Eventos do Azure](custom-event-quickstart.md).