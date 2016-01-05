<properties 
	pageTitle="Detectar e diagnosticar falhas em aplicativos da Windows Store e Windows Phone com o Application Insights" 
	description="Analise problemas de desempenho em seu aplicativo de dispositivo do Windows com o Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2015" 
	ms.author="awills"/>

# Detectar e diagnosticar falhas em aplicativos da Windows Store e Windows Phone com o Application Insights

*O Application Insights está em modo de visualização.*

Se os usuários experienciam falhas em seu aplicativo, você gostaria de saber a respeito rapidamente e obter detalhes sobre o que aconteceu. Com o Application Insights, você pode monitorar a frequência com que os falhas ocorrem, obter alertas quando elas ocorrem e investigar os relatórios de incidentes individuais.

"Falha" significa que o aplicativo é encerrado devido a uma exceção não detectada. Se seu aplicativo capturar uma exceção, você pode relatá-la com a [API TrackException][apiexceptions] e continuar em execução. Nesse caso, ele não será registrado como uma falha.


## Monitorar frequência de falha

Se ainda não tiver feito isso, adicione o [Application Insights ao seu projeto de aplicativo][windows] e republique-o.

As falhas são exibidas na folha Visão Geral do seu aplicativo no [portal do Application Insights][portal].

![](./media/app-insights-windows-crashes/appinsights-d018-oview.png)

Você pode editar o intervalo de tempo mostrado pelo gráfico.


## Definir um alerta para detectar falhas

![No gráfico de falhas, clique em Regras de Alerta e, em seguida, em Adicionar Alerta](./media/app-insights-windows-crashes/appinsights-d023-alert.png)

## Diagnosticar falhas

Para descobrir se algumas versões do seu aplicativo falham mais do que outras, clique no gráfico de falhas e, em seguida, segmente segundo a versão do aplicativo:

![](./media/app-insights-windows-crashes/appinsights-d26crashSegment.png)


Para descobrir as exceções que estão causando falhas, abra Diagnóstico de Pesquisa. Você talvez queira remover outros tipos de telemetria para concentrar-se nas exceções:

![](./media/app-insights-windows-crashes/appinsights-d26crashExceptions.png)

[Saiba mais sobre filtragem na Pesquisa de Diagnóstico][diagnostic].
 

Clique em qualquer exceção para ver seus detalhes, incluindo propriedades associadas e rastreamento de pilha.

![](./media/app-insights-windows-crashes/appinsights-d26crash.png)

Consulte as exceções e eventos que ocorreram perto dessa exceção:


![](./media/app-insights-windows-crashes/appinsights-d26crashRelated.png)

## Inserir eventos e logs de rastreamento

Para ajudar a diagnosticar problemas, você pode [inserir chamadas de rastreamento e pesquisar os logs no Application Insights][diagnostic].

## <a name="debug"></a>Modo Depurar versus Liberar

#### Depurar

Se você compilar no modo depurar, os eventos serão enviados assim que forem gerados. Se você perder a conectividade com a Internet e em seguida sair do aplicativo antes de retomar a conectividade, a telemetria offline será descartada.

#### Liberar

Se você compilar na configuração Liberar, os eventos serão armazenados no dispositivo e enviados quando a execução do aplicativo for retomada. Os dados também são enviados no primeiro uso do aplicativo. Se não houver conectividade com a Internet durante a inicialização, a telemetria anterior, bem como a telemetria para o ciclo de vida atual, é armazenada e enviada na próxima retomada.

## <a name="next"></a>Próximas etapas

[Detectar, realizar triagem e diagnosticar problemas com o Application Insights][detect]

[API do Application Insights][api]

[Capturar logs de diagnóstico][trace]

[Solucionar problemas](app-insights-windows-troubleshoot.md)




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[apiexceptions]: app-insights-api-custom-events-metrics.md#track-exception
[detect]: app-insights-detect-triage-diagnose.md
[diagnostic]: app-insights-diagnostic-search.md
[platforms]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=AcomDC_1203_2015-->