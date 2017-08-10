---
title: "Analytics - a ferramenta de pesquisa avançada do Azure Application Insights | Microsoft Docs"
description: "Visão geral da Análise, a ferramenta de pesquisa avançada do Application Insights. "
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.assetid: 0a2f6011-5bcf-47b7-8450-40f284274b24
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: sewhee
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: a2dc351bd0346f5ca46f1eaafeff678c3339c8c9
ms.contentlocale: pt-br
ms.lasthandoff: 08/02/2017

---
# <a name="analytics-in-application-insights"></a>Análise no Application Insights
O [Analytics](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta do Log Analytics. 

* **[Assista ao vídeo introdutório](https://applicationanalytics-media.azureedge.net/home_page_video.mp4)**.
* **[Faça um test drive do Analytics com nossos dados simulados](https://analytics.applicationinsights.io/demo)** se seu aplicativo ainda não estiver enviando dados para o Application Insights.
* **[Roteiro dos usuários do SQL](https://aka.ms/sql-analytics)** converte as linguagens mais comuns.
* **[Referência de linguagem](app-insights-analytics-reference.md)** Aprenda a usar todos os recursos eficientes da linguagem de consulta do Log Analytics.


## <a name="queries-in-analytics"></a>Consultas na Análise
Uma consulta comum é uma tabela de *origem* seguida por vários *operadores* separados por `|`. 

Por exemplo, vamos descobrir em qual hora do dia os cidadãos de Hyderabad experimentam usar nosso aplicativo Web. E enquanto estamos fazendo isso, vamos ver quais códigos de resultado são retornados para suas solicitações HTTP. 

```AIQL
requests
| where timestamp > ago(30d)
| summarize ClientCount = dcount(client_IP) by bin(timestamp, 1h), resultCode
| extend LocalTime = timestamp - 4h
| order by LocalTime desc
| render barchart
```

Contamos endereços IP de cliente distintos, agrupando-os por hora do dia nos últimos 7 dias. 

> [!NOTE]
> Para obter resultados fora das últimas 24h, inclua 'timestamp' explicitamente na consulta ou use o menu suspenso de intervalo de tempo.
>

Vamos exibir os resultados com a apresentação do gráfico de barras, optando por empilhar os resultados de códigos de resposta diferentes:

![Escolha o gráfico de barras, os eixos x e y e a segmentação](./media/app-insights-analytics/020.png)

Parece que nosso aplicativo é mais popular na hora do almoço e na hora de dormir em Hyderabad. (Então, devemos investigar esses 500 códigos).

Também há operações estatísticas avançadas:

![Resultados de consulta estatística](./media/app-insights-analytics/025.png)

A linguagem tem muitos recursos atrativos:


* [Filtre](https://docs.loganalytics.io/queryLanguage/query_language_whereoperator.html) a telemetria bruta de aplicativos por qualquer campo, incluindo suas métricas e propriedades personalizadas.
* [Una](https://docs.loganalytics.io/queryLanguage/query_language_joinoperator.html) várias tabelas: correlacione as solicitações com exibições de página, as chamadas de dependência, as exceções e os rastreamentos de log.
* [Agregações](https://docs.loganalytics.io/learn/tutorials/aggregations.html)estatísticas poderosas.
* Tão potente quando o SQL, mas muito fácil para consultas complexas: em vez de aninhar instruções, você redireciona os dados de uma operação elementar para a próxima.
* Visualizações imediatas e eficientes.
* [Fixe gráficos em painéis do Azure](app-insights-analytics-using.md#pin-to-dashboard).
* [Exporte as consultas para Power BI](app-insights-analytics-using.md#export-to-power-bi).
* Há uma [API REST](https://dev.applicationinsights.io/) que você pode usar para executar consultas de modo programático; no Powershell, por exemplo.


## <a name="connect-to-your-application-insights-data"></a>Conectar-se aos dados do Application Insights
Abra o Analytics na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights: 

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics/001.png)


## <a name="video"></a>Vídeo

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/123/player] 


[!INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]



## <a name="query-examples"></a>Exemplos de consulta

Tente usar essas explicações passo a passo que ilustram a eficiência do uso do Analytics:

 *  [Diagnóstico automático de picos e etapas ignoradas nas durações de solicitações](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Automatic%20diagnostics%20of%20sudden%20spikes%20or%20step%20jumps%20in%20requests%20duration&shared=true)
 *  [Analisar degradações de desempenho com análise de série temporal](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20performance%20degradations%20with%20time%20series%20analysis&shared=true)
 *  [Analisar as falhas do aplicativo com autocluster e diffpatterns](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Analyzing%20application%20failures%20with%20autocluster%20and%20diffpatterns&shared=true)
 *  [Detecções de forma avançadas com análise de série temporal](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Advanced%20shape%20detection%20with%20time%20series%20analysis&shared=true)
 *  [Usar operações de janela deslizante para analisar o uso do aplicativo (MAU/DAU contínuos, etc.)](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Using%20sliding%20window%20calculations%20to%20analyze%20usage%20metrics:%20rolling%20MAU~2FDAU%20and%20cohorts&shared=true)
 *  [Detecção de interrupções de serviço com base na análise dos logs de depuração](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Detection%20of%20service%20disruptions%20based%20on%20regression%20analysis%20of%20trace%20logs&shared=true) e uma postagem de blog correspondente [aqui](https://maximshklar.wordpress.com/2017/02/16/finding-trends-in-traces-with-smart-data-analytics).
 *  [Criação de perfil de desempenho de aplicativos usando logs de depuração simples](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Profiling%20applications'%20performance%20with%20simple%20debug%20logs&shared=true) e uma postagem de blog correspondente [aqui](https://yossiattasblog.wordpress.com/2017/03/13/first-blog-post/)
 *  [Medir a duração de cada etapa no seu fluxo de código usando logs de depuração simples](https://analytics.applicationinsights.io/demo#/discover/query/main?title=Measuring%20the%20duration%20of%20each%20step%20in%20your%20code%20flow%20using%20simple%20debug%20logs&shared=true) e uma postagem de blog correspondente [aqui](https://yossiattasblog.wordpress.com/2017/03/14/measuring-the-duration-of-each-step-in-your-code-flow-using-simple-debug-logs/)
 *  [Analisar a simultaneidade usando logs de depuração simples](https://analytics.applicationinsights.io/demo#/discover/query/results/chart?title=Analyzing%20concurrency%20with%20simple%20debug%20logs&shared=true) e uma postagem de blog correspondente [aqui](https://yossiattasblog.wordpress.com/2017/03/23/analyzing-concurrency-using-simple-debug-logs/)



## <a name="next-steps"></a>Próximas etapas
* É recomendável começar com o [tour de linguagem](app-insights-analytics-tour.md). 
* Saiba mais sobre [como usar a análise](app-insights-analytics-using.md). 
* [Referência da linguagem](app-insights-analytics-reference.md). 

