---
title: 'Início Rápido: Explore o ambiente de demonstração do Azure Time Series Insights Preview | Microsoft Docs'
description: Compreender o ambiente de demonstração do Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: anshan
ms.topic: quickstart
ms.workload: big-data
ms.custom: mvc seodec18
ms.date: 12/03/2018
ms.openlocfilehash: de5e853db6c6a0e98dea9251cc07b526288574e1
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276818"
---
# <a name="quickstart-explore-the-azure-time-series-insights-preview-demo-environment"></a>Início Rápido: Explorar o ambiente de demonstração do Azure Time Series Insights Preview

Este guia de início rápido mostra como usar o gerenciador do Azure Time Series Insights Preview em um ambiente de demonstração gratuita. Você aprende a usar o navegador da Web para visualizar grandes volumes de dados de IoT industriais históricos e conhece os principais recursos do gerenciador do Azure Time Series Insights Preview.

O Time Series Insights é uma oferta de PaaS (plataforma como serviço) de ponta a ponta. Ele pode ingerir, armazenar e consultar dados de escala de IoT otimizados para série temporal e altamente contextualizados. Ele também fornece análise operacional. O Time Series Insights é uma oferta diferenciada adaptada às necessidades exclusivas de implantações de IoT industriais.

O ambiente de demonstração mostra uma empresa de geração de eletricidade, a Contoso. No ambiente, use o Time Series Insights para localizar insights acionáveis nos dados da Contoso e realizar uma breve análise de causa-raiz. A Contoso opera dois farms de turbinas eólicas, cada uma com 10 turbinas. Cada turbina tem 20 sensores que relatam dados a cada minuto para o Hub IoT do Azure. Os sensores reúnem informações sobre condições climáticas, inclinação da pá e posição de guinada, desempenho do gerador, comportamento da embreagem e monitores de segurança.

Você usa o Time Series Insights Preview para analisar o conjunto de dados crescente da Contoso dos dois últimos anos, que está atualmente em 40 GB. Ele pode ajudá-lo a melhor entender e prever falhas críticas e problemas de lentidão na manutenção.

Caso não tenha uma assinatura do Azure, crie uma  [conta gratuita do Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)  antes de começar.

## <a name="explore-the-time-series-insights-explorer-in-a-demo-environment"></a>Explore o Time Series Insights Explorer em um ambiente de demonstração

1. No seu navegador, vá para o  [ambiente da Contoso Wind Farm](https://insights.timeseries.azure.com/preview/samples).  

1. Se solicitado, entre no Time Series Insights Explorer com suas credenciais da conta do Azure.

### <a name="demo-step-1"></a>Etapa 1 da demonstração

1. Vamos dar uma olhada na turbina eólica **W7** na **Usina 1 da Contoso**.  

    * **Ação**: Atualizar o intervalo de exibição para **1/1/17 20:00 a 10/3/17 20:00 (UTC)**, adicione o sensor **Usina 1 da Contoso** > **W7** > **Sistema de Gerador** > **GeneratorSpeed** e, em seguida, exiba os valores resultantes.

       ![Início Rápido um][1]

1. Recentemente, a Contoso descobriu um incêndio em turbina eólica **W7**. Vamos analisá-lo aqui. Podemos ver que o sensor de alerta de incêndio foi ativado durante o incêndio.

    * **Ação**: Atualizar o intervalo de exibição para **9/3/17 20:00 a 10/3/17 20:00 (UTC)** e adicione o sensor **Sistema de Segurança** > **FireAlert**.

      ![Início Rápido dois][2]

1. Vamos ver o que mais aconteceu no momento do incêndio. A pressão do óleo e os avisos ativos aumentaram logo antes do incêndio, mas, nesse momento, já era tarde demais para evitar o problema.

    * **Ação**: Adicionar o sensor **Sistema de Inclinação** > **HydraulicOilPressure** e o sensor **Sistema de Inclinação** > **ActiveWarning**.

      ![Início Rápido três][3]

1. Se reduzirmos o zoom, podemos ver que havia sinais levando ao incêndio. Ambos os sensores flutuaram. Esse problema já tinha acontecido antes?

    * **Ação**: Atualizar o intervalo de exibição para **24/2/17 20:00 a 10/3/17 20:00 (UTC)**.

      ![Início Rápido quatro][4]

1. Se examinamos os dois anos inteiros de dados, podemos ver um evento de incêndio anterior com os mesmos sinais. Com esses dados, podemos criar sistemas para detectar precocemente problemas como esse.

    * **Ação**: Atualizar o intervalo de exibição para **1/1/16 a 31/12/17** (todos os dados).

       ![Início Rápido cinco][5]

### <a name="demo-step-2"></a>Etapa 2 da demonstração

1. Outros problemas são mais sutis e mais difíceis de diagnosticar. O Time Series Insights fornece diversos recursos para nos ajudar a rastrear problemas difíceis. Aqui podemos ver uma interrupção de sensor de aviso na **W6** em **25/6**. Mas o que está ocorrendo de verdade?

    * **Ação**: Remover os sensores de corrente, atualizar o intervalo de exibição para **1/6/17 20:00 a 1/7/17 20:00 (UTC)** e, em seguida, adicionar o sensor **Usina 1 da Contoso** > **W6** > **Sistema de Segurança** > **VoltageActuatorSwitchWarning**.

       ![Início Rápido seis][6]

1. O aviso indica um problema com a tensão de saída do gerador. Mas qual é a causa? A saída de energia geral do gerador parece bem em um intervalo granular. Mas, ao agregar os dados, podemos ver uma queda definitiva.

    * **Ação**: Remover o sensor **VoltageActuatorSwitchWarning**, adicionar o sensor **Sistema Gerador** > **ActivePower** e atualizar o intervalo para **3d**.

       ![Início Rápido sete][7]

1. Se seguirmos em frente no conjunto de dados, veremos que não se trata apenas de um problema temporário. Ele é contínuo.

    * **Ação**: Estender o período de tempo para a direita.

       ![Início Rápido oito][8]

1. Vamos analisar mais. Podemos adicionar outros pontos de dados de sensor para exibir a tensão por fase. Mas todos os pontos de dados parecem comparáveis. Vamos criar um marcador para ver os valores reais. Parece que há um problema com a saída da fase 3.

    * **Ação**: Adicione os sensores **Gerador de Sistema** > **GridVoltagePhase1**, **GridVoltagePhase2** e **GridVoltagePhase3**. Crie um marcador no último ponto de dados na área visível.

       ![Início Rápido oito][8]

1. Se exibirmos os três pontos de dados na mesma escala, a queda da fase 3 ficará ainda mais óbvia. Neste ponto, estamos prontos para encaminhar o problema à nossa equipe de manutenção com uma boa indicação sobre a causa do aviso.  

    * **Ação**: Atualizar a exibição para sobrepor todos os sensores na mesma escala de gráfico.

       ![Início Rápido nove][9]

## <a name="next-steps"></a>Próximas etapas

Você está pronto para criar seu próprio ambiente do Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Planejar o ambiente do Time Series Insights Preview](time-series-insights-update-plan.md)

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
