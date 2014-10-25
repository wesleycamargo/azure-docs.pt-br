<properties title="Track usage with custom events and metrics" pageTitle="Track usage" description="Log user activities." metaKeywords="analytics monitoring application insights" authors="awills" manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Acompanhar uso

## <a name="webclient"></a>Configuração das análises de uso da web

Se você ainda não fez isso, [adicione o Application Insights ao seu projeto web][start].

## <a name="usage"></a>Análise de uso

Na folha de visão geral do aplicativo, você verá estes blocos de uso:

![](./media/appinsights/appinsights-47usage.png)

### Sessões por navegador

Uma *sessão* é um período que começa quando um usuário abre qualquer página em seu site, e termina depois que o usuário não envia qualquer solicitação de web por um período de tempo limite de 30 minutos.

Clique para aplicar zoom no gráfico.

### Visualizações da página principal

Mostra a contagem total nas últimas 24 horas.

Clique no bloco de exibições da página para obter um histórico mais detalhado.

![](./media/appinsights/appinsights-49usage.png)

Clique em Intervalo de Tempo para ver um histórico maior do que sete dias.

Clique em um gráfico para ver outras medidas além daquelas que você pode exibir.

![](./media/appinsights/appinsights-63usermetrics.png)

## Contagens da página personalizada

Por padrão, uma contagem de página ocorre todas as vezes que uma nova página carrega no navegador do cliente. Por exemplo, você talvez queira contar exibições de página adicionais. Por exemplo, uma página pode exibir seu conteúdo em guias e você quer contar uma página quando o usuário muda as guias. Ou o código do JavaScript na página pode carregar novo conteúdo sem mudar a URL do navegador.

Insira uma chamada do JavaScript como essa no ponto apropriado no código do seu cliente:

    appInsights.trackPageView(myPageName);

O nome da página pode conter os mesmos caracteres da URL, mas nada após o "\#" ou "?" será ignorado.

## Inspecionando os eventos de exibição da página individual

Normalmente a telemetria de exibição da página é analisada pelo Application Insights e você vê somente relatórios cumulativos, calculados sobre todos os seus usuários. Mas para a finalidade de depuração, você também pode olhar para os eventos de exibição da página individua.

Na folha de Pesquisa de Diagnóstico, defina Filtros para Exibição da Página.

![](./media/appinsights/appinsights-51searchpageviews.png)

Selecione qualquer evento para ver mais detalhes.

> [WACOM.NOTE] Se você usar [Pesquisar][diagnostic], observe que você tem que coincidir palavras inteiras: "Sobr" e "obre" não correspondem a "Sobre", mas "Sobr\*" sim. E você não pode iniciar um termo de pesquisa com um curinga. Por exemplo, pesquisar por "\*obr” não corresponde a "Sobre”.

> [Saiba mais sobre pesquisa de diagnóstico][diagnostic]

## Acompanhamento de uso

> [AZURE.VIDEO tracking-usage-with-application-insights]

## Saiba mais

-   [Application Insights - introdução][start]
-   [Monitore um servidor de web ao vivo agora][redfield]
-   [Monitore o desempenho dos aplicativos web][perf]
-   [Pesquise por logs de diagnóstico][diagnostic]
-   [Disponibilidade de acompanhamento com os testes web][availability]
-   [Acompanhar uso][usage]
-   [Perguntas e respostas e solução de problemas][qna]

<!--Link references-->

  [start]: ../app-insights-start-monitoring-app-health-usage/
  []: ./media/appinsights/appinsights-47usage.png
  [1]: ./media/appinsights/appinsights-49usage.png
  [2]: ./media/appinsights/appinsights-63usermetrics.png
  [3]: ./media/appinsights/appinsights-51searchpageviews.png
  [diagnostic]: ../app-insights-search-diagnostic-logs/
  [redfield]: ../app-insights-monitor-performance-live-website-now/
  [perf]: ../app-insights-web-monitor-performance/
  [availability]: ../app-insights-monitor-web-app-availability/
  [usage]: ../app-insights-web-track-usage/
  [qna]: ../app-insights-troubleshoot-faq/
