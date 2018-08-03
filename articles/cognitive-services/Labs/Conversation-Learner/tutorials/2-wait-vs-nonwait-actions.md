---
title: Como usar as ações de espera e não espera com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft| Microsoft Docs
titleSuffix: Azure
description: Saiba como usar as ações de espera e não espera com o modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: a8f7ccf79e750c9f3c21c25c50c3e275db7e4195
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39173077"
---
# <a name="wait-and-non-wait-actions"></a>Ações de espera e não espera

Este tutorial mostra a diferença entre as ações de espera e as ações de não espera no Aprendiz de Conversa.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 2](http://aka.ms/cl-tutorial-02-preview)](http://aka.ms/blis-tutorial-02)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- Ação de espera: depois que o sistema executar uma ação de "espera", ele parará de executar ações e aguardará a entrada do usuário.
- Ação de não espera: Depois que o sistema executar uma ação de "não espera", ele escolherá imediatamente outra ação (sem esperar pela entrada do usuário primeiro).

## <a name="steps"></a>Etapas

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em nome, insira WaitNonWait. Em seguida, clique em Criar.

### <a name="create-the-first-wait-action"></a>Criar a primeira ação de Espera

1. Clique em Ações e, em seguida, em Nova Ação.
2. Em Resposta, insira "Qual animal você quer?".
    - Esta é uma ação de Espera, portanto, deixe a caixa Aguardar Resposta marcada.
3. Clique em Criar.

### <a name="create-a-non-wait-action"></a>Criar uma ação de Não Espera

1. Clique em Nova ação
2. Em Resposta, digite 'vacas fazem muuu'.
3. Desmarque a caixa de seleção Aguardar Resposta.
4. Clicar em Criar

### <a name="create-a-second-non-wait-action"></a>Criar uma segunda ação de Não Espera

1. Clicar em Nova ação
2. Em Resposta, digite "Patos fazem quack".
3. Desmarque a caixa de seleção Aguardar Resposta.
4. Clicar em Criar

![](../media/tutorial2_actions.PNG)

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
2. Digite 'olá'
3. Clique em Pontuar Ações e selecione "Qual animal você quer?".
4. Inserir 'vaca'
5. Clique em Pontuar Ações e selecione "Vacas fazem muuu".
    - O bot não esperará pela entrada e tomará a próxima ação.
2. Selecione "Qual animal você quer?".
3. Inserir 'pato'
5. Clique em Pontuar Ações e selecione "patos fazem quack".

![](../media/tutorial2_dialogs.PNG)

> [!NOTE]
> A sequência das respostas do bot com relação às ações de espera e de não espera.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução a entidades](./3-introduction-to-entities.md)
