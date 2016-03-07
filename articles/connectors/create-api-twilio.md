<properties
pageTitle="Adicionar a API do Twilio nos seus Aplicativos Lógicos | Microsoft Azure"
description="Visão geral da API do Twilio com os parâmetros da API REST"
services=""	
documentationCenter="" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors"/>

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="na"
ms.date="02/23/2016"
ms.author="mandia"/>

# Introdução à API do Twilio

Conecte-se a Twilio para enviar e receber mensagens SMS, MMS e de IP globais.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [Twilio](../app-service-logic/app-service-logic-connector-twilio.md).

Com o Twilio, você pode:

- Compile seu fluxo de negócios baseado nos dados obtidos do Twilio. 
- Use ações para obter uma mensagem, listar mensagens e muito mais. Essas ações obtém uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando você recebe uma nova mensagem do Twilio, você pode obtê-la e usá-la como um fluxo de trabalho do Barramento de Serviço. 

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API do Twilio inclui as ações a seguir. Não há nenhum gatilho.

| Gatilhos | Ações|
| --- | --- |
|Nenhum| <ul><li>Obter Mensagem</li><li>Listar Mensagens</li><li>Enviar Mensagem</li></ul>|

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Twilio
Ao adicionar essa API aos seus aplicativos lógicos, insira os seguintes valores do Twilio:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|ID da Conta|Sim|Insira seu ID de conta do Twilio|
|Token de Acesso|Sim|Insira seu token de acesso do Twilio|

Consulte [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) para criar um token de acesso.

Depois de criar a conexão, insira as propriedades do Twilio. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do Twilio em outros aplicativos lógicos.

## Referência da API REST do Swagger
#### Esta documentação destina-se à versão: 1.0

### Obter Mensagem
Retorna uma única mensagem especificada pela ID de Mensagem fornecida. ```GET: /Messages/{MessageId}.json```

| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|MessageId|string|sim|path|nenhum|ID da Mensagem|

### Resposta
|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|404|Mensagem não encontrada|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


### Listar Mensagens
Retorna uma lista de mensagens associadas à sua conta. ```GET: /Messages.json```

| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|Para|string|não|query|nenhum|Para o número de telefone|
|Da|string|não|query|nenhum|Do número de telefone|
|DateSent|string|não|query|nenhum|Mostrar apenas as mensagens enviadas nessa data (no formato GMT), indicado como AAAA-MM-DD. Exemplo: DateSent = 2009-07-06. Você também pode especificar desigualdade, como DateSent < = AAAA-MM-DD para mensagens que foram enviadas até a meia-noite (inclusive) em uma data e DateSent > = AAAA-MM-DD para mensagens enviadas a partir da meia-noite (inclusive) em uma data.|
|PageSize|inteiro|não|query|50|Quantos recursos devem ser retornados em cada página da lista. O padrão é 50.|
|Página|inteiro|não|query|0|Número da página. O padrão é 0.|

### Resposta
|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|



### Enviar Mensagem
Enviar uma nova mensagem para um número de celular. ```POST: /Messages.json```

| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|sendMessageRequest| |sim|corpo|nenhum|Mensagem para Enviar|

### Resposta
|Nome|Descrição|
|---|---|
|200|Operação concluída com êxito|
|400|Solicitação incorreta|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## Definições de objeto

#### SendMessageRequest: modelo de solicitação para a operação Enviar Mensagem

|Nome da Propriedade | Tipo de Dados | Obrigatório|
|---|---|---|
|de|string|sim|
|para|string|sim|
|corpo|string|sim|
|media\_url|array|não|
|status\_callback|string|não|
|messaging\_service\_sid|string|não|
|application\_sid|string|não|
|max\_price|string|não|


#### Mensagem: modelo para mensagem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|corpo|string|não|
|de|string|não|
|para|string|não|
|status|string|não|
|sid|string|não|
|account\_sid|string|não|
|api\_version|string|não|
|num\_segments|string|não|
|num\_media|string|não|
|date\_created|string|não|
|date\_sent|string|não|
|date\_updated|string|não|
|direction|string|não|
|error\_code|string|não|
|error\_message|string|não|
|preço|string|não|
|price\_unit|string|não|
|uri|string|não|
|subresource\_uris|array|não|
|messaging\_service\_sid|string|não|

#### MessageList: modelo de resposta para a operação Listar Mensagens

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|mensagens|array|não|
|página|inteiro|não|
|page\_size|inteiro|não|
|num\_pages|inteiro|não|
|uri|string|não|
|first\_page\_uri|string|não|
|next\_page\_uri|string|não|
|total|inteiro|não|
|previous\_page\_uri|string|não|

#### IncomingPhoneNumberList: modelo de resposta para a operação Listar Mensagens

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|incoming\_phone\_numbers|array|não|
|página|inteiro|não|
|page\_size|inteiro|não|
|num\_pages|inteiro|não|
|uri|string|não|
|first\_page\_uri|string|não|
|next\_page\_uri|string|não|


#### AddIncomingPhoneNumberRequest: modelo de solicitação para a operação Adicionar Número de Entrada

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|PhoneNumber|string|sim|
|AreaCode|string|não|
|FriendlyName|string|não|


#### IncomingPhoneNumber: Número de Telefone de Entrada

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|phone\_number|string|não|
|friendly\_name|string|não|
|sid|string|não|
|account\_sid|string|não|
|date\_created|string|não|
|date\_updated|string|não|
|recursos|não definido|não|
|status\_callback|string|não|
|status\_callback\_method|string|não|
|api\_version|string|não|


#### Funcionalidades: Recursos de Número de Telefone

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|mms|booleano|não|
|sms|booleano|não|
|voice|booleano|não|

#### AvailablePhoneNumbers: Números de Telefone Disponíveis

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|phone\_number|string|não|
|friendly\_name|string|não|
|lata|string|não|
|latitude|string|não|
|longitude|string|não|
|postal\_code|string|não|
|rate\_center|string|não|
|region|string|não|
|MMS|booleano|não|
|SMS|booleano|não|
|voice|booleano|não|


#### UsageRecords: classe de Registros de Uso

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|categoria|string|não|
|uso|string|não|
|usage\_unit|string|não|
|description|string|não|
|preço|número|não|
|price\_unit|string|não|
|count|string|não|
|count\_unit|string|não|
|start\_date|string|não|
|end\_date|string|não|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->