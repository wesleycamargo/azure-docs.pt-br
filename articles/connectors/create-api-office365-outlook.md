<properties
pageTitle="Adicionar a API do Outlook do Office 365 aos seus Aplicativos Lógicos | Microsoft Azure"
description="Visão geral da API do Outlook do Office 365 com os parâmetros da API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="dwrede"	
editor="" tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/22/2016"
ms.author="deonhe"/>

# Introdução à API do Outlook do Office 365

A API do Office 365 permite a interação com o Office 365. Por exemplo: criação, edição e atualização de itens de calendário e contatos.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [API do Office 365](../app-service-logic/app-service-logic-connector-Office365.md).

Com o Office 365, você pode:

* Usá-lo para criar aplicativos lógicos
* Usá-lo para criar power apps

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Vamos conversar sobre gatilhos e ações

A API do Office365 pode ser usada como uma ação. Ela tem gatilhos. Todas as APIs dão suporte a dados nos formatos JSON e XML.

 A API do Office 365 possui as seguintes ações e/ou gatilhos disponíveis:

### Ações do Office365
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|GetEmails|Recupera emails de uma pasta|
|SendEmail|Envia uma mensagem de email|
|DeleteEmail|Exclui uma mensagem de email por ID|
|MarkAsRead|Marca uma mensagem de email como lida|
|ReplyTo|Responde a uma mensagem de email|
|GetAttachment|Recupera o anexo da mensagem por ID|
|SendMailWithOptions|Envia um email com várias opções e aguarda o destinatário responder com uma das opções.|
|SendApprovalMail|Envia um email de aprovação e aguarda uma resposta do destinatário de Para.|
|CalendarGetTables|Recupera calendários|
|CalendarGetItems|Recupera itens de um calendário|
|CalendarPostItem|Cria um novo evento.|
|CalendarGetItem|Recupera um item específico de um calendário|
|CalendarDeleteItem|Exclui um item de calendário|
|CalendarPatchItem|Atualiza parcialmente um item de calendário|
|ContactGetTables|Recupera pastas de contatos|
|ContactGetItems|Recupera os contatos de uma pasta de contatos|
|ContactPostItem|Cria um novo contato|
|ContactGetItem|Recupera um contato específico de uma pasta de contatos|
|ContactDeleteItem|Exclui um contato|
|ContactPatchItem|Atualiza parcialmente um contato|
### Gatilhos do Office365
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|OnUpcomingEvents|Dispara um fluxo quando um evento de calendário futuro está iniciando|
|OnNewEmail|Dispara um fluxo quando chega um novo email|
|CalendarGetOnNewItems|Disparado quando um novo item de calendário é criado|
|CalendarGetOnUpdatedItems|Disparado quando um item de calendário é modificado|


## Criar uma conexão com o Office 365
Para usar a API do Office 365, crie primeiro uma **conexão** e forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Forneça as credenciais do Office365|


>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Office365
#### Esta documentação destina-se à versão: 1.0


### No evento que inicia em breve 


 Dispara um fluxo quando um evento de calendário futuro está iniciando ```GET: /Events/OnUpcomingEvents```



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
------



### Obter emails 


 Recupera emails de uma pasta ```GET: /Mail```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|não|query|Caixa de Entrada|Caminho da pasta para recuperar mensagens (padrão: 'Caixa de Entrada')|
|top|inteiro|não|query|10|Número de emails a serem recuperados (padrão: 10)|
|fetchOnlyUnread|booleano|não|query|verdadeiro|Recuperar somente mensagens não lidas?|
|includeAttachments|booleano|não|query|false|Se for definido como true, os anexos também serão recuperados junto com a mensagem de email.|
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
------



### Enviar Email 


 Envia uma mensagem de email ```POST: /Mail```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|emailMessage| |sim|corpo|nenhum|Instância de mensagem de email|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Excluir email 


 Exclui uma mensagem de email por id ```DELETE: /Mail/{messageId}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|Id da mensagem a ser excluída.|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Marcar como lido 


 Marca uma mensagem de email como lida ```POST: /Mail/MarkAsRead/{messageId}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|ID da mensagem a ser marcada como lida|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Responder mensagem 


 Responde a uma mensagem de email ```POST: /Mail/ReplyTo/{messageId}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|Id da mensagem a ser respondida|
|comentário|string|sim|query|nenhum|Responder comentário|
|replyAll|booleano|não|query|false|Responder a todos os destinatários|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Obter anexo 


 Recupera o anexo da mensagem por id ```GET: /Mail/{messageId}/Attachments/{attachmentId}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|messageId|string|sim|path|nenhum|Id da mensagem|
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
------



