<properties 
	pageTitle="Application Insights: plataformas" 
	description="É possível usar o Application Insights com...?" 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="ronmart"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="awills"/>
 
# Application Insights: plataformas

[AZURE.INCLUDE [app-insights-selector-get-started](../includes/app-insights-selector-get-started.md)]

#### É possível usar o Application Insights com...?


## Idiomas

+ [C\#, VB](app-insights-start-monitoring-app-health-usage.md)
+ [Páginas da Web de JavaScript](app-insights-web-track-usage.md)
+ [Aplicativos JavaScript da Windows Store](#cordova)
+ [Java](app-insights-java.md)
+ [Ruby](https://rubygems.org/gems/application_insights) 
+ [PHP](https://github.com/Microsoft/AppInsights-PHP)
+ [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)

## Plataformas

+ [ASP.NET](app-insights-start-monitoring-app-health-usage.md)
+ [Aplicativos Web do Azure e máquinas virtuais](insights-perf-analytics.md)
+ [Android](https://github.com/Microsoft/AppInsights-Android)
+ [iOS](https://github.com/Microsoft/AppInsights-iOS)
+ [Cordova](#cordova)
+ [Angular](https://www.npmjs.com/package/angular-applicationinsights)
+ [Node.JS](https://www.npmjs.com/package/applicationinsights)
+ [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
+ [SharePoint](app-insights-sharepoint.md)
+ [WordPress](https://wordpress.org/plugins/application-insights/)
+ [Área de trabalho do Windows](app-insights-windows-desktop.md)


## Estruturas de log

+	[Log4Net, NLog ou System.Diagnostics.Trace](app-insights-diagnostic-search.md)
+	[Java, Log4J ou Logback](app-insights-java-trace-logs.md)


## Projetos

Visite também o [Projeto SDK do Application Insights no GitHub](https://github.com/Microsoft/AppInsights-Home)


### <a name="cordova"></a>Aplicativos JavaScript da Windows Store e Cordova

No Visual Studio, clique com o botão direito em seu projeto e escolha **Gerenciar pacotes NuGet**.

Selecione **Online** e pesquise no Application Insights.

Instale a **API do Application Insights para aplicativos JavaScript**.

Use o [script de aplicativo da Web](app-insights-web-track-usage.md) padrão de lado do cliente, mas com uma alteração.

Quando você obtiver o script do portal do Application Insights, insira uma linha após a chave de instrumentação:

    ...{
        instrumentationKey:"00000000-662d-4479-0000-40c89770e67c",
        endpointUrl:"https://dc.services.visualstudio.com/v2/track"
    } ...

[Cordova](http://cordova.apache.org/)

[Aplicativos da Windows Store usando JavaScript](https://msdn.microsoft.com/library/windows/apps/br211385.aspx)

<!--Link references-->



<!--HONumber=54-->