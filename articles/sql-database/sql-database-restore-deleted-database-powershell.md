---
title: Restaurar um Banco de Dados SQL do Azure (PowerShell) | Microsoft Docs
description: Restaure um Banco de Dados SQL do Azure (PowerShell).
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: ce7f0f3f-47a6-42af-b8a9-4a34bbbd8966
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3ba16154857f8e7b59a1013b736d6131a4161185
ms.openlocfilehash: 1ee79438f40336c3632accc0e3c228d8f2851afd


---
# <a name="restore-a-deleted-azure-sql-database-by-using-powershell"></a>Restaurar um Banco de Dados SQL do Azure usando o PowerShell
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [Restaurar o banco de dados excluído: portal](sql-database-restore-deleted-database-portal.md)
> * [**Restaurar o banco de dados excluído: PowerShell**](sql-database-restore-deleted-database-powershell.md)
> 
> 

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="get-a-list-of-deleted-databases"></a>Obter uma lista de bancos de dados excluídos
```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"

$DeletedDatabases = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName
```

## <a name="restore-your-deleted-database-into-a-single-database"></a>Restaurar o banco de dados excluído em um banco de dados individual
Obtenha o backup do banco de dados excluído que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx). Inicie a restauração do backup do banco de dados excluído usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx).

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase -FromDeletedDatabaseBackup -DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"
```


## <a name="restore-your-deleted-database-into-an-elastic-pool"></a>Restaurar o banco de dados excluído em um pool elástico
Obtenha o backup do banco de dados excluído que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387\(v=azure.300/\).aspx). Inicie a restauração do backup do banco de dados excluído usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300/\).aspx).

```
$resourceGroupName = "resourcegroupname"
$sqlServerName = "servername"
$databaseName = "deletedDbToRestore"

$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $resourceGroupName -ServerName $sqlServerName -DatabaseName $databaseName

Restore-AzureRmSqlDatabase -FromDeletedDatabaseBackup -DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $DeletedDatabase.ResourceID -ElasticPoolName "elasticpool01"
```


## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)




<!--HONumber=Dec16_HO3-->


