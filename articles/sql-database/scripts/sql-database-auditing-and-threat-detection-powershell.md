---
title: "Script do Azure PowerShell – Configurar detecção de ameaças e auditoria do banco de dados | Microsoft Docs"
description: "Amostra de script do Azure PowerShell – Configurar detecção de ameaças e auditoria do Banco de Dados SQL usando o PowerShell"
services: sql-database
documentationcenter: sql-database
author: janeng
manager: jstrauss
editor: carlrab
tags: azure-service-management
ms.assetid: 
ms.service: sql-database
ms.custom: sample
ms.devlang: PowerShell
ms.topic: article
ms.tgt_pltfrm: sql-database
ms.workload: database
ms.date: 03/07/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 25d25ea560d1a750a8a74e1f39f38eca9ffa7489
ms.lasthandoff: 03/10/2017

---

# <a name="configure-sql-database-auditing-and-threat-detection-using-powershell"></a>Configurar a auditoria e detecção de ameaças para um banco de dados SQL usando o PowerShell

Este script do PowerShell de exemplo configura a detecção de ameaças e auditoria do Banco de Dados SQL. 

Antes de executar esse script, certifique-se de que uma conexão com o Azure foi criada usando o cmdlet `Add-AzureRmAccount`.  

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/sql-database/database-auditing-and-threat-detection/database-auditing-and-threat-detection.ps1 "Configurar a auditoria e detecção de ameaças")]

## <a name="clean-up-deployment"></a>Limpar implantação

Após executar o exemplo de script, o comando a seguir pode ser usado para remover o grupo de recursos e todos os recursos associados a ele.

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Explicação sobre o script

Este script usa os seguintes comandos. Cada comando na tabela redireciona para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmSqlServer](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqlserver) | Cria um servidor lógico que hospeda um banco de dados ou pool elástico. |
| [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/new-azurermsqldatabase) | Cria um banco de dados em um servidor lógico como um banco de dados individual ou em pool. |
| [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.4.0/new-azurermstorageaccount) | Cria uma conta de armazenamento. |
| [Set-AzureRmSqlDatabaseAuditingPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabaseauditingpolicy) | Define a política de auditoria para um banco de dados. |
| [Set-AzureRmSqlDatabaseThreatDetectionPolicy](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.5.0/set-azurermsqldatabasethreatdetectionpolicy) | Define uma política de detecção de ameaças em um banco de dados. |
| [Remove-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/remove-azurermresourcegroup) | Exclui um grupo de recursos, incluindo todos os recursos aninhados. |
|||

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira [Documentação do Azure PowerShell](https://docs.microsoft.com/powershell/).

Os exemplos de script do PowerShell do Banco de Dados SQL adicionais podem ser encontrados nos [scripts do PowerShell do Banco de Dados SQL do Azure](../sql-database-powershell-samples.md).

