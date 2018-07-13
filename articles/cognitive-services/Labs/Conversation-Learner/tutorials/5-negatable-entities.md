---
title: Como usar entidades negáveis com um aplicativo Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades negáveis com um aplicativo Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3d65376c9c43ee1407468f3e8bf3e058048bd556
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364246"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-application"></a>Como usar entidades negáveis com um aplicativo Aprendiz de Conversa

Este tutorial demonstra a propriedade "negável" das entidades.

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Marque uma ação como "negável" se o usuário puder "limpar" um valor de entidade, como em "Não, não quero $entity" ou "não, não $entity." Por exemplo, "não, não quero sair de Boston".

Concretamente, se a propriedade "negável" de uma entidade for definida:

- Ao rotular menções de entidade, permite que você rotule instâncias normais (positivas) de uma entidade e instâncias "negativas" da entidade
- O LUIS aprende dois modelos de entidade: um para instâncias positivas e outro para instâncias negativas
- O efeito de uma instância negativa de uma entidade é limpar esse valor da variável de entidade (se houver)

## <a name="steps"></a>Etapas

### <a name="create-the-application"></a>Criar o aplicativo

1. Na Interface do Usuário da Web, clique em Novo Aplicativo
2. Em Nome, insira NegatableEntity. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira o nome.
3. Verificação de negável.
    - Isso indica que o usuário será capaz de fornecer um valor para a entidade ou dizer que algo *não* é o valor da entidade. No último caso, isso resultará na exclusão de um valor correspondente da entidade.
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
    - Observe que está obtendo uma pontuação de 100%, pois é a única ação válida.
2. Insira "meu nome é david"
3. Selecione "david" e escolha o rótulo "+name"
    - Observe que há duas instâncias de "name"': "+name" e "-name".  O sinal de adição significa que estamos fornecendo esse valor. O sinal de subtração significa que estamos informando ao sistema que algo não é o valor.
5. Clique em Ações de Pontuação
    - Observe que o valor de nome agora está na memória do bot.
    - "Sei o seu nome. É $name" é a única resposta disponível. 
6. Selecione "Sei o seu nome. É $name".

Vamos tentar limpar a entidade negável:

7. Insira "meu nome não é david".
    - Observe que "não" está selecionado como baseado em nome no padrão anterior. Isso está incorreto.
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
