---
title: Problemas conhecidos e v2 e solução de problemas do Azure Monitor de Status | Microsoft Docs
description: Os problemas conhecidos do Monitor de Status v2 e exemplos de solução de problemas. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: d640206fd72b4eb89afe5ed1750627823bca9637
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65415886"
---
# <a name="troubleshooting-status-monitor-v2"></a>Solução de problemas de Status monitorar v2

Quando você habilita o monitoramento, você poderá enfrentar problemas que impedem a coleta de dados. Este documento lista todos os problemas conhecidos e solução de problemas de exemplos.
Se você se deparar com um problema não listado aqui, você pode em contato conosco [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues).


> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="known-issues"></a>Problemas conhecidos

### <a name="conflicting-dlls-in-an-applications-bin-directory"></a>DLLs conflitantes no diretório bin de um aplicativo

Se qualquer uma dessas DLLs estiverem presentes no diretório bin, o monitoramento pode falhar.

- Microsoft.ApplicationInsights.dll
- Microsoft.AspNet.TelemetryCorrelation.dll
- System.Diagnostics.DiagnosticSource.dll

Algumas dessas DLLs são incluídas nos modelos de aplicativo padrão do Visual Studio, mesmo se seu aplicativo não usá-los.
Um sintoma comportamento pode ser visto usando as ferramentas de solução de problemas:

- PerfView:
    ```
    ThreadID="7,500" 
    ProcessorNumber="0" 
    msg="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ExtVer="2.8.13.5972" 
    SubscriptionId="" 
    AppName="" 
    FormattedMessage="Found 'System.Diagnostics.DiagnosticSource, Version=4.0.2.1, Culture=neutral, PublicKeyToken=cc7b13ffcd2ddd51' assembly, skipping attaching redfield binaries" 
    ```

- iisreset + aplicativo carregar (sem TELEMETRIA). Investigar com Sysinternals (Handle.exe e ListDLLs.exe)
    ```
    .\handle64.exe -p w3wp | findstr /I "InstrumentationEngine AI. ApplicationInsights"
    E54: File  (R-D)   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Runtime\Microsoft.ApplicationInsights.RedfieldIISModule.dll

    .\Listdlls64.exe w3wp | findstr /I "InstrumentationEngine AI ApplicationInsights"
    0x0000000009be0000  0x127000  C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\MicrosoftInstrumentationEngine_x64.dll
    0x0000000009b90000  0x4f000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.ExtensionsHost_x64.dll
    0x0000000004d20000  0xb2000   C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\Instrumentation64\Microsoft.ApplicationInsights.Extensions.Base_x64.dll
    ```

### <a name="conflict-with-iis-shared-configuration"></a>Conflito com a configuração compartilhada de IIS

