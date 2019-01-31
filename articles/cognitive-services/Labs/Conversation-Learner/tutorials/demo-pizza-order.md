---
title: Demonstração do modelo de Aprendiz de Conversa, pedido de pizza – Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como criar um modelo de Aprendiz de Conversa de demonstração.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3fe11bef6c505771ee1e3f2e12e647eafc7c45d1
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55212436"
---
# <a name="demo-pizza-order"></a>Demonstração: Pedido de pizza
Essa demonstração ilustra um bot de pedido de pizza, que dá suporte a um pedido único de pizza ao:

- reconhecer os sabores de pizza de enunciados do usuário
- gerenciar inventário de sabores e responder adequadamente
- lembrar-se de pedidos anteriores e acelerar a reordenação de uma pizza idêntica

## <a name="video"></a>Vídeo

[![Versão prévia da demostração de pedido de pizza](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder_Preview)](https://aka.ms/cl_Tutorial_v3_DemoPizzaOrder)

## <a name="requirements"></a>Requisitos
Este tutorial exige que o bot de pedido de pizza esteja em execução

    npm run demo-pizza

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista Modelo da interface do usuário da Web, clique no TutorialDemo de Pedido de Pizza. 

## <a name="entities"></a>Entidades

O modelo contém três entidades:

- "Sabores" acumulam ingredientes especificados do cliente, se disponível.
- "Sem estoque" sinaliza que o sabor selecionado pelo usuário está fora de estoque
- “Últimos Sabores" contém o histórico de sabores de pedidos anteriores

![](../media/tutorial_pizza_entities.PNG)

### <a name="actions"></a>Ações

O modelo contém um conjunto de ações que pede ao usuário a seleção de sabor, sabores acumulados e muito mais.

Também são fornecidas duas chamadas de API:

- "Finalizar Pedidor" lida com o preenchimento de pedidos
- "Usar Últimos Sabores" processa as informações de histórico de ingredientes

![](../media/tutorial_pizza_actions.PNG)

### <a name="training-dialogs"></a>Diálogos de treinamento

Várias caixas de diálogo de treinamento são encontradas no modelo.

![](../media/tutorial_pizza_dialogs.PNG)

Vamos treinar o modelo um pouco mais com a criação de outra caixa de diálogo Treinar.

1. No painel esquerdo, clique em "Diálogos de Treinamento" e, em seguida, no botão "Novo Diálogo de Treinamento".
2. No painel de chat, onde está escrito "Digite sua mensagem...", digite "Pedir uma pizza com queijo"
    - A palavra "queijo" foi extraída pelo extrator de entidade.
3. Clique no botão "Ações de Pontuação".
4. Selecione a resposta, "Você tem queijo na sua pizza".
5. Selecione a resposta, “Gostaria de algo mais?”
6. No painel de chat, onde está escrito "Digite sua mensagem...", digite "adicionar cogumelos e pimenta”
7. Clique no botão "Ações de Pontuação".
8. Selecione a resposta, "Você tem queijo, cogumelos e pimenta na sua pizza”.
9. Selecione a resposta, “Gostaria de algo mais?”
10. No painel de chat, onde está escrito "Digite sua mensagem...", digite "remover pimenta e acrescentar calabresa”
11. Clique no botão "Ações de Pontuação".
12. Selecione a resposta, "Você tem queijo, cogumelos e calabresa na sua pizza”.
13. Selecione a resposta, “Gostaria de algo mais?”
14. No painel de chat, onde está escrito "Digite sua mensagem...", digite "adicionar inhame"
15. Clique no botão "Ações de Pontuação".
    - O valor de "inhame" foi adicionado à "Fora de Estoque" pelo código de retorno de chamada de detecção de entidade, uma vez que o texto não correspondeu a nenhum ingredientes com suporte.
16. Selecione a resposta, "Fora de Estoque"
17. Selecione a resposta, “Gostaria de algo mais?”
18. No painel de chat, onde está escrito "Digite sua mensagem...", digite "não".
    - "Não" não está marcado como qualquer tipo de intenção. Em vez disso, selecionaremos a ação relevante com base no contexto atual.
19. Clique no botão "Ações de Pontuação".
20. Selecione a resposta, "Finalizar Pedido"
    - Selecionar essa Ação resultou nos sabores atuais do cliente sendo salvos na entidade “Últimos Sabores” e na exclusão da entidade “Sabores” pelo código de retorno de chamada de Finalizar Pedido.
21. No painel de chat, onde está escrito "Digite sua mensagem...", digite "pedir outra"
22. Clique no botão "Ações de Pontuação".
23. Selecione a resposta, "Gostaria de queijo, cogumelos e calabresa?”
    - Essa Ação agora está disponível devido a entidade "Últimos Sabores" sendo definida.
24. No painel de chat, onde está escrito "Digite sua mensagem...", digite "sim".
25. Clique no botão "Ações de Pontuação".
26. Selecione a resposta, "Usar Últimos Sabores"
27. Selecione a resposta, "Você tem queijo, cogumelos e calabresa na sua pizza”.
28. Selecione a resposta, “Gostaria de algo mais?”

![](../media/tutorial_pizza_callbackcode.PNG)

![](../media/tutorial_pizza_apicalls.PNG)

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Implantando um bot Aprendiz de Conversa](../deploy-to-bf.md)
