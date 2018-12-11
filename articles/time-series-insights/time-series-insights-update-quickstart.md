---
title: Explore o ambiente de demonstração do Azure Time Series Insights (versão prévia) | Microsoft Docs
description: Compreender o ambiente de demonstração do Azure Time Series Insights (versão prévia)
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc
ms.date: 12/03/2018
ms.openlocfilehash: 76bdc122cab5b4ee87717c9c4df9774ecc3bd8f7
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/05/2018
ms.locfileid: "52888766"
---
# <a name="explore-the-azure-time-series-insights-preview-demo-environment"></a>Explorar o ambiente de demonstração do Azure Time Series Insights (versão prévia)

Este guia de início rápido mostra como começar a usar o gerenciador da versão prévia do Azure Time Series Insights (TSI) em um ambiente de demonstração gratuita. Você aprenderá a usar o navegador da Web para visualizar grandes volumes de dados de IoT industriais históricos e conhecerá os principais recursos do gerenciador do Azure Time Series Insights (versão prévia).

O Azure TSI fornece uma plataforma como serviço de ponta a ponta com oferta para ingerir, processar, armazenar e consultar dados em escala de IoT altamente contextualizados e otimizados à série de tempo para a exploração de dados ad-hoc e análise operacional. O Time Series Insights é uma oferta diferenciada adaptada às necessidades exclusivas de implantações de IoT industriais.

O ambiente de demonstração mostra uma companhia de energia elétrica, a Contoso, usando o TSI para encontrar insights acionáveis em seus dados e realizar uma breve análise de causa-raiz. A Contoso opera dois farms de turbina eólica, cada uma com 10 turbinas, e cada turbina contendo 20 sensores relatando dados a cada minuto para Hub IoT do Azure. Os sensores reúnem informações sobre as condições climáticas, tom de folha e posição de guinada, desempenho do gerador, comportamento da embreagem e monitores de segurança.

O TSI (versão prévia) é usado para analisar o conjunto de dados em constante crescimento nos dois últimos anos – atualmente com 40 GB – para entender e prever melhor as falhas críticas e os problemas de manutenção lenta.

Caso não tenha uma assinatura do Azure, crie uma  [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  antes de começar.

## <a name="explore-time-series-insights-explorer-in-a-demo-environment"></a>Descubra o Time Series Insights Explorer em um ambiente de demonstração

1. No navegador, navegue até  [insights.timeseries.azure.com/preview/samples](https://insights.timeseries.azure.com/preview/samples).  

1. Se for solicitado, entre no gerenciador de TSI usando suas credenciais da conta do Azure.

### <a name="demo-step-one"></a>Etapa um da demonstração

1. Vamos dar uma olhada na **turbina eólica n° 7 no farm de vento n° 1**.  

    * **Ação**: atualize o intervalo de exibição para `1/1/17 20:00 – 3/10/17 20:00 (UTC)` e adicione o sensor `Farm 1 > W7 > Generator > GeneratorSpeed`. Depois exiba os valores resultantes.

       ![Início Rápido um][1]

1. Recentemente, **a Contoso descobriu um incêndio em turbina eólica n° 7**. Vamos analisá-lo aqui. Podemos ver que o sensor de alerta de incêndio foi ativado durante o período de incêndio.

    * **Ação**: atualize o intervalo de exibição para `3/9/17 20:00 – 3/10/17 20:00 (UTC)` e adicione o sensor `Safety > FireAlert`.

      ![Início Rápido dois][2]

1. Vamos ver o que mais aconteceu no momento do incêndio. A pressão do óleo e os avisos ativos aumentaram logo antes do incêndio, mas, nesse ponto, já era tarde demais para evitar o problema.

    * **Ação**: adicione os sensores `Pitch > HydraulicOilPressure` e `Pitch > ActiveWarning`.

      ![Início Rápido três][3]

1. Se reduzirmos o zoom, podemos ver que havia sinais levando ao incêndio. Ambos os sensores flutuaram. Isso já tinha acontecido antes?

    * **Ação**: atualize o intervalo de exibição para `2/24/17 20:00 – 3/10/17 20:00 (UTC)`.

      ![Início Rápido quatro][4]

1. Se examinamos os dois anos inteiros de dados, podemos ver um evento de incêndio anterior com os mesmos sinais. Com esses dados, criamos sistemas para detectar precocemente problemas como esse.

    * **Ação**: atualize o intervalo de exibição para `1/1/16 – 12/31/17` (todos os dados).

       ![Início Rápido cinco][5]

### <a name="demo-step-two"></a>Etapa dois da demonstração

1. Outros problemas são mais sutis e mais difíceis de diagnosticar. O Time Series Insights fornece diversos recursos para ajudar a rastrear problemas difíceis. Aqui podemos ver uma interrupção de sensor de aviso na `turbine #6` em `6/25`. Mas o que está ocorrendo de verdade?

    * **Ação**: remova os sensores atuais. Atualize o intervalo de exibição para `6/1/17 20:00 – 7/1/17 20:00 (UTC)` e adicione o `Farm 1 > W6 > Safety > VoltageActuatorSwitchWarning`.

       ![Início Rápido seis][6]

1. O aviso indica um problema com a tensão de saída do gerador. Mas qual é a causa? A saída de energia geral do gerador parece bem em um intervalo granular. Mas, ao agregar os dados, podemos ver uma queda definitiva.

    * **Ação**: torne o `VoltageActuatorSwitchWarning` remoto, adicione `Generator > ActivePower` e atualize o intervalo para `3d`.

       ![Início Rápido sete][7]

1. Se seguirmos em frente no conjunto de dados, podemos ver que não se trata apenas de um problema temporário. O problema continua.

    * **Ação**: estenda o período de tempo para a direita.

       ![Início Rápido oito][8]

1. Vamos analisar mais. Podemos obter outros pontos de dados de sensor para exibir a tensão por fase. Mas todos eles parecem semelhantes. Vamos criar um marcador para ver os valores reais. Parece que três é um problema com a saída da fase 3.

    * **Ação**: `Add Generator > GridVoltagePhase1, 2, & 3`. Crie um marcador no último ponto de dados na área visível.

       ![Início Rápido oito][8]

1. Se exibirmos os três na mesma escala, a queda da fase 3 fica ainda mais óbvia. Neste ponto, estamos prontos para encaminhar esse problema à nossa equipe de manutenção com uma boa indicação sobre a causa do aviso.  

    * **Ação**: atualize a exibição para sobrepor todos os sensores na mesma escala de gráfico.

       ![Início Rápido nove][9]

## <a name="next-steps"></a>Próximas etapas

Você está pronto para criar seu próprio ambiente do Azure TSI (versão prévia):

> [!div class="nextstepaction"]
> [Planejar seu ambiente de TSI do Azure (versão prévia)](time-series-insights-update-plan.md)

<!-- Images -->
[1]: media/v2-update-quickstart/quickstart-one.png
[2]: media/v2-update-quickstart/quickstart-two.png
[3]: media/v2-update-quickstart/quickstart-three.png
[4]: media/v2-update-quickstart/quickstart-four.png
[5]: media/v2-update-quickstart/quickstart-five.png
[6]: media/v2-update-quickstart/quickstart-six.png
[7]: media/v2-update-quickstart/quickstart-seven.png
[8]: media/v2-update-quickstart/quickstart-eight.png
[9]: media/v2-update-quickstart/quickstart-nine.png