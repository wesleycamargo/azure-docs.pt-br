---
title: Modelo de dados do Azure Application Insights Telemetry | Microsoft Docs
description: "Visão geral do modelo de dados do Application Insights"
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
ms.openlocfilehash: 52f700bd18da87a9a38e0a791d0ec3496f201e0a
ms.lasthandoff: 04/22/2017


---
# <a name="application-insights-telemetry-data-model"></a>Modelo de dados do Application Insights Telemetry

O Application Insights define o modelo de dados de telemetria para o APM (gerenciamento de desempenho de aplicativos). Esse modelo padroniza a coleta de dados e permite a criação de cenários de monitoramento independentes de linguagem e plataforma. Os dados coletados pelo Application Insights modelam o padrão de execução típico do aplicativo:

![Modelo de aplicativo do Application Insights](./media/application-insights-data-model/application-insights-data-model.png)

Há dois tipos de aplicativos – aplicativos com um ponto de extremidade que recebem ***solicitações*** externas – aplicativos Web e aplicativos que periodicamente "acordam" para processar dados armazenados em algum lugar – WebJobs ou Functions. Em ambos os casos, chamamos execução exclusiva de uma ***operação***. A operação é bem-sucedida ou falha por meio de ***exceção*** ou ela pode depender de outros serviços/armazenamento para executar sua lógica de negócios. Para refletir esses conceitos, o modelo de dados do Application Insights define três tipos de telemetria: [solicitação](./application-insights-data-model-request-telemetry.md), [exceção](/application-insights-data-model-exception-telemetry.md) e [dependência](/application-insights-data-model-dependency-telemetry.md).

Normalmente, esses tipos são definidos pela estrutura de aplicativos e são coletados automaticamente pelo SDK. `ASP.NET MVC` define a noção de uma execução de solicitação no seu direcionamento de controlador de exibição de modelo – ele marca o início e parada de uma solicitação. Chamadas de dependência para SQL são definidas por `System.Data`. Chamadas para pontos de extremidade HTTP são definidas por `System.Net`. Você pode estender os tipos de telemetria coletados pela plataforma e estrutura específicas usando medidas e propriedades personalizadas. No entanto, há casos em que você deseja relatar telemetria personalizada. Talvez você queira implementar o log de diagnóstico usando uma estrutura de instrumentação familiar para você, como `Log4Net` ou `System.Diagnostics`. Ou talvez você precise capturar a interação do usuário com o serviço para analisar os padrões de uso. O Application Insights reconhece três tipos de dados adicionais para modelar esses cenários: [rastreamento](/application-insights-data-model-trace-telemetry.md), [evento](/application-insights-data-model-event-telemetry.md) e [métrica](/application-insights-data-model-metric-telemetry.md).

O modelo do Application Insights Telemetry define a forma de [correlacionar](/correlation.md) telemetria à operação da qual ele faz parte. Por exemplo, uma solicitação pode tomar informações de diagnóstico registradas e de chamadas de um Banco de Dados SQL. Você pode definir o contexto de correlação para esses itens de telemetria aproximarão esse contexto novamente da telemetria de solicitação.

## <a name="schema-improvements"></a>Aprimoramentos de esquema

O modelo de dados do Application Insights é uma maneira simples e básica, mas poderosa de modelar a telemetria de aplicativo. Nos esforçamos para manter o modelo simples e reduzido para dar suporte a cenários essenciais e permitir a extensão do esquema para uso avançado.

Para relatar problemas de esquema ou modelo de dados e sugestões, use o repositório [ApplicationInsights-Home](https://github.com/Microsoft/ApplicationInsights-Home/labels/schema) do GitHub.

## <a name="next-steps"></a>Próximas etapas

- Confira as [plataformas](/app-insights-platforms.md) com suporte do Application Insights.
- Saiba como [estender e filtrar a telemetria](/app-insights-api-filtering-sampling.md).
- Use [amostragem](/app-insights-sampling.md) para minimizar a quantidade de telemetria com base no modelo de dados.

