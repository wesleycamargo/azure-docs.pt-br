---
title: Criar perfis de aplicativos Web em execução em uma VM do Azure com o Application Insights Profiler | Microsoft Docs
description: Crie perfis de aplicativos Web em uma VM do Azure com o Application Insights Profiler.
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
ms.openlocfilehash: 01d57a10189f9281736e628a83465c96d282af70
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55860132"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-a-virtual-machine-scale-set-by-using-application-insights-profiler"></a>Crie perfis de aplicativos Web em execução em uma máquina virtual do Azure ou um conjunto de dimensionamento de máquinas virtuais definido com o Application Insights Profiler

Você também pode implantar o Azure Application Insights Profiler nestes serviços:
* [Serviço de Aplicativo do Azure](../../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem do Azure](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-a-virtual-machine-scale-set"></a>Implantar o Profiler em uma máquina virtual ou em um conjunto de dimensionamento de máquinas virtuais
Este artigo orientará você pelas etapas necessárias para a execução do Application Insights Profiler em seu conjunto de dimensionamento de máquinas virtuais do Azure ou na VM (máquina virtual) do Azure. O Profiler é instalado com a extensão de Diagnóstico do Azure para VMs. Configure a extensão para executar o Profiler e criar o SDK do Application Insights em seu aplicativo.

1. Adicione o SDK do Application Insights ao seu [aplicativo ASP.NET](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou ao [aplicativo .NET](windows-services.md?toc=/azure/azure-monitor/toc.json) comum.  
  Para exibir perfis das suas solicitações, você precisa enviar a telemetria de solicitação para o Application Insights.

1. Instale a extensão do Diagnóstico do Azure na VM. Para exemplos completos de modelos do Resource Manager, consulte:  
    * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Conjunto de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
    
    A chave fundamental é a ApplicationInsightsProfilerSink no WadCfg. Para que o Diagnóstico do Azure habilite o Profiler a enviar dados para o iKey, adicione outro coletor a esta seção.
    
    ```json
      "SinksConfig": {
        "Sink": [
          {
            "name": "ApplicationInsightsSink",
            "ApplicationInsights": "85f73556-b1ba-46de-9534-606e08c6120f"
          },
          {
            "name": "MyApplicationInsightsProfilerSink",
            "ApplicationInsightsProfiler": "85f73556-b1ba-46de-9534-606e08c6120f"
          }
        ]
      },
    ```

1. Implante a definição de implantação de ambiente modificada.  

   A aplicação das modificações geralmente envolve uma implantação de modelo completo ou uma publicação baseada em serviço de nuvem por meio dos cmdlets do PowerShell ou do Visual Studio.  

   Os comandos do PowerShell a seguir são uma abordagem alternativa para máquinas virtuais existentes que interfere apenas na extensão do Diagnóstico do Azure. Adicione o ProfilerSink mencionado anteriormente à configuração que é retornada pelo comando Get-AzureRmVMDiagnosticsExtension e, em seguida, passe a configuração atualizada para o comando Set-AzureRmVMDiagnosticsExtension.

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se o aplicativo desejado estiver em execução por meio do [IIS](https://www.microsoft.com/web/downloads/platform.aspx), habilite o recurso do Windows `IIS Http Tracing`.

    a. Estabeleça acesso remoto ao ambiente e, em seguida, use a janela [Adicionar recursos do Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/). Ou execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto for um problema, você poderá usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o comando a seguir:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implante seu aplicativo.

## <a name="can-profiler-run-on-on-premises-servers"></a>O Profiler pode ser executado em servidores locais?
Não temos planos para dar suporte ao Application Insights Profiler para servidores locais.

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](monitor-web-app-availability.md)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](profiler-overview.md?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Para obter ajuda para a solução de problemas do Profiler, confira [Solução de problemas do Profiler](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
