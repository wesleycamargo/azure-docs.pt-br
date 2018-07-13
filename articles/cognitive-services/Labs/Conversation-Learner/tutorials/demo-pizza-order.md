---
title: Aplicativo de demonstração Aprendiz de Conversa, pedido de pizza – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um aplicativo de demonstração Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 99cd89c4f4430f2d65ed0963e3092d51a83842d7
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364265"
---
# <a name="demo-pizza-order"></a>Demonstração: pedido de pizza
Esta demonstração ilustra um bot de pedido de pizza. Ele dá suporte ao pedido de uma única pizza com esta funcionalidade:

- reconhecer as coberturas de pizza em elocuções do usuário
- verificar se as coberturas de pizza estão no estoque ou fora de estoque e responder adequadamente
- reconhecer as coberturas de pizza de um pedido anterior e oferecer-se para iniciar um novo pedido com as mesmas coberturas

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de pedido de pizza esteja em execução

    npm run demo-pizza

### <a name="open-the-demo"></a>Abra a demonstração

Na lista Aplicativos da interface do usuário da Web, clique no TutorialDemo de Pedido de Pizza. 

## <a name="entities"></a>Entidades

Criamos três entidades.

- Coberturas: acumulará as coberturas solicitadas pelo usuário. Inclui as coberturas válidas que estão em estoque. Verifica se uma cobertura está no estoque ou fora de estoque.
- OutofStock: isso é usado para comunicar ao usuário que a cobertura selecionada não está em estoque.
- LastToppings: após um pedido, essa entidade é usada para oferecer ao usuário a lista de coberturas em seu pedido.

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Ações

Você criou um conjunto de ações, incluindo a solicitação do que o usuário deseja em sua pizza, informando-o do que foi adicionado até o momento, etc.

Também há duas chamadas à API:

- FinalizeOrder: para fazer o pedido da pizza
- UseLastToppings: para migrar as coberturas do pedido anterior 

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Caixas de Diálogo de Treinamento
Definimos algumas caixas de diálogo de treinamento. 

![](../media/tutorial_pizza_dialogs.PNG)

Por exemplo, vamos testar uma sessão de ensino.

1. Clique em Diálogos de Treinamento e, em seguida, em Novo Diálogo de Treinamento.
1. Insira 'pedir uma pizza'.
2. Clique em Ação de Pontuação.
3. Clique para selecionar 'o que você gostaria em sua pizza?'
4. Insira 'cogumelos e queijo'.
    - Observe que o LUIS rotulou ambos como Coberturas. Se isso não estiver correto, você poderá clicar para realçar e corrigi-lo.
    - O sinal '+' ao lado da entidade significa que ela está sendo adicionada ao conjunto de coberturas.
5. Clique em Ações de Pontuação.
    - Observe que cogumelos e queijo não estão na memória de Coberturas.
3. Clique para selecionar 'você tem $Toppings em sua pizza'
    - Observe que essa é uma ação sem espera e, portanto, o bot solicitará a próxima ação.
6. Selecione 'Deseja mais alguma coisa?'
7. Insira 'remover cogumelos e adicionar pimenta'.
    - Observe que **cogumelos** tem um sinal '-' ao lado para que ele seja removido. O termo pimenta tem um '+' para ser adicionado às coberturas.
2. Clique em Ação de Pontuação.
    - Observe que **pimenta** agora está em negrito, pois ela é nova. O termo **cogumelos** foi riscado.
8. Clique para selecionar 'você tem $Toppings em sua pizza'
6. Selecione 'Deseja mais alguma coisa?'
7. Insira 'adicionar ervilhas'.
    - Ervilhas são um exemplo de uma cobertura que está fora de estoque. Observe que elas ainda são rotuladas como uma cobertura.
2. Clique em Ação de Pontuação.
    - As ervilhas são mostradas como OutOfStock.
    - Para ver como isso aconteceu, vamos abrir o código em C:\<\installedpath>\src\demos\demoPizzaOrder.ts. Observe também o método EntityDetectionCallback. Esse método é chamado após cada cobertura para ver se ela está em estoque. Caso contrário, ele a limpa do conjunto de coberturas e adiciona-a à entidade OutOfStock. A variável inStock é definida acima desse método que tem a lista de coberturas em estoque.
6. Selecione 'Não temos $OutOfStock'.
7. Selecione 'Deseja mais alguma coisa?'
8. Insira 'não'.
9. Clique em Ação de Pontuação.
10. Selecione a chamada à API 'FinalizeOrder'. 
    - Isso chamará a função 'FinalizeOrder' definida no código. Isso limpa as coberturas e retorna 'seu pedido está sendo processado'. 
2. Insira 'fazer outro pedido'. Estamos começando um novo pedido.
9. Clique em Ação de Pontuação.
    - Observe que queijo e pimenta estão na memória como coberturas do último pedido.
1. Selecione 'Deseja $LastToppings'.
2. Insira 'sim'
3. Clique em Ação de Pontuação.
    - O bot deseja executar a ação UseLastToppings. Esse é o segundo dos dois métodos de retorno de chamada. Ele copiará as coberturas do último pedido para coberturas e limpar as últimas coberturas. Essa é uma maneira de lembrar o último pedido e fornecer essas coberturas como opções, caso o usuário informe que deseja outra pizza.
2. Clique para selecionar 'você tem $Toppings em sua pizza'.
3. Selecione 'Deseja mais alguma coisa?'
8. Insira 'não'.
4. Clique em Ensino Concluído.

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Demonstração – inicializador de aplicativos VR](./demo-vr-app-launcher.md)
