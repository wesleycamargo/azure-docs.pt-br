---
title: Criar o perfil de Serviços de Nuvem do Azure ativos com o Application Insights | Microsoft Docs
description: Habilite o Application Insights Profiler para os Serviços de Nuvem do Azure.
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
ms.openlocfilehash: 2e13f1f09fcdfb68a99e705511e3659f1632132e
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/18/2019
ms.locfileid: "57895474"
---
# <a name="profile-live-azure-cloud-services-with-application-insights"></a>Criar o perfil de Serviços de Nuvem do Azure ativos com o Application Insights

Você também pode implantar o Application Insights Profiler nesses serviços:
* [Serviço de Aplicativo do Azure](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Aplicativos do Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Máquinas Virtuais do Azure](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

O Application Insights Profiler é instalado com a extensão de Diagnóstico do Azure. Você só precisa configurar o Diagnóstico do Azure para instalar o Profiler e enviar perfis para o recurso Application Insights.

## <a name="enable-profiler-for-azure-cloud-services"></a>Habilitar o Profiler para os Serviços de Nuvem do Azure
1. Verifique se você usa o [.NET Framework 4.6.1](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) ou posterior. É suficiente confirmar que os arquivos *ServiceConfiguration.\*.cscfg* têm um valor `osFamily` de "5" ou posterior.

1. Adicione o [SDK do Application Insights aos Serviços de Nuvem do Azure](../../azure-monitor/app/cloudservices.md?toc=/azure/azure-monitor/toc.json).

   >**Há um bug no criador de perfil que é fornecido com a versão mais recente do WAD para serviços de nuvem.** Para usar o criador de perfil com um serviço de nuvem, ele só dá suporte a SDK do AI até versão 2.7.2. Se você estiver usando uma versão mais recente do SDK do AI, você precisará voltar para 2.7.2 para usar o criador de perfil. Se você usar o Visual Studio para fazer downgrade da versão do SDK do App Insights, você pode receber um erro de redirecionamento de associação em tempo de execução. Isso ocorre porque o arquivo Web. config para Microsoft. applicationinsights "newVersion" deve ser definido como "2.7.2.0" depois de fazer o downgrade do SDK do AI, mas ele não é atualizado automaticamente.

1. Acompanhe as solicitações com o Application Insights:

    * Para as funções da Web do ASP.NET, o Application Insights pode rastrear as solicitações automaticamente.

    * Funções de trabalho, [adicione código para acompanhar as solicitações.](profiler-trackrequests.md?toc=/azure/azure-monitor/toc.json)

1. Configure a extensão de Diagnóstico do Azure para habilitar o Profiler, fazendo o seguinte:

     a. Localize o arquivo [diagnostics.wadcfgx](https://docs.microsoft.com/azure/monitoring-and-diagnostics/azure-diagnostics) do *Diagnóstico do Azure* para sua função de aplicativo, como mostrado aqui:  

      ![Local do arquivo de configuração de diagnóstico](./media/profiler-cloudservice/cloudservice-solutionexplorer.png)  

      Se não for possível encontrar o arquivo, confira [Configurar diagnóstico para Máquinas Virtuais e Serviços de Nuvem do Azure](https://docs.microsoft.com/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines).

    b. Adicione a seguinte seção `SinksConfig` como um elemento filho do `WadCfg`:  

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

    > [!NOTE]
    > Se o arquivo *diagnostics.wadcfgx* também contiver outro coletor do tipo ApplicationInsights, todas as três chaves de instrumentação a seguir deverão corresponder:  
    > * A chave que é usada pelo seu aplicativo. 
    > * A chave que é usada pelo coletor ApplicationInsights. 
    > * A chave que é usada pelo coletor ApplicationInsightsProfiler. 
    >
    > Você pode encontrar o valor real da chave de instrumentação que é usado pelo coletor `ApplicationInsights` nos arquivos *ServiceConfiguration.\*.cscfg*. 
    > Após o lançamento do Visual Studio 15.5 do Azure SDK, somente as chaves de instrumentação usadas pelo aplicativo e o coletor ApplicationInsightsProfiler precisam corresponder um ao outro.

1. Implante seu serviço com a nova configuração de diagnóstico e o Application Insights Profiler será configurado para ser executado em seu serviço.
 
## <a name="next-steps"></a>Próximas etapas

* Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
* Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
* Para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
