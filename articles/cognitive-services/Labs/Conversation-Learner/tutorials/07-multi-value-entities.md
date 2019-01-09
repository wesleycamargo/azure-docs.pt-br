---
title: Como usar entidades multivalor com um modelo do Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entidades multivalor com um modelo do Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2363dde7dc2462adde730fa9a4883ffb6c558f3f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796412"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Como usar entidades multivalor com um modelo de Aprendiz de Conversa
Este tutorial mostra a propriedade multivalor de entidades.

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de entidades multivalor](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
Uma entidade multivalor acumula os valores em uma lista em vez de armazenar um único valor.  Essas entidades são úteis quando os usuários podem especificar mais de um valor. Como por exemplo, ingredientes em uma pizza.

As entidades marcadas como multivalores terão cada instância reconhecida da entidade anexada a uma lista na memória do Bot. Acrescenta reconhecimento subsequente para a entidade valor, em vez de substituir.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em "Novo Modelo".
2. No campo "Nome", digite "MultiValueEntities" e pressione enter.
3. Clique no botão "Criar".

### <a name="entity-creation"></a>Criação da entidade

1. No painel esquerdo, clique em "Entidades" e, em seguida, no botão "Nova Entidade".
2. Selecione "Treinada Personalizada" para o "Tipo de Entidade".
3. Digite "ingredientes" para o "Nome da Entidade".
4. Clique na caixa de diálogos multivalores.
    - As entidades multivalores acumulam um ou mais valores da entidade.
5. Marque a caixa de seleção "Negativo".
    - A propriedade "Negativo" foi abordada em outro tutorial.
6. Clique no botão "Criar".

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Criar a primeira ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No campo "Bot de resposta...", digite "Aqui estão seus ingredientes: $toppings"
    - O cifrão principal indica uma referência de Entidade
3. Clique no botão "Criar".

### <a name="create-the-second-action"></a>Criar a segunda ação

1. No painel esquerdo, clique em "Ações" e, em seguida, no botão "Nova ação".
2. No "do Bot de resposta...", digite "Quais ingredientes você gostaria?"
3. No campo "Entidades desqualificadoras", digite "ingredientes".
4. Clique no botão "Criar".

Agora você tem duas ações.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Treinar o modelo

1. No painel esquerdo, clique em "Caixas de Diálogo de Treinamento" e, em seguida, no botão "Nova Caixa de Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "oi".
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Quais ingredientes você gostaria?"
    - O percentual de 100%, como a ação só é válida com base em restrições.
5. No painel de chat, onde está escrito "Digite sua mensagem...", digite "queijo e cogumelos"
6. Clique em 'queijo' e, em seguida, escolha o rótulo "+ ingredientes"
7. Clique em 'cogumelos' e, em seguida, escolha o rótulo "+ ingredientes"
8. Clique no botão "Ações de Pontuação".
9. Em Resposta, digite "Aqui estão seus ingredientes: $Toppings"
10. No painel de chat, onde está escrito "Digite sua mensagem...", digite "adicionar pimenta"
11. Clique em 'pimenta' e, em seguida, escolha o rótulo "+ ingredientes"
12. Clique no botão "Ações de Pontuação".
13. Em Resposta, digite "Aqui estão seus ingredientes: $Toppings"
14. No painel de chat, onde está escrito "Digite sua mensagem...", digite "remover queijo"
15. Clique em 'queijo' e, em seguida, escolha o rótulo "-ingredientes"
16. Clique no botão "Ações de Pontuação".
17. Em Resposta, digite "Aqui estão seus ingredientes: $Toppings"

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entidades previamente treinadas](./08-pre-trained-entities.md)
