<properties
pageTitle="RSS | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O conector do RSS permite que os usuários publiquem e recuperem itens de feed. Também permite que os usuários disparem operações quando um novo item é publicado no feed."
services="app-servicelogic"	
documentationCenter=".net,nodejs,java" 	
authors="msftman"	
manager="erikre"	
editor=""
tags="connectors" />

<tags
ms.service="app-service-logic"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="05/17/2016"
ms.author="deonhe"/>

# Introdução ao conector do RSS



O conector do RSS pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxo](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do RSS pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do RSS contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do RSS
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[ListFeedItems](connectors-create-api-rss.md#listfeeditems)|Obter todos os itens de RSS feed.|
### Gatilhos do RSS
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando um novo item de feed é publicado|Dispara um fluxo de trabalho quando um novo feed é publicado|


## Criar uma conexão com o RSS
Para criar Aplicativos Lógicos com o RSS, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do RSS
Aplica-se à versão: 1.0

## OnNewFeed
Quando um novo item de feed é publicado: dispara um fluxo de trabalho quando um novo feed é publicado

```GET: /OnNewFeed```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|feedUrl|string|sim|query|nenhum|URL de feed|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## ListFeedItems
Listar todos os itens de RSS feed: obtém todos os itens de RSS feed.

```GET: /ListFeedItems```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|feedUrl|string|sim|query|nenhum|URL de feed|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceita|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na Operação.|


## Definições de objeto 

### TriggerBatchResponse[FeedItem]


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|value|array|Não |



### FeedItem


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Sim |
|título|string|Sim |
|conteúdo|string|Sim |
|links|array|Não |
|updatedOn|string|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0518_2016-->