---
title: Como adicionar entidades pré-compiladas a um modelo de Aprendiz de Conversa – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como adicionar entidades pré-compiladas a um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cdd9ad16096c85db21829840b2bfd7acaced5942
ms.sourcegitcommit: db2cb1c4add355074c384f403c8d9fcd03d12b0c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51683485"
---
# <a name="how-to-add-pre-built-entities"></a>Como adicionar entidades predefinidas
Este tutorial mostra como adicionar entidades "pré-comiladas" ao modelo de Aprendiz de Conversa.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 7](https://aka.ms/cl-tutorial-07-preview)](https://aka.ms/blis-tutorial-07)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

Entidades predefinidas reconhecem tipos comuns de entidades, como números, datas, valores monetários e outros.  Diferentemente de entidades personalizadas, funcionam de maneira imediata e não exigem qualquer treinamento.  Diferentemente de entidades personalizadas, esse comportamento não pode ser alterado.  Por padrão, entidades predefinidas têm valores múltiplos, ou seja, a memória do bot acumulará toda instância identificada da entidade.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em Nome, insira BuiltInEntities. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Clique na lista suspensa EntityType e selecione datetimev2.
    - Opções programáveis e negativas são desativadas, porque elas não se aplicam a entidades pré-construídas.
3. Clique em Criar.

![](../media/tutorial7_entities_a.PNG)

### <a name="create-two-actions"></a>Crie duas ações

1. Clique em Ações e, em seguida, em Nova Ação.
1. Em resposta, digite ' a data é $builtin-datetimev2'.
1. Em entidades necessários, insira ' $builtin-datetimev2'.
1. Clique em Criar.

![](../media/tutorial7_actions_a.PNG)

Em seguida, crie a segunda ação:

1. Clique em Ações e, em seguida, em Nova Ação para criar uma segunda ação.
1. Em Resposta, digite 'Qual é a data?'.
1. Em entidades desqualificação, insira ' $builtin-datetimev2'.
1. Clique em Criar.

![](../media/tutorial7_actions2_a.PNG)

Agora você tem duas ações.

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione 'Qual é a data?'
2. Insira 'hoje'. 
    - Aviso hoje é marcado, e aparece na segunda linha, uma vez que é uma entidade predefinida e não editável.
5. Clique em “Score Actions” (Pontuar ações).
    - Observe que a data agora é exibida na seção Memória da Entidade. 
    - Se você passar o mouse sobre a data, verá os dados adicionais fornecidos pelo LUIS, que podem ser usados e passar por manipulação adicional no código. 
6. Selecione ' a data é $builtin-datetimev2'.
7. Clique em “Done Teaching” (Ensino concluído).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entradas alternativas](./8-alternative-inputs.md)
