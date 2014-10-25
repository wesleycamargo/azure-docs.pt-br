<properties title="Monitor your app's health and usage with Application Insights" pageTitle="Monitor your app's health and usage with Application Insights" description="Get started with Application Insights. Analyze usage, availability and performance of your on-premises or Microsoft Azure applications." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Monitore o desempenho dos aplicativos web

*O Application Insights está em visualizar.*

Certifique-se de que seu aplicativo está sendo bem executado, e saiba rapidamente sobre quaisquer falhas. O [Application Insights][start] vai lhe dizer sobre quaisquer problemas de desempenho e exceções, e o ajudará a localizar e diagnosticar as causas raízes.

O Application Insights pode monitorar os aplicativos da web ASP.NET hospedados no local ou em máquinas virtuais, bem como em sites do Microsoft Azure.

-   [Configurar o monitoramento de desempenho](#setup)
-   [Veja os dados](#view)
-   [O que significa tudo isso?](#metrics)
-   [Problemas de diagnóstico](#diagnosis)
-   [Próximas etapas](#next)

## <a name="setup"></a>Configurar o monitoramento de desempenho

Se você ainda não tem o Application Insights adicionado ao seu projeto (ou seja, não tem o ApplicationInsights.config), escolha uma destas formas para começar:

-   [Adicione o Application Insights ao seu projeto do aplicativo no Visual Studio][start] - Recomendado. Bem como o monitoramento de desempenho passivo, você pode inserir o registro em log do diagnóstico e o rastreamento de uso.
-   [Monitorar o desempenho de um site ao vivo agora][redfield] - Dessa forma, você não precisa atualizar o projeto do aplicativo ou reimplamnetar o site da web.

## <a name="view"></a>Exibir relatórios

Execute seu aplicativo com F5 e experimente - abra páginas diferentes.

No Visual Studio, você verá uma contagem de eventos que foram recebidas.

![](./media/appinsights/appinsights-09eventcount.png)

Abrir Application Insights do seu projeto.

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/appinsights/appinsights-04-openPortal.png)

Procure pelos dados nos mosaicos da **integridade do aplicativo**. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/appinsights/appinsights-41firstHealth.png)

Quando você executa no modo de depuração, a telemetria é expressa através da pipeline, de modo que voc~e deve ver dados aparecendo dentro de segundos. Quando você implanta seu aplicativo, os dados acumulam mais lentamente.

Se você não vê os dados de primeira, aguarde um minuto e, em seguida, clique em Atualizar.

### Explorando métricas

Clique em qualquer mosaico para ver mais detalhes e para ver os resultados por um longo período. Por exemplo, clique no mosaico de Solicitações e, em seguida, selecione um intervalo de tempo:

![Clique por mais dados e selecione um intervalo de tempo](./media/appinsights/appinsights-48metrics.png)

Clique em um gráfico para escolher quais métricas exibir:

![Clique em um gráfico para escolher métricas](./media/appinsights/appinsights-61perfchoices.png)

> [AZURE.NOTE] **Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas caem em grupos; quando qualquer membro de um grupo é selecionado, somente os outros membros do grupo aparecem.

## <a name="metrics"></a>O que significa tudo isso? Mosaicos e relatórios de desempenho

Existe uma variedade de métricas de desempenho que você pode obter. Vamos começar com estas que aparecem por padrão na folha do aplicativo.

### Solicitações

O número de solicitações de HTTP receberam em um período especifico. Compare isso com os resultados em outros reatórios para ver como seu aplicativo se comporta conforme a carga varia.

As solicitações HTTP incuem todas as solicitações GET ou POST para páginas, dados e imagens.

Clique no mosaico para obter contagens para URLs específicas.

### Tempo de resposta média

Mede o tempo entre uma solicitação da web inserindo seu aplicativo e a resposta que está sendo devolvida.

Os pontos mostram uma média da movimentação. Se existe várias solicitações, pode haver agumas que desviam da média sem um pico óbvio ou profundo no gráfico.

Procure por picos incomuns. Em geral, espere o tempo de resposta para eevar com uma elevação nas solicitações. Se a elevação é desproporcional, seu aplicativo pode ser atingido por um limite de recurso como CPU ou a capacidade de um serviço que usa.

Clique no mosaico para obter tempos para URLs específicas.

![](./media/appinsights/appinsights-42reqs.png)

### Solicitações mais lentas

![](./media/appinsights/appinsights-44slowest.png)

Mostra quais solicitações podem precisar de sintonização de desempenho.

### Solicitações falhas

![](./media/appinsights/appinsights-46failed.png)

Uma contagem de solicitações que gerou exceções não capturadas.

Clique no mosaico para ver os detalhes de falhas específicas, e selecione uma solicitação individual para ver seus detalhes.

Somente uma amostra representativa de falhas é retida para inspeção individual.

### Outras métricas

Para ver o que outras métricas que você pode exibir, clique em um gráfico e, em seguida, desmarque a seleção de todas as métricas para ver o conjunto disponível completo. Clique em (i) para ver cada definição da métrica.

![Desmarque a seleção de todas as métricas para ver o conjunto completo](./media/appinsights/appinsights-62allchoices.png)

Ao selecionar qualquer métrica, ocultará as outras que podem aparecer no mesmo gráfico.

## <a name="diagnosis"></a>Problemas de diagnóstico

Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

-   Configure os [testes de web][availability] para ser alertado se seu site cair ou responder de forma incorreta ou lenta.
-   Compare a contagem de Solicitação com outras métricas para ver se falhas ou resposta lenta são relatadas ao carregar.
-   [Inserir e pesquisar instruções de rastreamento][diagnostic] em seu código para ajudar problemas de pinpoint.

## <a name="next"></a>Próximas etapas

[Testes da web][availability] - Tem solicitações da web enviadas ao seu aplicativo em intervaos regulares em todo o mundo.

[Capture e pesquise rastreamento de diagnóstico][diagnostic] - Inserir chamadas de rastreamento e separar através dos resultados para problemas de pinpoint.

[Rastreamento de uso][usage] - Saiba como as pessoas usam se aplicativo.

[Solução de problemas][qna] - e Perguntas e respostas

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
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/
[webclient]: ../app-insights-start-monitoring-app-health-usage/#webclient
