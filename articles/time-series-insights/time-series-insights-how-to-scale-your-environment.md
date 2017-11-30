---
title: Como dimensionar o ambiente do Azure Time Series Insights | Microsoft Docs
description: "Este artigo descreve como dimensionar seu ambiente do Azure Time Series Insights. Use o Portal do Azure para adicionar ou subtrair capacidade em uma SKU de preços."
services: time-series-insights
ms.service: time-series-insights
author: sandshadow
ms.author: edett
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: edcd9561778998c4df09cc5014f8b8ba81c0e369
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="how-to-scale-your-time-series-insights-environment"></a>Como dimensionar o ambiente do Time Series Insights

Este artigo descreve como alterar a capacidade do seu ambiente Time Series Insights usando o Portal do Azure. A capacidade é o multiplicador aplicado à taxa de ingresso, à capacidade de armazenamento e ao custo associado com a SKU selecionada. 

Você pode usar o Portal do Azure para aumentar ou diminuir a capacidade de um determinada SKU de preços. 

No entanto, não é permitido alterar a SKU da camada de preços. Por exemplo, um ambiente com uma SKU de preço S1 não pode ser convertido em um S2 ou vice-versa. 


## <a name="s1-sku-ingress-rates-and-capacities"></a>As taxas e as capacidades de entrada de SKU S1

| Capacidade de SKU S1 | Taxa de entrada | Capacidade de armazenamento máxima
| --- | --- | --- |
| 1 | 1 GB (1 milhão de eventos) | 30 GB (30 milhões de eventos) por mês |
| 10 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |

## <a name="s2-sku-ingress-rates-and-capacities"></a>As taxas e as capacidades de entrada de SKU S2

| Capacidade de SKU S2 | Taxa de entrada | Capacidade de armazenamento máxima
| --- | --- | --- |
| 1 | 10 GB (10 milhões de eventos) | 300 GB (300 milhões de eventos) por mês |
| 10 | 100 GB (100 milhões de eventos) | 3 TB (3 bilhões de eventos) por mês |

As capacidades são dimensionadas linearmente, portanto, uma SKU S1 com capacidade 2 dá suporte a 2 GB (2 milhões) de eventos por taxa de entrada por dia e 60 GB (60 milhões de eventos) por mês.

## <a name="change-the-capacity-of-your-environment"></a>Alterar a capacidade do seu ambiente
1. No Portal do Azure, localize e selecione seu ambiente Time Series Insights. 

2. No menu de seu ambiente do Time Series Insights, selecione **Configurar**.

   ![configure.png](media/scale-your-environment/configure.png)

3. Ajuste o controle deslizante **Capacidade** para selecionar a capacidade que atenda aos requisitos de suas taxas de entrada e capacidade de armazenamento. Observe que a **Taxa de entrada**, a **Capacidade de armazenamento** e o **Custo estimado** são atualizadas dinamicamente para mostrar o impacto da alteração. 

   ![Controle deslizante](media/scale-your-environment/slider.png)

   Como alternativa, você pode digitar o número do multiplicador de capacidade na caixa de texto à direita do controle deslizante. 

4. Selecione **Salvar** para dimensionar o ambiente. O indicador de progresso é exibido momentaneamente até que a alteração seja confirmada. 

## <a name="next-steps"></a>Próximas etapas
> [!div class="nextstepaction"]
> [Verifique se a nova capacidade é suficiente para evitar a limitação](time-series-insights-diagnose-and-solve-problems.md).
