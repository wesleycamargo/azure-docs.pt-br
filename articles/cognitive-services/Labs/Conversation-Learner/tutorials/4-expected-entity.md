---
title: Como usar a propriedade "entidade esperada" das ações do Aprendiz de conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar a propriedade "entidade esperada" de um aplicativo de Aprendiz de conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 83911eba8112cf356af8c4cd562a87f746fbabc5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364243"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Como usar a propriedade "Entidade esperada" das ações

Este tutorial demonstra o campo "entidade esperada" das ações.

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Use o campo "entidade esperada" de uma ação para comunicar com o sistema que você espera a resposta do usuário para uma ação que será para o estado de uma entidade.

Concretamente, se o campo "entidade esperada" de uma ação for definido como $entity, então, na próxima declaração do usuário, o sistema irá:

1. Primeiro, como de costume, tentará localizar entidades usando o modelo de extração de entidade baseado em aprendizado de máquina
2. Se nenhuma entidade for encontrada na etapa 1, então, como uma heurística, atribua a declaração do usuário como $entity.
3. Chame `EntityDetectionCallback` como de costume, e vá para a seleção de ação.

## <a name="steps"></a>Etapas

### <a name="create-the-application"></a>Criar o aplicativo

1. Na Interface do Usuário da Web, clique em Novo Aplicativo
2. Em Nome, digite ExpectedEntities. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da entidade, insira o nome.
3. Clicar em Criar

Observe que o tipo de entidade é 'custom', ou seja, a entidade pode ser treinada.  Também há entidades predefinidas, o que significa que seu comportamento não pode ser ajustado, isso é, abordado em outro tutorial.

![](../media/tutorial4_entities.PNG)

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite 'What's your name?'.
3. Em Entidades esperadas, digite $name. Clique em Salvar.
    - Isso significa que se essa pergunta for feita, a resposta do usuário não terá nenhuma entidade detectada. O bot deve assumir que toda a resposta do usuário é esta entidade.
2. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
3. Em Resposta, digite 'Hello $name'.
    - Observe que a entidade é automaticamente adicionada como uma entidade desqualificadora. 
4. Clique em Salvar

Agora você tem duas ações.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione 'What's your name?'
    - Observe que a resposta 'Hello $name' não pode ser selecionada porque ela requer que a entidade $name seja definida, e $name não está na memória do bot.
2. Insira 'david'. 
    - Observe que o nome é realçado como uma entidade. Isso ocorre devido a heurística que configuramos acima para selecionar a resposta como a entidade.
5. Clique em Ações de Pontuação
    - Observe que o valor de nome agora está na memória do bot.
    - 'Hello $name' agora está disponível como uma resposta. 
6. Selecione 'Hello $name'.
7. Clique em Ensino Concluído.

Aqui estão dois exemplos em que o modelo de extração de entidade de aprendizado de máquina identifica um nome, assim a heurística "entidade esperada" não é acionada.

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Insira 'my name is david'.
    - Observe que ela identifica david como o nome da entidade porque já viu essa palavra antes.
2. Clique em Ações de Pontuação
3. Selecione 'Hello $name'.
4. Insira 'meu nome é susan'.
    - Observe que ela identifica susan como o nome porque já viu esse padrão.
2. Clique em Ações de Pontuação.
2. Selecione 'Hello susan'.
3. Clique em Ensino Concluído.

Aqui estão dois exemplos adicionais em que a heurística "entidade esperada" é disparada, mas está incorreta, e como podemos fazer uma correção.

1. Digite 'call me jose'.
    - Observe que ela não reconhece o nome como uma entidade.
2. Clique em jose e selecione o nome.
3. Clique em Ações de Pontuação.
4. Selecione hello $name.
5. Clique em Ensino Concluído.
1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Insira 'hello'.
3. Em resposta a 'what's your name', digite 'I am called frank'.
    - Observe que toda a frase é realçada. Isso ocorre porque o modelo de estatística não localizou um nome, de modo que a heurística acionou e selecionou a resposta inteira como o nome de entidade.
2. Para corrigir, clique na frase realçada, e depois clique no x vermelho. 
3. Clique para selecionar frank, depois clique no nome.
2. Clique em Ações de Pontuação
3. Selecione 'Hello $name'.
4. Clique em Ensino Concluído.

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades negáveis](./5-negatable-entities.md)
