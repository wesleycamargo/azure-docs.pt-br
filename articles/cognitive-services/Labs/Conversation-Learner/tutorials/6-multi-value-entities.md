---
title: Como usar entidades multivalor com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades multivalor com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a80577bb226cbec080edf5e06dbd0f31c80a5890
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321420"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Como usar entidades multivalor com um modelo de Aprendiz de Conversa
Este tutorial mostra a propriedade "multivalor" de entidades.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 6](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Uma entidade "multivalor" acumula os valores em uma lista em vez de armazenar um único valor.  Isso é útil para entidades em que o usuário pode especificar mais de um valor, como ingredientes em uma pizza.

Concretamente, se uma entidade for marcada como "multivalor", então cada instância reconhecida da entidade será anexada a uma lista na memória do bot (em vez de substituir um valor de entidade única).

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em Nome, digite EntidadesMultiValor. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira Ingredientes.
3. Verificação de multivalores.
    - As entidades multivalor acumulam um ou mais valores na entidade.
2. Marque Negável.  
    - Isso permitirá que o usuário remova ingredientes da sua lista de ingredientes de pizza acumulados.
3. Clique em Criar.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite 'Quais ingredientes você deseja?'.
3. Em Entidades de Desqualificação, insira Ingredientes.
3. Clique em Criar. 

Em seguida, crie a segunda ação.

1. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
3. Em Resposta, digite 'Aqui estão seus ingredientes: $Toppings'.
4. Clique em Criar. 

Agora você tem duas ações.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite ‘olá’.
3. Clique em Ações de Pontuação e Selecionar ‘Quais ingredientes você deseja?’
2. Insira 'cogumelos e queijo'. 
    - Você pode rotular nenhuma, uma ou mais de uma das entidades.
3. Clique em 'cogumelos' e selecione Ingredientes.
4. Clique em 'queijo' e selecione Ingredientes.
5. Clique em Ações de Pontuação
    - Os dois valores agora estão presentes na entidade Ingredientes. 
6. Selecione 'Aqui estão seus ingredientes: $Toppings'.

Podemos adicionar mais:

7. Insira 'adicionar pimentões'.
    - Clique em ‘pimentões’ na Detecção de Entidade e selecione Ingredientes.
3. Clique em “Score Actions” (Pontuar ações).
    - 'pimentões' agora aparece como um valor adicional em Ingredientes.
6. Selecione 'Aqui estão seus ingredientes: $Toppings'.

Vamos remover um ingrediente e adicionar outro:

2. Digite 'remover pimentões e adicionar linguiça'.
1. Clique em 'pimentões' e clique no x vermelho para removê-lo.
2. Clique em 'pimentões' e selecione '-Ingredientes’.
3. Clique em “Score Actions” (Pontuar ações).
    - 'pimentões' foi excluído e 'linguiça' foi adicionado.
6. Selecione 'Aqui estão seus ingredientes: $Toppings'.

Agora vamos tentar remover tudo:

6. Insira 'remover cogumelos, remover queijo e remover linguiça'.
7. Clique em cada um dos três e selecione '-Ingredientes.
7. Clique em “Score Actions” (Pontuar ações).
    - Todos os ingredientes estão desmarcados.
2. Selecione 'Quais ingredientes você deseja?'
3. Clique em Ensino Concluído

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades internas](./7-built-in-entities.md)
