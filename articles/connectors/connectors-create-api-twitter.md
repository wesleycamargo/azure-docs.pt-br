<properties
	pageTitle="Saiba como usar o conector de Twitter em aplicativos lógicos | Microsoft Azure"
	description="Visão geral do conector do Twitter com os parâmetros da API REST"
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
   ms.date="07/18/2016"
   ms.author="deonhe"/>


# Introdução ao conector do Twitter

Com o conector do Twitter, você pode:

- Postar tweets e obter tweets
- Acessar linhas do tempo, amigos e seguidores
- Executar qualquer um dos gatilhos e ações descritos abaixo

Para usar [qualquer conector](./apis-list.md), primeiro é preciso criar um aplicativo lógico. Você pode começar [criando um aplicativo lógico agora mesmo](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Conectar-se ao Twitter

Para que o aplicativo lógico possa acessar qualquer serviço, crie primeiro uma *conexão* com o serviço. Uma [conexão](./connectors-overview.md) fornece conectividade entre um aplicativo lógico e outro serviço.

### Criar uma conexão com o Twitter

>[AZURE.INCLUDE [Etapas para criar uma conexão com o Twitter](../../includes/connectors-create-api-twitter.md)]

## Usar um gatilho do Twitter

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre gatilhos](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Neste exemplo, mostrarei como usar o gatilho **Quando um novo tweet é postado** para procurar #Seattle e, se #Seattle for encontrada, atualizar um arquivo no Dropbox com o texto do tweet. Em um exemplo corporativo, você pode pesquisar o nome da sua empresa e atualizar um banco de dados SQL com o texto do tweet.

1. Digite *twitter* na caixa de pesquisa no designer de aplicativos lógicos e escolha o gatilho **Twitter – quando um novo tweet é postado**  
![Imagem 1 do gatilho Twitter](./media/connectors-create-api-twitter/trigger-1.png)  
- Digite *#Seattle* no controle **Texto de Pesquisa**  
![Imagem 2 do gatilho Twitter](./media/connectors-create-api-twitter/trigger-2.png)  

Neste ponto, seu aplicativo lógico foi configurado com um gatilho que iniciará uma execução de outros gatilhos e as ações no fluxo de trabalho.

>[AZURE.NOTE]Para que um aplicativo lógico funcione, ele deve conter pelo menos um gatilho e uma ação. Siga as etapas na próxima seção para adicionar uma ação.

## Adicione uma condição
Uma vez que estamos interessados em tweets de usuários com mais de 50 usuários, uma condição que confirma o número de seguidores deve ser adicionada ao aplicativo lógico.

1. Escolha **+ Nova etapa** para adicionar a ação que deseja tomar quando #Seattle for encontrada em um novo tweet  
![Imagem 1 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-1.png)  
- Escolha o link **Adicionar uma ação**.  
![Imagem 1 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-1.png)  
Isso abre o controle **Condição**, onde é possível verificar condições como *é igual a*, *é menor que*, *é maior que*, *contém*, etc.  
![Imagem 2 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-2.png)  
- Selecione o controle **Escolher um valor**. Nesse controle, é possível selecionar uma ou mais propriedades de quaisquer ações ou gatilhos anteriores como o valor cuja condição será avaliada como verdadeira ou falsa.  
![Imagem 3 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-3.png)  
- Escolha **...** para expandir a lista de propriedades, de modo que você possa ver todas as propriedades que estão disponíveis.  
![Imagem 4 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-4.png)  
- Escolha a propriedade **Contagem de seguidores**.  
![Imagem 5 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-5.png)  
- Observe que a propriedade Contagem de seguidores agora está no controle de valor.  
![Imagem 6 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-6.png)  
- Escolha **é maior que** na lista de operadores.  
![Imagem 7 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-7.png)  
- Digite 50 como o operando para o operador *é maior que*. Agora a condição está adicionada. Salve seu trabalho usando o link **Salvar** no menu acima.  
![Imagem 8 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-8.png)  

## Usar uma ação do Twitter

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).

