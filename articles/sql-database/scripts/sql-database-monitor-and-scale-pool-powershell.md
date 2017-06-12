---
title: "Script do Azure PowerShell – Monitorar e dimensionar um pool elástico SQL individual | Microsoft Docs"
description: "Amostra de script do Azure PowerShell – Monitorar e dimensionar um pool elástico de Banco de Dados SQL usando o PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 05/23/2017
ms.author: janeng
ms.translationtype: Human Translation
ms.sourcegitcommit: 67ee6932f417194d6d9ee1e18bb716f02cf7605d
ms.openlocfilehash: 5231f5b13c3502a5a45ceea0b329f30a2e1c311c
ms.contentlocale: pt-br
ms.lasthandoff: 05/26/2017

---

# <a name="monitor-and-scale-a-sql-database-elastic-pool-using-powershell"></a>Monitorar e dimensionar um pool elástico de Banco de Dados SQL usando o PowerShell

Este script do PowerShell de exemplo monitora as métricas de desempenho de um pool elástico, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/sql-database/monitor-and-scale-pool/monitor-and-scale-pool.ps1?highlight=16-17 "Monitorar e dimensionar um Banco de Dados SQL individual")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool) | Cria um pool elástico dentro de um servidor lógico. |
| [New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase) | Cria um banco de dados em um servidor lógico como um banco de dados individual ou em pool. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Mostra as informações de uso do tamanho do banco de dados.|
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Adiciona ou atualiza uma regra de alerta com base em métricas. |
| [Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool) | Atualiza as propriedades do pool elástico |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Define uma regra de alerta para monitorar automaticamente DTUs no futuro. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).

