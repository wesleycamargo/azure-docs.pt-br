---
title: "PowerShell: Criar e gerenciar bancos de dados SQL do Azure únicos | Microsoft Docs"
description: "Referência rápida sobre como criar e gerenciar um banco de dados único SQL do Azure usando o portal do Azure"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: single databases
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 02/06/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: c54e6541dc3694f23237945e22021d5f90a2687d
ms.openlocfilehash: 1d55c5c7fe99a9a744f69bad38faf3ce01d2227f


---
# <a name="create-and-manage-single-azure-sql-databases-with-powershell"></a>Criar e gerenciar bancos de dados SQL do Azure únicos com o PowerShell

Você pode criar e gerenciar um banco de dados SQL do Azure usando o [portal do Azure](https://portal.azure.com/), o PowerShell, o Transact-SQL, a API REST ou C#. Este tópico mostra como usar o PowerShell. Para o portal do Azure, veja [criar e gerenciar bancos de dados individuais com o portal do Azure](sql-database-manage-single-databases-powershell.md). Para o Transact-SQL, confira [Criar e gerenciar bancos de dados únicos com o Transact-SQL](sql-database-manage-single-databases-tsql.md). 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-an-azure-sql-database-using-powershell"></a>Criar um Banco de Dados SQL do Azure usando o PowerShell

Para criar um banco de dados SQL, use o cmdlet [New-AzureRmSqlDatabase](https://docs.microsoft.com/powershell/resourcemanager/azurerm.sql/v2.3.0/new-azurermsqldatabase). O grupo de recursos e o servidor precisam já existir em sua assinatura. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

> [!TIP]
> Para obter um script de exemplo, veja [Criar um script do PowerShell do banco de dados SQL](sql-database-get-started-powershell.md).
>

## <a name="change-the-service-tier-and-performance-level-of-a-single-database"></a>Alterar a camada de serviço e o nível de desempenho de um banco de dados único

Execute o cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433\(v=azure.300\).aspx) e defina o **-RequestedServiceObjectiveName** com o nível de desempenho do tipo de preço desejado, por exemplo, *S0*, *S1*, *S2*, *S3*, *P1*, *P2* etc.

Substitua ```{variables}``` por seus valores (não inclua chaves).

```
$SubscriptionId = "{4cac86b0-1e56-bbbb-aaaa-000000000000}"

$ResourceGroupName = "{resourceGroup}"
$Location = "{AzureRegion}"

$ServerName = "{server}"
$DatabaseName = "{database}"

$NewEdition = "{Standard}"
$NewPricingTier = "{S2}"

Add-AzureRmAccount
Set-AzureRmContext -SubscriptionId $SubscriptionId

Set-AzureRmSqlDatabase -DatabaseName $DatabaseName -ServerName $ServerName -ResourceGroupName $ResourceGroupName -Edition $NewEdition -RequestedServiceObjectiveName $NewPricingTier
```

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral das ferramentas de gerenciamento, consulte [Visão geral das ferramentas de gerenciamento](sql-database-manage-overview.md).
* Para saber como realizar tarefas de gerenciamento usando o Portal do Azure, consulte [Gerenciar Bancos de Dados SQL do Azure usando o Portal do Azure](sql-database-manage-portal.md).
* Para saber como realizar tarefas de gerenciamento usando o PowerShell, consulte [Gerenciar Bancos de Dados SQL do Azure usando o PowerShell](sql-database-manage-powershell.md).
* Para saber como realizar tarefas de gerenciamento usando o SQL Server Management Studio, consulte [SQL Server Management Studio](sql-database-manage-azure-ssms.md).
* Para obter informações sobre o serviço do Banco de Dados SQL, consulte [O que é o Banco de Dados SQL](sql-database-technical-overview.md). 
* Para obter informações sobre os servidores de Banco de Dados do Azure e recursos de banco de dados, confira [Recursos](sql-database-features.md).



<!--HONumber=Feb17_HO2-->


