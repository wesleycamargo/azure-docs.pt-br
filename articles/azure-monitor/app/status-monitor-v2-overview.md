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
ms.openlocfilehash: fac14281365ccf3c191684af8cfdebda69e734e0
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870441"
---
# <a name="status-monitor-v2"></a>V2 do Monitor de status

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
