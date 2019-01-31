---
title: Fluxo de controle de Aprendizado de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre o fluxo de controle de Aprendizado de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 07f4506b7dd0ac8ca0462e2a418983e561859c91
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55208373"
---
## <a name="control-flow"></a>Controlar fluxo

Este documento descreve o fluxo de controle de Aprendizado de Conversa (CL), conforme exibido no diagrama abaixo.

![](media/controlflow.PNG)

1. O usuário insere um termo ou frase no bot, por exemplo, “como está o tempo em Seattle?”
1. O CL passa a entrada do usuário para um modelo de aprendizado de máquina que extrai entidades
    - Esse modelo é criado pelo Aprendizado de Conversa e hospedados por www.luis.ai
1. Qualquer entidade extraída, e o texto de entrada do usuário são passados para o método de retorno de chamada de detecção de entidade no código do bot.
    - Esse código pode definir/limpar/manipular os valores da entidade
1. A rede neural do CL, em seguida, usa a saída da extração de entidade e a entrada do usuário e atribui uma pontuação de todas as ações definidas no bot
    - Neste exemplo, a ação de probabilidade mais alta é fornecer a previsão do tempo:

    ![](media/controlflow_forecast.PNG)

1. A ação selecionada, nesse caso, requer uma chamada à API para recuperar a previsão do tempo. 
1. Essa API, que foi registrada usando o método CL.AddCallback, é então invocada.  O resultado dessa API é, em seguida, retornado ao usuário como uma mensagem, por exemplo, “Ensolarado uma temperatura máxima de 19 °C”.
1. A chamada é feita para a rede neural para determinar a próxima ação com base na etapa anterior.
1. A rede neural, depois, prevê o próximo conjunto de ações possíveis e a ação selecionada é apresentada ao usuário, nesse caso, “Algo mais?”

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como ensinar com o Aprendizado de Conversa ](./how-to-teach-cl.md)
