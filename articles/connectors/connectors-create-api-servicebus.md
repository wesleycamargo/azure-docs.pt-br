<properties
pageTitle="Saiba como usar o conector do Barramento de Serviço do Azure nos seus aplicativos lógicos | Microsoft Azure"
description="Crie aplicativos lógicos com o serviço de Aplicativo do Azure. Conecte-se ao Barramento de Serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para a fila, enviar para o tópico, receber da fila, receber da assinatura, etc."
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
ms.date="08/02/2016"
ms.author="deonhe"/>

# Introdução ao conector do Barramento de Serviço do Azure

Conecte-se ao Barramento de Serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para a fila, enviar para o tópico, receber da fila, receber da assinatura, etc.

Para usar [qualquer conector](./apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao Barramento de Serviço do Azure

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço.

### Criar uma conexão com o Barramento de Serviço do Azure

>[AZURE.INCLUDE [Etapas para criar uma conexão com o Barramento de Serviço do Azure](../../includes/connectors-create-api-servicebus.md)]

## Usar um gatilho de Barramento de Serviço do Azure

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

>[AZURE.INCLUDE [Etapas para criar um gatilho de Barramento de Serviço](../../includes/connectors-create-api-servicebus-trigger.md)]

## Usar uma ação de Barramento de Serviço do Azure

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

[AZURE.INCLUDE [Etapas para criar uma ação de Barramento de Serviço](../../includes/connectors-create-api-servicebus-action.md)]

## Detalhes técnicos

Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos do Barramento de Serviço do Azure

O Barramento de Serviço do Azure tem os seguintes gatilhos:

|Gatilho | Descrição|
|--- | ---|
|[Quando uma mensagem é recebida em uma fila](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-queue)|Esta operação dispara um fluxo quando uma mensagem é recebida em uma fila.|
|[Quando uma mensagem é recebida em uma assinatura de tópico](connectors-create-api-servicebus.md#when-a-message-is-received-in-a-topic-subscription)|Esta operação dispara um fluxo quando uma mensagem é recebida em uma assinatura de tópico.|


## Ações do Barramento de Serviço do Azure

O Barramento de Serviço do Azure tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Enviar mensagem](connectors-create-api-servicebus.md#send-message)|Esta operação envia uma mensagem para uma fila ou um tópico.|
### Detalhes da ação

Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:



### Enviar mensagem
Esta operação envia uma mensagem para uma fila ou um tópico.


|Nome da propriedade| Nome de exibição|Descrição|
| ---|---|---|
|ContentData*|Conteúdo|Conteúdo da mensagem|
|ContentType|Tipo de conteúdo|Tipo do conteúdo da mensagem|
|Propriedades|Propriedades|Pares de chave-valor para cada propriedade agenciada|
|entityName*|Nome da fila/do tópico|Nome da fila ou do tópico|

Esses parâmetros avançados também estão disponíveis:

|Nome da propriedade| Nome de exibição|Descrição|
| ---|---|---|
|MessageId|ID da Mensagem|Esse é um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|Para|Para|Endereço de envio|
|ReplyTo|Responder Para|Endereço da fila para a qual responder|
|ReplyToSessionId|ID da Sessão Responder Para|Identificador da sessão para a qual responder|
|Rótulo|Rótulo|Rótulo específico do aplicativo|
|ScheduledEnqueueTimeUtc|ScheduledEnqueueTimeUtc|Data e hora, em UTC, de quando a mensagem será adicionada à fila|
|SessionId|Id da Sessão|Identificador da sessão|
|CorrelationId|ID de correlação|Identificador da correlação|
|TimeToLive|Vida Útil|Essa é a duração, em tiques, pela qual uma mensagem é válida. A duração inicia quando a mensagem é enviada ao Barramento de Serviço.|



Um * indica que uma propriedade é obrigatória




### Quando uma mensagem é recebida em uma fila
Esta operação dispara um fluxo quando uma mensagem é recebida em uma fila.


|Nome da propriedade| Nome de exibição|Descrição|
| ---|---|---|
|queueName*|Nome da fila|Nome da fila|


Um * indica que uma propriedade é obrigatória

#### Detalhes de saída

ServiceBusMessage: esse objeto tem o conteúdo e as propriedades de uma Mensagem do Barramento de Serviço.


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|ContentData|string|Conteúdo da mensagem|
|ContentType|string|Tipo do conteúdo da mensagem|
|Propriedades|objeto|Pares de chave-valor para cada propriedade agenciada|
|MessageId|string|Esse é um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|Para|string|Endereço de envio|
|ReplyTo|string|Endereço da fila para a qual responder|
|ReplyToSessionId|string|Identificador da sessão para a qual responder|
|Rótulo|string|Rótulo específico do aplicativo|
|ScheduledEnqueueTimeUtc|string|Data e hora, em UTC, de quando a mensagem será adicionada à fila|
|SessionId|string|Identificador da sessão|
|CorrelationId|string|Identificador da correlação|
|TimeToLive|string|Essa é a duração, em tiques, pela qual uma mensagem é válida. A duração inicia quando a mensagem é enviada ao Barramento de Serviço.|




### Quando uma mensagem é recebida em uma assinatura de tópico
Esta operação dispara um fluxo quando uma mensagem é recebida em uma assinatura de tópico.


|Nome da propriedade| Nome de exibição|Descrição|
| ---|---|---|
|topicName*|Nome do tópico|Nome do tópico|
|subscriptionName*|Nome da assinatura do tópico|Nome da assinatura do tópico|


Um * indica que uma propriedade é obrigatória

#### Detalhes de saída

ServiceBusMessage: esse objeto tem o conteúdo e as propriedades de uma Mensagem do Barramento de Serviço.


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|ContentData|string|Conteúdo da mensagem|
|ContentType|string|Tipo do conteúdo da mensagem|
|Propriedades|objeto|Pares de chave-valor para cada propriedade agenciada|
|MessageId|string|Esse é um valor definido pelo usuário que o Barramento de Serviço pode usar para identificar mensagens duplicadas, se habilitado.|
|Para|string|Endereço de envio|
|ReplyTo|string|Endereço da fila para a qual responder|
|ReplyToSessionId|string|Identificador da sessão para a qual responder|
|Rótulo|string|Rótulo específico do aplicativo|
|ScheduledEnqueueTimeUtc|string|Data e hora, em UTC, de quando a mensagem será adicionada à fila|
|SessionId|string|Identificador da sessão|
|CorrelationId|string|Identificador da correlação|
|TimeToLive|string|Essa é a duração, em tiques, pela qual uma mensagem é válida. A duração inicia quando a mensagem é enviada ao Barramento de Serviço.|



## Respostas HTTP

As ações e os gatilhos acima podem retornar um ou mais dos seguintes códigos de status HTTP:

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido.|
|padrão|Falha na Operação.|

## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!----HONumber=AcomDC_0803_2016-->
