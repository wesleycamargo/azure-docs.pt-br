---
title: Azure Application Insights para ASP.NET Core | Microsoft Docs
description: Monitorar aplicativos web de disponibilidade, desempenho e uso.
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 3b722e47-38bd-4667-9ba4-65b7006c074c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/30/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 4c97ec97d9d12d86796e995c71d8c9bd6c6a3df2


---
# <a name="application-insights-for-aspnet-core"></a>Application Insights para ASP.NET Core
O [Application Insights](app-insights-overview.md) permite que você monitore seu aplicativo Web quanto à disponibilidade, desempenho e uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-asp-net-core/sample.png)

Precisa de uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário que adicione você a ela usando sua conta da Microsoft.

## <a name="getting-started"></a>Introdução
Siga o [guia de Introdução](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Getting-Started).

## <a name="using-application-insights"></a>Usando o Application Insights
Entre no [Portal do Microsoft Azure](https://portal.azure.com) e navegue para o recurso que você criou para monitorar seu aplicativo.

Em uma janela separada do navegador, use seu aplicativo por algum tempo. Você verá os dados exibidos nos gráficos do Application Insights. (Talvez você precise clicar em Atualizar.) Haverá uma pequena quantidade de dados enquanto você estiver desenvolvendo, mas estes gráficos realmente ganharão vida quando você publicar seu aplicativo e tiver muitos usuários. 

A página Visão geral mostra os gráficos de desempenho em que você provavelmente está interessado: tempo de resposta do servidor, o tempo de carregamento de página e contagens de solicitações com falha. Clique em qualquer gráfico para ver mais gráficos e dados.

Os modos de exibição no portal se enquadram em duas categorias principais:

* O [Metrics Explorer](app-insights-metrics-explorer.md) mostra gráficos e tabelas de métricas e contagens, como tempos de resposta, taxas de falha ou métricas que você cria com a [API](app-insights-api-custom-events-metrics.md). Filtre e segmente os dados por valores de propriedade para obter uma compreensão melhor do seu aplicativo e seus usuários.
* O [Search Explorer](app-insights-diagnostic-search.md) lista eventos individuais, como solicitações específicas, exceções, rastreamentos de log ou eventos que você criou com a [API](app-insights-api-custom-events-metrics.md). Filtre e pesquise nos eventos e navegue entre os eventos relacionados para investigar problemas.
* [Análise](app-insights-analytics.md) permite que você execute consultas SQL em sua telemetria e é uma poderosa ferramenta de análise e de diagnóstico.

## <a name="alerts"></a>Alertas
* Você obtém automaticamente [alertas proativos de diagnóstico](app-insights-proactive-diagnostics.md) que informam sobre alterações anômalas em taxas de falha e outras métricas.
* Configure [testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar seu site continuamente em locais em todo o mundo e receber emails assim que qualquer teste falhar.
* Configure [alertas de métrica](app-insights-monitor-web-app-availability.md) para saber se métricas, como tempos de resposta ou taxas de exceção, saem dos limites aceitáveis.

## <a name="next-steps"></a>Próximas etapas
* [Adicione telemetria às suas páginas da Web](app-insights-javascript.md) para monitorar o uso e o desempenho de páginas.
* [Monitore dependências](app-insights-asp-net-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Use a API](app-insights-api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificam seu aplicativo constantemente em todo o mundo. 

## <a name="open-source"></a>Código-fonte aberto
[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnetcore#recent-updates)




<!--HONumber=Jan17_HO4-->


