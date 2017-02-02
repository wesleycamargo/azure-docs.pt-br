---
title: Esquemas de acompanhamento personalizado | Microsoft Docs
description: Saiba mais sobre esquemas de acompanhamento personalizado
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d88fa57c2f343636d7529780dc1b27ccb604ee02
ms.openlocfilehash: 0de8128b01f760340e85078b433707c566fa2e4f


---
# <a name="custom-tracking-schemas"></a>Esquemas de acompanhamento personalizado
Você pode usar um esquema de acompanhamento personalizado em sua conta de integração do Azure para ajudá-lo a monitorar transações B2B (entre empresas).

## <a name="custom-tracking-schema"></a>Esquema de controle personalizado
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| sourceType |   | O tipo da fonte de execução. Os valores permitidos são **Microsoft.Logic/workflows** e **personalizado**. (Obrigatório) |
| Fonte |   | Se o tipo de fonte for **Microsoft.Logic/workflows**, as informações de origem precisarão seguir este esquema. Se o tipo de origem for **personalizado**, o esquema será um JToken. (Obrigatório) |
| systemId | Cadeia de caracteres | ID do sistema de aplicativo lógico. (Obrigatório) |
| runId | Cadeia de caracteres | ID de execução do aplicativo lógico. (Obrigatório) |
| operationName | Cadeia de caracteres | O nome da operação (por exemplo, ação ou gatilho). (Obrigatório) |
| repeatItemScopeName | Cadeia de caracteres | Repita o nome do item se a ação estiver dentro de um loop `foreach`/`until`. (Obrigatório) |
| repeatItemIndex | Número inteiro | Se a ação está ou não dentro de um loop `foreach`/`until`. Indica o índice de itens repetidos. (Obrigatório) |
| trackingId | Cadeia de caracteres | A ID de acompanhamento para correlacionar as mensagens. (Opcional) |
| correlationId | Cadeia de caracteres | A ID de correlação para correlacionar as mensagens. (Opcional) |
| clientRequestId | Cadeia de caracteres | O cliente pode populá-la para correlacionar mensagens. (Opcional) |
| eventLevel |   | Nível do evento. (Obrigatório) |
| eventTime |   | A hora do evento no formato UTC AAAA-MM-DDTHH:MM:SS.00000Z. (Obrigatório) |
| recordType |   | Tipo do registro de acompanhamento. O valor permitido é **personalizado**. (Obrigatório) |
| record |   | Tipo de registro personalizado. O formato permitido é JToken. (Obrigatório) |

## <a name="b2b-protocol-tracking-schemas"></a>Esquemas de acompanhamento do protocolo B2B
Para obter informações sobre esquemas de acompanhamento do protocolo B2B, veja:
* [Esquemas de acompanhamento de AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquemas de acompanhamento de X12](app-service-logic-track-integration-account-x12-tracking-shemas.md)

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [monitoramento de mensagens de B2B](app-service-logic-monitor-b2b-message.md).   
* Saiba mais sobre [acompanhamento de mensagens B2B no portal do Operations Management Suite](app-service-logic-track-b2b-messages-omsportal.md).
* Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).



<!--HONumber=Dec16_HO3-->


