<properties 
	pageTitle="Application Insights para ASP.NET 5" 
	description="Monitorar aplicativos web de disponibilidade, desempenho e uso." 
	services="application-insights" 
    documentationCenter=".net"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/10/2016" 
	ms.author="awills"/>

# Application Insights para ASP.NET 5

O Visual Studio Application Insights permite que você monitore o seu aplicativo Web, a sua disponibilidade, desempenho e uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

![Exemplo](./media/app-insights-asp-net-five/sample.png)

Precisa de uma assinatura do [Microsoft Azure](http://azure.com). Entre com uma conta da Microsoft, que você pode ter para o Windows, XBox Live ou outros serviços de nuvem da Microsoft. Sua equipe pode ter uma assinatura organizacional do Azure: peça ao proprietário que adicione você a ela usando sua conta da Microsoft.

[Demonstração de exemplo](https://github.com/aspnet/Docs/tree/master/aspnet/fundamentals/application-insights/sample)

## Introdução

Se você criou o projeto no Visual Studio 2015, já deve ter o Application Insights. Caso contrário, siga o [Guia de introdução](https://github.com/Microsoft/ApplicationInsights-aspnet5/wiki/Getting-Started).

## Usando o Application Insights

Entre no [Portal do Microsoft Azure](https://portal.azure.com) e navegue para o recurso que você criou para monitorar seu aplicativo.

Em uma janela separada do navegador, use seu aplicativo por algum tempo. Você verá os dados exibidos nos gráficos do Application Insights. (Talvez você precise clicar em Atualizar.) Haverá uma pequena quantidade de dados enquanto você estiver desenvolvendo, mas estes gráficos realmente ganharão vida quando você publicar seu aplicativo e tiver muitos usuários.

A página Visão geral mostra os gráficos de desempenho em que você provavelmente está interessado: tempo de resposta do servidor, o tempo de carregamento de página e contagens de solicitações com falha. Clique em qualquer gráfico para ver mais gráficos e dados.

Os modos de exibição no portal se enquadram em duas categorias principais:

* O [Metrics Explorer](app-insights-metrics-explorer.md) mostra gráficos e tabelas de métricas e contagens, como tempos de resposta, taxas de falha ou métricas que você cria com a [API](app-insights-api-custom-events-metrics.md). Filtre e segmente os dados por valores de propriedade para obter uma compreensão melhor do seu aplicativo e seus usuários.
* O [Search Explorer](app-insights-diagnostic-search.md) lista eventos individuais, como solicitações específicas, exceções, rastreamentos de log ou eventos que você criou com a [API](app-insights-api-custom-events-metrics.md). Filtre e pesquise nos eventos e navegue entre os eventos relacionados para investigar problemas.

## Alertas

* Configure [testes de disponibilidade](app-insights-monitor-web-app-availability.md) para testar seu site continuamente em locais em todo o mundo e receber emails assim que qualquer teste falhar.
* Configure [alertas de métrica](app-insights-monitor-web-app-availability.md) para saber se métricas, como tempos de resposta ou taxas de exceção, saem dos limites aceitáveis.


## Obtenha mais telemetria

* [Monitore dependências](app-insights-dependencies.md) para ver se REST, SQL ou outros recursos externos estão causando lentidão.
* [Use a API](app-insights-api-custom-events-metrics.md) para enviar seus próprios eventos e métricas para uma exibição mais detalhada do desempenho e do uso do aplicativo.
* [Testes de disponibilidade](app-insights-monitor-web-app-availability.md) verificam seu aplicativo constantemente em todo o mundo. 


## Código-fonte aberto

[Ler e contribuir para o código](https://github.com/Microsoft/ApplicationInsights-aspnet5)


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apikey]: app-insights-api-custom-events-metrics.md#ikey
[availability]: app-insights-monitor-web-app-availability.md
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[netlogs]: app-insights-asp-net-trace-logs.md
[perf]: app-insights-web-monitor-performance.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[start]: app-insights-overview.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=AcomDC_0211_2016-->