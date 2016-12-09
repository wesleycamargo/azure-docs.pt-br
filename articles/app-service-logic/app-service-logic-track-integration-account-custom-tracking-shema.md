---
title: Esquema de controle personalizado | Microsoft Docs
description: Saiba mais sobre o esquema de controle personalizado
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>Esquema de controle personalizado
## <a name="supported-custom-tracking-schema"></a>Esquema de controle personalizado com suporte
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

| Propriedade | Descrição |
| --- | --- |
| sourceType |Obrigatório.  Indica o tipo de fonte de execução. Os valores permitidos são Microsoft.Logic/workflows ou personalizado |
| Fonte |Obrigatório – Se o tipo de fonte for Microsoft.Logic/workflows, as informações de origem precisam seguir este esquema. Se o tipo de origem for 'personalizado'; o esquema será um JToken  |
| systemId |Obrigatório, cadeia de caracteres.  Indica a ID do sistema de aplicativo lógico |
| runId |Obrigatório, cadeia de caracteres.  Indica a ID de execução de aplicativo lógico |
| operationName |Obrigatório, cadeia de caracteres.  Indica o nome da operação (por exemplo, ação ou gatilho) |
| repeatItemScopeName |Obrigatório, cadeia de caracteres. Indica o nome do item de repetição se a ação estiver dentro de um loop foreach/until |
| repeatItemIndex |Obrigatório, int.  Se a ação estiver dentro de um loop foreach/until; indica o índice do item repetido  |
| trackingId |Opcional, cadeia de caracteres. Indica a ID de controle para correlacionar as mensagens |
| correlationId |Opcional, cadeia de caracteres. Indica a ID de correlação para correlacionar as mensagens |
| clientRequestId |Opcional, cadeia de caracteres.  O cliente pode preenchê-lo para correlacionar mensagens |
| eventLevel |Obrigatório. Indica o nível do evento |
| eventTime |Obrigatório. Indica a hora do evento no formato UTC AAAA-MM-DDTHH:MM:SS.00000Z |
| recordType |Obrigatório, indica o tipo de registro de controle. O valor permitido é Personalizado |
| record |Obrigatório.  Indica o tipo de registro personalizado e o formato permitido é JToken |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>Esquemas de controle do protocolo B2B com suporte
* [Esquema de Controle do AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Esquema de Controle do X12](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o monitoramento de mensagens de B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Rastreando mensagens B2B no Portal do OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


