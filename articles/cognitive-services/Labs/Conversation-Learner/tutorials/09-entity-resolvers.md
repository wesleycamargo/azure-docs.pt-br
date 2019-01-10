---
title: Solucionadores de Entidade em um Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar os Solucionadores de Entidade no Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: cc1f9ea18d425679a4db9c7b91a1c0a3c4451d4b
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796471"
---
# <a name="entity-resolvers"></a>Solucionadores de entidade

Este tutorial mostra como usar os Solucionadores de Entidade no Conversation Learner

## <a name="video"></a>Vídeo

[![Visualização de Tutorial de Solucionadores de Entidade](https://aka.ms/cl_Tutorial_v3_EntityResolvers_Preview)](https://aka.ms/cl_Tutorial_v3_EntityResolvers)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes

- O Tipo de Solucionador é uma propriedade opcional de Entidades Personalizadas.
- Solucionadores de Entidade utilizam o poder dos reconhecedores de entidade pré-treinado no LUIS para fornecer detalhes adicionais e maior clareza para sua Entidade Personalizada.

## <a name="steps"></a>Etapas

### <a name="create-a-new-model"></a>Criar um novo modelo

1. Na UI de Web, clique no botão “Novo Modelo”.
2. No campo "Nome", digite “Solucionadores de Entidade", pressione enter ou clique no botão "Criar".

### <a name="create-a-pair-of-entities"></a>Criar um par de Entidades

1. No painel esquerdo, clique em "Entidades" e, em seguida, clique no botão "Nova Entidade".
2. No campo "Nome da Entidade", digite "saída".
3. No menu "Tipo de solucionador" suspenso, selecione "datetimeV2".
4. Clique no botão “Criar”.
5. Clique no botão de "OK" depois de ler o pop-up de informações.
6. Seguindo as mesmas etapas, crie outra entidade chamada "return" e que também tem um tipo de solucionador de "datetimeV2".

### <a name="create-a-pair-of-actions"></a>Criar um par de Ações

1. No painel esquerdo, clique em "Ações" e, em seguida, clique no botão "Nova ação".
2. No tipo de campo "resposta do Bot", "você está saindo na $departure e retornando em $return".
    - IMPORTANTE - ao digitar "$[entityName]" é necessário pressionar entre ou clicar na entidade na lista suspensa, caso contrário, o Conversation Learner irá considerar isso como o texto em vez de uma Entidade.
    - Observe que o campo “Entidades Necessárias" também receberá essas entidades e não pode ser removido. Isso impede que essa ação se torne disponível até que ambas Entidades necessárias estejam presentes.
3. Clique no botão “Criar”.
4. Clique no botão "Nova ação" novamente para criar uma segunda ação.
5. No campo"Resposta do Bot”, digite "Quando você está planejando viajar?".
6. No campo “Entidades Desqualificadoras”, digite “saída” e também digite, “retornar”.
    - Estes informam nosso Bot a NÃO executar esta ação se qualquer uma dessas entidades contiver um valor.
7. Clique no botão “Criar”.


### <a name="training"></a>Treinamento

1. Assista "Treinamento: [Status]" na parte superior esquerda da página e aguarde até ser "Concluído".
    - Se isso levar muito tempo, você pode clicar no link de "Atualização".
    - O treinamento de status "Concluído" é necessário para que funcionem os Solucionadores da Entidade ao treinar o Modelo.
2. No painel esquerdo, clique em "Treinar Diálogos" e, em seguida, clique no botão "Novo Diálogo de Treinamento".
3. Digite na primeira declaração de usuário, "reserve-me um voo" de tipo. 
4. Clique no botão "Ações de Pontuação".
5. Selecione a resposta "Quando você está planejando viajar?".
6. Como usuário, responda, "saindo amanhã e retornando domingo da próxima semana".
    - Observe como o Conversation Learner detectou duas entidades de “Data pré-treinada” nesse turno de usuário.
7. No painel "Detecção de entidade", selecione o texto "amanhã" e rotule-o como "saída"
8. Também rotular o texto "Domingo da próxima semana" como "retorno"
9. Clique no botão "Ações de Pontuação".
    - Observe como o painel "Memória" contém as datas de partida e retorno.
    - Passe o mouse sobre cada uma e observe como as Entidades são objetos de dado que claramente capturam a data do calendário real em vez de “Domingo” ou “amanhã”.
10. Selecione "Você está saindo em..." Resposta do Bot.
11. Clique no botão "Salvar".

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Entradas alternativas](./10-alternative-inputs.md)
