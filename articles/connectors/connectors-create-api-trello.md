---
title: Trello | Microsoft Docs
description: "Crie Aplicativos Lógicos com o serviço de Aplicativo do Azure. O Trello lhe dá uma perspectiva de todos os seus projetos, no trabalho e em casa.  É uma maneira fácil, gratuita, flexível e visual de gerenciar seus projetos e organizar qualquer coisa.  Conectar ao Trello para gerenciar seus quadros, listas e cartões"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: fe7a4377-5c24-4f72-ab1a-6d9d23e8d895
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2616db980cb37c1c3759096ec3c05b98d687e047


---
# <a name="get-started-with-the-trello-connector"></a>Introdução ao conector do Trello
O Trello lhe dá uma perspectiva de todos os seus projetos, no trabalho e em casa.  É uma maneira fácil, gratuita, flexível e visual de gerenciar seus projetos e organizar qualquer coisa.  Conectar ao Trello para gerenciar seus quadros, listas e cartões.

> [!NOTE]
> Esta versão do artigo aplica-se à versão do esquema 2015-08-01-preview de aplicativos lógicos.
> 
> 

É possível começar criando um aplicativo lógico agora; consulte [Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Gatilhos e ações
O conector do Trello pode ser usado como uma ação: ele tem um ou mais gatilhos. Todos os conectores dão suporte a dados nos formatos JSON e XML.

 O conector do Trello contém as seguintes ações e/ou gatilhos disponíveis:

### <a name="trello-actions"></a>Ações do Trello
Você pode executar as seguintes ações:

| Ação | Descrição |
| --- | --- |
| [ListCards](connectors-create-api-trello.md#listcards) |Lista cartões no quadro |
| [GetCard](connectors-create-api-trello.md#getcard) |Obtém um cartão por ID |
| [UpdateCard](connectors-create-api-trello.md#updatecard) |Atualiza cartão |
| [DeleteCard](connectors-create-api-trello.md#deletecard) |Exclui cartão |
| [CreateCard](connectors-create-api-trello.md#createcard) |Cria um novo cartão em sua conta do Trello |
| [ListBoards](connectors-create-api-trello.md#listboards) |Lista cartões |
| [GetBoard](connectors-create-api-trello.md#getboard) |Obtém um quadro por ID |
| [ListLists](connectors-create-api-trello.md#listlists) |Lista as listas de cartões no quadro |
| [GetList](connectors-create-api-trello.md#getlist) |Obtém uma lista por ID |

### <a name="trello-triggers"></a>Gatilhos do Trello
Você pode escutar estes eventos:

| Gatilho | Descrição |
| --- | --- |
| Quando um novo cartão é adicionado a um quadro |Dispara um fluxo quando um novo cartão é adicionado a um quadro |
| Quando um novo cartão é adicionado a uma lista |Dispara um fluxo quando um novo cartão é adicionado a uma lista |

## <a name="create-a-connection-to-trello"></a>Criar uma conexão com o Trello
Para criar Aplicativos lógicos com o Trello, você deve primeiro criar uma **conexão**, em seguida, fornecer os detalhes para as seguintes propriedades:

| Propriedade | Obrigatório | Descrição |
| --- | --- | --- |
| A criptografia do token |Sim |Fornecer as credenciais do Trello |

Depois de criar a conexão, é possível usá-la para executar as ações e ouvir os gatilhos descritos neste artigo.

> [!INCLUDE [Steps to create a connection to Trello](../../includes/connectors-create-api-trello.md)]
> 
> [!TIP]
> Você pode usar essa conexão em outros aplicativos lógicos.
> 
> 

## <a name="reference-for-trello"></a>Referência do Trello
Aplica-se à versão: 1.0

## <a name="onnewcardinboard"></a>OnNewCardInBoard
Quando um novo cartão é adicionado a um quadro: dispara um fluxo quando um novo cartão é adicionado a um quadro

```GET: /trigger/boards/{board_id}/cards```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sim |path |nenhum |ID exclusiva do quadro no qual os cartões serão buscados |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="onnewcardinlist"></a>OnNewCardInList
Quando um novo cartão é adicionado a uma lista: dispara um fluxo quando um novo cartão é adicionado a uma lista

```GET: /trigger/lists/{list_id}/cards```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID exclusiva do quadro no qual os cartões serão buscados |
| list_id |string |Sim |path |nenhum |ID exclusiva da lista na qual os cartões serão buscados |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="listcards"></a>ListCards
Listar cartões no quadro: lista os cartões no quadro

```GET: /boards/{board_id}/cards```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sim |path |nenhum |ID do quadro no qual todos os cartões serão buscados |
| actions |string |não |query |nenhum |Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| attachments |Booliano |não |query |nenhum |Mostra anexos |
| attachment_fields |string |não |query |nenhum |Lista os campos de anexo a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| stickers |Booliano |não |query |nenhum |Mostra os adesivos |
| membros |Booliano |não |query |nenhum |Mostra os membros |
| member_fields |string |não |query |nenhum |Lista os campos de membro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| CheckItemStates |Booliano |não |query |nenhum |Retorna os estados de cartão |
| Checklists |string |não |query |nenhum |Mostra as listas de verificação |
| limite |inteiro |não |query |nenhum |O número máximo de resultados a serem retornados, entre 1 e 1000 |
| since |string |não |query |nenhum |Busca todos os cartões após essa data |
| antes |string |não |query |nenhum |Busca todos os cartões antes dessa data |
| filtro |string |não |query |nenhum |Filtra a resposta |
| fields |string |não |query |nenhum |Lista os campos de cartão a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="getcard"></a>GetCard
Obter cartão por ID: obtém um cartão por ID

```GET: /cards/{card_id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID do quadro no qual os cartões serão buscados |
| card_id |string |Sim |path |nenhum |ID do cartão a ser buscado |
| actions |string |não |query |nenhum |Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| actions_entities |Booliano |não |query |nenhum |Retorna as entidades de ação |
| actions_display |Booliano |não |query |nenhum |Retorna as exibições de ação |
| actions_limit |inteiro |não |query |nenhum |Número máximo de ações a serem retornadas |
| action_fields |string |não |query |nenhum |Lista de campos de ação a serem retornados para cada ação. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| action_memberCreator_fields |string |não |query |nenhum |Lista de campos de criador de membro de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| attachments |Booliano |não |query |nenhum |Retorna os anexos |
| attachement_fields |string |não |query |nenhum |Lista de campos de anexo a serem retornados para cada anexo. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| membros |Booliano |não |query |nenhum |Retorna os membros |
| member_fields |string |não |query |nenhum |Lista de campos de membro a serem retornados para cada membro. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| membersVoted |Booliano |não |query |nenhum |Retorna os membros votados |
| memberVoted_fields |string |não |query |nenhum |Lista de campos de membro votados a serem retornados para cada membro votado. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| checkItemStates |Booliano |não |query |nenhum |Retorna os estados de cartão |
| checkItemState_fields |string |não |query |nenhum |Lista de campos de estado a serem retornados para cada estado do item de cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| checklists |string |não |query |nenhum |Retorna as listas de verificação |
| checklist_fields |string |não |query |nenhum |Lista de campos de lista de verificação a serem retornados para cada lista de verificação. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| board |Booliano |não |query |nenhum |Retorna o quadro ao qual pertence o cartão |
| board_fields |string |não |query |nenhum |Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| list |Booliano |não |query |nenhum |Retorna a lista à qual pertence o cartão |
| list_fields |string |não |query |nenhum |Lista os campos de lista a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| stickers |Booliano |não |query |nenhum |Retorna os adesivos |
| sticker_fields |string |não |query |nenhum |Lista os campos de adesivo a serem retornados para cada adesivo. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| fields |string |não |query |nenhum |Lista os campos de cartão a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="updatecard"></a>UpdateCard
Atualizar cartão: atualiza o cartão

```PUT: /cards/{card_id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID do quadro no qual os cartões serão buscados |
| card_id |string |Sim |path |nenhum |ID do cartão a ser atualizado |
| updateCard | |sim |corpo |nenhum |Valores do cartão atualizado |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="deletecard"></a>DeleteCard
Excluir cartão: exclui o cartão

```DELETE: /cards/{card_id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID do quadro no qual os cartões serão buscados |
| card_id |string |Sim |path |nenhum |ID do cartão a ser excluído |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="createcard"></a>CreateCard
Criar cartão: cria um novo cartão em sua conta do Trello

```POST: /cards```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID exclusiva do quadro no qual os cartões serão criados |
| newCard | |sim |corpo |nenhum |Novo cartão a ser adicionado ao painel do Trello |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="listboards"></a>ListBoards
Listar quadros: lista os quadros

```GET: /member/me/boards```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| filtro |string |não |query |nenhum |Lista os filtros a serem aplicados aos resultados do quadro. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| fields |string |não |query |nenhum |Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| actions |string |não |query |nenhum |Lista os campos de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| actions_entities |Booliano |não |query |nenhum |Retorna as entidades de ação |
| actions_limit |inteiro |não |query |nenhum |Número máximo de ações a serem retornadas |
| actions_format |string |não |query |nenhum |Especificar o formato das ações a serem retornadas |
| actions_since |string |não |query |nenhum |Retorna as ações após a data especificada |
| action_fields |string |não |query |nenhum |Lista os campos da ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| memberships |string |não |query |nenhum |Especifica as informações de associação a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| organization |Booliano |não |query |nenhum |Especifica o retorno das informações sobre a organização |
| organization_fields |string |não |query |nenhum |Lista os campos de organização a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| lists |string |não |query |nenhum |Especifica se as listas que pertencem ao quadro serão retornadas |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="getboard"></a>GetBoard
Obter quadro por ID: obtém quadro por ID

```GET: /boards/{board_id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sim |path |nenhum |ID exclusiva do quadro a ser obtido |
| actions |string |não |query |nenhum |Lista as ações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| action_entities |Booliano |não |query |nenhum |Especifica se as entidades de ação serão retornadas |
| actions_display |Booliano |não |query |nenhum |Especifica se a exibição de ações será retornada |
| actions_format |string |não |query |nenhum |Especificar o formato das ações a serem retornadas |
| actions_since |string |não |query |nenhum |Retorna somente as ações após esta data |
| actions_limit |inteiro |não |query |nenhum |Número máximo de ações a serem retornadas |
| action_fields |string |não |query |nenhum |Lista os campos a serem retornados com cada campo. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| action_member |Booliano |não |query |nenhum |Especifica se os membros de ação serão retornados |
| action_member_fields |string |não |query |nenhum |Lista os campos de membro a serem retornados com cada membro de ação. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| action_memberCreator |Booliano |não |query |nenhum |Especifica se o criador de membro de ação será retornado |
| action_memberCreator_fields |string |não |query |nenhum |Lista os campos de criador de membro de ação a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| cards |string |não |query |nenhum |Especifica os cartões a serem retornados |
| card_fields |string |não |query |nenhum |Lista os campos a serem retornados para cada cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| card_attachments |Booliano |sim |query |nenhum |Especifica se os anexos nos cartões serão retornados |
| card_attachment_fields |string |não |query |nenhum |Lista os campos de anexo a serem retornados para cada anexo. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| card_checklists |string |não |query |nenhum |Especifica as listas de verificação a serem retornadas para cada cartão |
| card_stickers |Booliano |não |query |nenhum |Especifica se os adesivos de cartão serão retornados |
| boardStarts |string |não |query |nenhum |Especifica as estrelas de quadro a serem retornadas |
| rótulos |string |não |query |nenhum |Especifica os rótulos a serem retornados |
| label_fields |string |não |query |nenhum |Lista os campos de rótulo a serem retornados para cada rótulo. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| labels_limits |inteiro |não |query |nenhum |Número máximo de rótulos a serem retornados |
| lists |string |não |query |nenhum |Especifica as listas a serem retornadas |
| list_fields |string |não |query |nenhum |Lista os campos de lista a serem retornados para cada lista. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| memberships |string |não |query |nenhum |Lista as associações a serem retornadas. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| memberships_member |Booliano |não |query |nenhum |Especifica se os membros de associação serão retornados |
| memberships_member_fields |string |não |query |nenhum |Lista os campos de membro a serem retornados para cada membro de associação. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| membros |string |não |query |nenhum |Lista os membros a serem retornados |
| member_fields |string |não |query |nenhum |Lista os campos de membro a serem retornados para cada membro. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| membersInvited |string |não |query |nenhum |Especifica os membros convidados a serem retornados |
| membersInvited_fields |string |não |query |nenhum |Lista os campos a serem retornados para cada um. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| checklists |string |não |query |nenhum |Especifica as listas de verificação a serem retornadas |
| checklist_fields |string |não |query |nenhum |Lista os campos de lista de verificação a serem retornados para cada lista de verificação. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| organization |Booliano |não |query |nenhum |Especifica se as informações sobre a organização serão retornadas |
| organization_fields |string |não |query |nenhum |Lista os campos de organização a serem retornados para cada organização. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| organization_memberships |string |não |query |nenhum |Lista os membros da organização a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| myPerfs |Booliano |não |query |nenhum |Especifica se meus perfs serão retornados |
| fields |string |não |query |nenhum |Lista os campos a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="listlists"></a>ListLists
Listar as listas de cartões no quadro: lista as listas de cartões no quadro

```GET: /boards/{board_id}/lists```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |Sim |path |nenhum |ID exclusiva do quadro para buscar listas |
| cards |string |não |query |nenhum |Especifica os cartões a serem retornados |
| card_fields |string |não |query |nenhum |Lista os campos de cartão do qual serão retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| filtro |string |não |query |nenhum |Especifica a propriedade de filtro para as listas |
| fields |string |não |query |nenhum |Lista os campos a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="getlist"></a>GetList
Obter lista por ID: obtém lista por ID

```GET: /lists/{list_id}```

| Nome | Tipo de Dados | Obrigatório | Localizado em | Valor Padrão | Descrição |
| --- | --- | --- | --- | --- | --- |
| board_id |string |sim |query |nenhum |ID exclusiva do quadro no qual as listas serão buscadas |
| list_id |string |Sim |path |nenhum |ID exclusiva da lista a ser buscada |
| cards |string |não |query |nenhum |Especifica os cartões a serem retornados |
| card_fields |string |não |query |nenhum |Lista os campos de cartão a serem retornados para cada cartão. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| board |Booliano |não |query |nenhum |Especificar se as informações do quadro serão retornadas |
| board_fields |string |não |query |nenhum |Lista os campos de quadro a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |
| fields |string |não |query |nenhum |Lista os campos de lista a serem retornados. Especifica “todos” ou uma lista de valores válidos separados por vírgula |

#### <a name="response"></a>Resposta
| Nome | Descrição |
| --- | --- |
| 200 |OK |
| 400 |Solicitação incorreta |
| 401 |Não Autorizado |
| 403 |Proibido |
| 404 |Não encontrado |
| 500 |Erro Interno do Servidor. Ocorreu um erro desconhecido |
| padrão |Falha na operação |

## <a name="object-definitions"></a>Definições de objeto
### <a name="card"></a>Card
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| checkItemStates |array |Não |
| closed |Booliano |Não |
| dateLastActivity |string |Não |
| desc |string |Não |
| idBoard |string |Não |
| idList |string |Não |
| idMembersVoted |array |Não |
| idShort |inteiro |Não |
| idAttachmentCover |string |Não |
| manualCoverAttachment |Booliano |Não |
| idLabels |array |Não |
| name |string |Não |
| pos |inteiro |Não |
| shortLink |string |Não |
| badges |não definido |Não |
| due |string |Não |
| email |string |Não |
| shortUrl |string |Não |
| subscribed |Booliano |Não |
| url |string |Não |

### <a name="badges"></a>Badges
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Votes |inteiro |Não |
| ViewingMemberVoted |Booliano |Não |
| Subscribed |Booliano |Não |
| Fogbugz |string |Não |
| CheckItems |inteiro |Não |
| CheckItemsChecked |inteiro |Não |
| Comentários |inteiro |Não |
| Anexos |inteiro |Não |
| Descrição |Booliano |Não |
| Due |string |Não |

### <a name="object"></a>Objeto
### <a name="createcard"></a>CreateCard
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| idList |string |Sim |
| name |string |Sim |
| desc |string |Não |
| pos |string |Não |
| idMembers |string |Não |
| idLabels |string |Não |
| urlSource |string |Não |
| fileSource |string |Não |
| idCardSource |string |Não |
| keepFromSource |string |Não |

### <a name="updatecard"></a>UpdateCard
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| Nome |string |Não |
| desc |string |Não |
| closed |Booliano |Não |
| idMembers |string |Não |
| idAttachmentCover |string |Não |
| idList |string |Não |
| idBoard |string |Não |
| pos |string |Não |
| due |string |Não |
| subscribed |Booliano |Não |

### <a name="board"></a>Board
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| closed |Booliano |Não |
| dateLastActivity |string |Não |
| dateLastView |string |Não |
| desc |string |Não |
| idOrganization |string |Não |
| invitations |array |Não |
| invited |Booliano |Não |
| labelNames |não definido |Não |
| memberships |array |Não |
| name |string |Não |
| pinned |Booliano |Não |
| powerUps |array |Não |
| perfs |não definido |Não |
| shortLink |string |Não |
| shortUrl |string |Não |
| starred |string |Não |
| subscribed |string |Não |
| url |string |Não |

### <a name="label"></a>Rótulo
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| green |string |Não |
| yellow |string |Não |
| orange |string |Não |
| red |string |Não |
| purple |string |Não |
| blue |string |Não |
| sky |string |Não |
| lime |string |Não |
| pink |string |Não |
| black |string |Não |

### <a name="membership"></a>Membership
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| idMember |string |Não |
| memberType |string |Não |
| unconfirmed |Booliano |Não |

### <a name="perfs"></a>Perfs
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| permissionLevel |string |Não |
| voting |string |Não |
| comentários |string |Não |
| invitations |string |Não |
| selfJoin |Booliano |Não |
| cardCovers |Booliano |Não |
| calendarFeedEnabled |Booliano |Não |
| background |string |Não |
| backgroundColor |string |Não |
| backgroundImage |string |Não |
| backgroundImageScaled |string |Não |
| backgroundTile |Booliano |Não |
| backgroundBrightness |string |Não |
| canBePublic |Booliano |Não |
| canBeOrg |Booliano |Não |
| canBePrivate |Booliano |Não |
| canInvite |Booliano |Não |

### <a name="list"></a>Listar
| Nome da Propriedade | Tipo de Dados | Obrigatório |
| --- | --- | --- |
| ID |string |Não |
| name |string |Não |
| closed |Booliano |Não |
| idBoard |string |Não |
| pos |número |Não |
| subscribed |Booliano |Não |
| cards |array |Não |
| board |não definido |Não |

## <a name="next-steps"></a>Próximas etapas
[Criar um aplicativo lógico](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO3-->


