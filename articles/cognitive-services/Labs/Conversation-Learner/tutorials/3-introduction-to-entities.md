---
title: Como usar entidades com um aplicativo Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades com um aplicativo Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 85df31c2e2ff3ca81698921a1f17f415daefb6c5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364225"
---
# <a name="introduction-to-entities"></a>Introdução a entidades

Este tutorial apresenta as entidades e mostra como usar os campos "Entidades de desqualificação" e "Entidades necessárias" em ações.

## <a name="requirements"></a>Requisitos

Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Este tutorial ilustra dois usos comuns para entidades.  Primeiro, as entidades podem extrair subcadeias de caracteres de uma mensagem do usuário, como identificar uma cidade em "qual é o clima em Seattle?".  Em segundo lugar, as entidades podem restringir quando as ações estarão disponíveis.  Especificamente, uma ação pode listar uma entidade como sendo "necessária" ou "desqualificadora":
- As entidades necessárias de uma ação devem estar presentes na memória do bot para que a ação esteja disponível
- As entidades de desqualificação *não* devem estar presentes na memória do bot para que a ação esteja disponível

Outros tutoriais abordam outros aspectos de entidades, como entidades predefinidas, entidades multivalor e negáveis, entidades programáticas e entidades de manipulação em código.

## <a name="steps"></a>Etapas

### <a name="create-the-application"></a>Criar o aplicativo

1. Na Interface do Usuário da Web, clique em Novo Aplicativo
2. Em Nome, insira IntroduçãoAEntidades. Em seguida, clique em Criar.

### <a name="create-entity"></a>Criar entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira a cidade.
3. Clicar em Criar

Observe que o tipo de entidade é 'personalizada', ou seja, a entidade pode ser treinada.  Também há entidades predefinidas, o que significa que seu comportamento não pode ser ajustado, isso é, abordado em outro tutorial.

### <a name="create-two-actions"></a>Criar duas ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite 'Não sei qual é a cidade que você deseja'.
3. Em Entidades de Desqualificação, insira $city. Clique em Salvar.
    - Isso significa que se essa entidade for definida na memória do bot, então essa ação *não* estará disponível.
2. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
3. Em Resposta, digite ‘O clima em $city provavelmente está ensolarado’.
4. Em Entidades Necessárias, observe que entidade cidade foi adicionada automaticamente desde que foi referenciada.
5. Clique em Salvar

Agora você tem duas ações.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
2. Digite ‘olá’.
3. Clique em Ações de Pontuação e selecione 'Não sei qual é a cidade que você deseja?'
    - Observe que a resposta em que a entidade de cidade é necessária não pode ser selecionada porque a entidade de cidade não está definida na memória do bot.
2. Selecione 'Não sei qual é a cidade você deseja'.
4. Insira 'seattle'. Realce seattle, então clique em cidade.
5. Clique em Ações de Pontuação
    - Observe que o valor de cidade agora está na memória do bot.
    - 'O clima em $city provavelmente está ensolarado' agora está disponível como uma resposta. 
6. Selecione ‘O clima em $city provavelmente está ensolarado’.

Digamos que o usuário insira 'repita isso'. 
1. Digite isso e pressione Enter. Observe que a entidade cidade e seu valor estão na memória e disponíveis.
2. Selecione ‘O clima em $city provavelmente está ensolarado’.

![](../media/tutorial3_entities.PNG)

Agora você criou uma entidade e rotulou instâncias dela em mensagens de usuário.  Você também usou a presença/ausência da entidade na memória do bot para controlar quando as ações estarão disponíveis por meio da desqualificação da ação e os campos de entidades necessárias.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidade esperada](./4-expected-entity.md)
