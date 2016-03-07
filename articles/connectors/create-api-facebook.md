<properties
	pageTitle="Adicionar a API do Facebook ao seus aplicativos lógicos | Microsoft Azure"
	description="Visão geral da API do Facebook com os parâmetros de API REST"
	services=""
	documentationCenter="" 
	authors="MandiOhlinger"
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

# Introdução à API do Facebook
Conecte-se ao Facebook e publique em uma linha do tempo, recebe um feed de página e muito mais.

A API do Facebook pode ser usada de aplicativos lógicos.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [Conector do Facebook](../app-service-logic/app-service-logic-connector-facebook.md).


Com o Facebook, você pode:

- Criar seu fluxo de negócios com base nos dados que você obtém do Facebook. 
- Usar um gatilho quando uma nova publicação for recebida.
- Usar ações que publicam em sua linha do tempo, obtêm uma feed de página e mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando há uma nova publicação em sua linha do tempo, você pode publicá-la e enviá-la ao seu feed do Twitter. 

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações
A API de Facebook inclui o gatilho e ações a seguir.

| Gatilhos | Ações|
| --- | --- |
| <ul><li>Quando há uma nova publicação em minha linha do tempo</li></ul> |<ul><li>Obter feed de minha linha do tempo</li><li>Publicar em minha linha do tempo</li><li>Quando há uma nova publicação em minha linha de tempo</li><li>Obter feed da página</li><li>Obter linha do tempo do usuário</li><li>Publicar na página</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.

## Criar uma conexão com o Facebook
Quando você adiciona essa API a seus aplicativos lógicos, precisa autorizar os aplicativos lógicos a se conectarem ao Facebook.

1. Entre em sua conta do Facebook
2. Selecione **Autorizar** e permita que seus aplicativos lógicos se conectem e usem o Facebook. 

Depois de criar a conexão, você pode inserir as propriedades do Facebook. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar esta conexão com o Facebook em outros aplicativos lógicos.

## Referência da API REST do Swagger
Aplica-se à versão: 1.0.

### Obter feed de minha linha de tempo
Obtém os feeds da linha do tempo do usuário conectado. ```GET: /me/feed```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|fields|string|não|query|nenhum |Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem).|
|limite|inteiro|não|query| nenhum|Número máximo de publicações para recuperação|
|por:|string|não|query| nenhum|Restringe a lista de publicações apenas às com local conectado.|
|filtro|string|não|query| nenhum|Recupera apenas as publicações que correspondem a um filtro de fluxo específico.|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### Publicar em minha linha do tempo
Publique uma mensagem de status na linha do tempo do usuário conectado. ```POST: /me/feed```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|post|string |sim|body|nenhum |Nova mensagem a ser publicada|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### Quando há uma nova publicação em minha linha do tempo
Dispara um novo fluxo quando há uma nova publicação na linha do tempo do usuário conectado. ```GET: /trigger/me/feed```

Não há parâmetros.

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### Obter feed da página
Obter publicações do feed de uma página especificada. ```GET: /{pageId}/feed```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|pageId|string|sim|path| nenhum|ID da página da qual as publicações devem ser recuperadas.|
|limite|inteiro|não|query| nenhum|Número máximo de publicações para recuperação|
|include\_hidden|booleano|não|query|nenhum |Se deseja ou não incluir quaisquer publicações que foram ocultas pela página|
|fields|string|não|query|nenhum |Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem).|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### Obter a linha do tempo do usuário
Obtenha as publicações da linha do tempo de um usuário. ```GET: /{userId}/feed```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|coluna|string|sim|path|nenhum |ID do usuário cuja linha do tempo precisa ser recuperada.|
|limite|inteiro|não|query|nenhum |Número máximo de publicações para recuperação|
|por:|string|não|query|nenhum |Restringe a lista de publicações apenas às com local conectado.|
|filtro|string|não|query| nenhum|Recupera apenas as publicações que correspondem a um filtro de fluxo específico.|
|fields|string|não|query| nenhum|Especifica os campos que você deseja retornar. Exemplo (id, nome, imagem).|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


### Publicar na página
Publicar uma mensagem em uma Página do Facebook como o usuário conectado. ```POST: /{pageId}/feed```

| Nome|Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|pageId|string|sim|path|nenhum |ID da página a ser postada.|
|post|many |sim|body|nenhum |Nova mensagem a ser postada.|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|500|Erro interno do servidor|
|padrão|Falha na operação.|


## Definições de objeto

#### GetFeedResponse

|Nome da Propriedade | Tipo de Dados | Obrigatório|
|---|---|---|
|data|array|não|

