---
title: Perfil de aplicativos da Web em execução em uma VM do Azure com o Application Insights Profiler | Microsoft Docs
description: Crie um perfil de aplicativos da Web em uma VM do Azure com o criador de perfil do Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 152632c55fc21d2b49f6dfd8ae734833ea870898
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/03/2018
ms.locfileid: "50978359"
---
# <a name="profile-web-apps-running-on-an-azure-virtual-machine-or-virtual-machine-scale-set-with-application-insights-profiler"></a>Perfis de aplicativos da Web em execução em uma escala de máquina virtual ou máquina virtual do Azure definida com o Application Insights Profiler
Você também pode implantar o Profiler do Application Insights nesses serviços:
* [Aplicativos Web do Azure](app-insights-profiler.md?toc=/azure/azure-monitor/toc.json)
* [Serviços de Nuvem](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Service Fabric](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

## <a name="deploy-profiler-on-a-virtual-machine-or-scale-set"></a>Implantar o Criador de perfil em uma máquina virtual ou em um conjunto de escala
Esta página orientará você pelas etapas necessárias para a execução do Profiler do Application Insights em seu conjunto de dimensionamento da VM virtual do Azure ou da máquina virtual do Azure. O Application Insights Profiler é instalado com a extensão de Diagnóstico do Windows Azure para VMs. Você precisa configurar a extensão para executar o criador de perfil e ter o SDK do App Insights integrado em seu aplicativo para obter perfis para seus aplicativos da web em execução em uma VM.

1. Adicione o aplicativo Insights SDK ao seu [aplicativo ASP.Net](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net) ou ao aplicativo [.NET regular.](https://docs.microsoft.com/azure/application-insights/app-insights-windows-services?toc=/azure/azure-monitor/toc.json) Você deve enviar a telemetria de solicitação para o Application Insights, consulte os perfis das suas solicitações.
1. Instale a extensão do Windows Azure Diagnostics na sua VM. Para exemplos completos de modelos do Resource Manager, consulte:  
    * [Máquina virtual](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)
    * [Conjunto de dimensionamento de máquinas virtuais](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)
1. Implante a definição de implantação de ambiente modificada.  

   Para aplicar as modificações, geralmente envolve uma implantação de modelo completo ou uma publicação baseada em serviço de nuvem por meio dos cmdlets do PowerShell ou do Visual Studio.  

   Os seguintes comandos do PowerShell são uma abordagem alternativa para máquinas virtuais existentes que tocam apenas a extensão de Diagnóstico do Azure:  

    ```powershell
    $ConfigFilePath = [IO.Path]::GetTempFileName()
    # After you export the currently deployed Diagnostics config to a file, edit it to include the ApplicationInsightsProfiler sink.
    (Get-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM").PublicSettings | Out-File -Verbose $ConfigFilePath
    # Set-AzureRmVMDiagnosticsExtension might require the -StorageAccountName argument
    # If your original diagnostics configuration had the storageAccountName property in the protectedSettings section (which is not downloadable), be sure to pass the same original value you had in this cmdlet call.
    Set-AzureRmVMDiagnosticsExtension -ResourceGroupName "MyRG" -VMName "MyVM" -DiagnosticsConfigurationPath $ConfigFilePath
    ```

1. Se o aplicativo desejado estiver em execução por meio do [IIS](https://www.microsoft.com/web/downloads/platform.aspx), habilite o recurso do Windows `IIS Http Tracing` fazendo o seguinte:  

   a. Estabeleça o acesso remoto ao ambiente e, em seguida, use a janela [Adicionar Recursos do Windows]( https://docs.microsoft.com/iis/configuration/system.webserver/tracing/) ou execute o seguinte comando no PowerShell (como administrador):  

    ```powershell
    Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All
    ```  
   b. Se estabelecer o acesso remoto for um problema, você poderá usar a [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) para executar o comando a seguir:  

    ```powershell
    az vm run-command invoke -g MyResourceGroupName -n MyVirtualMachineName --command-id RunPowerShellScript --scripts "Enable-WindowsOptionalFeature -FeatureName IIS-HttpTracing -Online -All"
    ```

1. Implante seu aplicativo.

## <a name="can-profiler-run-on-on-premises-servers"></a>Criador de perfil pode executar em servidores locais?
Não temos planos para dar suporte a aplicativos Insights Profiler para servidores locais. 

## <a name="next-steps"></a>Próximas etapas

- Gere tráfego para seu aplicativo (por exemplo, inicie um [teste de disponibilidade](https://docs.microsoft.com/azure/application-insights/app-insights-monitor-web-app-availability)). Em seguida, espere de 10 a 15 minutos para que os rastreamentos comecem a ser enviados à instância do Application Insights.
- Consulte [Rastreamentos do criador de perfil](https://docs.microsoft.com/azure/application-insights/app-insights-profiler-overview?toc=/azure/azure-monitor/toc.json) no portal do Azure.
- Obtenha ajuda para a solução de problemas do criador de perfil em [Solução de problemas do criador de perfil](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).
