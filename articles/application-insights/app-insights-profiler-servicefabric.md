---
title: Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights | Microsoft Docs
description: Habilitar o criador de perfil para um aplicativo do Service Fabric
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
ms.openlocfilehash: 3eb5b2300ea2af7bc778e0831d105f286eab247c
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721401"
---
# <a name="profile-live-azure-service-fabric-applications-with-application-insights"></a>Criar perfil de aplicativos ativos do Azure Service Fabric com o Application Insights

Você também pode implantar o Profiler do Application Insights nesses serviços:
* [Aplicativos Web do Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


## <a name="set-up-the-environment-deployment-definition"></a>Configurar a definição de implantação do ambiente

O Application Insights Profiler está incluído com o WAD (Diagnóstico do Windows Azure). A extensão do WAD pode ser instalada usando um modelo do Azure RM para seu cluster do Service Fabric. Há um modelo de exemplo: [**Modelo que instala WAD em um Cluster do Service Fabric.**](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json)

Para configurar seu ambiente, faça o seguinte:
1. Para garantir que você esteja usando o [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior, basta confirmar se o sistema operacional implantado é o `Windows Server 2012 R2` ou posterior.

1. Pesquise a extensão [Diagnóstico do Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) no arquivo do modelo de implantação e, em seguida, adicione a seção `SinksConfig` a seguir como um elemento filho de `WadCfg`. Substitua o valor da propriedade `ApplicationInsightsProfiler` pela sua própria chave de instrumentação do Application Insights:  

      ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "00000000-0000-0000-0000-000000000000"
          }
        ]
      }
      ```

      Para obter informações sobre como adicionar a extensão de Diagnóstico em seu modelo de implantação, consulte [Usar monitoramento e diagnóstico com uma VM Windows e modelos do Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-diagnostics-template?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
1. Implantar seu cluster do Service Fabric usando o modelo do Azure Resource Manager. Se suas configurações estiverem corretas, o Application Insights Profiler será instalado e habilitado quando a extensão do WAD for instalada. 
1. Adicione o Application Insights ao seu aplicativo do Service Fabric. Seu aplicativo deve enviar dados de solicitação ao application insights para que o criador de perfil colete os perfis para suas solicitações. Encontre instruções [aqui.](https://github.com/Microsoft/ApplicationInsights-ServiceFabric)
1. Reimplante o aplicativo.

> [DICA] Para Máquinas Virtuais, uma alternativa às etapas baseadas em JSON acima é navegar no portal do Azure para **Máquinas Virtuais**>**Configurações de Diagnóstico**>**Coletores** > Definir a opção Enviar dados de diagnóstico para o Application Insights como **Habilitado** e selecionar uma conta do Application Insights ou uma ikey específica.

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Obtenha ajuda para a solução de problemas do criador de perfil em [Solução de problemas do criador de perfil](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).