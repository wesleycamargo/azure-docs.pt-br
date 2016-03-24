<properties 
	pageTitle="Análise de Aplicativos – a poderosa ferramenta de pesquisa do Application Insights" 
	description="Visão geral da Análise de Aplicativos, a poderosa ferramenta de pesquisa do Application Insights." 
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
	ms.date="03/07/2016" 
	ms.author="awills"/>




# Análise do Application Insights: visão geral da linguagem


A [Análise do Application Insights](app-analytics.md) é um poderoso mecanismo de consulta para sua telemetria do [Application Insights](app-insights-overview.md). Essas páginas descrevem a linguagem de consulta do Application Insights, o AIQL.

[AZURE.INCLUDE [app-analytics-top-index](../../includes/app-analytics-top-index.md)]

 
Uma consulta AIQL típica é uma tabela de *origem* seguida por uma série de *operadores* separados por `|`.

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

A linguagem tem várias das funcionalidades do SQL e muito mais. Assim como no SQL, é possível filtrar dados, agrupar registros, classificar e unir tabelas. Também é possível executar cálculos nos campos. Ao contrário do SQL, essas funções são separadas em operações diferentes e, em vez de aninhar as consultas, é possível redirecionar os dados de uma operação para a próxima de uma maneira muito intuitiva. Isso facilita a escrita de consultas um tanto complexas.


>[AZURE.NOTE] Recomendamos que você inicie com o [tour da linguagem](app-analytics-tour.md).


## Conectar-se aos dados do Application Insights


Abra a Análise na [folha de visão geral](app-insights-dashboards.md) de seu aplicativo no Application Insights:

![Abra o portal.azure.com, abra o recurso do Application Insights e clique em Análise.](./media/app-analytics/001.png)





[AZURE.INCLUDE [app-analytics-footer](../../includes/app-analytics-footer.md)]

<!---HONumber=AcomDC_0309_2016-->