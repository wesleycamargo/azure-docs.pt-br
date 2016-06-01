<properties 
	pageTitle="Análise – a ferramenta de pesquisa avançada do Application Insights" 
	description="Visão geral da Análise, a ferramenta de pesquisa avançada do Application Insights." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/18/2016" 
	ms.author="awills"/>


# Análise no Application Insights


[Análise](app-insights-analytics.md) é o recurso de pesquisa avançado do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta da Análise.

[AZURE.INCLUDE [app-insights-analytics-top-index](../../includes/app-insights-analytics-top-index.md)]

 
Uma consulta típica é formada por uma tabela de *origem* seguida de uma série de *operadores* separados por `|`.

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
* [Una](app-insights-analytics-reference.md#join-operator) várias tabelas – correlacione as solicitações com exibições de página, as chamadas de dependência, as exceções e os rastreamentos de log.
* [Agregações](app-insights-analytics-reference.md#aggregations) estatísticas poderosas.
* Tão potente quando o SQL, mas muito fácil para consultas complexas: em vez de aninhar instruções, você redireciona os dados de uma operação elementar para a próxima.
* Visualizações imediatas e eficientes.



>[AZURE.NOTE] É recomendável começar com o [tour de linguagem](app-insights-analytics-tour.md).




## Conectar-se aos dados do Application Insights


Abra a Análise na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-insights-analytics/001.png)


## Limites

No momento, os resultados da consulta são limitados apenas a uma semana de dados anteriores.



[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]

<!---HONumber=AcomDC_0518_2016-->