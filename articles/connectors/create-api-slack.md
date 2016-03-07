<properties
pageTitle="Adicionar a API do Slack aos seus aplicativos lógicos | Microsoft Azure"
description="Começar a usar a API de Vídeo do Slack (conector) nos seus aplicativos lógicos do Serviço de Aplicativo do Microsoft Azure"
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

# Introdução à API do Slack

O Slack é uma ferramenta de comunicação da equipe que reúne todas as comunicações de equipe em um só lugar, que pode ser pesquisado instantaneamente e está disponível onde você estiver.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [Slack](../app-service-logic/app-service-logic-connector-Slack.md).

Com o conector do Slack, você pode:

* Usá-lo para criar aplicativos lógicos

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Vamos conversar sobre gatilhos e ações

O conector do Slack pode ser usado como uma ação. Ele não tem gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Slack possui as seguintes ações e/ou gatilhos disponíveis:

### Ações do Slack
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|PostMessage|Poste uma mensagem em um canal específico.|
## Criar uma conexão com o Slack
Para usar a API do Slack, crie primeiro uma **conexão** e forneça os detalhes dessas propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer credenciais do Slack|


>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência da API REST do Slack
#### Esta documentação destina-se à versão: 1.0


### Poste uma mensagem em um canal específico.
**```POST: /chat.postMessage```**



| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|canal|string|sim|query|nenhum|Canal, grupo privado ou canal de IM para envio de mensagem. Pode ser um nome (ex: #geral) ou uma ID codificada.|
|texto|string|sim|query|nenhum|Texto da mensagem a ser enviada. Para opções de formatação, confira https://api.slack.com/docs/formatting.|
|Nome de Usuário|string|não|query|nenhum|Nome do bot|
|as\_user|booleano|não|query|nenhum|Passa true para postar a mensagem como o usuário autenticado em vez de um bot|
|analisar|string|não|query|nenhum|Altera como as mensagens são tratadas. Para saber mais, confira https://api.slack.com/docs/formatting.|
|link\_names|inteiro|não|query|nenhum|Localiza e vincula os nomes de canal e nomes de usuário.|
|unfurl\_links|booleano|não|query|nenhum|Passe true para habilitar o desdobramento do conteúdo basicamente baseado em texto.|
|unfurl\_media|booleano|não|query|nenhum|Passe false para desabilitar o conteúdo de mídia.|
|icon\_url|string|não|query|nenhum|URL para uma imagem a ser usada como ícone para esta mensagem|
|icon\_emoji|string|não|query|nenhum|Emoji a ser usado como ícone para esta mensagem|


### Estas são as respostas possíveis:

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|408|Tempo Limite da Solicitação|
|429|Número Excessivo de Solicitações|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|503|Serviço do Slack Indisponível|
|504|Tempo Limite do Gateway|
|padrão|Falha na Operação.|
------



## Definições de objeto: 

 **Mensagem**: mensagem do Yammer

Propriedades necessárias para a mensagem:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|inteiro|
|content\_excerpt|string|
|sender\_id|inteiro|
|replied\_to\_id|inteiro|
|created\_at|string|
|network\_id|inteiro|
|message\_type|string|
|sender\_type|string|
|url|string|
|web\_url|string|
|group\_id|inteiro|
|corpo|não definido|
|thread\_id|inteiro|
|direct\_message|booleano|
|client\_type|string|
|client\_url|string|
|idioma|string|
|notified\_user\_ids|array|
|privacidade|string|
|liked\_by|não definido|
|system\_message|booleano|



 **PostOperationRequest**: representa uma solicitação post ao conector do Yammer para postar no Yammer

Propriedades obrigatórias para PostOperationRequest:

corpo

**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|corpo|string|
|group\_id|inteiro|
|replied\_to\_id|inteiro|
|direct\_to\_id|inteiro|
|difusão|booleano|
|topic1|string|
|topic2|string|
|topic3|string|
|topic4|string|
|topic5|string|
|topic6|string|
|topic7|string|
|topic8|string|
|topic9|string|
|topic10|string|
|topic11|string|
|topic12|string|
|topic13|string|
|topic14|string|
|topic15|string|
|topic16|string|
|topic17|string|
|topic18|string|
|topic19|string|
|topic20|string|



 **MessageList**: lista de mensagens

Propriedades obrigatórias para MessageList:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|mensagens|array|



 **MessageBody**: corpo da mensagem

Propriedades obrigatórias para MessageBody:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|analisado|string|
|sem formatação|string|
|avançado|string|



 **LikedBy**: curtido por

Propriedades obrigatórias para LikedBy:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|count|inteiro|
|nomes|array|



 **YammmerEntity**: curtido por

Propriedades obrigatórias para YammmerEntity:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|type|string|
|ID|inteiro|
|full\_name|string|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)
## Definições de objeto: 

 **WebResultModel**: resultados da pesquisa da Web do Bing

Propriedades obrigatórias para WebResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Title|string|
|Descrição|string|
|DisplayUrl|string|
|ID|string|
|FullUrl|string|



 **VideoResultModel**: resultados da pesquisa de vídeo do Bing

Propriedades obrigatórias para VideoResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Title|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|Tempo de execução|inteiro|
|Miniatura|não definido|



 **ThumbnailModel**: propriedades de miniatura do elemento multimídia

Propriedades obrigatórias para ThumbnailModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|MediaUrl|string|
|ContentType|string|
|Largura|inteiro|
|Altura|inteiro|
|FileSize|inteiro|



 **ImageResultModel**: resultados da pesquisa de imagens do Bing

Propriedades obrigatórias para ImageResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Title|string|
|DisplayUrl|string|
|ID|string|
|MediaUrl|string|
|SourceUrl|string|
|Miniatura|não definido|



 **NewsResultModel**: resultados da pesquisa de notícias do Bing

Propriedades obrigatórias para NewsResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Title|string|
|Descrição|string|
|DisplayUrl|string|
|ID|string|
|Fonte|string|
|Data|string|



 **SpellResultModel**: resultados de sugestões de ortografia do Bing

Propriedades obrigatórias para SpellResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ID|string|
|Valor|string|



 **RelatedSearchResultModel**: resultados de pesquisa relacionados ao Bing

Propriedades obrigatórias para RelatedSearchResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|Title|string|
|ID|string|
|BingUrl|string|



 **CompositeSearchResultModel**: resultados de pesquisa compostas do Bing

Propriedades obrigatórias para CompositeSearchResultModel:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|WebResultsTotal|inteiro|
|ImageResultsTotal|inteiro|
|VideoResultsTotal|inteiro|
|NewsResultsTotal|inteiro|
|SpellSuggestionsTotal|inteiro|
|WebResults|array|
|ImageResults|array|
|VideoResults|array|
|NewsResults|array|
|SpellSuggestionResults|array|
|RelatedSearchResults|array|


## Definições de objeto: 

 **PostOperationResponse**: representa a resposta da operação POST do conector do Slack para postar no Slack

Propriedades obrigatórias para PostOperationResponse:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|ok|booleano|
|canal|string|
|ts|string|
|message|não definido|
|error|string|



 **MessageItem**: uma mensagem de canal.

Propriedades obrigatórias para MessageItem:


Nenhuma das propriedades é obrigatória.


**Todas as propriedades**:


| Nome | Tipo de Dados |
|---|---|
|texto|string|
|ID|string|
|usuário|string|
|criado|inteiro|
|is\_user-deleted|booleano|


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0224_2016-->