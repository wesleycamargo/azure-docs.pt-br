---
title: Como usar Entidades Negáveis com um Modelo do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar Entidades Negáveis com um Modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: fea950e2c13d9b5ce0c3619990961e611edd6626
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55207367"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Como usar Entidades Negáveis com um Modelo do Conversation Learner

Este tutorial demonstra a propriedade "Negável" das Entidades.

## <a name="video"></a>Vídeo

[![Versão prévia de tutorial de Entidades Negáveis](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
A propriedade "Negável" de uma Entidade permite que você rotule as instâncias normal (positiva) e negativa da Entidade, ensine com base em modelos positivos e negativos e limpe o valor de uma Entidade existente. Entidades com o conjunto de propriedades "Negáveis" são chamadas de Entidades Negáveis no Conversation Leaner.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "Novo Modelo."
2. No campo "Nome", digite "NegatableEntity" e pressione enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Personalizado" para o "Tipo de Entidade."
3. Digite "nome" para o "Nome da Entidade."
4. Marque a caixa de seleção "Negável".
    - Marcar essa propriedade permite que o usuário forneça um valor de entidade, ou dizer que algo *não* é um valor de entidade. No último caso, o resultado é a exclusão do valor da entidade correspondente.
5. Clique no botão "Criar".

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova Ação".
2. No campo "Resposta do Bot...", digite "Eu não sei seu nome."
3. No campo "Entidades Desqualificadas", digite "nome."
4. Clique no botão "Criar".

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova Ação".
2. No campo "Resposta do Bot...", digite "Eu sei seu nome. Ele é $name."
3. Clique no botão "Criar".

> [!NOTE]
> Entidade "nome" foi automaticamente adicionada como "Entidades Necessárias" por referência na resposta.

Agora você tem duas ações.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Treinar o Modelo

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde diz "Digite sua mensagem...", digite "olá."
3. Clique no botão "Pontuar Ações".
4. Selecione a resposta "Eu não sei seu nome."
    - O percentual é 100%, como a única Ação válida com base nas restrições.
5. No painel de chat, onde diz "Digite sua mensagem...", digite "Meu nome é Pedro"
6. Selecione "Pedro" e escolha o marcador "+nome"
    - Há duas instâncias para a entidade "nome": "+nome" e "-nome".  (+) Mais, adiciona ou substitui o valor. (-) Menos, remove o valor.
7. Clique no botão "Pontuar Ações".
    - A Entidade "nome" agora está definida como "Pedro" na memória do Modelo, portanto, a Ação "Eu sei seu nome. Ele é $name" está disponível.
8. Selecione a resposta "Eu sei seu nome. Ele é $name."
9. No painel de chat, onde diz "Digite sua mensagem...", digite "Meu nome não é Pedro."
10. Selecione "Pedro" e escolha o rótulo "-nome"
    - Nós estamos selecionando "-nome" para limpar o valor atual da Entidade.
11. Clique no botão "Pontuar Ações".
12. Selecione a resposta "Eu não sei seu nome."
13. No painel de chato, onde diz "Digite sua mensagem...", digite "Meu nome é Susana."
14. Selecione "Susana" e escolha o rótulo "+nome"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades com vários valores](./07-multi-value-entities.md)
