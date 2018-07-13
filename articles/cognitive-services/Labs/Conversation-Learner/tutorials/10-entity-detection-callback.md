---
title: Como usar o retorno de chamada de detecção de entidade com um aplicativo Conversation Learner - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar o retorno de chamada de detecção de entidade com um aplicativo Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e41ea5930ff0c8395d0c93aa42e224ebfc894ba8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364231"
---
# <a name="how-to-use-entity-detection-callback"></a>Como usar o retorno de chamada de detecção de entidade

Este tutorial mostra o retorno de chamada de detecção de entidade e ilustra um padrão comum para determinar as entidades.

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot "tutorialEntityDetectionCallback" esteja em execução.

    npm run tutorial-entity-detection

## <a name="details"></a>Detalhes
O retorno de chamada de detecção de entidade permite usar um código personalizado para lidar com as regras de negócio relacionadas às entidades. Nesta demonstração, usaremos retornos de chamada e Entidades Programáticas para determinar o nome da cidade inserido pelo usuário com um nome aceito, por exemplo, determinar "a grande maçã" como "Nova Iorque".

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista Aplicativos, clique em Tutorial-10-EntityDetenctionCallback. 

### <a name="entities"></a>Entidades

Definimos três entidades no aplicativo.

![](../media/tutorial10_entities.PNG)

1. Cidade é uma entidade personalizada que o usuário fornecerá como a entrada de texto.
2. CityUnknown é uma Entidade Programática. Será preenchido pelo sistema. Se o sistema não souber qual é a cidade, ele copiará a entrada do usuário.
3. CityResolved é a cidade que o sistema conhece. Esse será o nome aceito da cidade, por exemplo “a grande maçã” será determinada como “Nova Iorque”.

### <a name="actions"></a>Ações

Criamos três ações. 

![](../media/tutorial10_actions.PNG)

1. A primeira ação é “Qual cidade você deseja?”
2. A segunda é “Não conheço essa cidade, $CityUknown. Qual cidade você deseja?”
3. A terceira é “Você informou $City e eu determinei isso como $CityResolved”.

### <a name="callback-code"></a>Código do retorno de chamada

Vejamos o código. Você pode encontrar o método EntityDetectionCallback no arquivo em C:\<installedpath>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial10_callbackcode.PNG)

Essa função é chamada após a resolução da entidade.
 
- A primeira coisa que ela fará é limpar $CityUknown. $CityUknown persistirá somente uma vez, pois ela sempre é limpa no início.
- Em seguida, podemos obter a lista de cidades que foram reconhecidas. Pegue a primeira e tente determiná-la.
- A função que a determina (resolveCity) é definida mais acima no código. Ela tem uma lista de nomes de cidades aceitos. Encontra o nome da cidade na lista e ele retorna-o. Caso contrário, examina “cityMap” e retorna o nome mapeado. Se não conseguir encontrar uma cidade, retornará null.
- Por fim, se a cidade foi determinada com um nome, iremos armazená-la na entidade $CityKnown. Caso contrário, exclua o que o usuário informou e preencha a entidade $CityUknown.

### <a name="train-dialogs"></a>Caixas de Diálogo de Treinamento

1. Clique em Caixas de Diálogo de Treinamento, em seguida, em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “nova iorque”.
    - Observe que ela é reconhecida como uma entidade de cidade.
5. Clique em Ações de Pontuação
    - Observe que City e CityResolved foram preenchidas.
6. Selecione “Você informou $City e eu determinei isso como $CityResolved”.
7. Clique em Ensino Concluído.

Adicione outra caixa de diálogo de exemplo:

1. Clique em Nova Caixa de Diálogo de Treinamento.
2. Digite 'olá'.
3. Clique em Ações de Pontuação e selecione "Qual cidade você deseja?"
2. Insira “grande maçã”.
    - Observe que ela é reconhecida como uma entidade de cidade.
5. Clique em Ações de Pontuação
    - Observe que CityResolved mostra o efeito de executar o código.
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
    - Observe que CityUknown foi excluída e CityResolved é preenchida.
6. Selecione “Você informou $City e eu determinei isso como $CityResolved”.
7. Clique em Ensino Concluído.

![](../media/tutorial10_bigapple.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Retornos de chamada de sessão](./11-session-callbacks.md)
