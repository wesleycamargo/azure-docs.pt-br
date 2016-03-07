<properties
	pageTitle="Adicionar a API do Twitter a aplicativos lógicos e ao PowerApps Enterprise | Microsoft Azure"
	description="Visão geral da API do Twitter com os parâmetros da API REST"
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


# Introdução à API do Twitter
Entre no Twitter para postar um tweet, obter a linha do tempo do usuário e muito mais.

A API do Twitter pode ser usada em aplicativos PowerApps Enterprise e em aplicativos lógicos.

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos. Para a versão do esquema 2014-12-01-preview, clique em [Conector do Twitter](../app-service-logic/app-service-logic-connector-twitter.md).

Com o Twitter, você pode:

- Criar seu fluxo de negócios baseado nos dados que obtém do Twitter. 
- Usar gatilhos para quando houver um novo tweet.
- Usar ações para postar um tweet, pesquisar tweets e muito mais. Essas ações obtêm uma resposta e disponibilizam a saída para outras ações. Por exemplo, quando um novo tweet aparece, você pode postá-lo no Facebook.
- Adicionar a API do Twitter ao PowerApps Enterprise Assim, seus usuários poderão usar essa API em seus próprios aplicativos. 

Para saber mais sobre como adicionar uma API ao PowerApps Enterprise, acesse [Registrar uma API no PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Para adicionar uma operação a aplicativos lógicos, confira [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).


## Gatilhos e ações
O Twitter inclui os gatilhos e as ações a seguir.

Gatilho | Ações
--- | ---
<ul><li>Quando um novo tweet é exibido</li></ul>| <ul><li>Postar um novo tweet</li><li>Quando um novo tweet é exibido</li><li>Obter linha do tempo de início</li><li>Obter usuário</li><li>Obter linha do tempo do usuário</li><li>Pesquisar tweet</li><li>Obter seguidores</li><li>Obter meus seguidores</li><li>Obter seguidos</li><li>Obter meus seguidos</li></ul>

Todas as APIs dão suporte a dados nos formatos JSON e XML.


## Criar a conexão com o Twitter

### Adicionar um configuração adicional no PowerApps
Ao adicionar o Twitter ao PowerApps Enterprise, insira os valores **Chave do consumidor** e o **Segredo do consumidor** de seu aplicativo do Twitter. O valor **URL de redirecionamento** também é usado em seu aplicativo do Twitter. Se você não tiver um aplicativo do Twitter, poderá usar as seguintes etapas para criar o aplicativo:

1. Entre no [Twitter](https://apps.twitter.com).

2. Clique em **Criar novo aplicativo**: ![Página de aplicativos do Twitter][6]

3. Em **Criar um aplicativo**:
   
	1. Insira qualquer valor desejado em **Nome**, **Descrição** e **Site**.
	2. Em **URL de retorno de chamada**, insira o valor **URL de redirecionamento** mostrado quando você adiciona a API do Twitter ao Portal do Azure.
	5. Aceite o contrato e **crie seu aplicativo do Twitter**.  

	![Criação de aplicativo do Twitter][7]

Agora, copie/cole os valores **Chave do consumidor** e **Segredo do consumidor** em sua configuração do Twitter no Portal do Azure.


### Adicionar configuração adicional em aplicativos lógicos
Quando você adiciona essa API a seus aplicativos lógicos, deve autorizar a conexão destes à sua conta do Twitter.

1. Entre em sua conta do Twitter.
2. Selecione **Autorizar** e permita que seus aplicativos lógicos se conectem e usem sua conta do Twitter. 

Depois de criar a conexão, insira as propriedades do Twitter, como o texto do tweet. A **Referência da API REST** neste tópico descreve essas propriedades.

>[AZURE.TIP] Você pode usar essa mesma conexão do Twitter em outros aplicativos lógicos.


## Referência da API REST do Swagger

### Postar um novo tweet 
Tweet. ```POST: /posttweet```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|tweetText|string|não|query|nenhum|Texto a ser postado|
|body| cadeia de caracteres (binário) |não|body|nenhum|Mídia a ser postada|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Quando um novo tweet é exibido 
Dispara um fluxo de trabalho quando um novo tweet é postado e corresponde à sua consulta de pesquisa. ```GET: /onnewtweet```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|searchQuery|string|sim|query|nenhum|Texto da consulta (você pode usar os operadores de consulta com suporte do Twitter: http://www.twitter.com/search)|

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
|padrão|Falha na operação.|


### Obter linha do tempo de início 
Recupera os tweets e re-tweets mais recentes postados por mim e pelos meus seguidores. ```GET: /hometimeline```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|maxResults|inteiro|não|query|20|Número máximo de tweets a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obter usuário 
Recupera os detalhes sobre o usuário especificado (exemplo: nome de usuário, descrição, contagem de seguidores, etc.). ```GET: /user```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|userName|string|sim|query|nenhum|Identificador do usuário no Twitter|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obter a linha do tempo do usuário 
Recupera uma coleção de tweets mais recentes postados pelo usuário especificado. ```GET: /usertimeline```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|userName|string|sim|query|nenhum|Identificador do Twitter|
|maxResults|inteiro|não|query|20|Número máximo de tweets a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Pesquisar tweet 
Recupera uma coleção de tweets relevantes que correspondem a uma consulta especificada. ```GET: /searchtweets```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|searchQuery|string|sim|query|nenhum|Texto da consulta (você pode usar os operadores de consulta com suporte do Twitter: http://www.twitter.com/search)|
|maxResults|inteiro|não|query|20|Número máximo de tweets a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obter seguidores 
Recupera os usuários que seguem o usuário especificado. ```GET: /followers```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|userName|string|sim|query|nenhum|Identificador do usuário no Twitter|
|maxResults|inteiro|não|query|20|Número máximo de usuários a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obter meus seguidores 
Recupera os usuários que estão me seguindo. ```GET: /myfollowers```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|maxResults|inteiro|não|query|20|Número máximo de usuários a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obtenha seguidos 
Recupera os usuários que o usuário especificado está seguindo. ```GET: /friends```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|userName|string|sim|query|nenhum|Identificador do usuário no Twitter|
|maxResults|inteiro|não|query|20|Número máximo de usuários a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


### Obter meus seguidos 
Recupera os usuários que estou seguindo. ```GET: /myfriends```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|maxResults|inteiro|não|query|20|Número máximo de usuários a recuperar|

#### Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação.|


## Definições de objeto

### TweetModel: representação do objeto de tweet

| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---| --- | 
|TweetText|string|sim|
|TweetId|string|não|
|CreatedAt|string|não|
|RetweetCount|inteiro|sim|
|TweetedBy|string|sim|
|MediaUrls|array|não|

### UserDetailsModel: representação dos detalhes de um usuário do Twitter

|Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|FullName|string|sim|
|Local|string|sim|
|ID|inteiro|não|
|UserName|string|sim|
|FollowersCount|inteiro|não|
|Descrição|string|sim|
|StatusesCount|inteiro|não|
|FriendsCount|inteiro|não|

### TweetResponseModel: modelo representando tweets postados

| Nome | Tipo de Dados | Obrigatório |
|---|---|---|
|TweetId|string|sim|

### TriggerBatchResponse[TweetModel]

|Nome da Propriedade | Tipo de Dados |Obrigatório |
|---|---|---|
|value|array|não|


## Próximas etapas
Depois de adicionar a API do Dropbox para PowerApps Enterprise, [dê aos usuários permissões](../power-apps/powerapps-manage-api-connection-user-access.md) usar a API em seus aplicativos.

[Crie um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->

[6]: ./media/create-api-twitter/twitter-apps-page.png
[7]: ./media/create-api-twitter/twitter-app-create.png

<!---HONumber=AcomDC_0224_2016-->