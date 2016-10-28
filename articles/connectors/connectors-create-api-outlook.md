<properties
pageTitle="Outlook.com | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. É possível executar várias ações, como enviar emails, agendar reuniões, adicionar contatos, etc."
services="logic-apps"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="08/18/2016"
ms.author="deonhe"/>

# Introdução ao conector do Outlook.com

O conector do Outlook.com permite que você gerencie seus emails, calendários e contatos. É possível executar várias ações, como enviar emails, agendar reuniões, adicionar contatos, etc.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do Outlook.com pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Outlook.com contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do Outlook.com
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[GetEmails](connectors-create-api-outlook.md#GetEmails)|Recupera emails de uma pasta|
|[SendEmail](connectors-create-api-outlook.md#SendEmail)|Envia um email|
|[DeleteEmail](connectors-create-api-outlook.md#DeleteEmail)|Exclui um email por ID|
|[MarkAsRead](connectors-create-api-outlook.md#MarkAsRead)|Marca um email como lido|
|[ReplyTo](connectors-create-api-outlook.md#ReplyTo)|Responde a um email|
|[GetAttachment](connectors-create-api-outlook.md#GetAttachment)|Recupera o anexo de email por ID|
|[SendMailWithOptions](connectors-create-api-outlook.md#SendMailWithOptions)|Envia um email com várias opções e aguarda uma resposta do destinatário com uma das opções|
|[SendApprovalMail](connectors-create-api-outlook.md#SendApprovalMail)|Envia um email de aprovação e aguarda uma resposta do destinatário|
|[CalendarGetTables](connectors-create-api-outlook.md#CalendarGetTables)|Recupera calendários|
|[CalendarGetItems](connectors-create-api-outlook.md#CalendarGetItems)|Recupera itens de um calendário|
|[CalendarPostItem](connectors-create-api-outlook.md#CalendarPostItem)|Cria um novo evento|
|[CalendarGetItem](connectors-create-api-outlook.md#CalendarGetItem)|Recupera um item específico de um calendário|
|[CalendarDeleteItem](connectors-create-api-outlook.md#CalendarDeleteItem)|Exclui um item de calendário|
|[CalendarPatchItem](connectors-create-api-outlook.md#CalendarPatchItem)|Atualiza parcialmente um item de calendário|
|[ContactGetTables](connectors-create-api-outlook.md#ContactGetTables)|Recupera pastas de contatos|
|[ContactGetItems](connectors-create-api-outlook.md#ContactGetItems)|Recupera os contatos de uma pasta de contatos|
|[ContactPostItem](connectors-create-api-outlook.md#ContactPostItem)|Cria um novo contato|
|[ContactGetItem](connectors-create-api-outlook.md#ContactGetItem)|Recupera um contato específico de uma pasta de contatos|
|[ContactDeleteItem](connectors-create-api-outlook.md#ContactDeleteItem)|Exclui um contato|
|[ContactPatchItem](connectors-create-api-outlook.md#ContactPatchItem)|Atualiza parcialmente um contato|
### Gatilhos do Outlook.com
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|No evento que inicia em breve|Dispara um fluxo quando um evento de calendário futuro está iniciando|
|No novo email|Dispara um fluxo quando chega um novo email|
|Em novos itens|Disparado quando um novo item de calendário é criado|
|Em itens atualizados|Disparado quando um item de calendário é modificado|


## Criar uma conexão com o Outlook.com
Para criar Aplicativos Lógicos com o Outlook.com, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|Token|Sim|Fornecer as credenciais do Outlook.com|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.INCLUDE [Etapas para criar uma conexão com o Outlook.com](../../includes/connectors-create-api-outlook.md)]

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do Outlook.com
Aplica-se à versão: 1.0

## OnUpcomingEvents
Em um evento que será iniciado em breve: dispara um fluxo quando um evento de calendário futuro estiver sendo iniciado

```GET: /Events/OnUpcomingEvents```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|query|nenhum|Identificador exclusivo do calendário|
|lookAheadTimeInMinutes|inteiro|não|query|15|Tempo (em minutos) para procurar eventos futuros|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|202|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## GetEmails
Obter emails: recupera emails de uma pasta

```GET: /Mail```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|não|query|Caixa de Entrada|Caminho da pasta para recuperar emails (padrão: “Caixa de Entrada”)|
|top|inteiro|não|query|10|Número de emails a serem recuperados (padrão: 10)|
|fetchOnlyUnread|Booliano|não|query|verdadeiro|Recuperar somente emails não lidos?|
|includeAttachments|Booliano|não|query|false|Se for definido como true, os anexos também serão recuperados junto com o email|
|searchQuery|string|não|query|nenhum|Consulta de pesquisa para filtrar emails|
|skip|inteiro|não|query|0|Número de emails a serem ignorados (padrão: 0)|
|skipToken|string|não|query|nenhum|Ignorar o token para buscar nova página|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## SendEmail
Enviar email: envia um email

```POST: /Mail```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|emailMessage| |sim|corpo|nenhum|Email|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## DeleteEmail
Excluir email: exclui um email por ID

```DELETE: /Mail/{messageId}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|ID do email a ser excluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## MarkAsRead
Marcar como lido: marca um email como lido

```POST: /Mail/MarkAsRead/{messageId}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|ID do email a ser marcado como lido|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## ReplyTo
Responder ao email: responde a um email

```POST: /Mail/ReplyTo/{messageId}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|ID do email a ser respondido|
|comentário|string|sim|query|nenhum|Responder comentário|
|replyAll|Booliano|não|query|false|Responder a todos os destinatários|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## GetAttachment
Obter anexo: recupera o anexo de email por ID

```GET: /Mail/{messageId}/Attachments/{attachmentId}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|ID do email|
|attachmentId|string|sim|path|nenhum|Id do anexo a ser baixado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## OnNewEmail
Em novos emails: dispara um fluxo quando chega um novo email

```GET: /Mail/OnNewEmail```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|não|query|Caixa de Entrada|Pasta de email a ser recuperada (padrão: caixa de entrada)|
|para|string|não|query|nenhum|Endereços de email do destinatário|
|de|string|não|query|nenhum|Do endereço|
|importância|string|não|query|Normal|Importância do email (Alta, Normal, Baixa) (padrão: Normal)|
|fetchOnlyWithAttachment|Booliano|não|query|false|Recuperar somente emails com anexo|
|includeAttachments|Booliano|não|query|false|Incluir anexos|
|subjectFilter|string|não|query|nenhum|Cadeia de caracteres a ser procurada no assunto|

#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|202|Aceita|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## SendMailWithOptions
Enviar email com opções: envia um email com várias opções e aguarda uma resposta do destinatário com uma das opções

```POST: /mailwithoptions/$subscriptions```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |sim|corpo|nenhum|Solicitação de assinatura para email de opções|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|201|Assinatura Criada|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## SendApprovalMail
Enviar email de aprovação: envia um email de aprovação e aguarda uma resposta do destinatário

```POST: /approvalmail/$subscriptions```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |sim|corpo|nenhum|Solicitação de assinatura para email de aprovação|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|201|Assinatura Criada|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|


## CalendarGetTables
Obter calendários: recupera calendários

```GET: /datasets/calendars/tables```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarGetItems
Obter eventos: recupera os itens de um calendário

```GET: /datasets/calendars/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo do calendário a ser recuperado|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarPostItem
Criar evento: cria um novo evento

```POST: /datasets/calendars/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|item| |sim|corpo|nenhum|Item de calendário a ser criado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarGetItem
Obter evento: recupera um item específico de um calendário

```GET: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser recuperado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarDeleteItem
Excluir eventos: exclui um item de calendário

```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser excluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarPatchItem
Atualizar evento: atualiza parcialmente um item de calendário

```PATCH: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser atualizado|
|item| |sim|corpo|nenhum|Item de calendário a ser atualizado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarGetOnNewItems
Em novos itens: disparado quando um novo item de calendário é criado

```GET: /datasets/calendars/tables/{table}/onnewitems```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## CalendarGetOnUpdatedItems
Em itens atualizados: disparado quando um item de calendário é modificado

```GET: /datasets/calendars/tables/{table}/onupdateditems```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactGetTables
Obter pastas de contatos: recupera as pastas de contatos

```GET: /datasets/contacts/tables```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactGetItems
Obter contatos: recupera os contatos de uma pasta de contatos

```GET: /datasets/contacts/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo da pasta Contatos a ser recuperada|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactPostItem
Criar contato: cria um novo contato

```POST: /datasets/contacts/tables/{table}/items```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|item| |sim|corpo|nenhum|Contato a ser criado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactGetItem
Obter contato: recupera um contato específico de uma pasta de contatos

```GET: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|string|sim|path|nenhum|Identificador exclusivo de um contato a ser recuperado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactDeleteItem
Excluir contato: exclui um contato

```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|string|sim|path|nenhum|Identificador exclusivo do contato a excluir|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## ContactPatchItem
Atualizar contato: atualiza parcialmente um contato

```PATCH: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|string|sim|path|nenhum|Identificador exclusivo do contato a atualizar|
|item| |sim|corpo|nenhum|Item de contato a ser atualizado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


## Definições de objeto 

### TriggerBatchResponse[IDictionary[String,Object]]


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### Objeto


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|



### SendMessage


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Anexos|array|Não |
|Da|string|Não |
|Co|string|Não |
|Cco|string|Não |
|Subject|string|Sim |
|Corpo|string|Sim |
|Importância|string|Não |
|IsHtml|Booliano|Não |
|Para|string|Sim |



### SendAttachment


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|@odata.type|string|Não |
|Nome|string|Sim |
|ContentBytes|string|Sim |



### ReceiveMessage


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|IsRead|Booliano|Não |
|HasAttachment|Booliano|Não |
|DateTimeReceived|string|Não |
|Anexos|array|Não |
|Da|string|Não |
|Co|string|Não |
|Cco|string|Não |
|Subject|string|Sim |
|Corpo|string|Sim |
|Importância|string|Não |
|IsHtml|Booliano|Não |
|Para|string|Sim |



### ReceiveAttachment


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Sim |
|ContentType|string|Sim |
|@odata.type|string|Não |
|Nome|string|Sim |
|ContentBytes|string|Sim |



### DigestMessage


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Subject|string|Sim |
|Corpo|string|Não |
|Importância|string|Não |
|Digest|array|Sim |
|Anexos|array|Não |
|Para|string|Sim |



### TriggerBatchResponse[ReceiveMessage]


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### DataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|tabular|não definido|Não |
|blob|não definido|Não |



### TabularDataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|Não |
|displayName|string|Não |
|urlEncoding|string|Não |
|tableDisplayName|string|Não |
|tablePluralName|string|Não |



### BlobDataSetsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|fonte|string|Não |
|displayName|string|Não |
|urlEncoding|string|Não |



### TableMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|name|string|Não |
|título|string|Não |
|x-ms-permission|string|Não |
|x-ms-capabilities|não definido|Não |
|schema|não definido|Não |



### TableCapabilitiesMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|sortRestrictions|não definido|Não |
|filterRestrictions|não definido|Não |
|filterFunctions|array|Não |



### TableSortRestrictionsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|sortable|Booliano|Não |
|unsortableProperties|array|Não |
|ascendingOnlyProperties|array|Não |



### TableFilterRestrictionsMetadata


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|filterable|Booliano|Não |
|nonFilterableProperties|array|Não |
|requiredProperties|array|Não |



### OptionsEmailSubscription


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|NotificationUrl|string|Não |
|Mensagem|não definido|Não |



### MessageWithOptions


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Subject|string|Sim |
|Opções|string|Sim |
|Corpo|string|Não |
|Importância|string|Não |
|Anexos|array|Não |
|Para|string|Sim |



### SubscriptionResponse


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|recurso|string|Não |
|notificationType|string|Não |
|notificationUrl|string|Não |



### ApprovalEmailSubscription


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|NotificationUrl|string|Não |
|Mensagem|não definido|Não |



### ApprovalMessage


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Subject|string|Sim |
|Opções|string|Sim |
|Corpo|string|Não |
|Importância|string|Não |
|Anexos|array|Não |
|Para|string|Sim |



### ApprovalEmailResponse


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|SelectedOption|string|Não |



### TablesList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### Tabela


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Não |
|DisplayName|string|Não |



### Item


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ItemInternalId|string|Não |



### CalendarItemsList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### CalendarItem


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ItemInternalId|string|Não |



### ContactItemsList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### ContactItem


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ItemInternalId|string|Não |



### DataSetsList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### DataSet


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Nome|string|Não |
|DisplayName|string|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->