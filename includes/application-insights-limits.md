---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: application-insights
author: mrbullwinkle
ms.service: application-insights
ms.topic: include
ms.date: 06/21/2018
ms.author: mbullwin
ms.custom: include file
ms.openlocfilehash: 1efddb605422f00575cc0e44592315b725c838e3
ms.sourcegitcommit: da69285e86d23c471838b5242d4bdca512e73853
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2019
ms.locfileid: "54002766"
---
Há alguns limites no número de métricas e eventos por aplicativo (isto é, por chave de instrumentação). Os limites dependem do [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) que você escolher.

| Recurso | Limite padrão | Observação
| --- | --- | --- |
| Total de dados por dia | 100 GB | Você pode reduzir os dados ao definir um limite. Caso precise de mais dados, é possível aumentar o limite até 1.000 GB. Para capacidades maiores que 1.000 GB, envie um email para AIDataCap@microsoft.com.
| Limitação | 32.000 eventos/segundo | O limite é medido em um minuto.
| Retenção de dados | 90 dias | Este recurso destina-se a [Pesquisa](../articles/azure-monitor/app/diagnostic-search.md), [Análise](../articles/azure-monitor/app/analytics.md) e [Metrics Explorer](../articles/azure-monitor/app/metrics-explorer.md).
| Retenção de resultados detalhados do [Teste de disponibilidade de várias etapas](../articles/azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) | 90 dias | Esse recurso fornece resultados detalhados de cada etapa.
| Tamanho máximo de evento | 64 k | 
| Tamanho dos nomes de propriedade e métrica | 150 | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Tamanho da cadeia de caracteres do valor da propriedade | 8.192 | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Comprimento da mensagem de rastreamento e de exceção | 10.000 | Veja [esquemas de tipo](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/EndpointSpecs/Schemas/Docs/).
| Contagem de [testes de disponibilidade](../articles/azure-monitor/app/monitor-web-app-availability.md) por aplicativo | 100 |
| Retenção de dados do [criador de perfil](../articles/application-insights/app-insights-profiler.md) | 5 dias |
| Dados do [criador de perfil](../articles/application-insights/app-insights-profiler.md) enviados por dia | 10 GB |

Para obter mais informações, consulte [sobre preços e cotas no Application Insights](../articles/azure-monitor/app/pricing.md).