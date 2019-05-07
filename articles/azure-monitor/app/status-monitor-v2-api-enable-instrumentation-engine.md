---
title: 'Referência de API do v2 do Monitor de Status do Azure: Habilitar mecanismo de instrumentação | Microsoft Docs'
description: Status Monitor v2 API referência Enable-InstrumentationEngine. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: d886aa364ca928d32100c570689f13beb0c682c9
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143428"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>API de v2 do Monitor de status: Enable-InstrumentationEngine (v0.2.1-alpha)

Este documento descreve um cmdlet que é enviado como um membro do [módulo do Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>DESCRIÇÃO

Esse cmdlet permitirá que o mecanismo de instrumentação, definindo algumas chaves do registro.
Reinicie o IIS para que essas alterações entrem em vigor.

O mecanismo de instrumentação podem complementar os dados coletados pelos SDKs .NET.
Mensagens e eventos serão coletadas que descrevem a execução de um processo gerenciado. Incluindo mas não limitado a códigos de resultado de dependência, verbos HTTP e texto do comando SQL. 

Habilite o mecanismo de instrumentação se:
- Você já tiver habilitado o monitoramento usando o cmdlet Enable, mas não tiver habilitado o InstrumentationEngine.
- Você equipou manualmente seu aplicativo com os SDKs do .NET e coletar telemetria adicional.

> [!IMPORTANT] 
> Esse cmdlet requer uma sessão do PowerShell com permissões de administrador.

> [!NOTE] 
> Esse cmdlet exigirá que você examine e aceite nossa declaração de licença e privacidade.

> [!NOTE] 
> O mecanismo de instrumentação adiciona uma sobrecarga adicional e está desativado por padrão.

## <a name="examples"></a>Exemplos

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>parâmetros 

### <a name="-acceptlicense"></a>-AcceptLicense
**Opcional** Use essa opção para aceitar a declaração de licença e privacidade em instalações sem periféricos.

### <a name="-verbose"></a>-Verbose
**Parâmetro comum.** Use esta opção para gerar logs detalhados.

## <a name="output"></a>Saída


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Exemplo de saída com êxito, habilite o mecanismo de instrumentação

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
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
