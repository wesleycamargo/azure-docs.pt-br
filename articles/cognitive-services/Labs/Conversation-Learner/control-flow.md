---
title: Fluxo de controle de Aprendizado de Conversa - Serviços Cognitivos da Microsoft | Microsoft Docs
titleSuffix: Azure
description: Saiba mais sobre o fluxo de controle de Aprendizado de Conversa.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 592ca82db7e0ab3ff89d25b61f38f8b226f3bc86
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/23/2018
ms.locfileid: "35364202"
---
## <a name="control-flow"></a>Controlar fluxo

Este documento descreve o fluxo de controle de Aprendizado de Conversa (CL), conforme exibido no diagrama abaixo.

![](media/controlflow.PNG)

1. O usuário insere um termo ou frase no bot, por exemplo, “como está o tempo em Seattle?”
1. O CL passa a entrada do usuário para um modelo de aprendizado de máquina que extrai entidades
    - Esse modelo é criado pelo Aprendizado de Conversa e hospedados por www.luis.ai
2. Qualquer entidade extraída, e o texto de entrada do usuário são passados para o método de retorno de chamada de detecção de entidade no código do bot.
    - Esse código pode definir/limpar/manipular os valores da entidade
1. A rede neural do CL, em seguida, usa a saída da extração de entidade e a entrada do usuário e atribui uma pontuação de todas as ações definidas no bot
    - Neste exemplo, a ação de probabilidade mais alta é fornecer a previsão do tempo:

![](media/controlflow_forecast.PNG)

5. A ação selecionada, nesse caso, requer uma chamada à API para recuperar a previsão do tempo. 
6. Essa API, que havia sido registrada usando o método CL.AddAPICallback, é então invocada.  O resultado dessa API é, em seguida, retornado ao usuário como uma mensagem, por exemplo, “Ensolarado uma temperatura máxima de 19 °C”.
7. A chamada é feita para a rede neural para determinar a próxima ação com base na etapa anterior.
8. A rede neural, depois, prevê o próximo conjunto de ações possíveis e a ação selecionada é apresentada ao usuário, nesse caso, “Algo mais?”

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Como ensinar com o Aprendizado de Conversa ](./how-to-teach-cl.md)