Agora que você adicionou um gatilho, siga estas etapas para adicionar uma ação que postará um novo tweet com o conteúdo dos tweets encontrados pelo gatilho. Para os fins deste passo a passo, apenas tweets de usuários com mais de 50 seguidores serão postados.

Na próxima etapa, você adicionará uma ação do Twitter que postará um tweet usando algumas das propriedades de cada tweet que foi postado por um usuário com mais de 50 seguidores.

1. Escolha **Adicionar uma ação**. Isso abre o controle de pesquisa, onde é possível procurar outros gatilhos e ações.  
![Imagem 9 da condição do Twitter](../../includes/media/connectors-create-api-twitter/condition-9.png)  
- Insira *twitter* na caixa de pesquisa e escolha a ação **Twitter – postar um tweet**. Isso abre o controle **Postar um tweet**, onde você vai inserir todos os detalhes para o tweet que está sendo postado.  
![Imagem de 1 a 5 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-1-5.png)  
- Escolha o controle **Texto do tweet**. Todas as saídas de ações e gatilhos anteriores no aplicativo lógico agora estão visíveis. Você pode escolher qualquer um deles e usá-los como parte do texto do tweet do novo tweet.  
![Imagem 2 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-2.png)  
- Escolha **Nome de usuário**
- Digite *diz:* no controle de texto do tweet. Faça isso logo após o Nome de usuário.
- Escolha *Texto do tweet*.  
![Imagem 3 da ação do Twitter](../../includes/media/connectors-create-api-twitter/action-3.png)  
- Salve seu trabalho e envie um tweet com a hashtag #Seattle para ativar o fluxo de trabalho.

## Detalhes técnicos

Veja abaixo os detalhes sobre os gatilhos, as ações e as respostas que essa conexão permite:

## Gatilhos do Twitter

O conector do Twitter tem os seguintes gatilhos:

