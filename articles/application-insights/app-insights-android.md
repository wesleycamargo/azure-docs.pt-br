<properties
    pageTitle="Análises para aplicativos Android | Microsoft Azure"
    description="Analise o uso e o desempenho de seu aplicativo Android."
    services="application-insights"
    documentationCenter="android"
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="na"
    ms.topic="get-started-article"
	ms.date="08/26/2016"
    ms.author="awills"/>

# Análise de aplicativos Android

A Microsoft fornece duas soluções para devOps de dispositivo: [HockeyApp](http://hockeyapp.net/) para análise do cliente e [Application Insights](app-insights-overview.md) para o servidor.

O [HockeyApp](http://hockeyapp.net/) é a nossa solução de DevOps Móvel para aplicativos de dispositivos iOS, OS X, Android ou Windows, bem como aplicativos de plataforma cruzada baseados no Xamarin, no Cordova e no Unity. Com ele, você pode distribuir compilações para testadores beta, coletar dados de falha e obter comentários dos usuários. Ele é integrado ao Visual Studio Team Services, permitindo a compilação fácil de implantações e a integração de itens de trabalho.

Acesse:

* [HockeyApp](http://support.hockeyapp.net/kb)
* [Introdução ao HockeyApp para Android](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk).
* [Blog do HockeyApp](http://hockeyapp.net/blog/)
* Ingresse em [Hockeyapp Preseason](http://hockeyapp.net/preseason/) para obter versões anteriores.

Se seu aplicativo tiver um lado servidor, use o [Application Insights](app-insights-overview.md) para monitorar o lado do servidor Web de seu aplicativo em [ASP.NET](app-insights-asp-net.md) ou em [J2EE](app-insights-java-get-started.md).


## Análise, exportação e acesso da API a dados de HockeyApp 

[Configure uma ponte HockeyApp](app-insights-hockeyapp-bridge-app.md) no Application Insights. Isso permite a você:

* Use a poderosa linguagem de consulta de [Análise](app-insights-analytics.md) na telemetria.
* [Exporte a telemetria](app-insights-export-telemetry.md) para o armazenamento de blobs do Azure.

## Próximas etapas

* [Introdução ao HockeyApp para Android](http://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)

<!---HONumber=AcomDC_0831_2016-->