#### TriggerFeedResponse

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|data|array|não|

#### PostItem: uma única entrada no feed de um perfil
O perfil pode ser um usuário, página, aplicativo ou grupo.

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|admin\_creator|array|não|
|caption|string|não|
|created\_time|string|não|
|description|string|não|
|feed\_targeting|não definido|não|
|from|não definido|não|
|ícone|string|não|
|is\_hidden|booleano|não|
|is\_published|booleano|não|
|link|string|não|
|message|string|não|
|name|string|não|
|object\_id|string|não|
|picture|string|não|
|place|não definido|não|
|privacy|não definido|não|
|propriedades|array|não|
|fonte|string|não|
|status\_type|string|não|
|story|string|não|
|targeting|não definido|não|
|para|array|não|
|type|string|não|
|updated\_time|string|não|
|with\_tags|não definido|não|

#### TriggerItem: uma única entrada no feed de um perfil
O perfil pode ser um usuário, página, aplicativo ou grupo.

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|created\_time|string|não|
|from|não definido|não|
|message|string|não|
|type|string|não|

#### AdminItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|link|string|não|

#### PropertyItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|texto|string|não|
|href|string|não|

#### UserPostFeedRequest

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|message|string|sim|
|link|string|não|
|picture|string|não|
|name|string|não|
|caption|string|não|
|description|string|não|
|place|string|não|
|marcas|string|não|
|privacy|não definido|não|
|object\_attachment|string|não|

#### PagePostFeedRequest

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|message|string|sim|
|link|string|não|
|picture|string|não|
|name|string|não|
|caption|string|não|
|description|string|não|
|actions|array|não|
|place|string|não|
|marcas|string|não|
|object\_attachment|string|não|
|targeting|não definido|não|
|feed\_targeting|não definido|não|
|published|booleano|não|
|scheduled\_publish\_time|string|não|
|backdated\_time|string|não|
|backdated\_time\_granularity|string|não|
|child\_attachments|array|não|
|multi\_share\_end\_card|booleano|não|

#### PostFeedResponse

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|

#### ProfileCollection

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|data|array|não|

#### UserItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|first\_name|string|não|
|last\_name|string|não|
|name|string|não|
|gender|string|não|
|about|string|não|

#### ActionItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|name|string|não|
|link|string|não|

#### TargetItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|countries|array|não|
|locales|array|não|
|regions|array|não|
|cities|array|não|

#### FeedTargetItem: objeto que controla o direcionamento do feed de notícias dessa publicação
É mais provável que qualquer pessoa nesses grupos veja essa publicação, enquanto em outros a probabilidade é menor. Aplica-se somente às Páginas.

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|countries|array|não|
|regions|array|não|
|cities|array|não|
|age\_min|inteiro|não|
|age\_max|inteiro|não|
|genders|array|não|
|relationship\_statuses|array|não|
|interested\_in|array|não|
|college\_years|array|não|
|interests|array|não|
|relevant\_until|inteiro|não|
|education\_statuses|array|não|
|locales|array|não|

#### PlaceItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|não|
|name|string|não|
|overall\_rating|número|não|
|location|não definido|não|

#### LocationItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|city|string|não|
|country|string|não|
|latitude|número|não|
|located\_in|string|não|
|longitude|número|não|
|name|string|não|
|region|string|não|
|state|string|não|
|street|string|não|
|zip|string|não|

#### PrivacyItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|description|string|não|
|value|string|sim|
|allow|string|não|
|deny|string|não|
|friends|string|não|

#### ChildAttachmentsItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|link|string|não|
|picture|string|não|
|image\_hash|string|não|
|name|string|não|
|description|string|não|

#### PostPhotoRequest

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|url|string|sim|
|caption|string|não|

#### PostPhotoResponse

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|sim|
|post\_id|string|sim|

#### PostVideoRequest

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|videoData|string|sim|
|description|string|sim|
|título|string|sim|
|uploadedVideoName|string|não|

#### GetPhotoResponse

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|data|não definido|sim|

#### GetPhotoResponseItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|url|string|sim|
|is\_silhouette|booleano|sim|
|height|string|não|
|width|string|não|

#### GetEventResponse

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|data|array|sim|

#### GetEventResponseItem

|Nome da Propriedade | Tipo de Dados |Obrigatório|
|---|---|---|
|ID|string|sim|
|name|string|sim|
|start\_time|string|não|
|end\_time|string|não|
|timezone|string|não|
|location|string|não|
|description|string|não|
|ticket\_uri|string|não|
|rsvp\_status|string|sim|


## Próximas etapas

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->