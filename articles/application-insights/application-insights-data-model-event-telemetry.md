---
title: "Modelo de dados do Azure Application Insights Telemetry – telemetria de eventos | Microsoft Docs"
description: Modelo de dados do Application Insights para telemetria de eventos
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: modelo de dados do Application Insights

Eventos representam uma ação em um ponto no tempo ocorrida no aplicativo. Geralmente trata-se de uma interação do usuário como um clique de botão ou finalização de compra. Também pode ser um evento de ciclo de vida do aplicativo como a inicialização ou atualização de configuração. O nome do evento deve ser uma cadeia de caracteres curta de baixa cardinalidade. 

Semanticamente, eventos podem ou não ser correlacionados às solicitações. No entanto, se usada corretamente, a telemetria de eventos é mais importante que solicitações ou rastreamentos. Os eventos representam a telemetria de negócios e devem estar sujeitos a uma amostragem separada, menos agressiva.

## <a name="name"></a>Nome

Nome do evento. Mantenha uma baixa cardinalidade para permitir métricas úteis e um agrupamento apropriado.

Comprimento máximo: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](/application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- Saiba como usar a [API do Application Insights para métricas e eventos personalizados](/app-insights-asp-net-dependencies.md).
- Confira as [plataformas](/app-insights-platforms.md) com suporte do Application Insights.

