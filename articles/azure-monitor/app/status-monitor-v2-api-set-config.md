---
title: 'Referência de API do v2 do Monitor de Status do Azure: Definir a configuração de | Microsoft Docs'
description: Conjunto de referência do Status Monitor v2 API-ApplicationInsightsMonitoringConfig. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: 953edcb98de6ea705721aef0922562d23e18f0f5
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148256"
---
# <a name="status-monitor-v2-api-set-applicationinsightsmonitoringconfig-v021-alpha"></a>API de v2 do Monitor de status: Set-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como um membro do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIÇÃO

Defina o arquivo de configuração sem repetir uma reinstalação completa. Reinicie o IIS para que as alterações entrem em vigor.

> [!IMPORTANT] 
> Esse cmdlet requer uma sessão do PowerShell com permissões de administrador.


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


### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use esta opção para gerar logs detalhados.


## <a name="output"></a>Saída

Nenhuma saída por padrão.

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkey"></a>Exemplo detalhado de saída desde a configuração de arquivo de configuração por meio de-InstrumentationKey

```
VERBOSE: Operation: InstallWithIkey
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

#### <a name="example-verbose-output-from-setting-the-config-file-via--instrumentationkeymap"></a>Exemplo detalhado de saída desde a configuração de arquivo de configuração por meio de-InstrumentationKeyMap

```
VERBOSE: Operation: InstallWithIkeyMap
VERBOSE: InstrumentationKeyMap parsed:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
VERBOSE: set config file
VERBOSE: Config File Path:
C:\Program Files\WindowsPowerShell\Modules\Az.ApplicationMonitor\content\applicationInsights.ikey.config
```

## <a name="next-steps"></a>Próximas etapas

  Exiba sua telemetria:
 - [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorar o desempenho e o uso
- [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
- [Analise](../../azure-monitor/app/analytics.md) para obter mais consultas avançadas
- [Crie painéis](../../azure-monitor/app/app-insights-dashboards.md)
 
 Adicione mais telemetria:
 - [Criar testes da web](monitor-web-app-availability.md) para garantir que seu site permanece ativo.
- [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver as exceções no código de página da web e permitir que você insira chamadas de rastreamento.
- [Adicione o SDK do Application Insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir o rastreamento e chamadas de log
 
 Faça mais com v2 de Monitor de Status:
 - Use nosso guia para [solucione o problema](status-monitor-v2-troubleshoot.md) v2 do Monitor de Status.
 - [Obter a configuração](status-monitor-v2-api-get-config.md) para confirmar que suas configurações foram registradas corretamente.
 - [Obter o status](status-monitor-v2-api-get-status.md) para inspecionar o monitoramento.
