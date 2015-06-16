<properties 
	pageTitle="Monitorar utilização em aplicativos da Windows Store e Windows Phone com o Application Insights" 
	description="Analise a utilização de seu aplicativo de dispositivo do Windows com o Application Insights." 
	services="application-insights" 
    documentationCenter="windows"
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/28/2015" 
	ms.author="awills"/>

#  Monitorar utilização em aplicativos da Windows Store e Windows Phone com o Application Insights

*O Application Insights está em modo de visualização.*

Saiba quantos usuários você tem e quais páginas eles estão visitando em seu aplicativo. O Application Insights fornece esses dados a você, prontos para uso. E inserindo algumas linhas de código em seu aplicativo, você pode saber mais sobre os caminhos que os usuários percorrem pelo seu aplicativo e o que obtêm ao fazer isso.

Se ainda não tiver feito isso, adicione o [Application Insights ao seu projeto de aplicativo][windows] e republique-o.


## <a name="usage"></a>Acompanhar uso

Na linha do tempo Visão Geral, clique nos gráficos Usuários e Sessões para ver uma análise mais detalhada.


![](./media/app-insights-windows-usage/appinsights-d018-oview.png)

* Os **Usuários** são rastreados anonimamente, portanto, o mesmo usuário em dispositivos diferentes seria contabilizado duas vezes.
* Uma **sessão** é contabilizada quando o aplicativo está suspenso (por mais de um breve intervalo, para evitar a contabilização de suspensões acidentais).

#### Segmentação

Segmente um gráfico para obter uma análise segundo uma variedade de critérios. Por exemplo, para ver quantos usuários estão usando cada versão do seu aplicativo, abra o gráfico Usuários e o segmento de Versão do Aplicativo:

![No gráfico de usuários, ative a Segmentação e escolha Versão do aplicativo](./media/app-insights-windows-usage/appinsights-d25-usage.png)


#### Visualizações de página

Para descobrir os caminhos que os usuários seguem por meio de seu aplicativo, insira [telemetria de exibição de página][api] em seu código:

    var telemetry = new TelemetryClient();
    telemetry.TrackPageView("GameReviewPage");

Veja os resultados no gráfico de visualizações de página e abrindo seus detalhes:

![](./media/app-insights-windows-usage/appinsights-d27-pages.png)

Clique em qualquer página para ver os detalhes de ocorrências específicas.

#### Eventos personalizados

Ao inserir código para enviar eventos personalizados do seu aplicativo, você pode controlar o comportamento de seus usuários e o uso de cenários e recursos específicos.

Por exemplo:

    telemetry.TrackEvent("GameOver");

Os dados aparecerão na grade Eventos Personalizados. Você pode ver uma exibição agregada no Metrics Explorer ou clicar em qualquer evento para ver ocorrências específicas.

![](./media/app-insights-windows-usage/appinsights-d28-events.png)


Você pode adicionar propriedades numéricas e de cadeia de caracteres a qualquer evento.


    // Set up some properties:
    var properties = new Dictionary <string, string> 
       {{"Game", currentGame.Name}, {"Difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
       {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("GameOver", properties, measurements);


Clique em qualquer ocorrência para ver suas propriedades detalhadas, inclusive aquelas definidas por você.


![](./media/app-insights-windows-usage/appinsights-d29-eventProps.png)

Consulte [referência da API][api] para obter mais informações sobre eventos personalizados.



## <a name="debug"></a>Modo Depurar versus Liberar

#### Depurar

Se você compilar no modo depurar, os eventos serão enviados assim que forem gerados. Se você perder a conectividade com a Internet e em seguida sair do aplicativo antes de retomar a conectividade, a telemetria offline será descartada.

#### Liberar

Se você compilar na configuração Liberar, os eventos serão armazenados no dispositivo e enviados quando a execução do aplicativo for retomada. Os dados também são enviados no primeiro uso do aplicativo. Se não houver conectividade com a Internet durante a inicialização, a telemetria anterior, bem como a telemetria para o ciclo de vida atual, é armazenada e enviada na próxima retomada.

## <a name="next"></a>Próximas etapas

[Conheça seus usuários][knowUsers]

[Saiba mais sobre o Metrics Explorer][metrics]


[Solucionar problemas][qna]




<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[knowUsers]: app-insights-overview-usage.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[windows]: app-insights-windows-get-started.md


<!--HONumber=54--> 