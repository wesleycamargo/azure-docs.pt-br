---
title: "Saiba como usar o conector do Barramento de Serviço do Azure nos seus aplicativos lógicos | Microsoft Docs"
description: "Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao Barramento de Serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para a fila, enviar para o tópico, receber da fila e receber da assinatura."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: d6d14f5f-2126-4e33-808e-41de08e6721f
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/02/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 66fc8f7e1da55dbe6bb1dd8b8d6a535c498c1cf7
ms.openlocfilehash: 1e23402cbc63aeb262bfb471745589cc0bbd734f


---
# <a name="get-started-with-the-azure-service-bus-connector"></a>Introdução ao conector do Barramento de Serviço do Azure
Conecte-se ao Barramento de Serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para a fila, enviar para o tópico, receber da fila e receber da assinatura.

Para usar [qualquer conector](apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="connect-to-service-bus"></a>Conectar-se ao Barramento de Serviço
Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma conexão com o serviço. Uma [conexão](connectors-overview.md) fornece uma conectividade entre um aplicativo lógico e outro serviço.  

> [!INCLUDE [Steps to create a connection to Azure Service Bus](../../includes/connectors-create-api-servicebus.md)]
> 
> 

## <a name="use-a-service-bus-trigger"></a>Usar um gatilho do Barramento de Serviço
Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).  

> [!INCLUDE [Steps to create a Service Bus trigger](../../includes/connectors-create-api-servicebus-trigger.md)]
> 
> 

## <a name="use-a-service-bus-action"></a>Usar uma ação de Barramento de Serviço
Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-what-are-logic-apps.md#logic-app-concepts).

[!INCLUDE [Steps to create a Service Bus action](../../includes/connectors-create-api-servicebus-action.md)]

## <a name="technical-details"></a>Detalhes técnicos
Estes são os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite.

### <a name="service-bus-triggers"></a>Gatilhos do Barramento de Serviço
O Barramento de Serviço tem os seguintes gatilhos:  

| Gatilho | Descrição |
| --- | --- |
| [Quando uma mensagem é recebida em uma fila](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue) |Esta operação dispara um fluxo quando uma mensagem é recebida em uma fila. |
| [Quando uma mensagem é recebida em uma assinatura de tópico](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription) |Esta operação dispara um fluxo quando uma mensagem é recebida em uma assinatura de tópico. |

### <a name="service-bus-actions"></a>Ações do Barramento de Serviço
O Barramento de Serviço tem as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [Enviar mensagem](connectors-create-api-servicebus.md#send-message) |Esta operação envia uma mensagem para uma fila ou um tópico. |

### <a name="action-and-trigger-details"></a>Detalhes da ação e do gatilho
A seguir, os detalhes das ações e dos gatilhos para esse conector, com suas respostas.

#### <a name="send-message"></a>Enviar mensagem
| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| ContentData* |Conteúdo |Conteúdo da mensagem. |
| ContentType |Tipo de conteúdo |Tipo do conteúdo da mensagem. |
| Propriedades |Propriedades |Pares de chave-valor para cada propriedade agenciada. |
| entityName* |Nome da fila/do tópico |Nome da fila ou do tópico. |

Esses parâmetros avançados também estão disponíveis:

| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| MessageId |ID da Mensagem |Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado. |
| Para |Para |Endereço para envio. |
| ReplyTo |Responder Para |Endereço de resposta da fila. |
| ReplyToSessionId |ID da Sessão Responder Para |Identificador de resposta da sessão. |
| Rótulo |Rótulo |Rótulo específico do aplicativo. |
| ScheduledEnqueueTimeUtc |ScheduledEnqueueTimeUtc |Data e hora, em UTC, em que a mensagem será adicionada à fila. |
| SessionId |Id da Sessão |Identificador da sessão. |
| CorrelationId |ID de correlação |Identificador da correlação. |
| TimeToLive |Vida Útil |A duração, em tiques, pela qual uma mensagem é válida. A duração começa quando a mensagem é enviada ao Barramento de Serviço. |

Um * indica que uma propriedade é obrigatória.

#### <a name="when-a-message-is-received-in-a-queue"></a>Quando uma mensagem é recebida em uma fila
| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| queueName* |Nome da fila |Nome da fila. |

Um * indica que uma propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes de saída
ServiceBusMessage: esse objeto tem o conteúdo e as propriedades de uma Mensagem do Barramento de Serviço.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| ContentData |string |Conteúdo da mensagem. |
| ContentType |string |Tipo do conteúdo da mensagem. |
| Propriedades |objeto |Pares de chave-valor para cada propriedade agenciada. |
| MessageId |string |Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado. |
| Para |string |Endereço de envio. |
| ReplyTo |cadeia de caracteres |Endereço de resposta da fila. |
| ReplyToSessionId |string |Identificador de resposta da sessão. |
| Rótulo |string |Rótulo específico do aplicativo. |
| ScheduledEnqueueTimeUtc |string |Data e hora, em UTC, em que a mensagem será adicionada à fila. |
| SessionId |string |Identificador da sessão. |
| CorrelationId |string |Identificador da correlação. |
| TimeToLive |cadeia de caracteres |A duração, em tiques, pela qual uma mensagem é válida. A duração começa quando a mensagem é enviada ao Barramento de Serviço. |

#### <a name="when-a-message-is-received-in-a-topic-subscription"></a>Quando uma mensagem é recebida em uma assinatura de tópico
| Nome da propriedade | Nome de exibição | Descrição |
| --- | --- | --- |
| topicName* |Nome do tópico |Nome do tópico. |
| subscriptionName* |Nome da assinatura do tópico |Nome da assinatura do tópico. |

Um * indica que uma propriedade é obrigatória.

##### <a name="output-details"></a>Detalhes de saída
ServiceBusMessage: esse objeto tem o conteúdo e as propriedades de uma Mensagem do Barramento de Serviço.

| Nome da propriedade | Tipo de dados | Descrição |
| --- | --- | --- |
| ContentData |string |Conteúdo da mensagem. |
| ContentType |string |Tipo do conteúdo da mensagem. |
| Propriedades |objeto |Pares de chave-valor para cada propriedade agenciada. |
| MessageId |string |Um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado. |
| Para |string |Endereço de envio. |
| ReplyTo |cadeia de caracteres |Endereço de resposta da fila. |
| ReplyToSessionId |string |Identificador de resposta da sessão. |
| Rótulo |string |Rótulo específico do aplicativo. |
| ScheduledEnqueueTimeUtc |string |Data e hora, em UTC, em que a mensagem será adicionada à fila. |
| SessionId |string |Identificador da sessão. |
| CorrelationId |string |Identificador da correlação. |
| TimeToLive |cadeia de caracteres |A duração, em tiques, pela qual uma mensagem é válida. A duração começa quando a mensagem é enviada ao Barramento de Serviço. |

### <a name="http-responses"></a>Respostas HTTP
As ações e os gatilhos anteriores podem retornar um ou mais dos seguintes códigos de status HTTP:

| Name | Descrição |
| --- | --- |
| 200 |OK |
| 202 |Aceita |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro interno do servidor. Ocorreu um erro desconhecido. |
| padrão |Falha na operação. |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Jan17_HO3-->


