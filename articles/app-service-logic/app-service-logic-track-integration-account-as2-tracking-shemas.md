---
title: Esquemas de Acompanhamento AS2 | Microsoft Docs | Microsoft Docs
description: Saiba mais sobre os esquemas de acompanhamento AS2
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: e79f93c94d729a604944fc065b7016e9dbfcb9b6
ms.openlocfilehash: 65a1da23b1fdd671035f99a2a66da7c948573eb9


---
# <a name="as2-tracking-schemas"></a>Esquemas de acompanhamento AS2
Você pode usar esses esquemas de acompanhamento AS2 em sua conta de integração do Azure para ajudá-lo a monitorar transações B2B (entre empresas):

* Esquema de acompanhamento de mensagens AS2
* Esquema de acompanhamento MDN AS2

## <a name="as2-message-tracking-schema"></a>Esquema de acompanhamento de mensagens AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | Cadeia de caracteres | Nome do destinatário da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| as2From | Cadeia de caracteres | Nome do remetente da mensagem AS2, dos cabeçalhos da mensagem AS2. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction | Cadeia de caracteres | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | Cadeia de caracteres | ID da mensagem AS2, dos cabeçalhos da mensagem AS2 (Opcional) |
| dispositionType |Cadeia de caracteres | Valor do tipo de disposição MDN (notificação de disposição de mensagem). (Opcional) |
| fileName | Cadeia de caracteres | Nome do arquivo, do cabeçalho da mensagem AS2. (Opcional) |
| isMessageFailed |Booliano | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned | Booliano | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isMessageEncrypted | Booliano | Se a mensagem AS2 foi ou não criptografada. (Obrigatório) |
| isMessageCompressed |Booliano | Se a mensagem AS2 foi ou não comprimida. (Obrigatório) |
| correlationMessageId | Cadeia de caracteres | ID da mensagem AS2 para correlacionar mensagens com MDNs. (Opcional) |
| incomingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de entrada AS2. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Detalhes do cabeçalho da mensagem de saída AS2. (Opcional) |
| isNrrEnabled | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| isMdnExpected | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| mdnType | Enum | Os valores permitidos são **NotConfigured**, **Sync** e **Async**. (Obrigatório) |

## <a name="as2-mdn-tracking-schema"></a>Esquema de acompanhamento MDN AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": {
                },
                "outgoingHeaders": {
                }
            }
    }
````

| Propriedade | Tipo | Descrição |
| --- | --- | --- |
| senderPartnerName | Cadeia de caracteres | O nome do parceiro do remetente da mensagem AS2. (Opcional) |
| receiverPartnerName | Cadeia de caracteres | O nome do parceiro do destinatário da mensagem AS2. (Opcional) |
| as2To | Cadeia de caracteres | O nome do parceiro que recebe a mensagem AS2. (Obrigatório) |
| as2From | Cadeia de caracteres | O nome do parceiro que envia a mensagem AS2. (Obrigatório) |
| agreementName | Cadeia de caracteres | Nome do contrato AS2 para o qual as mensagens são resolvidas. (Opcional) |
| direction |Cadeia de caracteres | Indica a direção do fluxo de mensagens, receber ou enviar. (Obrigatório) |
| messageId | Cadeia de caracteres | ID da mensagem AS2. (Opcional) |
| originalMessageId |Cadeia de caracteres | ID da mensagem AS2 original. (Opcional) |
| dispositionType | Cadeia de caracteres | Valor do tipo de disposição MDN. (Opcional) |
| isMessageFailed |Booliano | Se a mensagem AS2 falha ou não. (Obrigatório) |
| isMessageSigned |Booliano | Se a mensagem AS2 foi ou não assinada. (Obrigatório) |
| isNrrEnabled | Booliano | Use o valor padrão se o valor não for conhecido. (Obrigatório) |
| statusCode | Enum | Os valores aceitos são **Accepted**, **Rejected**, **AcceptedWithErrors**. (Obrigatório) |
| micVerificationStatus | Enum | Os valores permitidos são **NotApplicable**, **Succeeded** ou **Failed**. (Obrigatório) |
| correlationMessageId | Cadeia de caracteres | ID de correlação. A ID da mensagem original (a ID da mensagem para a qual o MDN está configurado). (Opcional) |
| incomingHeaders | Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de entrada. (Opcional) |
| outgoingHeaders |Dicionário de JToken | Indica detalhes do cabeçalho da mensagem de saída. (Opcional) |

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre o [Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md).    
* Saiba mais sobre o [monitoramento de mensagens de B2B](app-service-logic-monitor-b2b-message.md).   
* Saiba mais sobre os esquemas de [acompanhamento personalizado B2B](app-service-logic-track-integration-account-custom-tracking-shema.md).   
* Saiba mais sobre os [esquemas de acompanhamento X12](app-service-logic-track-integration-account-x12-tracking-shemas.md).   
* Saiba mais sobre [acompanhamento de mensagens B2B no portal do Operations Management Suite](app-service-logic-track-b2b-messages-omsportal.md).



<!--HONumber=Dec16_HO3-->


