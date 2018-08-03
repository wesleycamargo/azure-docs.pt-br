---
title: Como usar entradas alternativas com um Aprendiz de conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar entradas alternativas com o Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 8d3b3f419ceacbb9a6fe2b19cf68ea6873de536f
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171010"
---
# <a name="how-to-use-alternative-inputs"></a>Como usar entradas alternativas

Este tutorial mostra como usar o campo “entradas alternativas” para a entrada do usuário na interface de ensino.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 8](http://aka.ms/cl-tutorial-08-preview)](http://aka.ms/blis-tutorial-08)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de tutorial geral esteja em execução

    npm run tutorial-general

## <a name="details"></a>Detalhes
“Entradas alternativas” são enunciados de usuário alternativos que o usuário pode ter dito em um determinado ponto em um diálogo de treinamento. Entradas alternativas permitem que você especifique de forma mais compacta as variações do que um usuário pode dizer, sem ter que listar cada variação em um diálogo de treinamento separado.

## <a name="steps"></a>Etapas

### <a name="create-the-model"></a>Criar o modelo

1. Na interface do usuário da Web, clique em Novo Modelo
2. Em Nome, digite AlternativeInputs. Em seguida, clique em Criar.

### <a name="create-an-entity"></a>Criar uma entidade

1. Clique em Entidades e, em seguida, em Nova Entidade.
2. Em Nome da Entidade, insira a cidade.
3. Clique em Criar.

### <a name="create-three-actions"></a>Criar três ações

1. Clique em Ações e, em seguida, em Nova Ação
2. Em Resposta, digite "Qual cidade você deseja?".
3. Em Entidades de Desqualificação, insira $city.
3. Clicar em Criar

Em seguida, crie a segunda ação:

1. Clique em Ações e, em seguida, em Nova Ação.
3. Em Resposta, digite "O clima em $city é provavelmente ensolarado".
4. Entidades Obrigatórias, insira $city.
4. Clique em Criar.

Crie a terceira ação:

1. Clique em Ações e, em seguida, em Nova Ação.
3. Em resposta, digite “Tente perguntar sobre o tempo”.
    - Isso seria em resposta a uma pergunta do usuário, como “o que o sistema pode fazer?”
4. Em Entidades de Desqualificação, insira $city.
4. Clicar em Criar

Agora você tem três ações.

### <a name="train-the-bot"></a>Treinar o bot

1. Clique em Caixas de Diálogos de Treinamento, depois em Nova Caixa de Diálogo de Treinamento.
2. Digite "como está o clima".
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “denver”.
3. Clique duas vezes em "denver" e selecione a cidade.
    - Isso a marca como uma entidade de cidade.
5. Clique em Ações de Pontuação
    - 'denver' agora está presente na entidade da cidade. 
6. Selecione "O clima em $city é provavelmente ensolarado".
7. Clique em Ensino Concluído.

Adicione outro exemplo de diálogo:

1. Clique em Nova Ação, depois Novo Diálogo de Treinamento.
2. Digite “o que você pode fazer?”.
3. Clique em Pontuar Ações e selecione “Tente perguntar sobre o tempo”.
2. Digite "Como está o tempo em Seattle".
3. Clique duas vezes em "seattle" e selecione a cidade.
    - Isso a marca como uma entidade de cidade.
5. Clique em Ações de Pontuação
    - 'seattle' agora está presente na entidade da cidade. 
6. Selecione "O clima em $city é provavelmente ensolarado".
7. Clique em Ensino Concluído.

Vamos ver o que acontece se o usuário diz algo semanticamente semelhante ao acima:

1. Clique em Nova Ação, depois Novo Diálogo de Treinamento.
2. Digite “ajuda”.
3. Clique em Ações de Pontuação.
    - As classificações para as duas respostas potenciais são muito próximas. Isso informa que o modelo está confuso sobre o limite entre as duas ações.
6. Clique em Abandonar Ensino e Confirmar.

![](../media/tutorial8_closescores.png)

Nesse caso, ajudaria adicionar entradas alternativas para os diálogos. Você pode adicioná-las conforme durante o processo de ensino. Você também pode voltar e adicioná-las posteriormente.

2. Clique em “O que você pode fazer?” em Diálogos de Treinamento.
2. No diálogo, clique em “o que você pode fazer?” para selecioná-lo.
    1. No painel direito, em Detecção de Entidade, em Adicionar entrada alternativa insira duas alternativas:
    1. Insira “quais são minhas opções?”
    2. Insira “Diga-me minhas opções”.
    3. Insira “ajuda”
    1. clique em Enviar Alterações.


![](../media/tutorial8_helpalternates.png)

2. Agora clique em "como está o tempo em seattle".
    1. Em Adicionar entrada alterativa, digite “previsão para seattle”.
    2. Clique duas vezes em "seattle" e selecione a cidade. As entidades de entradas alternativas devem estar presentes e ter o mesmo conjunto de entidades. Não há problema se o conteúdo das entidades for diferente.
    3. Em Adicionar entrada alternativa, digite “hoje vai chover em denver”.
    4. Clique em "denver" e selecione cidade.
    5. clique em Enviar Alterações e Concluído.


Vamos adicionar entradas alternativas para o primeiro diálogo:

1. Clique Diálogo de Treinamento.
2. Clique no diálogo que começa com “o que é o tempo”.
2. Clique para selecionar “o que é o tempo” no painel esquerdo:
    1. Em Adicionar entrada alterativa, digite “previsão do tempo”.
    2. Insira “vai chover?”
    3. Clique em Enviar Alterações.
4. Clique para selecionar “denver” no painel esquerdo:
    1. Em Adicionar entrada alterativa, digite “para denver”.
    2. Insira “previsão para austin”.
        - A frase completa é realçada. Clique na frase, em seguida, no x vermelho. Depois selecione austin e clique em cidade.
        - Clique em Enviar Alterações
    1. Clique em Concluído para que o modelo seja treinado novamente.

![](../media/tutorial8_altcities.png)

Vamos experimentar as variações:

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Digite “quais são os seus recursos”.
3. Clique em Ações de Pontuação.
    - As classificações serão mais decisivas na próxima ação que indicará a certeza do modelo.
2. Selecione “Tente perguntar sobre o tempo”.
6. Clique em Ensino Concluído

Agora você viu como as entradas alternativas podem ser usadas para indicar outras coisas que o usuário possa ter dito. Elas ajudam a evitar a criação de muitos diálogos que são, em grande parte, os mesmos, recolhendo-os em um único diálogo e enumerando o que o usuário pode dizer.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Registras diálogos em log](./9-log-dialogs.md)
