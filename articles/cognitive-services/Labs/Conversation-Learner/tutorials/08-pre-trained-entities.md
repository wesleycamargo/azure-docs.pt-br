---
title: Como adicionar entidades previamente treinadas a um modelo de Conversation Learner – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como adicionar entidades previamente treinadas a um modelo de Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: afa927009e684fa7f8c6217c91dcb589b331b5f5
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55224166"
---
# <a name="how-to-add-pre-trained-entities"></a>Como adicionar entidades previamente treinadas
Este tutorial mostra como adicionar entidades previamente treinadas ao modelo de Conversation Learner.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial entidades previamente treinadas](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Entidades previamente treinadas reconhecem tipos comuns de entidades, como números, datas, valores monetários e outros.  Elas funcionam "out-of-the-box," não exigem qualquer treinamento e seu comportamento não pode ser alterado ao contrário de entidades personalizadas.  Por padrão, entidades previamente treinadas têm vários valores, acumulando todas as instâncias da entidade identificadas.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em “Novo Modelo”.
2. No campo "Nome", digite "PretrainedEntities" e pressione enter.
3. Clique no botão “Criar”.

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecionar "Pré-Trained/datetimeV2" para o "tipo de entidade".
3. Marque a caixa de seleção "Múltiplos valores".
    - As entidades múltiplos valores acumulam um ou mais valores na entidade.
    - Propriedades negáveis estão desabilitadas para entidades previamente treinadas.
4. Clique no botão “Criar”.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo “resposta de Bot...”, digite “A data é $builtin-datetimev2”
3. Clique no botão “Criar”.

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Resposta do bot...", digite "Qual é a data?"
    - Entidades previamente treinadas não podem ser entidades necessárias já que elas são reconhecidas por padrão para todas as declarações de usuário.
3. No campo "Desqualificação autoriza", digite "builtin datetimev2."
4. Clique no botão “Criar”.

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde diz "Digite sua mensagem...", digite "olá."
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Qual é a data?"
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "hoje"
    - A expressão hoje é automaticamente reconhecida por modelos previamente treinados no LUIS.
    - Passar o mouse sobre os valores de entidades previamente treinadas mostra dados adicionais fornecidos pelo LUIS.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Resolvedores de entidade](./09-entity-resolvers.md)
