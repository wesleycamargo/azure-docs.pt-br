---
title: O Azure v2 do Monitor de Status de Introdução | Microsoft Docs
description: Um guia de início rápido para o Monitor de Status v2. Monitorar o desempenho do site sem reimplantar o site. Funciona com aplicativos web ASP.NET hospedado no local, em máquinas virtuais ou no Azure.
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
ms.openlocfilehash: c20cbf50129247fa01f10f12a99c558bc86492b8
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145173"
---
# <a name="getting-started-with-status-monitor-v2"></a>Introdução ao Monitor de Status v2

Este documento contém os comandos de início rápido, esperados que funcione para a maioria dos ambientes. Essas instruções dependem da Galeria do PowerShell para distribuir atualizações. Suportam a esses comandos do PowerShell `-Proxy` parâmetro.

Examine nossa [instruções detalhadas](status-monitor-v2-detailed-instructions.md) página para obter uma explicação de como esses comandos, instruções sobre como personalizar e como solucionar problemas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. 

> [!IMPORTANT]
> Status Monitor v2 está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [termos complementares de uso para visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="download--install-via-powershell-gallery"></a>Baixar e instalar por meio da Galeria do PowerShell

### <a name="install-prerequisites"></a>Instalar pré-requisitos
Execute o PowerShell como administrador
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-PackageProvider -Name NuGet -MinimumVersion 2.8.5.201 -Force
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
Install-Module -Name PowerShellGet -Force
``` 
Saia do PowerShell

### <a name="install-status-monitor-v2"></a>Status de instalação monitorar v2
Execute o PowerShell como administrador
```powershell   
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Install-Module -Name Az.ApplicationMonitor -AllowPrerelease -AcceptLicense
``` 

### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process -Force
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
```
    
        
## <a name="download--install-manually-offline-option"></a>Baixar e instalar manualmente (opção offline)
### <a name="manual-download"></a>Download manual
Baixe manualmente a versão mais recente do módulo: https://www.powershellgallery.com/packages/Az.ApplicationMonitor

### <a name="unzip-and-install-status-monitor-v2"></a>Descompacte e instale o Monitor de Status v2
```powershell
$pathToNupkg = "C:\Users\t\Desktop\Az.ApplicationMonitor.0.2.1-alpha.nupkg"
$pathToZip = ([io.path]::ChangeExtension($pathToNupkg, "zip"))
$pathToNupkg | rename-item -newname $pathToZip
$pathInstalledModule = "$Env:ProgramFiles\WindowsPowerShell\Modules\Az.ApplicationMonitor"
Expand-Archive -LiteralPath $pathToZip -DestinationPath $pathInstalledModule
```
### <a name="enable-monitoring"></a>Habilitar o monitoramento
```powershell
Enable-ApplicationInsightsMonitoring -InstrumentationKey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
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

- Examine os [instruções detalhadas](status-monitor-v2-detailed-instructions.md) para obter uma explicação dos comandos encontrados neste guia.
- Use nosso guia para [solucione o problema](status-monitor-v2-troubleshoot.md) v2 do Monitor de Status.
