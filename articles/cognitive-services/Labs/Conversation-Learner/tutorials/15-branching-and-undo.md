---
title: Como usar operações desfazer e ramificação com um aplicativo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar operações desfazer e ramificação com um aplicativo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 724a9e47267e0bd7417130efe54c609ac7a465fb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364235"
---
# <a name="how-to-use-branching-and-undo-operations"></a>Como usar operações desfazer e ramificação
Neste tutorial, apresentaremos as operações desfazer e ramificação.

## <a name="details"></a>Detalhes
- Desfazer: permite que o desenvolvedor "desfaça" uma entrada do usuário ou escolha de ação. Na verdade, "desfazer" realmente cria um novo diálogo e o reproduz até a etapa anterior.  Isso significa que o retorno de chamada de detecção de entidade e chamadas à API no diálogo serão chamados novamente.

- Ramificação: cria um novo diálogo de treinamento que começa da mesma forma que um diálogo de treinamento existente – isso economiza o esforço de reintroduzir manualmente os turnos do diálogo. Na realidade, “ramificação” cria um novo diálogo e reproduz o diálogo de treinamento existente até a etapa selecionada.  Isso significa que o retorno de chamada de detecção de entidade e as chamadas à API no diálogo serão chamados novamente.


## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de pedido de pizza esteja em execução:

    npm run demo-pizza

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista de aplicativos da interface do usuário da Web, clique no Tutorial de demonstração de pedido de pizza. 

Para obter detalhes sobre a demonstração do Pedido de Pizza, consulte o tutorial de pedido de pizza.

## <a name="undo"></a>Desfazer

Vamos desfazer parte do diálogo e recriá-lo a partir dessa etapa.

### <a name="training-dialogs"></a>Diálogos de treinamento
Vamos iniciar uma sessão de treinamento. 

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
1. Insira 'pedir uma pizza'.
2. Clique em Pontuar Ação.
3. Clique para Selecionar 'o que você gostaria na sua pizza?'
4. Insira 'cogumelos e queijo'.
5. Clique em Pontuar Ações.
3. Clique para selecionar 'você tem $Toppings em sua pizza'.
6. Selecione 'Deseja mais alguma coisa?'
7. Insira 'retirar cogumelos e adicionar pimentas'.
    - Selecione os cogumelos e desmarque as entidades Ingredientes. Estamos criando uma ação que iremos desfazer.
2. Clique na Etapa Desfazer.
    - Observe que a última entrada foi removida e voltamos para 'Deseja mais alguma coisa?'  (captura de tela abaixo)
2. Insira 'retirar cogumelos e adicionar pimentas'.
8. Clique para selecionar 'você tem $Toppings em sua pizza'
    - Verifique se que ambas as entidades estão selecionadas corretamente.
2. Clique em Pontuar Ação. Agora, você pode continuar com o diálogo corrigido.
4. Clique em Ensino Concluído.

Você já viu como usar o recurso Desfazer para remover uma entrada e uma ação do usuário.

![](../media/tutorial15_undo.PNG)

## <a name="branch"></a>Ramificação

Como exemplo, vamos abrir um diálogo de treinamento existente e criar outro diálogo de treinamento por ramificação.

1. Clique em Diálogos de Treinamento e, em seguida, em 'novo pedido' para abrir o diálogo existente. 
2. Clique no último 'não' no diálogo (veja a imagem abaixo).
3. clique em Ramificação.
    - Observe que 'não' é removido e todo o diálogo até esse ponto é copiado para um novo. 
    - Isso evita que você entre novamente nos turnos anteriores para explorar uma nova "ramificação" a partir desse ponto.
1. Insira 'sim'.
2. Clique em Pontuar Ação.
3. Clique para selecionar 'Você tem $Toppings em sua pizza'.
6. Selecione 'Deseja mais alguma coisa?'
7. Insira 'não'.
4. Clique em Ensino Concluído.

![](../media/tutorial15_branch.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Controle de versão e marcação](./16-versioning-and-tagging.md)
