<properties
	pageTitle="Restaurar um Banco de Dados SQL do Azure (PowerShell) | Microsoft Azure"
	description="Restaure um Banco de Dados SQL do Azure (PowerShell)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/09/2016"
	ms.author="sstein"
	ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar um Banco de Dados SQL do Azure usando o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-restore-deleted-database.md)
- [Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]


## Restaurar o banco de dados excluído em um banco de dados autônomo

1. Obtenha o backup do banco de dados excluído que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie a restauração do backup do banco de dados excluído usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

## Restaurar o banco de dados existente para um pool de banco de dados elástico

1. Obtenha o backup do banco de dados excluído que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx).

        $DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Inicie a restauração do backup do banco de dados excluído usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $DeletedDatabase.ResourceID –ElasticPoolName "elasticpool01" 

## Próximas etapas

- Para obter as etapas detalhadas de como restaurar um banco de dados excluído usando o Portal do Azure, confira [Restore a deleted database using the Azure Portal](sql-database-restore-deleted-database-portal.md) (Restaurar um banco de dados excluído usando o Portal do Azure).
- Para obter informações sobre como restaurar um banco de dados excluído, confira [Restore a deleted database using the REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Restaurar um banco de dados excluído usando a API REST).
- Para obter informações detalhadas sobre como restaurar um banco de dados excluído, confira [Restore a deleted database](sql-database-restore-deleted-database.md) (Restaurar um banco de dados excluído)
- Para obter informações detalhadas sobre os backups automatizados do Banco de Dados SQL do Azure, confira [SQL Database automated backups](sql-database-automated-backups.md) (Backups automáticos do Banco de Dados SQL).

## Recursos adicionais

- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0622_2016-->