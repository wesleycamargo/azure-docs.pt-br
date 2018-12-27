---
title: Faça o perfil dos serviços em nuvem do Azure com o Application Insights | Microsoft Docs
description: Habilite o Profiler do Application Insights para o Cloud Services.
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
ms.openlocfilehash: 5bb70bf56efac28029401b69ee4f87c2738c52e3
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721843"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Faça o perfil dos serviços em nuvem do Azure com o Application Insights

Você também pode implantar o Profiler do Application Insights nesses serviços:
* [Aplicativos Web do Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas virtuais](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler é instalado com a extensão WAD (Windows Azure Diagnostics). Você só precisa configurar o WAD para instalar o criador de perfil e enviar perfis para o recurso Application Insights.

## <a name="enable-profiler-for-your-azure-cloud-service"></a>Ativar o criador de perfil para seu Serviço de Nuvem do Azure
1. Verifique que você usar [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior.  É suficiente confirmar que os arquivos *ServiceConfiguration.\*.cscfg* têm um valor `osFamily` de "5" ou posterior.
1. Adicione o [Application Insights SDK ao serviço em nuvem](app-insights-cloudservices.md?toc=/azure/azure-monitor/toc.json).
1. Acompanhe as solicitações com o Application Insights:

    Para as funções da Web do ASP.Net, o Application Insights pode rastrear as solicitações automaticamente.

    Funções de trabalho, [adicionar código para acompanhar as solicitações.](app-insights-profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

    

1. Configure o Windows Azure extensão WAD (Diagnóstico) para habilitar o criador de perfil.



    1. Localize o arquivo [diagnostics.wadcfgx](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) do *Diagnóstico do Azure* para sua função de aplicativo, como mostrado aqui:  

       ![Local do arquivo de configuração de diagnóstico](./media/enable-profiler-compute/cloudservice-solutionexplorer.png)  

        Se você não encontrar o arquivo, para saber como habilitar a extensão de Diagnóstico em seu projeto de Serviços de Nuvem do Azure, consulte [Configurando o diagnóstico para os Serviços de Nuvem do Azure e máquinas virtuais](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#enable-diagnostics-in-cloud-service-projects-before-deploying-them).

    1. Adicione a seguinte seção `SinksConfig` como um elemento filho do `WadCfg`:  

      ```xml
      <WadCfg>
        <DiagnosticMonitorConfiguration>...</DiagnosticMonitorConfiguration>
        <SinksConfig>
          <Sink name="MyApplicationInsightsProfiler">
            <!-- Replace with your own Application Insights instrumentation key. -->
            <ApplicationInsightsProfiler>00000000-0000-0000-0000-000000000000</ApplicationInsightsProfiler>
          </Sink>
        </SinksConfig>
      </WadCfg>
      ```

    >   **OBSERVAÇÃO:** Se o arquivo *diagnostics.wadcfgx* também tiver outro coletor do tipo `ApplicationInsights`, todas as três chaves de instrumentação deverão corresponder:  
    >  * A chave que é usada pelo seu aplicativo.  
    >  * A chave que é usada pelo coletor `ApplicationInsights`.  
    >  * A chave que é usada pelo coletor `ApplicationInsightsProfiler`.  
    >
    > Você pode encontrar o valor real da chave de instrumentação que é usado pelo coletor `ApplicationInsights` nos arquivos *ServiceConfiguration.\*.cscfg*.  
    > Após o lançamento do Visual Studio 15.5 do Azure SDK, somente as chaves de instrumentação usadas pelo aplicativo e o coletor `ApplicationInsightsProfiler` precisam corresponder um ao outro.
1. Implante seu serviço com a nova configuração de diagnóstico e o Application Insights Profiler será configurado para ser executado em seu serviço.
 
## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Obtenha ajuda para a solução de problemas do criador de perfil em [Solução de problemas do criador de perfil](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).