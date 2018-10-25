---
title: Como usar a propriedade "entidade esperada" das ações do Aprendiz de conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar a propriedade "entidade esperada" de um modelo do Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: d0ad7093249bea761e0a36c6fffab8cdd151a662
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268014"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Como usar a propriedade "Entidade esperada" das ações

Este tutorial demonstra o campo "entidade esperada" das ações.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 4](http://aka.ms/cl-tutorial-04-preview)](http://aka.ms/blis-tutorial-04)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Use o campo "entidade esperada" de uma ação para comunicar com o sistema que você espera a resposta do usuário para uma ação que será para o estado de uma entidade.

Concretamente, se o campo "entidade esperada" de uma ação for definido como $entity, então, na próxima declaração do usuário, o sistema irá:

1. Primeiro, como de costume, tentará localizar entidades usando o modelo de extração de entidade baseado em aprendizado de máquina
2. Se nenhuma entidade for encontrada na etapa 1, então, como uma heurística, atribua a declaração do usuário como $entity.
3. Chame `EntityDetectionCallback` como de costume, e vá para a seleção de ação.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em Nome, digite ExpectedEntities. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira o nome.
3. Clique em Criar. 

> [!NOTE]
> O tipo de entidade é 'custom'. Esse valor significa que a entidade pode ser treinada.  Também há entidades pré-compiladas, o que significa que o comportamento não pode ser ajustado.  Essas entidades são abordadas no [Tutorial de entidades pré-compiladas](./7-built-in-entities.md).

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação.
2. Em Resposta, digite 'What's your name?'.
3. Em Entidades esperadas, digite $name. Clique em Salvar.
    - Esse valor significa que, se essa pergunta for feita, e a resposta do usuário não tiver nenhuma entidade detectada, o bot deverá assumir que a resposta do usuário é essa entidade.
    - A entidade é automaticamente adicionada como uma entidade desqualificadora. 
2. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
3. Em Resposta, digite 'Hello $name'.
    - A entidade é automaticamente adicionada como uma entidade necessária.
4. Clique em Salvar.

Agora você tem duas ações.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione 'What's your name?'
    - A resposta 'Hello $name' não pode ser selecionada porque requer que a entidade $name seja definida e $name não esteja na memória do bot.
2. Insira 'david'. 
    - O nome é realçado como uma entidade. Isso ocorre devido a heurística que configuramos acima para selecionar a resposta como a entidade.
5. Clique em Ações de Pontuação
    - O valor do nome agora está na memória do bot.
    - 'Hello $name' agora está disponível como uma resposta. 
6. Selecione 'Hello $name'.
7. Clique em Ensino Concluído.

Aqui estão dois exemplos em que o modelo de extração de entidade de aprendizado de máquina identifica um nome, assim a heurística "entidade esperada" não é acionada.

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Insira 'my name is david'.
    - O modelo identifica david como a entidade de nome porque já constatou essa palavra antes.
2. Clique em Ações de Pontuação
3. Selecione 'Hello $name'.
4. Insira 'meu nome é susan'.
    - O modelo identifica susan como o nome porque já constatou esse padrão.
2. Clique em “Score Actions” (Pontuar ações).
2. Selecione 'Hello susan'.
3. Clique em “Done Teaching” (Ensino concluído).

Nos exemplos a seguir, a heurística "entidade esperada" é disparada, mas está incorreta. Os exemplos mostram como fazer uma correção.

1. Digite 'call me jose'.
    - O modelo não reconhece o nome como uma entidade.
2. Clique em jose e selecione o nome.
3. Clique em Ações de Pontuação.
4. Selecione hello $name.
5. Clique em Ensino Concluído.
1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Insira 'hello'.
3. Em resposta a 'what's your name', digite 'I am called frank'.
    - A frase inteira é realçada. Isso ocorre porque o modelo de estatística não localizou um nome, de modo que a heurística acionou e selecionou a resposta inteira como o nome de entidade.
2. Para corrigir isso, clique na frase realçada e no ícone vermelho da lixeira. 
3. Clique para selecionar frank, depois clique no nome.
2. Clique em Ações de Pontuação
3. Selecione 'Hello $name'.
4. Clique em “Done Teaching” (Ensino concluído).

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades negáveis](./5-negatable-entities.md)
