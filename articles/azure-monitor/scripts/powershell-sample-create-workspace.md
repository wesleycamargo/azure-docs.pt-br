---
title: Script de exemplo do Azure PowerShell – criar um workspace do Log Analytics | Microsoft Docs
description: Script de exemplo do Azure PowerShell – criar um workspace do Log Analytics
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 5ad04c52da4709a7097ff7915d7af7404d6725eb
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/08/2018
ms.locfileid: "53110263"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Criar um workspace do Log Analytics com o PowerShell

Esse script coloca rapidamente em funcionamento um workspace do Azure Log Analytics, o que é necessário se você deseja começar a coletar, analisar e executar ações nos dados.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um novo workspace do Log Analytics em sua assinatura. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/get-azurermoperationalinsightsworkspace) | Obtém informações sobre um workspace existente. |
| [New-AzureRmOperationalInsightsWorkspace](/powershell/module/azurerm.operationalinsights/new-azurermoperationalinsightsworkspace) | Cria um workspace no grupo de recursos e no local especificados. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

