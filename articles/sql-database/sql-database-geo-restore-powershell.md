---
title: Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica (PowerShell) | Microsoft Docs
description: Restaurar um Banco de Dados SQL para um novo servidor de um backup com redundância geográfica
services: sql-database
documentationcenter: ''
author: stevestein
manager: jhubbard
editor: ''

ms.service: sql-database
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 07/17/2016
ms.author: sstein

---
# Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o PowerShell
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [Restauração geográfica: Portal do Azure](sql-database-geo-restore-portal.md)
> 
> 

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica. Isso pode ser feito por meio do PowerShell.

[!INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Restaurar geograficamente seu banco de dados em um banco de dados autônomo
1. Obtenha o backup geograficamente redundante do banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie a restauração do backup geograficamente redundante usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

## Restaurar geograficamente seu banco de dados existente em um pool de banco de dados elástico
1. Obtenha o backup geograficamente redundante do banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie a restauração do backup geograficamente redundante usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx). Especifique o nome do pool no qual você deseja restaurar o banco de dados.
   
        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  

## Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre como usar backups automatizados para recuperação, consulte [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre opções de recuperação mais rápidas, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md).
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md).

<!---HONumber=AcomDC_0803_2016-->