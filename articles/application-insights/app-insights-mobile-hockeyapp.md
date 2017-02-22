---
title: "Monitoramento de desempenho de aplicativos móveis - Azure Application Insights | Microsoft Docs"
description: "Desempenho do aplicativo e monitoramento de uso para desenvolvedores de aplicativos móveis. DevOps com HockeyApp e Application Insights."
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 93e0f108-9605-4d8b-8fce-512bfe8c3f0f
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: c4077aa88a401a1c75c0d3fbcd58186f7cb137c2


---
# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Análise móvel com o HockeyApp e o Application Insights
Monitore o desempenho e o uso do seu teste beta e os aplicativos móveis e da área de trabalho implantados com o [HockeyApp](https://hockeyapp.net/). Monitore os aplicativos Web e de back-end de suporte com o [Azure Application Insights](app-insights-overview.md). Analise os dados dos aplicativos cliente e de servidor no Analytics e exiba os gráficos lado a lado em um painel do Azure.

A Análise para o Desenvolvedor da Microsoft oferece duas soluções de monitoramento de desempenho do aplicativo:

* **HockeyApp para aplicativos** móveis e da área de trabalho.
  * Distribua versões de teste para usuários selecionados.
  * Análise de falha.
  * Telemetria personalizada para análise de uso.
* **Application Insights para sites da Web** e serviços, e aplicativos de back-end.
  * Alertas e métricas de desempenho e uso.
  * Relatórios de exceção e rastreamento de diagnóstico.
  * Pesquisa de diagnóstico com filtragem e links de telemetria relacionados.

Ambas as soluções oferecem:

* **[Linguagem de consulta analítica](app-insights-analytics.md)** avançada para diagnóstico e análise.
* **[Exporte dados](app-insights-export-telemetry.md)** para seu próprio armazenamento.
* Exibição de gráficos e tabelas de análise em **painel integrado**.

## <a name="monitor-your-app-components"></a>Monitorar os componentes do aplicativo
Siga as instruções nessas páginas para instalar o SDK no seu código e iniciar o rastreamento de seu aplicativo.

### <a name="web-apps---application-insights"></a>Aplicativos Web - Application Insights
* [Aplicativo Web ASP.NET](app-insights-asp-net.md) 
* [Aplicativo Web Java](app-insights-java-get-started.md)
* [Aplicativo Web Node.js](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Serviços de Nuvem do Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Aplicativos móveis - HockeyApp
* [Aplicativo iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicativo Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicativo Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicativo Universal do Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicativo do Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Aplicativo do Windows Presentation Foundation](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Para aplicativos de área de trabalho do Windows, recomendamos o HockeyApp. Mas você também pode [enviar telemetria de um aplicativo do Windows para o Application Insights](app-insights-windows-desktop.md). Você talvez queira fazer isso para fazer experiências com o Application Insights.

## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Análise e exportação de telemetria do HockeyApp
Você pode investigar a telemetria personalizada e de log do HockeyApp usando os recursos de análise e exportação contínua do Application Insights ao [configurar uma ponte](app-insights-hockeyapp-bridge-app.md).




<!--HONumber=Jan17_HO4-->


