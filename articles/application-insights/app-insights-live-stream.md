---
title: Live Metrics Stream no Azure Application Insights | Microsoft Docs
description: "Monitore o desempenho do seu aplicativo Web em tempo real a fim de observar os efeitos de uma versão ou outra alteração."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: c453774b04c7005ce5948617beea8770b499b88d
ms.openlocfilehash: 2da6d02055616db5f9854481054ff12c69dc4801


---

# <a name="live-metrics-stream-instant-metrics-for-close-monitoring"></a>Live Metrics Stream: métricas instantâneas para um monitoramento preciso
O Live Metrics Stream mostra as métricas do [Application Insights](app-insights-overview.md) no momento exato, com uma latência quase de tempo real de um segundo. Esse monitoramento imediato é muito útil quando você está lançando uma nova compilação e deseja ter certeza de que tudo está funcionando como esperado ou se quiser investigar um incidente em tempo real.

![Na folha Visão Geral, clique em Live Stream](./media/app-insights-live-stream/live-stream.png)

Ao contrário do [Metrics Explorer](app-insights-metrics-explorer.md), o Live Metrics Stream exibe um conjunto fixo de métricas. Os dados persistem somente durante o período em que estão no gráfico e, em seguida, são descartados.

Os dados do Live Metrics Stream são gratuitos: não são acrescentados à sua fatura.

## <a name="live-failures"></a>Falhas em tempo real

Se quaisquer falhas ou exceções forem registradas, o Live Stream selecionará uma amostra delas. Clique em **Pausar** para manter um exemplo específico e selecione um evento para exibir seus detalhes.

![Amostra de falhas em tempo real](./media/app-insights-live-stream/live-stream-failures.png)


O Live Stream em tempo real está disponível com a versão mais recente do [SDK do Application Insights para Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/).



## <a name="troubleshooting"></a>Solucionar problemas

Não há dados? O Live Metrics Stream usa uma porta diferente da usada pela Application Insights telemetry. Certifique-se de que [essas portas](app-insights-ip-addresses.md) estejam abertas no firewall.



## <a name="next-steps"></a>Próximas etapas
* [Monitorando o uso com o Application Insights](app-insights-overview-usage.md)
* [Usando a Pesquisa de diagnóstico](app-insights-diagnostic-search.md)




<!--HONumber=Feb17_HO2-->


