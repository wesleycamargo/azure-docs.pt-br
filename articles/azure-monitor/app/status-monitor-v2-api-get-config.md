---
title: 'Referência de API do v2 do Monitor de Status do Azure: Obter configuração | Microsoft Docs'
description: Status Monitor v2 API referência Get-ApplicationInsightsMonitoringConfig. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: 28893e0782a7fa928757bbbba42662e6d3fa272c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153577"
---
# <a name="status-monitor-v2-api-get-applicationinsightsmonitoringconfig-v021-alpha"></a>API de v2 do Monitor de status: Get-ApplicationInsightsMonitoringConfig (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como um membro do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIÇÃO

Obtenha o arquivo de configuração e imprimir os valores no console.

> [!IMPORTANT] 
> Esse cmdlet requer uma sessão do PowerShell com permissões de administrador.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Get-ApplicationInsightsMonitoringConfig
```

## <a name="parameters"></a>parâmetros 

(Sem parâmetros necessários)

## <a name="output"></a>Saída


#### <a name="example-output-from-reading-the-config-file"></a>Exemplo de saída da leitura do arquivo de configuração

```
RedfieldConfiguration:
Filters:
0)InstrumentationKey:  AppFilter: WebAppExclude MachineFilter: .*
1)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx2 AppFilter: WebAppTwo MachineFilter: .*
2)InstrumentationKey: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxdefault AppFilter: .* MachineFilter: .*
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
 - Fazer alterações a essa configuração usando o [Set config](status-monitor-v2-api-set-config.md) cmdlet.