### No novo email 


 Dispara um fluxo quando chega um novo email ```GET: /Mail/OnNewEmail```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|folderPath|string|não|query|Caixa de Entrada|Pasta de email a ser recuperada (padrão: caixa de entrada)|
|para|string|não|query|nenhum|Endereços de email do destinatário|
|de|string|não|query|nenhum|Do endereço|
|importância|string|não|query|Normal|Importância do email (Alta, Normal, Baixa) (padrão: Normal)|
|fetchOnlyWithAttachment|booleano|não|query|false|Recuperar somente emails com anexo|
|includeAttachments|booleano|não|query|false|Incluir anexos|
|subjectFilter|string|não|query|nenhum|Cadeia de caracteres a ser procurada no assunto.|


#### Resposta

|Nome|Descrição|
|---|---|
|200|Operação bem-sucedida.|
|202|Aceita|
|400|BadRequest|
|401|Não Autorizado|
|403|Proibido|
|500|Erro interno do servidor|
|padrão|Falha na Operação.|
------



### Enviar email com opções 


 Envie um email com várias opções e aguarde o destinatário responder com uma das opções. ```POST: /mailwithoptions/$subscriptions```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|optionsEmailSubscription| |sim|corpo|nenhum|Email para solicitar opções de assinatura|


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
------



### Enviar email de aprovação 


 Envia um email de aprovação e aguarda uma resposta do destinatário de Para. ```POST: /approvalmail/$subscriptions```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|approvalEmailSubscription| |sim|corpo|nenhum|Solicitação de assinatura de email de aprovação.|


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
------



### Obter calendários 


 Recupera calendários ```GET: /datasets/calendars/tables```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obter eventos 


 Recupera os itens de um calendário ```GET: /datasets/calendars/tables/{table}/items```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo do calendário a ser recuperado|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Criar evento 


 Cria um novo evento ```POST: /datasets/calendars/tables/{table}/items```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|item| |sim|corpo|nenhum|Item de calendário a ser criado|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obter evento 


 Recupera um item específico de um calendário ```GET: /datasets/calendars/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser recuperado|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Excluir evento 


 Exclui um item de calendário ```DELETE: /datasets/calendars/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário.|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser excluído|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Atualizar evento 


 Atualiza um item de calendário parcialmente ```PATCH: /datasets/calendars/tables/{table}/items/{id}```



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
------



### Em novos itens 


 Disparado quando um novo item de calendário é criado ```GET: /datasets/calendars/tables/{table}/onnewitems```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Em itens atualizados 


 Disparado quando um item de calendário é modificado ```GET: /datasets/calendars/tables/{table}/onupdateditems```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obter pastas de contatos 


 Recupera pastas de contatos ```GET: /datasets/contacts/tables```

Não há parâmetros para essa chamada
#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obter contatos 


 Recupera os contatos de uma pasta de contatos ```GET: /datasets/contacts/tables/{table}/items```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo da pasta Contatos a ser recuperada|
