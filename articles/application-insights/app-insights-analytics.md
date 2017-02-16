---
title: "Analytics - a ferramenta de pesquisa avançada do Azure Application Insights | Microsoft Docs"
description: "Visão geral da Análise, a ferramenta de pesquisa avançada do Application Insights. "
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 70f77fd155627ef1d06cf466d19768a0eaad6856
ms.openlocfilehash: 4d3393b935d2ebd24bccd0fe040f1948402f6e5b


---
# <a name="analytics-in-application-insights"></a>Análise no Application Insights
O [Analytics](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise. 

* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não estiver enviando dados para o Application Insights.
* **[Roteiro dos usuários do SQL](https://aka.ms/sql-analytics)** converte as linguagens mais comuns.
* **[Referência de linguagem](app-insights-analytics-reference.md)** Aprenda a usar todos os recursos eficientes da linguagem de consulta do Analytics.

## <a name="queries-in-analytics"></a>Consultas na Análise
Uma consulta comum é uma tabela de *origem* seguida por vários *operadores* separados por `|`. 

Por exemplo, vamos descobrir em qual hora do dia os cidadãos de Hyderabad experimentam usar nosso aplicativo Web. E enquanto estamos fazendo isso, vamos ver quais códigos de resultado são retornados para suas solicitações HTTP. 

```AIQL

    requests      // Table of events that log HTTP requests.
    | where timestamp > ago(7d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Contamos endereços IP de cliente distintos, agrupando-os por hora do dia nos últimos 7 dias. 

Vamos exibir os resultados com a apresentação do gráfico de barras, optando por empilhar os resultados de códigos de resposta diferentes:

![Escolha o gráfico de barras, os eixos x e y e a segmentação](./media/app-insights-analytics/020.png)

Parece que nosso aplicativo é mais popular na hora do almoço e na hora de dormir em Hyderabad. (Então, devemos investigar esses 500 códigos).

Também há operações estatísticas avançadas:

![](./media/app-insights-analytics/025.png)

A linguagem tem muitos recursos atrativos:

* [Filtre](app-insights-analytics-reference.md#where-operator) a telemetria bruta de aplicativos por qualquer campo, incluindo suas métricas e propriedades personalizadas.
* [Una](app-insights-analytics-reference.md#join-operator) várias tabelas: correlacione as solicitações com exibições de página, as chamadas de dependência, as exceções e os rastreamentos de log.
* [Agregações](app-insights-analytics-reference.md#aggregations)estatísticas poderosas.
* Tão potente quando o SQL, mas muito fácil para consultas complexas: em vez de aninhar instruções, você redireciona os dados de uma operação elementar para a próxima.
* Visualizações imediatas e eficientes.
* [Fixe gráficos em painéis do Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Exporte as consultas para Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Há uma [API REST](https://dev.applicationinsights.io/) que você pode usar para executar consultas de modo programático; no Powershell, por exemplo.


## <a name="connect-to-your-application-insights-data"></a>Conectar-se aos dados do Application Insights
Abra o Analytics na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights: 

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics/001.png)



[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

## <a name="next-steps"></a>Próximas etapas
* É recomendável começar com o [tour de linguagem](app-insights-analytics-tour.md).





<!--HONumber=Jan17_HO4-->


