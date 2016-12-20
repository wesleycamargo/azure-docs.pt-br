---
title: "Adicionar o Conector do Twilio aos seus Aplicativos lógicos | Microsoft Docs"
description: "Visão geral do Conector do Twilio com os parâmetros da API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0999d96b4819d6bf03faa843750c5c4b19649774


---
# <a name="get-started-with-the-twilio-connector"></a>Introdução ao conector do Twilio
Conecte-se a Twilio para enviar e receber mensagens SMS, MMS e de IP globais.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

Com o Twilio, você pode:

* Compile seu fluxo de negócios baseado nos dados obtidos do Twilio. 
* Use ações para obter uma mensagem, listar mensagens e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando você recebe uma nova mensagem do Twilio, você pode obtê-la e usá-la como um fluxo de trabalho do Barramento de Serviço. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O Conector do Twilio inclui as ações a seguir. Não há nenhum gatilho. 

| Gatilhos | Ações |
| --- | --- |
| Nenhum |<ul><li>Obter Mensagem</li><li>Listar Mensagens</li><li>Enviar Mensagem</li></ul> |

Todos os conectores dão suporte a dados nos formatos JSON e XML. 

## <a name="create-a-connection-to-twilio"></a>Criar uma conexão com o Twilio
Ao adicionar esse Conector aos seus aplicativos lógicos, insira os seguintes valores do Twilio:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| ID da Conta |Sim |Insira seu ID de conta do Twilio |
| Token de Acesso |Sim |Insira seu token de acesso do Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Se você não tiver uma, confira [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) para criar um token de acesso.

> [!TIP]
> Você pode usar essa mesma conexão do Twilio em outros aplicativos lógicos.
> 
> 

## <a name="swagger-rest-api-reference"></a>Referência da API REST do Swagger
#### <a name="this-documentation-is-for-version-10"></a>Esta documentação destina-se à versão: 1.0
### <a name="get-message"></a>Obter Mensagem
Retorna uma única mensagem especificada pela ID de Mensagem fornecida.  
```GET: /Messages/{MessageId}.json```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |Sim |path |Nenhum |ID da Mensagem |

### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 404 |Mensagem não encontrada |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

### <a name="list-messages"></a>Listar Mensagens
Retorna uma lista de mensagens associadas à sua conta.  
```GET: /Messages.json```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| Para |string |não |query |Nenhum |Para o número de telefone |
| Da |string |não |query |Nenhum |Do número de telefone |
| DateSent |string |não |query |Nenhum |Mostrar apenas as mensagens enviadas nessa data (no formato GMT), indicado como AAAA-MM-DD. Exemplo: DateSent = 2009-07-06. Você também pode especificar desigualdade, como DateSent < = AAAA-MM-DD para mensagens que foram enviadas até a meia-noite (inclusive) em uma data e DateSent > = AAAA-MM-DD para mensagens enviadas a partir da meia-noite (inclusive) em uma data. |
| PageSize |inteiro |não |query |50 |Quantos recursos devem ser retornados em cada página da lista. O padrão é 50. |
| Página |inteiro |não |query |0 |Número da página. O padrão é 0. |

### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

### <a name="send-message"></a>Enviar Mensagem
Enviar uma nova mensagem para um número de celular.  
```POST: /Messages.json```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Sim |corpo |Nenhum |Mensagem para Enviar |

### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação concluída com êxito |
| 400 |Solicitação incorreta |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest: modelo de solicitação para a operação Enviar Mensagem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Da |string |Sim |
| Para |string |Sim |
| corpo |string |sim |
| media_url |array |não |
| status_callback |string |não |
| messaging_service_sid |string |não |
| application_sid |string |não |
| max_price |string |não |

#### <a name="message-model-for-message"></a>Mensagem: modelo para mensagem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| corpo |string |não |
| Da |string |não |
| Para |string |não |
| status |string |não |
| sid |string |não |
| account_sid |string |não |
| api_version |string |não |
| num_segments |string |não |
| num_media |string |não |
| date_created |string |não |
| date_sent |string |não |
| date_updated |string |não |
| direction |string |não |
| error_code |string |não |
| error_message |string |não |
| preço |string |não |
| price_unit |string |não |
| uri |string |não |
| subresource_uris |array |não |
| messaging_service_sid |string |não |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList: modelo de resposta para a operação Listar Mensagens
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| mensagens |array |não |
| Página |inteiro |não |
| page_size |inteiro |não |
| num_pages |inteiro |não |
| uri |string |não |
| first_page_uri |string |não |
| next_page_uri |string |não |
| total |inteiro |não |
| previous_page_uri |string |não |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList: modelo de resposta para a operação Listar Mensagens
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| incoming_phone_numbers |array |não |
| Página |inteiro |não |
| page_size |inteiro |não |
| num_pages |inteiro |não |
| uri |string |não |
| first_page_uri |string |não |
| next_page_uri |string |não |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest: modelo de solicitação para a operação Adicionar Número de Entrada
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| PhoneNumber |string |Sim |
| AreaCode |string |não |
| FriendlyName |string |não |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber: Número de Telefone de Entrada
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| phone_number |string |não |
| friendly_name |string |não |
| sid |string |não |
| account_sid |string |não |
| date_created |string |não |
| date_updated |string |não |
| recursos |não definido |não |
| status_callback |string |não |
| status_callback_method |string |não |
| api_version |string |não |

#### <a name="capabilities-phone-number-capabilities"></a>Funcionalidades: Recursos de Número de Telefone
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| mms |booleano |não |
| sms |booleano |não |
| voice |booleano |não |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers: Números de Telefone Disponíveis
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| phone_number |string |não |
| friendly_name |string |não |
| lata |string |não |
| latitude |string |não |
| longitude |string |não |
| postal_code |string |não |
| rate_center |string |não |
| region |string |não |
| mms |booleano |não |
| sms |booleano |não |
| voice |booleano |não |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords: classe de Registros de Uso
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| categoria |string |não |
| uso |string |não |
| usage_unit |string |não |
| Descrição |string |não |
| preço |número |não |
| price_unit |string |não |
| count |string |não |
| count_unit |string |não |
| start_date |string |não |
| end_date |string |não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


