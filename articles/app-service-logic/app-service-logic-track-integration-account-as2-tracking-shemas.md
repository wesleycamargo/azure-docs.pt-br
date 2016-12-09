---
title: Esquema de controle as2 | Microsoft Docs
description: Saiba mais sobre o esquema de controle as2
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
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>Esquemas de controle AS2
Os esquemas de controle AS2 com suporte são

* Esquema de controle de mensagens AS2
* Esquema de controle de MDN AS2

## <a name="as2-message-tracking-schema"></a>Esquema de controle de mensagens AS2
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

| Propriedade | Descrição |
| --- | --- |
| senderPartnerName |Opcional, cadeia de caracteres.  Indica o nome do parceiro do remetente da mensagem AS2 |
| receiverPartnerName |Opcional, cadeia de caracteres.  Indica o nome do parceiro do destinatário da mensagem AS2 |
| as2To |Obrigatório, cadeia de caracteres.  Indica o nome do destinatário de mensagem AS2 incluído nos cabeçalhos da mensagem AS2 |
| as2From |Obrigatório, cadeia de caracteres. Indica o nome do remetente de mensagem AS2 incluído nos cabeçalhos da mensagem AS2 |
| agreementName |Opcional, cadeia de caracteres.  Nome do contrato AS2 para o qual as mensagens são resolvidas |
| direction |Obrigatório, cadeia de caracteres.  Indica a direção do fluxo de mensagens, recebidas ou enviadas |
| messageId |Opcional, cadeia de caracteres.  Indica a ID da Mensagem AS2 incluída nos cabeçalhos da mensagem AS2 |
| dispositionType |Opcional, cadeia de caracteres. Indica o valor do tipo de disposição MDN |
| fileName |Opcional, cadeia de caracteres.  Indica a o nome de arquivo incluído no cabeçalho da mensagem AS2 |
| isMessageFailed |Obrigatório, booliano.  Indica se mensagem AS2 falhou ou não |
| isMessageSigned |Obrigatório, booliano.  Indica se mensagem AS2 foi assinada ou não |
| isMessageEncrypted |Obrigatório, booliano. Indica se mensagem AS2 foi criptografada ou não |
| isMessageCompressed |Obrigatório, booliano. Indica se mensagem AS2 foi compactada ou não |
| correlationMessageId |Opcional, cadeia de caracteres. Indica a AS2messageid para correlacionar mensagens com MDNs |
| incomingHeaders |Opcional, dicionário de JToken.  Indica detalhes de cabeçalho da mensagem AS2 recebida |
| outgoingHeaders |Opcional, dicionário de JToken.  Indica detalhes de cabeçalho da mensagem AS2 enviada |
| isNrrEnabled |Obrigatório, booliano.  Use o valor padrão se o valor não for conhecido |
| isMdnExpected |Obrigatório, booliano. Use o valor padrão se o valor não for conhecido |
| mdnType |Obrigatório, enum. Os valores permitidos são NotConfigured ou Sync ou Async |

## <a name="as2-mdn-tracking-schema"></a>Esquema de controle de MDN AS2
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

| Propriedade | Descrição |
| --- | --- |
| senderPartnerName |Opcional, cadeia de caracteres. Indica o nome do parceiro do remetente da mensagem AS2 |
| receiverPartnerName |Opcional, cadeia de caracteres. Indica o nome do parceiro do destinatário da mensagem AS2 |
| as2To |Obrigatório, cadeia de caracteres. Indica o nome do parceiro que recebe a mensagem AS2 |
| as2From |Obrigatório, cadeia de caracteres. Indica o nome do parceiro que envia a mensagem AS2 |
| agreementName |Opcional, cadeia de caracteres. Nome do contrato AS2 para o qual as mensagens são resolvidas |
| direction |Obrigatório, cadeia de caracteres. Indica a direção do fluxo de mensagens, recebidas ou enviadas |
| messageId |Opcional, cadeia de caracteres. Indica a ID da mensagem AS2 |
| originalMessageId |Opcional, cadeia de caracteres. Indica a ID da mensagem AS2 original |
| dispositionType |Opcional, cadeia de caracteres. Indica o valor do tipo de disposição MDN |
| isMessageFailed |Obrigatório, booliano. Indica se mensagem AS2 falhou ou não |
| isMessageSigned |Obrigatório, booliano. Indica se mensagem AS2 foi assinada ou não |
| isNrrEnabled |Obrigatório, booliano.  Use o valor padrão se o valor não for conhecido |
| statusCode |Obrigatório, enum.  Os valores aceitos são Accepted ou Rejected ou AcceptedWithErrros |
| micVerificationStatus |Obrigatório, enum.  Os valores permitidos são não NotApplicable ou Succeeded ou Failed |
| correlationMessageId |Opcional, cadeia de caracteres.  Indica a ID da correlação.  A ID da correlação é a ID da mensagem original (id da mensagem para a qual o MDN está configurado) |
| incomingHeaders |Opcional, dicionário de JToken.  Indica detalhes de cabeçalho da mensagem recebida |
| outgoingHeaders |Opcional, dicionário de JToken.  Indica detalhes de cabeçalho da mensagem enviada |

## <a name="next-steps"></a>Próximas etapas
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[Saiba mais sobre o monitoramento de mensagens de B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Rastreando mensagens B2B no Portal do OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[Saiba mais sobre o Esquema de controle personalizado B2B](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[Saiba mais sobre o esquema de controle X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[Saiba mais sobre o Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