|Gatilho | Descrição|
|--- | ---|
|[Quando um novo tweet é postado](connectors-create-api-twitter.md#when-a-new-tweet-is-posted)|Esta operação dispara um fluxo quando um novo tweet que corresponde a uma determinada consulta de pesquisa é postado.|


## Ações de Twitter

O conector do Twitter tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Obter a linha do tempo do usuário](connectors-create-api-twitter.md#get-user-timeline)|Esta operação obtém uma lista dos tweets mais recentes postados por um determinado usuário.|
|[Obter linha do tempo de início](connectors-create-api-twitter.md#get-home-timeline)|Esta operação obtém os tweets mais recentes e retweets postados por mim e meus seguidores.|
|[Pesquisar tweets](connectors-create-api-twitter.md#search-tweets)|Esta operação obtém uma lista de tweets relevantes correspondentes à consulta de pesquisa.|
|[Obter seguidores](connectors-create-api-twitter.md#get-followers)|Esta operação obtém a lista de usuários que seguem um determinado usuário.|
|[Obter meus seguidores](connectors-create-api-twitter.md#get-my-followers)|Esta operação obtém a lista de usuários que estão me seguindo.|
|[Obtenha seguidos](connectors-create-api-twitter.md#get-following)|A operação obtém a lista de pessoas que determinado usuário segue.|
|[Obter meus seguidos](connectors-create-api-twitter.md#get-my-following)|Esta operação obtém a lista de usuários que eu estou seguindo.|
|[Obter usuário](connectors-create-api-twitter.md#get-user)|Esta operação obtém os detalhes do perfil de um determinado usuário, como nome de usuário, descrição, contagem de seguidores e muito mais.|
|[Publicar um tweet](connectors-create-api-twitter.md#post-a-tweet)|Esta operação posta um novo tweet.|
## Detalhes da ação

Veja abaixo os detalhes das ações e dos gatilhos para esse conector, com suas respostas:



### Obter a linha do tempo do usuário
Esta operação obtém uma lista dos tweets mais recentes postados por um determinado usuário.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|userName*|Nome de usuário|Identificador do usuário no Twitter|
|maxResults|Máximo de resultados|Número máximo de tweets a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

TweetModel: representação do objeto de tweet


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|TweetText|string|Conteúdo do texto do tweet|
|TweetId|string|ID do tweet|
|CreatedAt|string|Hora em que o tweet foi postado|
|RetweetCount|inteiro|Número total de retweets para o tweet|
|TweetedBy|string|Nome do usuário que postou o tweet|
|MediaUrls|array|URL da mídia postada com o tweet|
|TweetLanguageCode|string|Código de idioma do tweet|
|TweetInReplyToUserId|string|ID de usuário do autor do tweet para o qual o tweet atual é uma resposta|
|Favorited|booleano|Indica se o tweet está marcado como favorito ou não|
|UserMentions|array|Lista de usuários mencionados no tweet|
|OriginalTweet|não definido|Tweet original do qual o tweet atual é retweetado|
|UserDetails|não definido|Detalhes do usuário que tweetou|




### Obter linha do tempo de início
Esta operação obtém os tweets mais recentes e retweets postados por mim e meus seguidores.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|maxResults|Máximo de resultados|Número máximo de tweets a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

TweetModel: representação do objeto de tweet


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|TweetText|string|Conteúdo do texto do tweet|
|TweetId|string|ID do tweet|
|CreatedAt|string|Hora em que o tweet foi postado|
|RetweetCount|inteiro|Número total de retweets para o tweet|
|TweetedBy|string|Nome do usuário que postou o tweet|
|MediaUrls|array|URL da mídia postada com o tweet|
|TweetLanguageCode|string|Código de idioma do tweet|
|TweetInReplyToUserId|string|ID de usuário do autor do tweet para o qual o tweet atual é uma resposta|
|Favorited|booleano|Indica se o tweet está marcado como favorito ou não|
|UserMentions|array|Lista de usuários mencionados no tweet|
|OriginalTweet|não definido|Tweet original do qual o tweet atual é retweetado|
|UserDetails|não definido|Detalhes do usuário que tweetou|




### Pesquisar tweets
Esta operação obtém uma lista de tweets relevantes correspondentes à consulta de pesquisa.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|searchQuery*|Texto da pesquisa|Termo de pesquisa, como "happy hour", #haicai, amor OR ódio|
|maxResults|Máximo de resultados|Número máximo de tweets a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

TweetModel: representação do objeto de tweet


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|TweetText|string|Conteúdo do texto do tweet|
|TweetId|string|ID do tweet|
|CreatedAt|string|Hora em que o tweet foi postado|
|RetweetCount|inteiro|Número total de retweets para o tweet|
|TweetedBy|string|Nome do usuário que postou o tweet|
|MediaUrls|array|URL da mídia postada com o tweet|
|TweetLanguageCode|string|Código de idioma do tweet|
|TweetInReplyToUserId|string|ID de usuário do autor do tweet para o qual o tweet atual é uma resposta|
|Favorited|booleano|Indica se o tweet está marcado como favorito ou não|
|UserMentions|array|Lista de usuários mencionados no tweet|
|OriginalTweet|não definido|Tweet original do qual o tweet atual é retweetado|
|UserDetails|não definido|Detalhes do usuário que tweetou|




### Obter seguidores
Esta operação obtém a lista de usuários que seguem um determinado usuário.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|userName*|Nome de usuário|Identificador do usuário no Twitter|
|maxResults|Máximo de resultados|Número máximo de usuários a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

UserDetailsModel: Detalhes do usuário do Twitter


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|FullName|string|Nome do usuário|
|Local|string|Local do usuário|
|ID|inteiro|ID do usuário no Twitter|
|UserName|string|Nome do usuário na tela|
|FollowersCount|inteiro|Número de seguidores|
|Descrição|string|Descrição do usuário|
|StatusesCount|inteiro|Contagem de status do usuário|
|FriendsCount|inteiro|Número de amigos|
|FavouritesCount|inteiro|Número de tweets que o usuário marcou como favorito|
|ProfileImageUrl|string|URL da imagem do perfil|




### Obter meus seguidores
Esta operação obtém a lista de usuários que estão me seguindo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|maxResults|Máximo de resultados|Número máximo de usuários a serem obtidos|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

UserDetailsModel: Detalhes do usuário do Twitter


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|FullName|string|Nome do usuário|
|Local|string|Local do usuário|
|ID|inteiro|ID do usuário no Twitter|
|UserName|string|Nome do usuário na tela|
|FollowersCount|inteiro|Número de seguidores|
|Descrição|string|Descrição do usuário|
|StatusesCount|inteiro|Contagem de status do usuário|
|FriendsCount|inteiro|Número de amigos|
|FavouritesCount|inteiro|Número de tweets que o usuário marcou como favorito|
|ProfileImageUrl|string|URL da imagem do perfil|




### Obtenha seguidos
A operação obtém a lista de pessoas que determinado usuário segue.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|userName*|Nome de usuário|Identificador do usuário no Twitter|
|maxResults|Máximo de resultados|Número máximo de usuários a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

UserDetailsModel: Detalhes do usuário do Twitter


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|FullName|string|Nome do usuário|
|Local|string|Local do usuário|
|ID|inteiro|ID do usuário no Twitter|
|UserName|string|Nome do usuário na tela|
|FollowersCount|inteiro|Número de seguidores|
|Descrição|string|Descrição do usuário|
|StatusesCount|inteiro|Contagem de status do usuário|
|FriendsCount|inteiro|Número de amigos|
|FavouritesCount|inteiro|Número de tweets que o usuário marcou como favorito|
|ProfileImageUrl|string|URL da imagem do perfil|




### Obter meus seguidos
Esta operação obtém a lista de usuários que eu estou seguindo.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|maxResults|Máximo de resultados|Número máximo de usuários a serem retornados|

Um * indica que uma propriedade é obrigatória



#### Detalhes da Saída

UserDetailsModel: Detalhes do usuário do Twitter


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|FullName|string|Nome do usuário|
|Local|string|Local do usuário|
|ID|inteiro|ID do usuário no Twitter|
|UserName|string|Nome do usuário na tela|
|FollowersCount|inteiro|Número de seguidores|
|Descrição|string|Descrição do usuário|
|StatusesCount|inteiro|Contagem de status do usuário|
|FriendsCount|inteiro|Número de amigos|
|FavouritesCount|inteiro|Número de tweets que o usuário marcou como favorito|
|ProfileImageUrl|string|URL da imagem do perfil|




### Obter usuário
Esta operação obtém os detalhes do perfil de um determinado usuário, como nome de usuário, descrição, contagem de seguidores e muito mais.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|userName*|Nome de usuário|Identificador do usuário no Twitter|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

UserDetailsModel: Detalhes do usuário do Twitter


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|FullName|string|Nome do usuário|
|Local|string|Local do usuário|
|ID|inteiro|ID do usuário no Twitter|
|UserName|string|Nome do usuário na tela|
|FollowersCount|inteiro|Número de seguidores|
|Descrição|string|Descrição do usuário|
|StatusesCount|inteiro|Contagem de status do usuário|
|FriendsCount|inteiro|Número de amigos|
|FavouritesCount|inteiro|Número de tweets que o usuário marcou como favorito|
|ProfileImageUrl|string|URL da imagem do perfil|




### Publicar um tweet
Esta operação posta um novo tweet.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|tweetText|Texto do tweet|Texto a ser postado|
|corpo|Mídia|Mídia a ser postada|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

TweetResponseModel: modelo representando o tweet postado


| Nome da Propriedade | Tipo de Dados | Descrição |
|---|---|---|
|TweetId|string|ID do tweet recuperado|




### Quando um novo tweet é postado
Esta operação dispara um fluxo quando um novo tweet que corresponde a uma determinada consulta de pesquisa é postado.


|Nome da Propriedade| Nome de exibição|Descrição|
| ---|---|---|
|searchQuery*|Texto da pesquisa|Termo de pesquisa, como "happy hour", #haicai, amor OR ódio|

Um * indica que uma propriedade é obrigatória

#### Detalhes da Saída

TriggerBatchResponse[TweetModel]


| Nome da Propriedade | Tipo de Dados |
|---|---|
|value|array|



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

<!---HONumber=AcomDC_0727_2016-->
