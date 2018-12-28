---
title: Modelo de dados do Azure Application Insights Telemetry – telemetria de rastreamento | Microsoft Docs
description: Modelo de dados do Application Insights para telemetria de rastreamento
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/25/2017
ms.reviewer: sergkanz
ms.author: mbullwin
ms.openlocfilehash: f87b2d1bd105a9a9b8eabb3f2f6686c36f71e6df
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721724"
---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetria de rastreamento: Modelo de dados do Application Insights

A telemetria de rastreamento (em [Application Insights](app-insights-overview.md)) representa instruções de rastreamento de estilo `printf` pesquisadas por texto. `Log4Net`, `NLog` e outras entradas do arquivo de log baseadas em texto são convertidas em instâncias desse tipo. O rastreamento não tem medidas como uma extensibilidade.

## <a name="message"></a>Mensagem

Mensagem de rastreamento.

Tamanho máx. 32768 caracteres

## <a name="severity-level"></a>Nível de severidade

Nível de severidade de rastreamento. O valor pode ser `Verbose`, `Information`, `Warning`, `Error` ou `Critical`.

## <a name="custom-properties"></a>Propriedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Próximas etapas

- [Explore os logs de rastreamento do .NET no Application Insights](app-insights-asp-net-trace-logs.md).
- [Explore os logs de rastreamento de Java no Application Insights](app-insights-java-trace-logs.md).
- Consulte [modelo de dados](application-insights-data-model.md) para modelo de dados e tipos do Application Insights.
- [Escrever telemetria personalizada de rastreamento](app-insights-api-custom-events-metrics.md#tracktrace)
- Confira as [plataformas](app-insights-platforms.md) com suporte do Application Insights.
