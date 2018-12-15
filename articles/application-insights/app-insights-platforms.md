---
title: 'Application Insights: linguagens, plataformas e integrações| Microsoft Docs'
description: Linguagens, plataformas e integrações disponíveis para o Application Insights
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 974db106-54ff-4318-9f8b-f7b3a869e536
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/01/2016
ms.reviewer: olegan
ms.author: mbullwin
ms.openlocfilehash: 9f7f7731490467ff32a72507239bfeedc794f2f2
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000544"
---
# <a name="developer-analytics-languages-platforms-and-integrations"></a>Análise para o desenvolvedor: linguagens, plataformas e integrações
Esses itens são implementações do [Application Insights](app-insights-overview.md) sobre as quais ouvimos falar, incluindo algumas de terceiros.

## <a name="languages---officially-supported-by-application-insights-team"></a>Linguagens oficialmente suportadas pela equipe do Application Insights
* [C#|VB (.NET)](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Páginas da Web JavaScript](app-insights-javascript.md)
* [Node.JS](app-insights-nodejs.md)

## <a name="languages---community-supported"></a>Linguagens com suporte da comunidade
* [F#](https://safe-stack.github.io/docs/template-azure-ai/)
* [PHP](https://github.com/Microsoft/ApplicationInsights-PHP)
* [Python](https://pypi.python.org/pypi/applicationinsights/0.1.0)
* [Ruby](https://rubygems.org/gems/application_insights)
* [Qualquer outra](#projects)

## <a name="platforms-and-frameworks"></a>Plataformas e estruturas
* [ASP.NET](app-insights-asp-net.md)
* [ASP.NET – para aplicativos que já estejam ao vivo](app-insights-monitor-performance-live-website-now.md)
* [ASP.NET Core](app-insights-asp-net-core.md)
* [Android](app-insights-mobile-center-quickstart.md) (App Center)
* [Android](https://github.com/Microsoft/ApplicationInsights-Android) (App Center)
* [Angular](https://github.com/MarkPieszak/angular-application-insights)
* [Aplicativos Web do Azure](app-insights-azure-web-apps.md)
* [Serviços de Nuvem do Azure](app-insights-cloudservices.md)&#151; incluindo funções Web e de trabalho
* [Funções do Azure](https://github.com/christopheranderson/azure-functions-app-insights-sample)
* [Docker](app-insights-docker.md)
* [Glimpse](https://azure.microsoft.com/blog/glimpse-application-insights/)
* [iOS](app-insights-mobile-center-quickstart.md) (App Center)
* [Ionic](https://github.com/SoftwarePioniere/ionic-application-insights)
* [iOS](https://github.com/Microsoft/ApplicationInsights-iOS) (App Center)
* [J2EE](app-insights-java-get-started.md)
* [J2EE – para aplicativos que já estejam ao vivo](app-insights-java-live.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [OSX](https://github.com/Microsoft/ApplicationInsights-OSX)
* [Pilha de segurança](https://safe-stack.github.io/docs/template-azure-ai/)
* [Spring](https://joe.blog.freemansoft.com/2015/12/enabling-microsoft-application-insight.html)
* [Aplicativo Universal do Windows](app-insights-mobile-center-quickstart.md) (App Center)
* [WCF](https://github.com/Microsoft/ApplicationInsights-SDK-Labs/blob/master/WCF/readme.md)
* [Funções de trabalho, serviços e aplicativos da área de trabalho do Windows](app-insights-windows-desktop.md)
* [Qualquer outra](#projects)

## <a name="logging-frameworks"></a>Estruturas de registro em log
* [Log4Net, NLog ou System.Diagnostics.Trace](app-insights-asp-net-trace-logs.md)
* [Java, Log4J ou Logback](app-insights-java-trace-logs.md)
* [Registro Semântico (SLAB)](https://github.com/fidmor89/SLAB_AppInsights) - é integrado ao [Bloco de Aplicativos do Registro Semântico](https://msdn.microsoft.com/library/dn440729.aspx)
* [Teste de carga baseado em nuvem](https://blogs.msdn.com/b/visualstudioalm/archive/2015/07/30/getting-application-insights-counters-with-cloud-based-load-testing.aspx)
* [Plug-in LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Log Analytics](https://blogs.technet.microsoft.com/msoms/2016/09/26/application-insights-connector-in-oms/)
* [Logary](https://www.nuget.org/packages/Logary.Targets.AppInsights/)
* [Logrus](https://github.com/jjcollinge/logrus-appinsights)

## <a name="content-management-systems"></a>Sistemas de Gerenciamento de Conteúdo
* [Concrete](https://github.com/fidmor89/appInsights-Concrete)
* [Drupal](https://github.com/fidmor89/AppInsights-Drupal)
* [Joomla](https://github.com/fidmor89/AppInsights-Joomla)
* [Orchard](https://azure.microsoft.com/blog/integrating-application-insights-into-a-modular-cms-and-a-multi-tenant-public-saas/preview/)
* [SharePoint](app-insights-sharepoint.md)
* [WordPress](https://wordpress.org/plugins/application-insights/)

## <a name="export-and-data-analysis"></a>Análise de Dados e Exportação
* [Alooma](https://www.alooma.com/blog/application-insights-amazon-redshift)
* [Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/11/04/explore-your-application-insights-data-with-power-bi.aspx)
* [Stream Analytics](app-insights-export-power-bi.md)

## <a name="projects"></a> Criar seu próprio SDK
Se ainda não houver um SDK para sua linguagem ou plataforma, você gostaria de criar um? Examine o código dos SDKs existentes listados no [Projeto de SDK do Application Insights no GitHub](https://github.com/Microsoft/AppInsights-Home).
