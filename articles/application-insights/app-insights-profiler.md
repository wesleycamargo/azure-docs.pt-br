---
title: Faça o perfil de aplicativos da Web do Azure ao vivo com o Application Insights | Microsoft Docs
description: Faça o perfil de aplicativos da Web ao vivo no Azure com o Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 4d957c26bd4e4ae278c0909c9df1476b02954b86
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53138000"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Criar o perfil de aplicativos Web dinâmicos do Azure com o Application Insights

O Profiler atualmente funciona para aplicativos Web do ASP.NET e ASP.NET Core em execução nos Aplicativos Web. A camada de serviço Básica ou superior é necessária para usar o Profiler. Habilitar o Profiler no Linux somente é possível através [deste método](app-insights-profiler-aspnetcore-linux.md).

## <a id="installation"></a> Habilitar o Profiler para seus Aplicativos Web
Para ativar o Profiler para um aplicativo da Web, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para ativar o Profiler em outras plataformas suportadas:
* [Serviços de Nuvem](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler é pré-instalado como parte do tempo de execução do Serviços de Aplicativos, mas é necessário ativá-lo para obter perfis para os seus aplicativos da Web do Azure. Depois de implantar um Web App, mesmo que você tenha incluído o SDK do App Insights no código-fonte, siga as etapas abaixo para ativar o criador de perfil.

1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
1. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo Web. Verifique também o Profiler está **On**.

   ![Adicione a extensão de site do App Insights][Enablement UI]

1. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para parar ou reiniciar o Profiler para uma instância individual do aplicativo Web, em **Trabalhos Web**, vá para o recurso dos Aplicativos Web. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos Web para descobrir problemas de desempenho o mais rápido possível.

Se você usar o Web Deploy para implantar alterações em seu aplicativo da Web, exclua a exclusão da pasta App_Data durante a implantação. Caso contrário, os arquivos da extensão do Profiler serão excluídos na próxima vez em que você implantar o aplicativo Web no Azure.



## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png

