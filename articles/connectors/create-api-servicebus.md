<properties
pageTitle="Usar a API do Barramento de Serviço do Azure nos seus Aplicativos Lógicos | Microsoft Azure"
description="Começar a usar a API do Barramento de Serviço do Azure (conector) nos seus Aplicativos lógicos do serviço de aplicativo do Microsoft Azure"
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
ms.date="02/18/2016"
ms.author="deonhe"/>

# Introdução à API do Barramento de Serviço do Azure

Conecte-se ao Barramento de Serviço do Azure para enviar e receber mensagens. Você pode executar ações como enviar para a fila, enviar para o tópico, receber da fila, receber da assinatura, etc.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema For the 2014-12-01-preview, clique em [Barramento de Serviço do Azure](../app-service-logic/app-service-logic-connector-Azure Service Bus.md).

Com o Barramento de Serviço do Azure, você pode:

* Use-o para compilar aplicativos lógicos  

Para adicionar uma operação a aplicativos lógicos, consulte [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Agora vamos falar sobre gatilhos e ações

A API do Barramento de Serviço do Azure pode ser usada como uma ação; ele tem gatilhos. Todas as APIs dão suporte a dados nos formatos JSON e XML.

 A API do Barramento de Serviço do Azure tem as seguintes ações e/ou gatilhos disponíveis:

### Ações do Barramento de Serviço do Azure
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|SendMessage|Envia a mensagem para a fila ou o tópico do Barramento de Serviço do Azure.|
### Gatilhos do Barramento de Serviço do Azure
Você pode escutar esse(s) evento(s):

|Gatilho | Descrição|
|--- | ---|
|GetMessageFromQueue|Obtém uma nova mensagem da fila do Barramento de Serviço do Azure.|
|GetMessageFromTopic|Obtém uma nova mensagem da assinatura do tópico do Barramento de Serviço do Azure.|


## Criar uma conexão com o Barramento de Serviço do Azure
Para usar a API do Barramento de Serviço do Azure, crie primeiro uma **conexão** e, em seguida, forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|ConnectionString|Sim|Fornecer uma Cadeia de Conexão do Barramento de Serviço do Azure|  

>[AZURE.TIP] Você pode usar esta conexão em outros aplicativos lógicos.

## Referência da API REST do Barramento de Serviço do Azure
#### Esta documentação destina-se à versão: 1.0


### Envia a mensagem para a fila ou o tópico do Barramento de Serviço do Azure.
**```POST: /{entityName}/messages```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|message| |sim|corpo|nenhum|Mensagem do Barramento de Serviço|
|entityName|string|sim|path|nenhum|Nome da fila ou do tópico|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obtém uma nova mensagem da fila do Barramento de Serviço do Azure.
**```GET: /{queueName}/messages/head```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|queueName|string|sim|path|nenhum|Nome da fila.|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



### Obtém uma nova mensagem da assinatura do tópico do Barramento de Serviço do Azure.
**```GET: /{topicName}/subscriptions/{subscriptionName}/messages/head```**



| Nome| Tipo de Dados|Obrigatório|Localizado Em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|topicName|string|sim|path|nenhum|Nome do tópico.|
|subscriptionName|string|sim|path|nenhum|Nome da assinatura do tópico.|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|padrão|Falha na Operação.|
------



## Definições de objeto: 

 **ServiceBusMessage**: uma mensagem composta por conteúdo e propriedades

Propriedades obrigatórias para o ServiceBusMessage:

ContentTransferEncoding

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ContentData|string|
|ContentType|string|
|ContentTransferEncoding|string|
|Propriedades|objeto|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->