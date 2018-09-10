---
title: Como usar entidades negáveis com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades negáveis com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171394"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Como usar entidades negáveis com um modelo de Aprendiz de Conversa

Este tutorial demonstra a propriedade "negável" das entidades.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 5](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Marque uma ação como "negável" se o usuário puder "limpar" um valor de entidade, como em "Não, não quero $entity" ou "não, não $entity." Por exemplo, "não, não quero sair de Boston".

Concretamente, se a propriedade "negável" de uma entidade for definida:

- Ao rotular menções de entidade, permite que você rotule instâncias normais (positivas) de uma entidade e instâncias "negativas" da entidade
- O LUIS aprende dois modelos de entidade: um para instâncias positivas e outro para instâncias negativas
- O efeito de uma instância negativa de uma entidade é limpar esse valor da variável de entidade (se houver)

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em Nome, insira NegatableEntity. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira o nome.
3. Marque Negável.
    - Essa propriedade indica que o usuário poderá fornecer um valor para a entidade, ou informar que algo *não* é o valor da entidade. No último caso, isso resultará na exclusão de um valor correspondente da entidade.
3. Clique em Criar.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite "Não sei o seu nome".
3. Em Entidades de Desqualificação, insira o nome.
3. Clicar em Criar

Em seguida, crie a segunda ação.

1. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
3. Em Resposta, digite "Sei o seu nome. É $name".
4. Clicar em Criar

Agora você tem duas ações.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Eu não sei o seu nome"
    - A classificação é 100% porque é a única ação válida.
2. Insira "meu nome é david"
3. Selecione "david" e escolha o rótulo "+name"
    - Há duas instâncias de 'nome': '+name' e '-name'.  (+) Mais, adiciona ou substitui o valor. (-) Menos, remove o valor.
5. Clique em Ações de Pontuação
    - O valor do nome agora está na memória do bot.
    - "Sei o seu nome. É $name" é a única resposta disponível. 
6. Selecione "Sei o seu nome. É $name".

Vamos tentar limpar a entidade negável:

7. Insira "meu nome não é david".
    - Observe que "não" está selecionado como baseado em nome no padrão anterior. Este rótulo está incorreto.
2. Clique em "não" e, depois, no x vermelho. 
3. Clique em "david".
    - Agora, isso é uma entidade negativa comunicando que isso não é o valor da entidade de nome.
2. Selecione "-name".
3. Clique em Ações de Pontuação.
    - Observe que o valor foi excluído da memória.
2. Selecione "Não sei o seu nome", que é a única ação.

Em seguida, mostramos como um novo valor para o nome pode ser inserido.

3. Insira "john" como o nome. Depois, selecione "john" e clique no nome.
4. Clique em Ações de Pontuação.
5. Selecione "Sei o seu nome. É $name".

Agora, tente substituir o nome inserido.

6. Insira 'meu nome é susan'.
7. Selecione "Sei o seu nome. É $name".
7. Clique em Ações de Pontuação.
8. Aviso **susan** substituiu **john** nos valores de entidade.
9. Insira 'meu nome não é susan'.
    - Observe que o sistema rotulou essa instância como negativa.
2. Clique em Ações de Pontuação.
3. Selecione "Não sei o seu nome", que é a única ação.
7. Clique em Ensino Concluído.

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades com vários valores](./6-multi-value-entities.md)
