---
title: Como usar o retorno de chamada de detecção de entidade com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o retorno de chamada de detecção de entidade com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f168018a23d03ffb957da2dd1f67881420a21208
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171096"
---
# <a name="how-to-use-entity-detection-callback"></a>Como usar o retorno de chamada de detecção de entidade

Este tutorial mostra o retorno de chamada de detecção de entidade e ilustra um padrão comum para determinar as entidades.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 10](http://aka.ms/cl-tutorial-10-preview)](http://aka.ms/blis-tutorial-10)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot `tutorialEntityDetectionCallback` esteja em execução.

    npm run tutorial-entity-detection

## <a name="details"></a>Detalhes
O retorno de chamada de detecção de entidade permite usar um código personalizado para lidar com as regras de negócio relacionadas às entidades. Esta demonstração usa retornos de chamada e Entidades Programáticas para determinar o nome da cidade inserido pelo usuário com um nome aceito -- por exemplo, determinar "a grande maçã" como "Nova Iorque".

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de modelos, clique em Tutorial-10-EntityDetectionCallback. 

### <a name="entities"></a>Entidades

Três entidades são definidas no modelo.

![](../media/tutorial10_entities.PNG)

1. Cidade é uma entidade personalizada que o usuário fornecerá como entrada de texto.
2. CityUnknown é uma Entidade Programática. Essa entidade será preenchida pelo sistema. Se o sistema não souber qual é a cidade, ele copiará a entrada do usuário.
3. CityResolved é a cidade que o sistema conhece. Esta entidade será o nome aceito da cidade, por exemplo, 'a grande maçã' será resolvida para 'Nova Iorque'.

### <a name="actions"></a>Ações

Três ações são definidas no modelo.

![](../media/tutorial10_actions.PNG)

1. A primeira ação é “Qual cidade você deseja?”
2. A segunda é “Não conheço essa cidade, $CityUknown. Qual cidade você deseja?”
3. A terceira é “Você informou $City e eu determinei isso como $CityResolved”.

### <a name="callback-code"></a>Código do retorno de chamada

Vejamos o código. Você pode encontrar o método EntityDetectionCallback no arquivo em C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial10_callbackcode.PNG)

Essa função é chamada após a resolução da entidade.
 
- A primeira coisa que ela fará é limpar $CityUknown. $CityUknown persistirá somente uma vez, pois ela sempre é limpa no início.
- Em seguida, obtenha a lista de cidades que foram reconhecidas. Pegue a primeira e tente determiná-la.
- A função que a determina (resolveCity) é definida mais acima no código. Ela tem uma lista de nomes de cidades aceitos. Encontra o nome da cidade na lista e ele retorna-o. Caso contrário, examina “cityMap” e retorna o nome mapeado. Se não conseguir encontrar uma cidade, retornará null.
- Por fim, se a cidade foi determinada com um nome, iremos armazená-la na entidade $CityKnown. Caso contrário, exclua o que o usuário informou e preencha a entidade $CityUknown.

### <a name="train-dialogs"></a>Caixas de Diálogo de Treinamento

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “nova iorque”.
    - O texto é reconhecido como uma entidade da cidade.
5. Clique em Ações de Pontuação
    - `City` e `CityResolved` foram preenchidos.
6. Selecione “Você informou $City e eu determinei isso como $CityResolved”.
7. Clique em Ensino Concluído.

Adicione outra caixa de diálogo de exemplo:

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “grande maçã”.
    - O texto é reconhecido como uma entidade da cidade.
5. Clique em Ações de Pontuação
    - `CityResolved` mostra o efeito do código em execução.
6. Selecione “Você informou $City e eu determinei isso como $CityResolved”.
7. Clique em Ensino Concluído.

Adicione outra caixa de diálogo de exemplo:

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “foo”.
    - Este é um exemplo de cidade que o sistema não conhece. 
5. Clique em Ações de Pontuação
6. Selecione “Não conheço essa cidade, $CityUknown. Qual cidade você deseja?”.
7. Insira “nova iorque”.
8. Clique em Ações de Pontuação.
    - `CityUknown` foi limpo e `CityResolved` é preenchido.
6. Selecione “Você informou $City e eu determinei isso como $CityResolved”.
7. Clique em Ensino Concluído.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Retornos de chamada de sessão](./11-session-callbacks.md)
