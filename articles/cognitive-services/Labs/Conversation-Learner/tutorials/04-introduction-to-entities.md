---
title: Como usar entidades com um Modelo do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades com um Modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 4239cbfe369060516653145b04a2bcfa0882f20d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796441"
---
# <a name="introduction-to-entities"></a>Introdução a Entidades

Este tutorial apresenta as entidades, desqualificação de entidades, entidades necessárias e seu uso dentro do Conversation Learner.

## <a name="video"></a>Vídeo

[![Introdução ao tutorial de entidades multivalor](https://aka.ms/cl_Tutorial_v3_IntroEntities_Preview)](https://aka.ms/cl_Tutorial_v3_IntroEntities)

## <a name="requirements"></a>Requisitos

Este tutorial exige que Bot de tutorial esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

As entidades capturam as partes de informações que o Bot precisa para realizar sua tarefa, seja por meio da extração de declarações de usuário ou a atribuição por código personalizado. As entidades em si também podem restringir a disponibilidade de ação por sejam explicitamente classificadas como "Necessárias" ou "Desqualificação".

- As entidades necessárias de uma ação devem estar presentes na memória do Modelo para que a Ação esteja disponível
- As entidades de desqualificação *não* devem estar presentes na memória do Modelo para que a ação esteja disponível

Este tutorial se concentra nas Entidades Personalizadas. Previamente treinadas, multivalores, entidades negáveis e entidades programáticas são apresentadas em outros tutoriais.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em “Novo Modelo”.
2. No campo "Nome", digite "ExpectedEntities" e pressione enter.
3. Clique no botão “Criar”.

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Treinada Personalizada" para o "Tipo de Entidade".
3. Digite "city" para o "Nome da Entidade".
4. Clique no botão “Criar”.

> [!NOTE]
> O tipo de entidade 'Personalizada Treinada' significa que essa entidade pode fazer o treinamento, ao contrário de outros tipos de entidades.

### <a name="create-the-actions"></a>Criar as ações

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Bot's de resposta...", digite 'Não sei qual é a cidade que você deseja'.
3. No campo "Entidades desqualificadoras", digite "city".
4. Clique no botão “Criar”.

> [!NOTE]
> Adicionar a entidade "cidade" a "Entidades de desqualificação" seria desqualificar esta ação da consideração do Bot, quando a entidade "cidade" é definida na memória do Bot.

Crie agora uma segunda ação.

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite ‘O clima em $city provavelmente está ensolarado’.
3. Clique no botão “Criar”.

> [!NOTE]
> A entidade "cidade" foi adicionada automaticamente na lista de entidades exigidas por referência na resposta.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi".
    - Isso simula o lado do usuário da conversa.
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta: "Não sei qual é a cidade que você deseja".
5. Como usuário, responda "sim".
6. Clique no botão "Ações de Pontuação".
7. Selecione a resposta: "O clima em $city é provavelmente ensolarado".
8. Clique no botão "Salvar".

![](../media/tutorial3_entities.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidade esperada](./05-expected-entity.md)
