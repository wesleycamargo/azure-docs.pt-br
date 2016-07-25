<properties 
	pageTitle="Aproveite melhor o Application Insights" 
	description="Após começar a usar o Application Insights, veja um resumo dos recursos que você pode explorar." 
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
	ms.date="06/02/2016" 
	ms.author="awills"/>

# Mais telemetria do Application Insights

Veja está um resumo dos recursos que você não pode ter testado no [Visual Studio Application Insights](app-insights-overview.md). Vamos presumir que você já [começou](app-insights-asp-net.md). O Application Insights permite que você monitore o seu aplicativo Web quanto à disponibilidade, o desempenho e o uso. Com os comentários que você obtiver sobre o desempenho e a eficiência de seu aplicativo em uso, você pode fazer escolhas informadas sobre a direção do projeto em cada ciclo de vida de desenvolvimento.

## Mais telemetria

||
|---|---
|[**Testes de disponibilidade**](app-insights-monitor-web-app-availability.md)<br/>envie solicitações HTTP para seu aplicativo Web de todo o mundo em intervalos regulares. Nós o alertaremos se a resposta estiver lenta ou não for confiável.| 
|[**Chamadas de dependência**](app-insights-asp-net-dependencies.md)<br/>Monitore consultas SQL e chamadas para REST ou outros recursos.|
|[**Exceções**](app-insights-asp-net-exceptions.md)<br/>conte exceções com e sem tratamento, obtenha rastreamentos de pilha e clique até o código.|
|[**Páginas da Web**](app-insights-javascript.md)<br/>Monitore o uso e o desempenho da página e chamadas AJAX, instrumentando suas páginas da Web.
|**Desempenho do host: [Diagnóstico do Azure](app-insights-azure-diagnostics.md), [Contadores de desempenho do Windows](app-insights-web-monitor-performance.md)**<br/>veja o uso da CPU e outras métricas de contexto. |![](./media/app-insights-asp-net-more/04.png)
|[**API do SDK - telemetria personalizada**](app-insights-api-custom-events-metrics.md)<br/>Envie seus próprios eventos e métricas para ter uma exibição mais detalhada do desempenho e do uso do aplicativo, tanto no servidor quanto no código do cliente.|
|[**Integração de log**](app-insights-asp-net-trace-logs.md)<br/>Se você usar uma estrutura de registros como Log4Net, NLog ou System.Diagnostics.Trace, haverá um adaptador que envia os rastreamentos ao Application Insights com outros dados de telemetria.|
|[**TelemetryProcessors**](app-insights-api-filtering-sampling.md)<br/>filtre, modifique ou aumente a telemetria enviada do SDK em seu aplicativo. |


## Análise e apresentação avançadas

||
|---|---
|[**Encontre dados de instância com a pesquisa de diagnóstico**](app-insights-visual-studio.md)<br/>pesquise e filtre eventos como solicitações, exceções, chamadas de dependência, rastreamentos de log e exibições de página. No Visual Studio, vá até o código dos rastreamentos de pilha.|![Visual Studio](./media/app-insights-asp-net/61.png)
|[**Metrics Explorer para dados agregados**](app-insights-metrics-explorer.md)<br/>explore, filtre e segmente dados agregados, como taxas de solicitações, falhas e exceções; tempos de resposta e tempos de carregamento de página.|![Visual Studio](./media/app-insights-asp-net-more/060.png)
|[**Painéis**](app-insights-dashboards.md#dashboards)<br/>permitem fazer o mashup de dados de vários recursos e compartilhar com outras pessoas. Excelente para aplicativos com vários componentes e para exibição contínua no ambiente de equipe. |![Exemplos de painéis](./media/app-insights-asp-net/62.png)
|[**Live Metrics Stream**](app-insights-metrics-explorer.md#live-metrics-stream)<br/>quando implantar um novo build, acompanhe esses indicadores de desempenho quase em tempo real para verificar se tudo está funcionando conforme esperado.|![Exemplo de análise](./media/app-insights-asp-net-more/050.png)
|[**Análise**](app-insights-analytics.md)<br/>responda perguntas difíceis sobre o desempenho e o uso do seu aplicativo usando essa poderosa linguagem de consulta.|![Exemplo de análise](./media/app-insights-asp-net-more/010.png)
|[**Alertas automáticos e manuais**](app-insights-alerts.md)<br/>alertas automáticos se adaptam aos padrões normais de telemetria do seu aplicativo e são disparados quando há algo fora do padrão normal. Você também pode definir alertas em níveis específicos de métricas padrão ou personalizadas.|![Exemplo de alerta](./media/app-insights-asp-net-more/020.png)

## Gerenciamento de dados

|||
|---|---|
|[**Exportação contínua**](app-insights-export-telemetry.md)<br/>copie toda a telemetria para um armazenamento para poder analisá-la como quiser.|
|**API de acesso a dados**<br/>em breve.|
|[**Amostragem**](app-insights-sampling.md)<br/>reduz a taxa de dados e ajuda você a permanecer dentro do limite de seu tipo de preço.|![Bloco de amostragem](./media/app-insights-asp-net-more/030.png)

<!---HONumber=AcomDC_0713_2016-->