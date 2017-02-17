---
title: Outlook.com | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. É possível executar várias ações, como enviar emails, agendar reuniões, adicionar contatos, etc."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 87113c85-d158-4dd5-9ed5-5748130003d6
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 409e1a104fa73d911ea508cbff311cb48fc20f9f


---
# <a name="get-started-with-the-outlookcom-connector"></a>Introdução ao conector do Outlook.com
O conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. É possível executar várias ações, como enviar emails, agendar reuniões, adicionar contatos, etc.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
>
>

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do Outlook.com pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Outlook.com contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="outlookcom-actions"></a>Ações do Outlook.com
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [GetEmails](connectors-create-api-outlook.md#getemails) |Recupera emails de uma pasta |
| [SendEmail](connectors-create-api-outlook.md#sendemail) |Envia um email |
| [DeleteEmail](connectors-create-api-outlook.md#deleteemail) |Exclui um email por ID |
| [MarkAsRead](connectors-create-api-outlook.md#markasread) |Marca um email como lido |
| [ReplyTo](connectors-create-api-outlook.md#replyto) |Responde a um email |
| [GetAttachment](connectors-create-api-outlook.md#getattachment) |Recupera o anexo de email por ID |
| [SendMailWithOptions](connectors-create-api-outlook.md#sendmailwithoptions) |Envia um email com várias opções e aguarda uma resposta do destinatário com uma das opções |
| [SendApprovalMail](connectors-create-api-outlook.md#sendapprovalmail) |Envia um email de aprovação e aguarda uma resposta do destinatário |
| [CalendarGetTables](connectors-create-api-outlook.md#calendargettables) |Recupera calendários |
| [CalendarGetItems](connectors-create-api-outlook.md#calendargetitems) |Recupera itens de um calendário |
| [CalendarPostItem](connectors-create-api-outlook.md#calendarpostitem) |Cria um novo evento |
| [CalendarGetItem](connectors-create-api-outlook.md#calendargetitem) |Recupera um item específico de um calendário |
| [CalendarDeleteItem](connectors-create-api-outlook.md#calendardeleteitem) |Exclui um item de calendário |
| [CalendarPatchItem](connectors-create-api-outlook.md#calendarpatchitem) |Atualiza parcialmente um item de calendário |
| [ContactGetTables](connectors-create-api-outlook.md#contactgettables) |Recupera pastas de contatos |
| [ContactGetItems](connectors-create-api-outlook.md#contactgetitems) |Recupera os contatos de uma pasta de contatos |
| [ContactPostItem](connectors-create-api-outlook.md#contactpostitem) |Cria um novo contato |
| [ContactGetItem](connectors-create-api-outlook.md#contactgetitem) |Recupera um contato específico de uma pasta de contatos |
| [ContactDeleteItem](connectors-create-api-outlook.md#contactdeleteitem) |Exclui um contato |
| [ContactPatchItem](connectors-create-api-outlook.md#contactpatchitem) |Atualiza parcialmente um contato |

### <a name="outlookcom-triggers"></a>Gatilhos do Outlook.com
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| No evento que inicia em breve |Dispara um fluxo quando um evento de calendário futuro está iniciando |
| No novo email |Dispara um fluxo quando chega um novo email |
| Em novos itens |Disparado quando um novo item de calendário é criado |
| Em itens atualizados |Disparado quando um item de calendário é modificado |

## <a name="create-a-connection-to-outlookcom"></a>Criar uma conexão com o Outlook.com
Para criar Aplicativos Lógicos com o Outlook.com, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do Outlook.com |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to Outlook.com](../../includes/connectors-create-api-outlook.md)]
>
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.  
>
>

## <a name="reference-for-outlookcom"></a>Referência do Outlook.com
Aplica-se à versão: 1.0

## <a name="onupcomingevents"></a>OnUpcomingEvents
Em um evento que será iniciado em breve: dispara um fluxo quando um evento de calendário futuro estiver sendo iniciado

```GET: /Events/OnUpcomingEvents```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |sim |query |nenhum |Identificador exclusivo do calendário |
| lookAheadTimeInMinutes |inteiro |não |query |15 |Tempo (em minutos) para procurar eventos futuros |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="getemails"></a>GetEmails
Obter emails: recupera emails de uma pasta

```GET: /Mail```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |não |query |Caixa de Entrada |Caminho da pasta para recuperar emails (padrão: “Caixa de Entrada”) |
| top |inteiro |não |query |10 |Número de emails a serem recuperados (padrão: 10) |
| fetchOnlyUnread |booleano |não |query |verdadeiro |Recuperar somente emails não lidos? |
| includeAttachments |booleano |não |query |false |Se for definido como true, os anexos também serão recuperados junto com o email |
| searchQuery |string |não |query |nenhum |Consulta de pesquisa para filtrar emails |
| skip |inteiro |não |query |0 |Número de emails a serem ignorados (padrão: 0) |
| skipToken |string |não |query |nenhum |Ignorar o token para buscar nova página |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="sendemail"></a>SendEmail
Enviar email: envia um email

```POST: /Mail```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| emailMessage | |sim |corpo |nenhum |Email |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="deleteemail"></a>DeleteEmail
Excluir email: exclui um email por ID

```DELETE: /Mail/{messageId}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sim |path |nenhum |ID do email a ser excluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="markasread"></a>MarkAsRead
Marcar como lido: marca um email como lido

```POST: /Mail/MarkAsRead/{messageId}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sim |path |nenhum |ID do email a ser marcado como lido |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="replyto"></a>ReplyTo
Responder ao email: responde a um email

```POST: /Mail/ReplyTo/{messageId}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sim |path |nenhum |ID do email a ser respondido |
| comentário |string |sim |query |nenhum |Responder comentário |
| replyAll |booleano |não |query |false |Responder a todos os destinatários |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="getattachment"></a>GetAttachment
Obter anexo: recupera o anexo de email por ID

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| messageId |string |Sim |path |nenhum |ID do email |
| attachmentId |string |Sim |path |nenhum |Id do anexo a ser baixado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="onnewemail"></a>OnNewEmail
Em novos emails: dispara um fluxo quando chega um novo email

```GET: /Mail/OnNewEmail```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| folderPath |string |não |query |Caixa de Entrada |Pasta de email a ser recuperada (padrão: caixa de entrada) |
| para |string |não |query |nenhum |Endereços de email do destinatário |
| de |string |não |query |nenhum |Do endereço |
| importância |string |não |query |Normal |Importância do email (Alta, Normal, Baixa) (padrão: Normal) |
| fetchOnlyWithAttachment |booleano |não |query |false |Recuperar somente emails com anexo |
| includeAttachments |booleano |não |query |false |Incluir anexos |
| subjectFilter |string |não |query |nenhum |Cadeia de caracteres a ser procurada no assunto |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |Operação bem-sucedida |
| 202 |Aceita |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="sendmailwithoptions"></a>SendMailWithOptions
Enviar email com opções: envia um email com várias opções e aguarda uma resposta do destinatário com uma das opções

```POST: /mailwithoptions/$subscriptions```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| optionsEmailSubscription | |sim |corpo |nenhum |Solicitação de assinatura para email de opções |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 201 |Assinatura Criada |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="sendapprovalmail"></a>SendApprovalMail
Enviar email de aprovação: envia um email de aprovação e aguarda uma resposta do destinatário

```POST: /approvalmail/$subscriptions```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| approvalEmailSubscription | |sim |corpo |nenhum |Solicitação de assinatura para email de aprovação |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 201 |Assinatura Criada |
| 400 |BadRequest |
| 401 |Não Autorizado |
| 403 |Proibido |
| 500 |Erro interno do servidor |
| padrão |Falha na Operação. |

## <a name="calendargettables"></a>CalendarGetTables
Obter calendários: recupera calendários

```GET: /datasets/calendars/tables```

Não há parâmetros para essa chamada

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendargetitems"></a>CalendarGetItems
Obter eventos: recupera os itens de um calendário

```GET: /datasets/calendars/tables/{table}/items```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo do calendário a ser recuperado |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendarpostitem"></a>CalendarPostItem
Criar evento: cria um novo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| item | |Sim |corpo |nenhum |Item de calendário a ser criado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendargetitem"></a>CalendarGetItem
Obter evento: recupera um item específico de um calendário

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| ID |string |Sim |path |nenhum |Identificador exclusivo do item de calendário a ser recuperado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendardeleteitem"></a>CalendarDeleteItem
Excluir eventos: exclui um item de calendário

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| ID |string |Sim |path |nenhum |Identificador exclusivo do item de calendário a ser excluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendarpatchitem"></a>CalendarPatchItem
Atualizar evento: atualiza parcialmente um item de calendário

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| ID |string |Sim |path |nenhum |Identificador exclusivo do item de calendário a ser atualizado |
| item | |Sim |corpo |nenhum |Item de calendário a ser atualizado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendargetonnewitems"></a>CalendarGetOnNewItems
Em novos itens: disparado quando um novo item de calendário é criado

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="calendargetonupdateditems"></a>CalendarGetOnUpdatedItems
Em itens atualizados: disparado quando um item de calendário é modificado

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de um calendário |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactgettables"></a>ContactGetTables
Obter pastas de contatos: recupera as pastas de contatos

```GET: /datasets/contacts/tables```

Não há parâmetros para essa chamada

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactgetitems"></a>ContactGetItems
Obter contatos: recupera os contatos de uma pasta de contatos

```GET: /datasets/contacts/tables/{table}/items```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo da pasta Contatos a ser recuperada |
| $filter |string |não |query |Nenhum |Uma consulta de filtro ODATA para restringir o número de entradas |
| $orderby |string |não |query |Nenhum |Uma consulta orderBy do ODATA para especificar a ordem das entradas |
| $skip |inteiro |não |query |Nenhum |Número de entradas a serem ignoradas (padrão = 0) |
| $top |inteiro |não |query |Nenhum |Número máximo de entradas a serem recuperadas (padrão = 256) |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactpostitem"></a>ContactPostItem
Criar contato: cria um novo contato

```POST: /datasets/contacts/tables/{table}/items```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de uma pasta de contatos |
| item | |Sim |corpo |nenhum |Contato a ser criado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactgetitem"></a>ContactGetItem
Obter contato: recupera um contato específico de uma pasta de contatos

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de uma pasta de contatos |
| ID |string |Sim |path |nenhum |Identificador exclusivo de um contato a ser recuperado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactdeleteitem"></a>ContactDeleteItem
Excluir contato: exclui um contato

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de uma pasta de contatos |
| ID |string |Sim |path |nenhum |Identificador exclusivo do contato a excluir |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na Operação. |

## <a name="contactpatchitem"></a>ContactPatchItem
Atualizar contato: atualiza parcialmente um contato

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| tabela |string |Sim |path |nenhum |Identificador exclusivo de uma pasta de contatos |
| ID |string |Sim |path |nenhum |Identificador exclusivo do contato a atualizar |
| item | |Sim |corpo |nenhum |Item de contato a ser atualizado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| padrão |Falha na operação. |

## <a name="object-definitions"></a>Definições de objeto
### <a name="triggerbatchresponseidictionarystringobject"></a>TriggerBatchResponse[IDictionary[String,Object]]
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="object"></a>Objeto
### <a name="sendmessage"></a>SendMessage
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Anexos |array |Não |
| Da |string |Não |
| Co |string |Não |
| Cco |string |Não |
| Subject |string |Sim |
| Corpo |string |Sim |
| Importância |string |Não |
| IsHtml |Booliano |Não |
| Para |string |Sim |

### <a name="sendattachment"></a>SendAttachment
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| @odata.type |string |Não |
| Nome |string |Sim |
| ContentBytes |string |Sim |

### <a name="receivemessage"></a>ReceiveMessage
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| IsRead |Booliano |Não |
| HasAttachment |Booliano |Não |
| DateTimeReceived |string |Não |
| Anexos |array |Não |
| Da |string |Não |
| Co |string |Não |
| Cco |string |Não |
| Subject |string |Sim |
| Corpo |string |Sim |
| Importância |string |Não |
| IsHtml |Booliano |Não |
| Para |string |Sim |

### <a name="receiveattachment"></a>ReceiveAttachment
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Sim |
| ContentType |string |Sim |
| @odata.type |string |Não |
| Nome |string |Sim |
| ContentBytes |string |Sim |

### <a name="digestmessage"></a>DigestMessage
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Assunto |string |Sim |
| Corpo |string |Não |
| Importância |string |Não |
| Digest |array |Sim |
| Anexos |array |Não |
| Para |string |Sim |

### <a name="triggerbatchresponsereceivemessage"></a>TriggerBatchResponse[ReceiveMessage]
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="datasetsmetadata"></a>DataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| tabular |não definido |Não |
| blob |não definido |Não |

### <a name="tabulardatasetsmetadata"></a>TabularDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |Não |
| displayName |string |Não |
| urlEncoding |string |Não |
| tableDisplayName |string |Não |
| tablePluralName |string |Não |

### <a name="blobdatasetsmetadata"></a>BlobDataSetsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| fonte |string |Não |
| displayName |string |Não |
| urlEncoding |string |Não |

### <a name="tablemetadata"></a>TableMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Não |
| título |string |Não |
| x-ms-permission |string |Não |
| x-ms-capabilities |não definido |Não |
| schema |não definido |Não |

### <a name="tablecapabilitiesmetadata"></a>TableCapabilitiesMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| sortRestrictions |não definido |Não |
| filterRestrictions |não definido |Não |
| filterFunctions |array |Não |

### <a name="tablesortrestrictionsmetadata"></a>TableSortRestrictionsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| sortable |Booliano |Não |
| unsortableProperties |array |Não |
| ascendingOnlyProperties |array |Não |

### <a name="tablefilterrestrictionsmetadata"></a>TableFilterRestrictionsMetadata
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| filterable |Booliano |Não |
| nonFilterableProperties |array |Não |
| requiredProperties |array |Não |

### <a name="optionsemailsubscription"></a>OptionsEmailSubscription
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| NotificationUrl |string |Não |
| Mensagem |não definido |Não |

### <a name="messagewithoptions"></a>MessageWithOptions
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Assunto |string |Sim |
| Opções |string |Sim |
| Corpo |string |Não |
| Importância |string |Não |
| Anexos |array |Não |
| Para |string |Sim |

### <a name="subscriptionresponse"></a>SubscriptionResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| recurso |string |Não |
| notificationType |string |Não |
| notificationUrl |string |Não |

### <a name="approvalemailsubscription"></a>ApprovalEmailSubscription
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| NotificationUrl |string |Não |
| Mensagem |não definido |Não |

### <a name="approvalmessage"></a>ApprovalMessage
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Assunto |string |Sim |
| Opções |string |Sim |
| Corpo |string |Não |
| Importância |string |Não |
| Anexos |array |Não |
| Para |string |Sim |

### <a name="approvalemailresponse"></a>ApprovalEmailResponse
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| SelectedOption |string |Não |

### <a name="tableslist"></a>TablesList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="table"></a>Tabela
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Não |
| DisplayName |string |Não |

### <a name="item"></a>Item
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ItemInternalId |string |Não |

### <a name="calendaritemslist"></a>CalendarItemsList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="calendaritem"></a>CalendarItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ItemInternalId |string |Não |

### <a name="contactitemslist"></a>ContactItemsList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="contactitem"></a>ContactItem
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ItemInternalId |string |Não |

### <a name="datasetslist"></a>DataSetsList
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| value |array |Não |

### <a name="dataset"></a>DataSet
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Não |
| DisplayName |string |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)



<!--HONumber=Jan17_HO3-->


