<properties
	pageTitle="Adicionar a API do Outlook do Office 365 ao PowerApps Enterprise ou aos Aplicativos Lógicos | Microsoft Azure"
	description="Visão geral da API do Outlook do Office 365 com os parâmetros da API REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="03/03/2016"
ms.author="mandia"/>

# Introdução à API do Outlook do Office 365 

Conecte-se ao Outlook do Office 365 para receber emails, responder a um email, atualizar seu calendário e seus contatos e muito mais. A API do Outlook do Office 365 pode ser usada de:

- Aplicativos lógicos 
- PowerApps

> [AZURE.SELECTOR]
- [Aplicativos lógicos](../articles/connectors/create-api-office365-outlook.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-outlook.md)

&nbsp;

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [API do Office 365](../app-service-logic/app-service-logic-connector-office365.md).

Com o Outlook do Office 365, você pode:

- Criar seu fluxo de negócios com base nos dados obtidos do Outlook do Office 365. 
- Usar um gatilho quando houver um novo email, ao criar um novo contato e muito mais.
- Usar ações que respondam a um email, criar um novo evento de calendário e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando houver um novo objeto no Salesforce, você poderá obter o objeto e atualizar seus contatos do Outlook do Office 365. 
- Adicione a API do Outlook do Office 365 ao PowerApps Enterprise. Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação aos aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

A API do Outlook do Office 365 disponibiliza os gatilhos e as ações a seguir.

| Gatilhos | Ações|
| --- | --- |
|<ul><li>Em evento iniciando em breve</li><li>Em novo email</li><li>Em novos itens</li><li>Em itens atualizados</li></ul>| <ul><li>Criar contato</li><li>Criar evento</li><li>Enviar email de aprovação</li><li>Enviar email</li><li>Excluir contato</li><li>Excluir email</li><li>Excluir evento</li><li>Obter anexo</li><li>Obter calendários</li><li>Obter contato</li><li>Obter pastas de contatos</li><li>Obter contatos</li><li>Receber emails</li><li>Obter evento</li><li>Obter eventos</li><li>Marcar como lido</li><li>Em evento iniciando em breve</li><li>Em novo email</li><li>Em novos itens</li><li>Em itens atualizados</li><li>Responder à mensagem</li><li>Enviar email com opções</li><li>Atualizar contato</li><li>Atualizar evento</li></ul> |

Todas as APIs dão suporte a dados nos formatos JSON e XML.


## Criar uma conexão com o Office 365

Quando você adicionar essa API aos seus aplicativos lógicos, é necessário entrar em sua conta do Outlook do Office 365 e permitir que os aplicativos lógicos se conectem à sua conta.

1. Entre em sua conta do Outlook do Office 365.
2. Permita que seus aplicativos lógicos se conectem e usem sua conta do Office 365. 

Depois de criar a conexão, insira as propriedades do Outlook do Office 365, como o caminho da pasta de caixa de entrada ou a mensagem de email. A **referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] É possível usar essa mesma conexão do Outlook do Office 365 em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.


### No evento que inicia em breve 
Dispara um fluxo quando um evento de calendário futuro estiver iniciando.
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


### Obter emails 
Recupera emails de uma pasta.
```GET: /Mail```

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


### Enviar Email 
Envia uma mensagem de email.
```POST: /Mail```

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


### Excluir email 
Exclui uma mensagem de email por id.
```DELETE: /Mail/{messageId}```

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


### Marcar como lido 
Marca uma mensagem de email como lida.
```POST: /Mail/MarkAsRead/{messageId}```

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


### Responder mensagem 
Responde a uma mensagem de email.
```POST: /Mail/ReplyTo/{messageId}```

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


### Obter anexo 
Recupera o anexo da mensagem por id.
```GET: /Mail/{messageId}/Attachments/{attachmentId}```

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


### No novo email 
Dispara um fluxo quando chega um novo email.
```GET: /Mail/OnNewEmail```

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


### Enviar email com opções 
Envie um email com várias opções e aguarde o destinatário responder com uma das opções.
```POST: /mailwithoptions/$subscriptions```

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


### Enviar email de aprovação 
Envie um email de aprovação e aguarde uma resposta do destinatário de Para.
```POST: /approvalmail/$subscriptions```

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




### Obter calendários 
Recupera calendários.
```GET: /datasets/calendars/tables```

Não há parâmetros para esta chamada.

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|




### Obter eventos 
Recupera os itens de um calendário.
```GET: /datasets/calendars/tables/{table}/items```

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


### Criar evento 
Cria um novo evento.
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


### Obter evento 
Recupera um item específico de um calendário.
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


