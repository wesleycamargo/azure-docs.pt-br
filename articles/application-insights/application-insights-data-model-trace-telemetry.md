---
title: "Modelo de dados do Azure Application Insights Telemetry – telemetria de rastreamento | Microsoft Docs"
description: Modelo de dados do Application Insights para telemetria de rastreamento
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
ms.openlocfilehash: e7825b26c47a80debf92be1ad166e5a18bae4a61
ms.lasthandoff: 04/22/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: modelo de dados do Application Insights

A telemetria de rastreamento representa as instruções de rastreamento de estilo `printf` que são pesquisadas por texto. `Log4Net`, `NLog` e outras entradas do arquivo de log baseadas em texto são convertidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Comprimento máximo: 32.768 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Próximas etapas

- Consulte [modelo de dados](/application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- [Explore os logs de rastreamento do .NET no Application Insights](/app-insights-asp-net-trace-logs.md).
- [Explore os logs de rastreamento de Java no Application Insights](/app-insights-java-trace-logs.md).
- Confira as [plataformas](/app-insights-platforms.md) com suporte do Application Insights.

