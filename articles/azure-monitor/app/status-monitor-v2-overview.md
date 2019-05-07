---
title: Visão geral do Azure v2 de Monitor de Status | Microsoft Docs
description: Uma visão geral do Monitor de Status v2. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: 77c2cd9e0eac6717d91a73a6fc033dcaa2390444
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145037"
---
# <a name="status-monitor-v2"></a>Status Monitor v2

V2 do Monitor de status é um módulo do PowerShell publicado para o [PowerShellGallery](https://www.powershellgallery.com/packages/Az.ApplicationMonitor) e é a substituição [Monitor de Status](https://docs.microsoft.com/azure/azure-monitor/app/monitor-performance-live-website-now). Este módulo fornece sem código de instrumentação de aplicativos web do .NET hospedados com o IIS.
Telemetria será enviada ao portal do Azure onde você pode [monitor](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) seu aplicativo.

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="powershell-gallery"></a>Galeria do PowerShell

https://www.powershellgallery.com/packages/Az.ApplicationMonitor


## <a name="instructions"></a>Instruções
- Examine nossa [instruções de Introdução](status-monitor-v2-get-started.md) para começar agora mesmo com exemplos de código conciso.
- Examine nossa [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para uma análise aprofundada sobre como começar.

## <a name="powershell-api-reference"></a>Referência de API do PowerShell
- [Disable-ApplicationInsightsMonitoring](status-monitor-v2-api-disable-monitoring.md)
- [Disable-InstrumentationEngine](status-monitor-v2-api-disable-instrumentation-engine.md)
- [Enable-ApplicationInsightsMonitoring](status-monitor-v2-api-enable-monitoring.md)
- [Enable-InstrumentationEngine](status-monitor-v2-api-enable-instrumentation-engine.md)
- [Get-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-get-config.md)
- [Get-ApplicationInsightsMonitoringStatus](status-monitor-v2-api-get-status.md)
- [Set-ApplicationInsightsMonitoringConfig](status-monitor-v2-api-set-config.md)

## <a name="troubleshooting"></a>solução de problemas
- [Solução de problemas](status-monitor-v2-troubleshoot.md)
- [Problemas Conhecidos](status-monitor-v2-troubleshoot.md#known-issues)


## <a name="faq"></a>Perguntas frequentes

- Monitor de Status v2 dá suporte a instalações de proxy?

  **Sim**. Você tem várias opções para baixar o Monitor de Status v2. Se o computador tiver acesso à internet, você pode integrar à Galeria do PowerShell usando `-Proxy` parâmetros. Você pode baixar manualmente esse módulo e instale-o em seu computador ou usar o módulo diretamente. Cada uma dessas opções é descrita em nossa [instruções detalhadas](status-monitor-v2-detailed-instructions.md).
  
- Como verificar a habilitação foi bem-sucedida?

   Não temos um cmdlet para verificar que essa habilitação foi bem-sucedida. É recomendável usar [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) para observar rapidamente se seu aplicativo está enviando telemetria.

## <a name="next-steps"></a>Próximas etapas

Exiba sua telemetria:

* [Explore as métricas](../../azure-monitor/app/metrics-explorer.md) para monitorar o desempenho e o uso
* [Pesquise eventos e logs](../../azure-monitor/app/diagnostic-search.md) para diagnosticar problemas
* [Analise](../../azure-monitor/app/analytics.md) para obter mais consultas avançadas
* [Crie painéis](../../azure-monitor/app/app-insights-dashboards.md)

Adicione mais telemetria:

* [Criar testes da web](monitor-web-app-availability.md) para garantir que seu site permanece ativo.
* [Adicione telemetria do cliente web](../../azure-monitor/app/javascript.md) para ver as exceções no código de página da web e permitir que você insira chamadas de rastreamento.
* [Adicione o SDK do Application Insights ao seu código](../../azure-monitor/app/asp-net.md) para que você possa inserir o rastreamento e chamadas de log

