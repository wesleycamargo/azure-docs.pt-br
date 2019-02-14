---
title: Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights | Microsoft Docs
description: Crie o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights Profiler.
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 58ee4b7d615c70845e1239d4ee98b086b2124c77
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55874168"
---
# <a name="profile-live-azure-app-service-apps-with-application-insights"></a>Criar o perfil ativo de aplicativos do Serviço de Aplicativo do Azure com o Application Insights

Atualmente, o Profiler funciona para aplicativos do ASP.NET e ASP.NET Core em aplicativos execução no Serviço de Aplicativo do Azure. A camada de serviço Básica ou superior é necessária para usar o Profiler. Habilitar o Profiler no Linux somente é possível através [deste método](profiler-aspnetcore-linux.md).

## <a id="installation"></a> Habilitar o Profiler em seu aplicativo
Para habilitar o Profiler em um aplicativo, siga as instruções abaixo. Se você estiver executando um tipo diferente de serviço do Azure, aqui estão as instruções para ativar o Profiler em outras plataformas suportadas:
* [Serviços de Nuvem](../../azure-monitor/app/profiler-cloudservice.md ?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](../../azure-monitor/app/profiler-servicefabric.md ?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](../../azure-monitor/app/profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler vem pré-instalado como parte do tempo de execução do Serviços de Aplicativos, mas é necessário ativá-lo para obter perfis para os seus aplicativos do Serviço de Aplicativo. Depois de implantar um aplicativo, mesmo que você tenha incluído o SDK do App Insights no código-fonte, execute as etapas abaixo para habilitar o criador de perfil.

1. Vá ao painel **Serviços de Aplicativos** no portal do Azure.
2. Navegue até o painel **Configurações > Application Insights**.

   ![Habilitar Insights de Aplicativo no portal de Serviços de Aplicativos](./media/profiler/AppInsights-AppServices.png)

3. Siga as instruções no painel para criar um novo recurso ou selecione um recurso existente do App Insights para monitorar seu aplicativo. Verifique também o Profiler está **On**.

   ![Adicione a extensão de site do App Insights][Enablement UI]

4. O Profiler agora está ativado usando uma configuração de aplicativo do Serviço de Aplicativo.

    ![Configuração de aplicativo para o Profiler][profiler-app-setting]

## <a name="disable-profiler"></a>Desabilitar o Profiler

Para interromper ou reiniciar o Profiler de uma instância individual do aplicativo, em **Trabalhos Web**, acesse o recurso do aplicativo. Para excluir o Profiler, vá para **Extensões**.

![Desabilitar o Profiler para trabalhos da web][disable-profiler-webjob]

Recomendamos que você tenha o Profiler habilitado em todos os seus aplicativos para descobrir problemas de desempenho o mais rápido possível.

Se você usar o Web Deploy para implantar alterações em seu aplicativo da Web, exclua a exclusão da pasta App_Data durante a implantação. Caso contrário, os arquivos da extensão do Profiler serão excluídos na próxima vez em que você implantar o aplicativo Web no Azure.



## <a name="next-steps"></a>Próximas etapas

* [Trabalhar com o Application Insights no Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[Enablement UI]: ./media/profiler/Enablement_UI.png
[profiler-app-setting]:./media/profiler/profiler-app-setting.png
[disable-profiler-webjob]: ./media/profiler/disable-profiler-webjob.png
