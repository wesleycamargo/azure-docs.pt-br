---
title: Como exibir o uso da API de Mapas do Azure | Microsoft Docs
description: Saiba como exibir as métricas para suas chamadas de API do Azure Mapas no portal.
author: dsk-2015
ms.author: dkshir
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: e62d2ff1fdd6bc94244511a2de95c4268a58d6f9
ms.sourcegitcommit: 0fcd6e1d03e1df505cf6cb9e6069dc674e1de0be
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2018
ms.locfileid: "42145657"
---
# <a name="how-to-view-the-azure-maps-api-usage"></a>Como exibir o uso da API do Azure Mapas
Este artigo mostra como exibir as métricas de uso da API para sua conta do Azure Mapas no [portal](https://portal.azure.com). As métricas são mostradas em um formato gráfico conveniente ao longo de uma duração de tempo personalizáveis. 

## <a name="view-metric-snapshot"></a>Exibir instantâneo de métrica 

Você pode ver algumas métricas comuns sobre a página de **visão geral** de sua conta de mapas. Atualmente, ele mostra *Total de Solicitações*, *Total de Erros*, e *Disponibilidade* em um período de tempo selecionável. 

![Visão geral das métricas do Azure Mapas](media/how-to-view-api-usage/portal-overview.png)

Continue para a próxima seção se você precisar personalizar esses gráficos para sua análise específica.


## <a name="view-detailed-metrics"></a>Exibir métricas detalhadas

1. Entre em sua assinatura do Azure no [portal](https://portal.azure.com). 

2. Clique o **Todos os recursos** no lado esquerdo do item de menu e navegue até a sua *conta do Azure Mapas*.

3. Quando sua conta de Mapas for aberta, clique no menu **Métricas** à esquerda.

4. Sobre o painel **Métricas** painel, escolha entre um dos seguintes:

    1. **Disponibilidade**, que mostra a *Média* de disponibilidade de API em um período de tempo, ou 
    2. **Uso**, que mostra como o uso *contagem* para sua conta. 

    ![Métricas do Azure Mapas](media/how-to-view-api-usage/portal-metrics.png)

5. Depois de selecionar as métricas, você pode selecionar o *Intervalo de tempo* selecionando sobre as **últimas 12 horas (automático)** que é o valor padrão. Você também pode selecionar a *Granularidade de tempo*, bem como optar por mostrar a hora como *local* ou *GMT* no mesmo menu suspenso. Clique em **Aplicar**.

    ![Intervalo de tempo das métricas do Azure Mapas](media/how-to-view-api-usage/time-range.png)
 
6. Depois de adicionar sua métrica, você pode então **Adicionar filtro** entre as propriedades relevantes dessa métrica e, em seguida, selecione o valor da propriedade que você deseja ver o gráfico. 

    ![Filtro de Métricas do Azure Mapas](media/how-to-view-api-usage/filter.png)

7. Você também pode **Aplicar a divisão** para sua métrica com base em sua propriedade de métrica selecionada. Isso permite que o gráfico seja dividido em vários gráficos, cada um para cada valor da propriedade. Por exemplo, na imagem a seguir, a cor de cada gráfico corresponde ao valor da propriedade mostrado na parte inferior do gráfico.

    ![Divisão das Métricas do Azure Mapas](media/how-to-view-api-usage/splitting.png)
 
8. Você também pode observar várias métricas no mesmo gráfico, simplesmente clicando no botão **Adicionar métrica** na parte superior.


## <a name="next-steps"></a>Próximas etapas

Agora que aprendeu a acompanhar o uso do Azure Mapas, você pode ler mais sobre as APIs que está utilizando com a:

* [Documentação API REST do Azure Mapas](https://docs.microsoft.com/rest/api/maps)