|$skip|inteiro|não|query|nenhum|Número de entradas a serem ignoradas (padrão = 0)|
|$top|inteiro|não|query|nenhum|Número máximo de entradas a serem recuperadas (padrão = 256)|
|$filter|string|não|query|nenhum|Uma consulta de filtro ODATA para restringir o número de entradas|
|$orderby|string|não|query|nenhum|Uma consulta orderBy do ODATA para especificar a ordem das entradas|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Criar contato 


 Cria um novo contato ```POST: /datasets/contacts/tables/{table}/items```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|item| |sim|corpo|nenhum|Contato a ser criado|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obter contato 


 Recupera um contato específico de uma pasta de contatos ```GET: /datasets/contacts/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos|
|ID|string|sim|path|nenhum|Identificador exclusivo de um contato a ser recuperado|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Excluir contato 


 Exclui um contato ```DELETE: /datasets/contacts/tables/{table}/items/{id}```



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos.|
|ID|string|sim|path|nenhum|Identificador exclusivo do contato a excluir|


#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Atualizar contato 


 Atualiza um contato parcialmente ```PATCH: /datasets/contacts/tables/{table}/items/{id}```



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
------



## Definições de objeto: 

 **TriggerBatchResponse[IDictionary[String,Object]]**:

Propriedades obrigatórias para TriggerBatchResponse[IDictionary[String,Object]]:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **SendMessage**: enviar mensagem de email

Propriedades obrigatórias para SendMessage:

Assunto, Corpo, Para

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Anexos|array|
|Da|string|
|Co|string|
|Cco|string|
|Assunto|string|
|Corpo|string|
|Importância|string|
|IsHtml|booleano|
|Para|string|



 **SendAttachment**: anexo

Propriedades obrigatórias para SendAttachment:

Nome, ContentBytes

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|@odata.type|string|
|Nome|string|
|ContentBytes|string|



 **ReceiveMessage**: mensagem de email

Propriedades obrigatórias para ReceiveMessage:

Assunto, Corpo, Para

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|IsRead|booleano|
|HasAttachment|booleano|
|DateTimeReceived|string|
|Anexos|array|
|Da|string|
|Co|string|
|Cco|string|
|Assunto|string|
|Corpo|string|
|Importância|string|
|IsHtml|booleano|
|Para|string|



 **ReceiveAttachment**: receber anexo

Propriedades obrigatórias para ReceiveAttachment:

ID, ContentType, Nome, ContentBytes

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|ContentType|string|
|@odata.type|string|
|Nome|string|
|ContentBytes|string|



 **DigestMessage**: enviar mensagem de email

Propriedades obrigatórias para DigestMessage:

Assunto, Digest, Para

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Assunto|string|
|Corpo|string|
|Importância|string|
|Digest|array|
|Anexos|array|
|Para|string|



 **TriggerBatchResponse[ReceiveMessage]**:

Propriedades obrigatórias para TriggerBatchResponse[ReceiveMessage]:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **DataSetsMetadata**:

Propriedades obrigatórias para DataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|tabular|não definido|
|blob|não definido|



 **TabularDataSetsMetadata**:

Propriedades obrigatórias para TabularDataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|fonte|string|
|displayName|string|
|urlEncoding|string|
|tableDisplayName|string|
|tablePluralName|string|



 **BlobDataSetsMetadata**:

Propriedades obrigatórias para BlobDataSetsMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|fonte|string|
|displayName|string|
|urlEncoding|string|



 **TableMetadata**:

Propriedades obrigatórias para TableMetadata:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|name|string|
|título|string|
|x-ms-permission|string|
|schema|não definido|



 **OptionsEmailSubscription**: modelo para assinatura de emails com opção

Propriedades obrigatórias para OptionsEmailSubscription:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|NotificationUrl|string|
|Mensagem|não definido|



 **MessageWithOptions**: mensagem de email com opções para o usuário. Essa é a mensagem esperada como parte da entrada do usuário

Propriedades obrigatórias para MessageWithOptions:

Assunto, Opções, Para

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Assunto|string|
|Opções|string|
|Corpo|string|
|Importância|string|
|Anexos|array|
|Para|string|



 **SubscriptionResponse**: modelo de assinatura de email de aprovação

Propriedades obrigatórias para SubscriptionResponse:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|recurso|string|
|notificationType|string|
|notificationUrl|string|



 **ApprovalEmailSubscription**: modelo de assinatura de email de aprovação

Propriedades obrigatórias para ApprovalEmailSubscription:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|NotificationUrl|string|
|Mensagem|não definido|



 **ApprovalMessage**: mensagem de email de aprovação. Essa é a mensagem esperada como parte da entrada do usuário

Propriedades obrigatórias para ApprovalMessage:

Assunto, Opções, Para

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Assunto|string|
|Opções|string|
|Corpo|string|
|Importância|string|
|Anexos|array|
|Para|string|



 **ApprovalEmailResponse**: resposta de email de aprovação

Propriedades obrigatórias para ApprovalEmailResponse:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|SelectedOption|string|



 **TablesList**:

Propriedades obrigatórias para TablesList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **Tabela**:

Propriedades necessárias para Table:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Nome|string|
|DisplayName|string|



 **Item**:

Propriedades obrigatórias para Item:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ItemInternalId|string|



 **CalendarItemsList**: a lista de itens de calendário

Propriedades obrigatórias para CalendarItemsList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **CalendarItem**: representa um item da tabela de calendário

Propriedades obrigatórias para CalendarItem:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ItemInternalId|string|



 **ContactItemsList**: a lista de itens de contato

Propriedades obrigatórias para ContactItemsList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **ContactItem**: representa um item da tabela de contatos

Propriedades obrigatórias para ContactItem:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ItemInternalId|string|



 **DataSetsList**:

Propriedades obrigatórias para DataSetsList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|value|array|



 **DataSet**:

Propriedades obrigatórias para DataSet:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Nome|string|
|DisplayName|string|


## Próximas etapas
Depois de adicionar a API do Office 365 ao PowerApps Enterprise, [conceda permissões aos usuários](../power-apps/powerapps-manage-api-connection-user-access.md) para que eles usem a API em seus aplicativos.

[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->