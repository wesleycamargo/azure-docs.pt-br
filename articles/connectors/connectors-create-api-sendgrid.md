<properties
pageTitle="SendGrid | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Provedor de Conexão do SendGrid permite enviar emails e gerenciar listas de destinatários."
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

# Introdução ao conector do SendGrid

O Provedor de Conexão do SendGrid permite enviar emails e gerenciar listas de destinatários.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do SendGrid pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector SendGrid tem as ações a seguir disponíveis. Não há nenhum gatilho.

### Ações do SendGrid
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[SendEmail](connectors-create-api-sendgrid.md#sendemail)|Envia um email usando a API do SendGrid (limitado a 10.000 destinatários)|
|[AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist)|Adiciona um destinatário individual a uma lista de destinatários|


## Criar uma conexão com o SendGrid
Para criar Aplicativos Lógicos com o SendGrid, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|ApiKey|Sim|Fornecer sua chave de API do SendGrid|
 

>[AZURE.INCLUDE [Etapas para criar uma conexão com o SendGrid](../../includes/connectors-create-api-sendgrid.md)]

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

## Referência do SendGrid
Aplica-se à versão: 1.0

## SendEmail
Enviar email: envia um email usando a API do SendGrid (limitado a 10.000 destinatários)

```POST: /api/mail.send.json```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|solicitação| |sim|corpo|nenhum|Mensagem de email a ser enviada|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|429|Número excessivo de solicitações|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## AddRecipientToList
Adicionar destinatário à lista: adiciona um destinatário individual a uma lista de destinatários

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|listId|string|sim|path|nenhum|ID exclusiva da lista de destinatários|
|recipientId|string|sim|path|nenhum|ID exclusiva do destinatário|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## Definições de objeto 

### EmailRequest


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|de|string|Sim |
|fromname|string|Não |
|para|string|Sim |
|toname|string|Não |
|subject|string|Sim |
|corpo|string|Sim |
|ishtml|Booliano|Não |
|cc|string|Não |
|ccname|string|Não |
|bcc|string|Não |
|bccname|string|Não |
|replyto|string|Não |
|data|string|Não |
|headers|string|Não |
|de entrada|array|Não |
|filenames|array|Não |



### EmailResponse


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|message|string|Não |



### RecipientLists


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|lists|array|Não |



### RecipientList


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|inteiro|Não |
|name|string|Não |
|recipient\_count|inteiro|Não |



### Destinatários


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|recipients|array|Não |



### Destinatário


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|email|string|Não |
|last\_name|string|Não |
|first\_name|string|Não |
|ID|string|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->