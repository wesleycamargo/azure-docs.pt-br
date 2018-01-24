---
title: "Exemplo do PowerShell para monitorar e dimensionar um único Banco de Dados SQL do Azure | Microsoft Docs"
description: "Script de exemplo do Azure PowerShell para monitorar e dimensionar um único banco de dados SQL do Azure"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 12/14/2017
ms.author: janeng
ms.openlocfilehash: 70dcac2449b18e7afef3f96817593128ca1ab315
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2017
---
# <a name="use-powershell-to-monitor-and-scale-a-single-sql-database"></a>Usar o PowerShell para monitorar e dimensionar um único banco e dados SQL

Este exemplo de script do PowerShell monitora as métricas de desempenho de um banco de dados, dimensiona-o para um nível de desempenho mais alto e cria uma regra de alerta em uma das métricas de desempenho. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/sql-database/monitor-and-scale-database/monitor-and-scale-database.ps1?highlight=13-14 "Monitor and scale single SQL Database")]

> [!TIP]
> Use [Get-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/get-azurermsqldatabaseactivity) para obter o status das operações de banco de dados, e use [Stop-AzureRmSqlDatabaseActivity](/powershell/module/azurerm.sql/stop-azurermsqldatabaseactivity) para cancelar uma operação de atualização no banco de dados.

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourcegroupname
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Get-Help | Observações |
|---|---|
 [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [Get-AzureRmMetric](/powershell/module/azurerm.insights/get-azurermmetric) | Mostra as informações de uso do tamanho do banco de dados.|
| [Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase) | Atualiza as propriedades do banco de dados ou move um banco de dados para dentro, para fora entre pools elásticos. |
| [Add-AzureRMMetricAlertRule](/powershell/module/azurerm.insights/add-azurermmetricalertrule) | Define uma regra de alerta para monitorar automaticamente DTUs no futuro. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).