### Excluir evento 
Exclui um item de calendário.
```DELETE: /datasets/calendars/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de um calendário.|
|ID|string|sim|path|nenhum|Identificador exclusivo do item de calendário a ser excluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Atualizar evento 
Atualiza parcialmente um item de calendário.
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


### Em novos itens 
Disparado quando um novo item de calendário é criado.
```GET: /datasets/calendars/tables/{table}/onnewitems```

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


### Em itens atualizados 
Disparado quando um item de calendário é modificado.
```GET: /datasets/calendars/tables/{table}/onupdateditems```

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


### Obter pastas de contatos 
Recupera pastas de contatos.
```GET: /datasets/contacts/tables```

Não há parâmetros para esta chamada.

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Obter contatos 
Recupera os contatos de uma pasta de contatos.
```GET: /datasets/contacts/tables/{table}/items```

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


### Criar contato 
Cria um novo contato.
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


### Obter contato 
Recupera um contato específico de uma pasta de contatos.
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


### Excluir contato 
Exclui um contato.
```DELETE: /datasets/contacts/tables/{table}/items/{id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tabela|string|sim|path|nenhum|Identificador exclusivo de uma pasta de contatos.|
|ID|string|sim|path|nenhum|Identificador exclusivo do contato a excluir|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|


### Atualizar contato 
Atualiza parcialmente um contato.
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
|padrão|Falha na operação.|


## Definições de objeto

#### TriggerBatchResponse[IDictionary[String,Object]]

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### SendMessage: Enviar Mensagem de Email

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Anexos|array|não|
|Da|string|não|
|Co|string|não|
|Cco|string|não|
|Assunto|string|sim|
|Corpo|string|sim|
|Importância|string|não|
|IsHtml|booleano|não|
|Para|string|sim|

#### SendAttachment: Anexo

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|@odata.type|string|não|
|Nome|string|sim|
|ContentBytes|string|sim|


#### ReceiveMessage: Receber Mensagem de Email

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|IsRead|booleano|não|
|HasAttachment|booleano|não|
|DateTimeReceived|string|não|
|Anexos|array|não|
|Da|string|não|
|Co|string|não|
|Cco|string|não|
|Assunto|string|sim|
|Corpo|string|sim|
|Importância|string|não|
|IsHtml|booleano|não|
|Para|string|sim|


#### ReceiveAttachment: Receber Anexo

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|sim|
|ContentType|string|sim|
|@odata.type|string|não|
|Nome|string|não|
|ContentBytes|string|sim|


#### DigestMessage: Enviar Mensagem de Email

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Assunto|string|sim|
|Corpo|string|não|
|Importância|string|não|
|Digest|array|sim|
|Anexos|array|não|
|Para|string|sim|

#### TriggerBatchResponse[ReceiveMessage]

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### DataSetsMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|tabular|não definido|não|
|blob|não definido|não|


#### TabularDataSetsMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|
|tableDisplayName|string|não|
|tablePluralName|string|não|


#### BlobDataSetsMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|fonte|string|não|
|displayName|string|não|
|urlEncoding|string|não|


#### TableMetadata

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|título|string|não|
|x-ms-permission|string|não|
|schema|não definido|não|


#### OptionsEmailSubscription: Modelo para Assinatura de Email com Opções

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|NotificationUrl|string|não|
|Mensagem|não definido|não|

#### MessageWithOptions: Mensagem de Email de Opções do Usuário. Essa é a mensagem esperada como parte da entrada do usuário

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Assunto|string|sim|
|Opções|string|sim|
|Corpo|string|não|
|Importância|string|não|
|Anexos|array|não|
|Para|string|sim|

#### SubscriptionResponse: Modelo para Assinatura de Email de Aprovação

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|recurso|string|não|
|notificationType|string|não|
|notificationUrl|string|não|


#### ApprovalEmailSubscription: Modelo para Assinatura de Email de Aprovação

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|NotificationUrl|string|não|
|Mensagem|não definido|não|


#### ApprovalMessage: Mensagem de Email de Aprovação. Essa é a mensagem esperada como parte da entrada do usuário

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Assunto|string|sim|
|Opções|string|sim|
|Corpo|string|não|
|Importância|string|não|
|Anexos|array|não|
|Para|string|sim|

#### ApprovalEmailResponse: Resposta a Email de Aprovação

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|SelectedOption|string|não|

#### TablesList

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### Tabela

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|


#### Item

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ItemInternalId|string|não|


#### CalendarItemsList: a ista de itens de calendário

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### CalendarItem: representa um item da tabela de calendário

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ItemInternalId|string|não|


#### ContactItemsList: a lista de itens de contato

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### ContactItem: representa um item da tabela de contatos

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|ItemInternalId|string|não|


#### DataSetsList

| Nome | Tipo de Dados |Obrigatório|
|---|---|---|
|value|array|não|


#### DataSet

| Nome | Tipo de Dados | Obrigatório|
|---|---|---|
|Nome|string|não|
|DisplayName|string|não|


## Próximas etapas

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

Volte para a [Lista de APIs](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-outlook/aad-tenant-applications.png
[8]: ./media/create-api-office365-outlook/aad-tenant-applications-add-appinfo.png
[9]: ./media/create-api-office365-outlook/aad-tenant-applications-add-app-properties.png
[10]: ./media/create-api-office365-outlook/contoso-aad-app.png
[11]: ./media/create-api-office365-outlook/contoso-aad-app-configure.png
[12]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook.png
[13]: ./media/create-api-office365-outlook/contoso-aad-app-delegate-office365-outlook-permissions.png

<!---HONumber=AcomDC_0309_2016-->