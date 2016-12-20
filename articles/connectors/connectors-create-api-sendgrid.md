---
title: SendGrid | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Provedor de Conexão do SendGrid permite enviar emails e gerenciar listas de destinatários."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ad35f6590a39972ec9be7168059738b6daae0574


---
# <a name="get-started-with-the-sendgrid-connector"></a>Introdução ao conector do SendGrid
O Provedor de Conexão do SendGrid permite enviar emails e gerenciar listas de destinatários.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. 
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do SendGrid pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML. 

 O conector SendGrid tem as ações a seguir disponíveis. Não há nenhum gatilho.

### <a name="sendgrid-actions"></a>Ações do SendGrid
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Envia um email usando a API do SendGrid (limitado a 10.000 destinatários) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Adiciona um destinatário individual a uma lista de destinatários |

## <a name="create-a-connection-to-sendgrid"></a>Criar uma conexão com o SendGrid
Para criar Aplicativos lógicos com o SendGrid, primeiro você deve criar uma **conexão**, em seguida, forneça os detalhes para as seguintes propriedades: 

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| ApiKey |Sim |Fornecer sua chave de API do SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

## <a name="reference-for-sendgrid"></a>Referência do SendGrid
Aplica-se à versão: 1.0

## <a name="sendemail"></a>SendEmail
Enviar email: envia um email usando a API do SendGrid (limitado a 10.000 destinatários) 

```POST: /api/mail.send.json``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| solicitação | |sim |corpo |nenhum |Mensagem de email a ser enviada |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 429 |Número excessivo de solicitações |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na Operação. |

## <a name="addrecipienttolist"></a>AddRecipientToList
Adicionar destinatário à lista: adiciona um destinatário individual a uma lista de destinatários 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| listId |string |Sim |path |nenhum |ID exclusiva da lista de destinatários |
| recipientId |string |Sim |path |nenhum |ID exclusiva do destinatário |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
### <a name="emailrequest"></a>EmailRequest
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Da |string |Sim |
| fromname |string |Não |
| para |string |Sim |
| toname |string |Não |
| subject |string |Sim |
| corpo |string |Sim |
| ishtml |Booliano |Não |
| cc |string |Não |
| ccname |string |Não |
| bcc |string |Não |
| bccname |string |Não |
| replyto |string |Não |
| data |string |Não |
| headers |string |Não |
| de entrada |array |Não |
| filenames |array |Não |

### <a name="emailresponse"></a>EmailResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| message |string |Não |

### <a name="recipientlists"></a>RecipientLists
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| lists |array |Não |

### <a name="recipientlist"></a>RecipientList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |inteiro |Não |
| name |string |Não |
| recipient_count |inteiro |Não |

### <a name="recipients"></a>Destinatários
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| recipients |array |Não |

### <a name="recipient"></a>Destinatário
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| email |string |Não |
| last_name |string |Não |
| first_name |string |Não |
| ID |string |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)




<!--HONumber=Nov16_HO3-->


