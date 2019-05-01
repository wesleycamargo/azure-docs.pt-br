---
title: 'Referência de API do v2 do Monitor de Status do Azure: Habilitar o monitoramento | Microsoft Docs'
description: Status Monitor v2 API referência Enable-ApplicationInsightsMonitoring. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: e2d964316e83138711547fb18fc5d04c56b4002f
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870516"
---
# <a name="status-monitor-v2-api-enable-applicationinsightsmonitoring-v021-alpha"></a>API de v2 do Monitor de status: Enable-ApplicationInsightsMonitoring (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como um membro do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIÇÃO

Habilitar código sem anexar o monitoramento de aplicativos do IIS em um computador de destino.
Esse cmdlet modificará o IIS applicationHost. config e defina algumas chaves do registro.
Esse cmdlet também criará um applicationinsights.ikey.config, que define qual chave de instrumentação é usado pelo aplicativo.
IIS carregará o RedfieldModule na inicialização, o que injetará SDK do Application Insights em aplicativos como esses aplicativos de inicialização.
Reinicie o IIS para que as alterações entrem em vigor.

Depois de habilitar o monitoramento, é recomendável usar [Live Metrics](live-stream.md) para observar rapidamente se seu aplicativo está enviando telemetria.


> [!NOTE] 
> Para começar, você deve ter uma chave de instrumentação. Para obter mais informações, leia [criar novo recurso](create-new-resource.md#copy-the-instrumentation-key).


> [!IMPORTANT] 
> Esse cmdlet requer uma sessão do PowerShell com permissões de administrador e uma política de execução com privilégios elevados. Leia [aqui](status-monitor-v2-detailed-instructions.md#run-powershell-as-administrator-with-an-elevated-execution-policy) para obter mais informações.

> [!NOTE] 
> Esse cmdlet exigirá que você examine e aceite nossa declaração de licença e privacidade.


## <a name="examples"></a>Exemplos

### <a name="example-with-single-instrumentation-key"></a>Exemplo com a única chave de instrumentação
Neste exemplo, todos os aplicativos no computador atual serão atribuídos uma única chave de instrumentação.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```

### <a name="example-with-instrumentation-key-map"></a>Exemplo com o mapa de chave de instrumentação
Neste exemplo, 
- `MachineFilter` corresponderá a máquina atual usando o `'.*'` curinga.
- `AppFilter='WebAppExclude'` Fornece um `null` InstrumentationKey. Este aplicativo não será instrumentado.
- `AppFilter='WebAppOne'` irá atribuir esse aplicativo específico em uma chave de instrumentação exclusivo.
- `AppFilter='WebAppTwo'` também atribuirá esse aplicativo específico uma chave de instrumentação exclusivo.
- Por fim, `AppFilter` também usa o `'.*'` curinga para corresponder a todos os outros aplicativos da web não correspondido pelas regras anteriores e atribui uma chave de instrumentação padrão.
- Espaços adicionados para facilitar a leitura somente.

```powershell
PS C:\> Enable-ApplicationInsightsMonitoring -InstrumentationKeyMap 
    @(@{MachineFilter='.*';AppFilter='WebAppExclude'},
      @{MachineFilter='.*';AppFilter='WebAppOne';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx1'},
      @{MachineFilter='.*';AppFilter='WebAppTwo';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2'},
      @{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault'})

```


## <a name="parameters"></a>parâmetros 

### <a name="-instrumentationkey"></a>-InstrumentationKey
**Obrigatório.** Use esse parâmetro para fornecer um único iKey para uso por todos os aplicativos no computador de destino.

### <a name="-instrumentationkeymap"></a>-InstrumentationKeyMap
**Obrigatório.** Use esse parâmetro para fornecer várias ikeys e um mapeamento de quais aplicativos usar quais ikey. Você pode criar um script de instalação única para várias máquinas, definindo o MachineFilter. 

> [!IMPORTANT] 
> Aplicativos corresponderá em relação às regras na ordem em que elas são fornecidas. Como tal, você deve especificar as regras mais específicas primeiro e último as regras mais genéricas.

#### <a name="schema"></a>Esquema
`@(@{MachineFilter='.*';AppFilter='.*';InstrumentationKey='xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'})`

- **MachineFilter** é um regex necessária c# do nome do computador ou vm.
    - '. *' corresponderá a todos
    - 'ComputerName' corresponderá apenas computadores com esse nome exato.
- **AppFilter** é um regex necessária c# do nome do computador ou vm.
    - '. *' corresponderá a todos
    - 'ApplicationName' será compatível com aplicativos do IIS somente com esse nome exato.
- **InstrumentationKey** é necessária para habilitar o monitoramento dos aplicativos que correspondem aos dois filtros acima.
    - Deixar esse valor nulo se você quiser definir regras para excluir o monitoramento


### <a name="-enableinstrumentationengine"></a>-EnableInstrumentationEngine
**Opcional** Use essa opção para habilitar o mecanismo de instrumentação coletar eventos e mensagens do que acontece durante a execução de um processo gerenciado. Incluindo mas não limitado a códigos de resultado de dependência, verbos HTTP e texto do comando SQL. O mecanismo de instrumentação adicionará uma sobrecarga adicional e está desativado por padrão.

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional** Use essa opção para aceitar a declaração de licença e privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use esta opção para gerar logs detalhados.

### <a name="-whatif"></a>-WhatIf 
**Parâmetro comum.** Use essa opção para testar e validar seus parâmetros de entrada sem, na verdade, habilitando o monitoramento.

## <a name="output"></a>Saída


#### <a name="example-output-from-a-successful-enablement"></a>Exemplo de saída de uma ativação bem-sucedida

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
Installing GAC module 'C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\0.2.0\content\Runtime\Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.dll'
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
Found GAC module Microsoft.AppInsights.IIS.ManagedHttpModuleHelper.ManagedHttpModuleHelper, Microsoft.AppInsights.IIS.ManagedHttpModuleHelper, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-52z_1'
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'enable'
Configuring IIS Environment for codeless attach...
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
Updating app pool permissions...
Successfully enabled Application Insights Status Monitor
```