Se você tiver um cluster de servidores web, talvez você esteja usando um [configuração compartilhada](https://docs.microsoft.com/iis/web-hosting/configuring-servers-in-the-windows-web-platform/shared-configuration_211). Estamos automaticamente não pode injetar nosso HttpModule esta configuração compartilhada. Cada servidor web primeiro deve executar o comando Enable instalar nossa DLL em seu GAC.

Depois de executar o comando Enable, 
1. navegar até o diretório de configuração compartilhada e encontre seu `applicationHost.config` arquivo.
2. Adicione esta linha para a configuração na seção de módulos:
    ```
    <modules>
        <!-- Registered global managed http module handler. The 'Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll' must be installed in the GAC before this config is applied. -->
        <add name="ManagedHttpModuleHelper" type="Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" preCondition="managedHandler,runtimeVersionv4.0" />
    </modules>
    ```
    


## <a name="troubleshooting"></a>solução de problemas
    
### <a name="troubleshooting-powershell"></a>Solução de problemas do PowerShell

#### <a name="how-to-inspect-what-modules-are-available"></a>Como inspecionar quais módulos estão disponíveis?
Você pode auditar os módulos instalados usando o comando: `Get-Module -ListAvailable`

#### <a name="how-to-import-a-module-into-the-current-session"></a>Como importar um módulo para a sessão atual?
Se o módulo não foi carregado para uma sessão do PowerShell, pode carregar manualmente usando o comando: `Import-Module <path to psd1>`


### <a name="troubleshooting-the-status-monitor-v2-module"></a>O módulo v2 do Monitor de Status de solução de problemas

#### <a name="how-to-review-what-commands-are-available-in-the-status-monitor-v2-module"></a>Como analisar quais comandos estão disponíveis no módulo v2 Status Monitor?
- Execute o comando: `Get-Command -Module Az.ApplicationMonitor` para obter os comandos disponíveis:

    ```
    CommandType     Name                                               Version    Source
    -----------     ----                                               -------    ------
    Cmdlet          Disable-ApplicationInsightsMonitoring              0.2.1      Az.ApplicationMonitor
    Cmdlet          Disable-InstrumentationEngine                      0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-ApplicationInsightsMonitoring               0.2.1      Az.ApplicationMonitor
    Cmdlet          Enable-InstrumentationEngine                       0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    Cmdlet          Get-ApplicationInsightsMonitoringStatus            0.2.1      Az.ApplicationMonitor
    Cmdlet          Set-ApplicationInsightsMonitoringConfig            0.2.1      Az.ApplicationMonitor
    ```

#### <a name="what-is-the-current-version-of-the-status-monitor-v2-module"></a>O que é a versão atual do módulo do Monitor de Status v2?
- Execute o comando: `Get-ApplicationInsightsMonitoringStatus` para obter uma saída de informações sobre este módulo:
    - Versão do módulo do PowerShell
    - Versão do Application Insights SDK
    - Caminhos de arquivo do módulo do PowerShell
    
Examine nossa [referência da API](status-monitor-v2-api-get-status.md) para obter uma descrição detalhada de como usar este cmdlet.



### <a name="troubleshooting-running-processes"></a>Solução de problemas de processos em execução

Você pode inspecionar o processo no computador instrumentado para ver se todas as DLLs são carregadas.
Se o monitoramento está funcionando, pelo menos 12 DLLS devem ser carregados.

- Cmd: `Get-ApplicationInsightsMonitoringStatus -InspectProcess`

Examine nossa [referência da API](status-monitor-v2-api-get-status.md) para obter uma descrição detalhada de como usar este cmdlet.


### <a name="collect-etw-logs-with-perfview"></a>Coletar logs ETW com PerfView

#### <a name="setup"></a>Configuração

1. Baixar PerfView.exe e PerfView64.exe do https://github.com/Microsoft/perfview/releases
2. Iniciar PerfView64.exe
3. Expanda "Opções avançadas"
4. Desmarque a opção:
    - Zip
    - Mesclar
    - Coleção de símbolos do .NET
5. Conjunto de provedores adicionais: `61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,925fa42b-9ef6-5fa7-10b8-56449d7a2040,f7d60e07-e910-5aca-bdd2-9de45b46c560,7c739bb9-7861-412e-ba50-bf30d95eae36,61f6ca3b-4b5f-5602-fa60-759a2a2d1fbd,323adc25-e39b-5c87-8658-2c1af1a92dc5,252e28f4-43f9-5771-197a-e8c7e750a984`


#### <a name="collecting-logs"></a>Coletando logs

1. Em um console de cmd com privilégios de administrador, execute `iisreset /stop` para desativar o IIS e todos os aplicativos web.
2. No PerfView, clique em "Iniciar coleta"
3. Em um console de cmd com privilégios de administrador, execute `iisreset /start` para iniciar o IIS.
4. Tente navegar para seu aplicativo.
5. Quando seu aplicativo termina de carregar, retorne ao PerfView e clique em "Parar coleção"



## <a name="next-steps"></a>Próximas etapas

- Examine nossa [referência da API](status-monitor-v2-overview.md#powershell-api-reference) para encontrar um parâmetro que você pode ter perdido.
- Se você se deparar com um problema não listado aqui, você pode em contato conosco [aqui](https://github.com/Microsoft/ApplicationInsights-Home/issues).
