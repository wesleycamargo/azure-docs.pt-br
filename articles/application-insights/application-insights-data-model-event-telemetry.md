---
title: Modelo de dados do Azure Application Insights Telemetry – telemetria de eventos | Microsoft Docs
description: Modelo de dados do Application Insights para telemetria de eventos
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: 062478783465edc2d3afa4b80a22f119e68da049
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47091699"
---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetria de eventos: modelo de dados do Application Insights

Você pode criar itens de telemetria do evento (em [Application Insights](app-insights-overview.md)) para representar um evento que ocorreu em seu aplicativo. Geralmente trata-se de uma interação do usuário como um clique de botão ou finalização de compra. Também pode ser um evento de ciclo de vida do aplicativo como a inicialização ou atualização de configuração. 

Semanticamente, eventos podem ou não ser correlacionados às solicitações. No entanto, se usada corretamente, a telemetria de eventos é mais importante que solicitações ou rastreamentos. Os eventos representam a telemetria de negócios e devem estar sujeitos a uma [amostragem](app-insights-api-filtering-sampling.md) separada, menos agressiva.

## <a name="name"></a>NOME

Nome do evento. Para permitir o agrupamento adequado e métricas úteis, restrinja seu aplicativo de maneira que ele gere um pequeno número de nomes de eventos separados. Por exemplo, não use um nome à parte para cada instância gerado de um evento.

Comprimento máximo: 512 caracteres

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- [Escrever telemetria do evento personalizada](app-insights-api-custom-events-metrics.md#trackevent)
- Confira as [plataformas](app-insights-platforms.md) com suporte do Application Insights.
