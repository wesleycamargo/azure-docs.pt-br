<properties
pageTitle="Trello | Microsoft Azure"
description="Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Trello lhe dá uma perspectiva de todos os seus projetos, no trabalho e em casa. É uma maneira fácil, gratuita, flexível e visual de gerenciar seus projetos e organizar qualquer coisa. Conectar ao Trello para gerenciar seus quadros, listas e cartões"
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
ms.date="04/29/2016"
ms.author="deonhe"/>

# Introdução ao conector do Trello



O conector do Trello pode ser usado por meio de:

- [Aplicativos lógicos](../app-service-logic/app-service-logic-what-are-logic-apps.md)  
- [PowerApps](http://powerapps.microsoft.com)  
- [Fluxos](http://flows.microsoft.com)  

>[AZURE.NOTE] Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.

É possível começar criando um Aplicativo Lógico agora; veja [Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Gatilhos e ações

O conector do Trello pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Trello contém as seguintes ações e/ou gatilhos disponíveis:

### Ações do Trello
Você pode executar as seguintes ações:

|Ação|Descrição|
|--- | ---|
|[ListCards](connectors-create-api-trello.md#listcards)|Lista cartões no quadro|
|[GetCard](connectors-create-api-trello.md#getcard)|Obtém um cartão por ID|
|[UpdateCard](connectors-create-api-trello.md#updatecard)|Atualiza cartão|
|[DeleteCard](connectors-create-api-trello.md#deletecard)|Exclui cartão|
|[CreateCard](connectors-create-api-trello.md#createcard)|Cria um novo cartão em sua conta do Trello|
|[ListBoards](connectors-create-api-trello.md#listboards)|Lista cartões|
|[GetBoard](connectors-create-api-trello.md#getboard)|Obtém um quadro por ID|
|[ListLists](connectors-create-api-trello.md#listlists)|Lista as listas de cartões no quadro|
|[GetList](connectors-create-api-trello.md#getlist)|Obtém uma lista por ID|
### Gatilhos do Trello
Você pode escutar estes eventos:

|Gatilho | Descrição|
|--- | ---|
|Quando um novo cartão é adicionado a um quadro|Dispara um fluxo quando um novo cartão é adicionado a um quadro|
|Quando um novo cartão é adicionado a uma lista|Dispara um fluxo quando um novo cartão é adicionado a uma lista|


## Criar uma conexão com o Trello
Para criar Aplicativos Lógicos com o Trello, primeiro, você deve criar uma **conexão** e, em seguida, fornecer os detalhes das seguintes propriedades:

|Propriedade| Obrigatório|Descrição|
| ---|---|---|
|A criptografia do token|Sim|Fornecer as credenciais do Trello|
Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

>[AZURE.TIP] Você pode usar essa conexão em outros aplicativos lógicos.

## Referência do Trello
Aplica-se à versão: 1.0

## OnNewCardInBoard
Quando um novo cartão é adicionado a um quadro: dispara um fluxo quando um novo cartão é adicionado a um quadro

```GET: /trigger/boards/{board_id}/cards```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|path|nenhum|ID exclusiva do quadro no qual os cartões serão buscados|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## OnNewCardInList
Quando um novo cartão é adicionado a uma lista: dispara um fluxo quando um novo cartão é adicionado a uma lista

```GET: /trigger/lists/{list_id}/cards```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID exclusiva do quadro no qual os cartões serão buscados|
|list\_id|string|sim|path|nenhum|ID exclusiva da lista na qual os cartões serão buscados|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## ListCards
Listar cartões no quadro: lista os cartões no quadro

```GET: /boards/{board_id}/cards```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|path|nenhum|ID do quadro no qual todos os cartões serão buscados|
|actions|string|não|query|nenhum|Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|attachments|booleano|não|query|nenhum|Mostra anexos|
|attachment\_fields|string|não|query|nenhum|Lista os campos de anexo a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|stickers|booleano|não|query|nenhum|Mostra os adesivos|
|membros|booleano|não|query|nenhum|Mostra os membros|
|member\_fields|string|não|query|nenhum|Lista os campos de membro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|CheckItemStates|booleano|não|query|nenhum|Retorna os estados de cartão|
|Checklists|string|não|query|nenhum|Mostra as listas de verificação|
|limite|inteiro|não|query|nenhum|O número máximo de resultados a serem retornados, entre 1 e 1000|
|since|string|não|query|nenhum|Busca todos os cartões após essa data|
|antes|string|não|query|nenhum|Busca todos os cartões antes dessa data|
|filtro|string|não|query|nenhum|Filtra a resposta|
|fields|string|não|query|nenhum|Lista os campos de cartão a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## GetCard
Obter cartão por ID: obtém um cartão por ID

```GET: /cards/{card_id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID do quadro no qual os cartões serão buscados|
|card\_id|string|sim|path|nenhum|ID do cartão a ser buscado|
|actions|string|não|query|nenhum|Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|actions\_entities|booleano|não|query|nenhum|Retorna as entidades de ação|
|actions\_display|booleano|não|query|nenhum|Retorna as exibições de ação|
|actions\_limit|inteiro|não|query|nenhum|Número máximo de ações a serem retornadas|
|action\_fields|string|não|query|nenhum|Lista de campos de ação a serem retornados para cada ação. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|action\_memberCreator\_fields|string|não|query|nenhum|Lista de campos de criador de membro de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|attachments|booleano|não|query|nenhum|Retorna os anexos|
|attachement\_fields|string|não|query|nenhum|Lista de campos de anexo a serem retornados para cada anexo. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|membros|booleano|não|query|nenhum|Retorna os membros|
|member\_fields|string|não|query|nenhum|Lista de campos de membro a serem retornados para cada membro. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|membersVoted|booleano|não|query|nenhum|Retorna os membros votados|
|memberVoted\_fields|string|não|query|nenhum|Lista de campos de membro votados a serem retornados para cada membro votado. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|checkItemStates|booleano|não|query|nenhum|Retorna os estados de cartão|
|checkItemState\_fields|string|não|query|nenhum|Lista de campos de estado a serem retornados para cada estado do item de cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|checklists|string|não|query|nenhum|Retorna as listas de verificação|
|checklist\_fields|string|não|query|nenhum|Lista de campos de lista de verificação a serem retornados para cada lista de verificação. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|board|booleano|não|query|nenhum|Retorna o quadro ao qual pertence o cartão|
|board\_fields|string|não|query|nenhum|Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|list|booleano|não|query|nenhum|Retorna a lista à qual pertence o cartão|
|list\_fields|string|não|query|nenhum|Lista os campos de lista a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|stickers|booleano|não|query|nenhum|Retorna os adesivos|
|sticker\_fields|string|não|query|nenhum|Lista os campos de adesivo a serem retornados para cada adesivo. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|fields|string|não|query|nenhum|Lista os campos de cartão a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## UpdateCard
Atualizar cartão: atualiza o cartão

```PUT: /cards/{card_id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID do quadro no qual os cartões serão buscados|
|card\_id|string|sim|path|nenhum|ID do cartão a ser atualizado|
|updateCard| |sim|corpo|nenhum|Valores do cartão atualizado|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## DeleteCard
Excluir cartão: exclui o cartão

```DELETE: /cards/{card_id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID do quadro no qual os cartões serão buscados|
|card\_id|string|sim|path|nenhum|ID do cartão a ser excluído|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## CreateCard
Criar cartão: cria um novo cartão em sua conta do Trello

```POST: /cards```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID exclusiva do quadro no qual os cartões serão criados|
|newCard| |sim|corpo|nenhum|Novo cartão a ser adicionado ao painel do Trello|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## ListBoards
Listar quadros: lista os quadros

```GET: /member/me/boards```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|filtro|string|não|query|nenhum|Lista os filtros a serem aplicados aos resultados do quadro. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|fields|string|não|query|nenhum|Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|actions|string|não|query|nenhum|Lista os campos de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|actions\_entities|booleano|não|query|nenhum|Retorna as entidades de ação|
|actions\_limit|inteiro|não|query|nenhum|Número máximo de ações a serem retornadas|
|actions\_format|string|não|query|nenhum|Especificar o formato das ações a serem retornadas|
|actions\_since|string|não|query|nenhum|Retorna as ações após a data especificada|
|action\_fields|string|não|query|nenhum|Lista os campos da ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|memberships|string|não|query|nenhum|Especifica as informações de associação a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|organization|booleano|não|query|nenhum|Especifica o retorno das informações sobre a organização|
|organization\_fields|string|não|query|nenhum|Lista os campos de organização a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|lists|string|não|query|nenhum|Especifica se as listas que pertencem ao quadro serão retornadas|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## GetBoard
Obter quadro por ID: obtém quadro por ID

```GET: /boards/{board_id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|path|nenhum|ID exclusiva do quadro a ser obtido|
|actions|string|não|query|nenhum|Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|action\_entities|booleano|não|query|nenhum|Especifica se as entidades de ação serão retornadas|
|actions\_display|booleano|não|query|nenhum|Especifica se a exibição de ações será retornada|
|actions\_format|string|não|query|nenhum|Especificar o formato das ações a serem retornadas|
|actions\_since|string|não|query|nenhum|Retorna somente as ações após esta data|
|actions\_limit|inteiro|não|query|nenhum|Número máximo de ações a serem retornadas|
|action\_fields|string|não|query|nenhum|Lista os campos a serem retornados com cada campo. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|action\_member|booleano|não|query|nenhum|Especifica se os membros de ação serão retornados|
|action\_member\_fields|string|não|query|nenhum|Lista os campos de membro a serem retornados com cada membro de ação. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|action\_memberCreator|booleano|não|query|nenhum|Especifica se o criador de membro de ação será retornado|
|action\_memberCreator\_fields|string|não|query|nenhum|Lista os campos de criador de membro de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|cards|string|não|query|nenhum|Especifica os cartões a serem retornados|
|card\_fields|string|não|query|nenhum|Lista os campos a serem retornados para cada cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|card\_attachments|booleano|sim|query|nenhum|Especifica se os anexos nos cartões serão retornados|
|card\_attachment\_fields|string|não|query|nenhum|Lista os campos de anexo a serem retornados para cada anexo. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|card\_checklists|string|não|query|nenhum|Especifica as listas de verificação a serem retornadas para cada cartão|
|card\_stickers|booleano|não|query|nenhum|Especifica se os adesivos de cartão serão retornados|
|boardStarts|string|não|query|nenhum|Especifica as estrelas de quadro a serem retornadas|
|rótulos|string|não|query|nenhum|Especifica os rótulos a serem retornados|
|label\_fields|string|não|query|nenhum|Lista os campos de rótulo a serem retornados para cada rótulo. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|labels\_limits|inteiro|não|query|nenhum|Número máximo de rótulos a serem retornados|
|lists|string|não|query|nenhum|Especifica as listas a serem retornadas|
|list\_fields|string|não|query|nenhum|Lista os campos de lista a serem retornados para cada lista. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|memberships|string|não|query|nenhum|Lista as associações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|memberships\_member|booleano|não|query|nenhum|Especifica se os membros de associação serão retornados|
|memberships\_member\_fields|string|não|query|nenhum|Lista os campos de membro a serem retornados para cada membro de associação. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|membros|string|não|query|nenhum|Lista os membros a serem retornados|
|member\_fields|string|não|query|nenhum|Lista os campos de membro a serem retornados para cada membro. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|membersInvited|string|não|query|nenhum|Especifica os membros convidados a serem retornados|
|membersInvited\_fields|string|não|query|nenhum|Lista os campos a serem retornados para cada um. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|checklists|string|não|query|nenhum|Especifica as listas de verificação a serem retornadas|
|checklist\_fields|string|não|query|nenhum|Lista os campos de lista de verificação a serem retornados para cada lista de verificação. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|organization|booleano|não|query|nenhum|Especifica se as informações sobre a organização serão retornadas|
|organization\_fields|string|não|query|nenhum|Lista os campos de organização a serem retornados para cada organização. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|organization\_memberships|string|não|query|nenhum|Lista os membros da organização a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|myPerfs|booleano|não|query|nenhum|Especifica se meus perfs serão retornados|
|fields|string|não|query|nenhum|Lista os campos a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## ListLists
Listar as listas de cartões no quadro: lista as listas de cartões no quadro

```GET: /boards/{board_id}/lists```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|path|nenhum|ID exclusiva do quadro para buscar listas|
|cards|string|não|query|nenhum|Especifica os cartões a serem retornados|
|card\_fields|string|não|query|nenhum|Lista os campos de cartão do qual serão retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|filtro|string|não|query|nenhum|Especifica a propriedade de filtro para as listas|
|fields|string|não|query|nenhum|Lista os campos a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## GetList
Obter lista por ID: obtém lista por ID

```GET: /lists/{list_id}```

| Nome| Tipo de Dados|Obrigatório|Localizado em|Valor Padrão|Descrição|
| ---|---|---|---|---|---|
|board\_id|string|sim|query|nenhum|ID exclusiva do quadro no qual as listas serão buscadas|
|list\_id|string|sim|path|nenhum|ID exclusiva da lista a ser buscada|
|cards|string|não|query|nenhum|Especifica os cartões a serem retornados|
|card\_fields|string|não|query|nenhum|Lista os campos de cartão a serem retornados para cada cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|board|booleano|não|query|nenhum|Especificar se as informações do quadro serão retornadas|
|board\_fields|string|não|query|nenhum|Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|
|fields|string|não|query|nenhum|Lista os campos de lista a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula|

#### Resposta

|Nome|Descrição|
|---|---|
|200|OK|
|400|Solicitação incorreta|
|401|Não Autorizado|
|403|Proibido|
|404|Não encontrado|
|500|Erro Interno do Servidor. Ocorreu um erro desconhecido|
|padrão|Falha na operação|


## Definições de objeto 

### Card


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|checkItemStates|array|Não |
|closed|booleano|Não |
|dateLastActivity|string|Não |
|desc|string|Não |
|idBoard|string|Não |
|idList|string|Não |
|idMembersVoted|array|Não |
|idShort|inteiro|Não |
|idAttachmentCover|string|Não |
|manualCoverAttachment|booleano|Não |
|idLabels|array|Não |
|name|string|Não |
|pos|inteiro|Não |
|shortLink|string|Não |
|badges|não definido|Não |
|due|string|Não |
|email|string|Não |
|shortUrl|string|Não |
|subscribed|booleano|Não |
|url|string|Não |



### Badges


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|Votes|inteiro|Não |
|ViewingMemberVoted|booleano|Não |
|Subscribed|booleano|Não |
|Fogbugz|string|Não |
|CheckItems|inteiro|Não |
|CheckItemsChecked|inteiro|Não |
|Comentários|inteiro|Não |
|Anexos|inteiro|Não |
|Descrição|booleano|Não |
|Due|string|Não |



### Objeto


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|



### CreateCard


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|idList|string|Sim |
|name|cadeia de caracteres|Sim |
|desc|string|Não |
|pos|string|Não |
|idMembers|string|Não |
|idLabels|string|Não |
|urlSource|string|Não |
|fileSource|string|Não |
|idCardSource|string|Não |
|keepFromSource|string|Não |



### UpdateCard


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|name|string|Não |
|desc|string|Não |
|closed|booleano|Não |
|idMembers|string|Não |
|idAttachmentCover|string|Não |
|idList|string|Não |
|idBoard|string|Não |
|pos|string|Não |
|due|string|Não |
|subscribed|booleano|Não |



### Board


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|closed|booleano|Não |
|dateLastActivity|string|Não |
|dateLastView|string|Não |
|desc|string|Não |
|idOrganization|string|Não |
|invitations|array|Não |
|invited|booleano|Não |
|labelNames|não definido|Não |
|memberships|array|Não |
|name|string|Não |
|pinned|booleano|Não |
|powerUps|array|Não |
|perfs|não definido|Não |
|shortLink|string|Não |
|shortUrl|string|Não |
|starred|string|Não |
|subscribed|string|Não |
|url|string|Não |



### Rótulo


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|green|string|Não |
|yellow|string|Não |
|orange|string|Não |
|red|string|Não |
|purple|string|Não |
|blue|string|Não |
|sky|string|Não |
|lime|string|Não |
|pink|string|Não |
|black|string|Não |



### Membership


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|idMember|string|Não |
|memberType|string|Não |
|unconfirmed|booleano|Não |



### Perfs


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|permissionLevel|string|Não |
|voting|string|Não |
|comentários|string|Não |
|invitations|string|Não |
|selfJoin|booleano|Não |
|cardCovers|booleano|Não |
|calendarFeedEnabled|booleano|Não |
|background|string|Não |
|backgroundColor|string|Não |
|backgroundImage|string|Não |
|backgroundImageScaled|string|Não |
|backgroundTile|booleano|Não |
|backgroundBrightness|string|Não |
|canBePublic|booleano|Não |
|canBeOrg|booleano|Não |
|canBePrivate|booleano|Não |
|canInvite|booleano|Não |



### Listar


| Nome da Propriedade | Tipo de Dados | Obrigatório |
|---|---|---|
|ID|string|Não |
|name|string|Não |
|closed|booleano|Não |
|idBoard|string|Não |
|pos|número|Não |
|subscribed|booleano|Não |
|cards|array|Não |
|board|não definido|Não |


## Próximas etapas
[Criar um aplicativo lógico](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0504_2016-->