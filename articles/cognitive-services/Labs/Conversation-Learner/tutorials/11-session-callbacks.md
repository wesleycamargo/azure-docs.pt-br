---
title: Como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 73fb2a3f10476550bbe6bd20c387a1cc2a7727d8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246473"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Como usar retornos de chamada da sessão com um modelo de Aprendiz de Conversa

Este tutorial ilustra os retornos de chamada onSessionStart e onSessionEnd.

## <a name="video"></a>Vídeo

[![Versão prévia do Tutorial 11](https://aka.ms/cl-tutorial-11-preview)](https://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Requisitos
Este tutorial requer que o bot `tutorialSessionCallbacks` esteja em execução.

    npm run tutorial-session-callbacks

## <a name="details"></a>Detalhes
Ele apresenta o conceito de uma sessão, como as sessões são lidadas por padrão e como você pode substituir esse comportamento.

Uma sessão é uma conversa com o bot. Ela pode ter várias ativas, mas não há longas interrupções na conversa (por exemplo, 30 minutos).  Consulte a página de ajuda em "Limites” para ver a duração de tempo limite da sessão padrão.

Se houver longas interrupções, o bot irá para a próxima sessão.  Iniciar uma nova sessão coloca a rede neural recorrente em seu estado inicial.  Por padrão, todos os valores de entidade também são limpos, embora esse comportamento possa ser alterado (ilustrado abaixo).

### <a name="open-the-demo"></a>Abrir a demonstração

Na lista Modelo, clique em Tutorial-11-SessionCallbacks. 

### <a name="entities"></a>Entidades

Quatro entidades são definidas no modelo.

![](../media/tutorial11_entities.PNG)

Algo a notar é que BotName é uma Entidade de Programação.  Essa entidade será definida pelo bot no horário de início da sessão.

### <a name="actions"></a>Ações

Quatro ações são definidas no modelo.

![](../media/tutorial11_actions.PNG)

Primeiro, este tutorial mostrará como controlar os valores de entidade no início da sessão, por exemplo, definir a entidade BotName antes do usuário informar qualquer coisa.

Segundo, o tutorial mostrará como manter os valores entre as sessões.  Neste tutorial, vamos supor que o nome de usuário e o número de telefone permanecem os mesmos entre as sessões, mas o local pode mudar.  Portanto, manteremos o nome e o número de telefone entre as sessões, mas limparemos o local do usuário.

### <a name="train-dialog"></a>Caixa de Diálogo de Treinamento

Veja uma caixa de exemplo. Esta é uma sessão, ou seja, não há nenhuma longa interrupção na caixa de diálogo.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Código para retornos de chamada

O código para os métodos de retorno de chamada está no arquivo c:\<installedpath>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Ambos os métodos são opcionais.

- OnSessionStartCallback: este método define a entidade BotName.
- OnSessionEndCallback: é possível especificar o que você quer preservar. Isso limpará todas as entidades, exceto o nome de usuário e o telefone.

### <a name="try-the-bot"></a>Experimentar o bot

Troque para a IU da Web e clique em Caixas de Diálogo de Log.

1. Insira “olá”.
2. Sistema: “Olá, sou Botty. Qual é o seu nome?” que tem o nome Botty vindo de OnSessionStartCallback.
3. Insira “jason”.
4. Sistema: “Olá jason. Qual é seu número de telefone?”
5. Digite “555-555-5555”.
6. Sistema: “Você pode informar a Botty sua localização, jason?”
7. Digite “Redmond”.

Esta é uma sessão. Para iniciar uma nova sessão, é necessário encerrar esta sessão. 

1. Clique em Tempo Limite da Sessão. Você irá para a próxima sessão.
    - O botão "Tempo Limite da Sessão" é fornecido para fins de depuração.  Em uma sessão real, uma longa pausa precisaria ocorrer, de aproximadamente 30 minutos.  Consulte a página de ajuda em "Limites” para ver a duração de tempo limite da sessão padrão.
1. Digite “olá”.
2. Sistema: “Você pode informar a Botty sua localização, jason?”
    - O sistema lembrou o nome e o número de telefone.
2. Insira um novo local: “Seattle”.
3. Sistema: “Então, jason, você está em Seattle”.
4. Clique em Concluir Teste.

Vamos voltar para as Caixas de Diálogo de Log. Observe que a última conversa se dividiu em duas porque cada caixa de diálogo log corresponde a uma sessão.  

![](../media/tutorial11_splitdialogs.PNG)

- Na primeira interação, Botty é definido, mas o nome e o número de telefone não.
- A segunda interação mostra o nome e o número de telefone.

Agora você viu como as sessões são lidadas por padrão e como pode substituir o comportamento padrão. 

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Chamadas de API](./12-api-calls.md)
