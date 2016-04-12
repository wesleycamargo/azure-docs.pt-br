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
	ms.date="03/21/2016" 
	ms.author="awills"/>


# Análise no Application Insights


A [Análise](app-analytics.md) permite executar consultas avançadas na telemetria de seu aplicativo coletada pelo [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Uma consulta típica é formada por uma tabela de *origem* seguida de uma série de *operadores* separados por `|`.

Por exemplo, vamos descobrir em qual hora do dia os cidadãos de Hyderabad experimentam usar nosso aplicativo Web. E enquanto estamos fazendo isso, vamos ver quais códigos de resultado são retornados para suas solicitações HTTP.

```AIQL

    requests 
    | where timestamp > ago(30d) and client_City == "Hyderabad"
    | summarize clients = dcount(client_IP) 
      by tod_UTC=bin(timestamp % 1d, 1h), resultCode
    | extend local_hour = (tod_UTC + 5h + 30min) % 24h + datetime("2001-01-01") 
```

Contamos endereços IP de cliente distintos, agrupando-os por hora do dia nos últimos 30 dias.

Vamos exibir os resultados com a apresentação do gráfico de barras, optando por empilhar os resultados de códigos de resposta diferentes:

![Escolha o gráfico de barras, os eixos x e y e a segmentação](./media/app-analytics/020.png)

Parece que nosso aplicativo é mais popular na hora do almoço e na hora de dormir em Hyderabad. (Então, devemos investigar esses 500 códigos).


Também há operações estatísticas avançadas:

![](./media/app-analytics/025.png)


A linguagem tem muitos recursos atrativos:

* [Filtre](app-analytics-queries.md) a telemetria bruta de aplicativos por qualquer campo, incluindo suas métricas e propriedades personalizadas.
* [Una](app-analytics-queries.md#join-operator) várias tabelas – correlacione as solicitações com exibições de página, as chamadas de dependência, as exceções e os rastreamentos de log.
* [Agregações](app-analytics-aggregations.md) estatísticas poderosas.
* Tão potente quando o SQL, mas muito fácil para consultas complexas: em vez de aninhar instruções, você redireciona os dados de uma operação elementar para a próxima.
* Visualizações imediatas e eficientes.



>[AZURE.NOTE] É recomendável começar com o [tour de linguagem](app-analytics-tour.md).




## Conectar-se aos dados do Application Insights


Abra a Análise na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-analytics/001.png)


## Limites

No momento, os resultados da consulta são limitados apenas a uma semana de dados anteriores.



[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!-----------HONumber=AcomDC_0330_2016-->