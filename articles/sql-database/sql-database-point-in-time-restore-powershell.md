<properties 
    pageTitle="Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior (PowerShell) | Microsoft Azure" 
    description="Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior" 
    services="sql-database" 
    documentationCenter="" 
    authors="stevestein" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management" 
    ms.date="06/09/2016"
    ms.author="sstein"/>

# Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o PowerShell

Este artigo mostra como restaurar seu banco de dados para um ponto anterior de [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) usando o PowerShell.

[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Restaurar o banco de dados para um determinado momento como um banco de dados autônomo

1. Obtenha o banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure o banco de dados para um determinado momento usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"


## Restaure o banco de dados para um determinado momento em um pool de banco de dados elástico
   
1. Obtenha o banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx).

        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Restaure o banco de dados para um determinado momento usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx).
    
        Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" –ResourceId $Database.ResourceID –ElasticPoolName "elasticpool01"

## Próximas etapas

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Recuperação pontual usando o portal do Azure](sql-database-point-in-time-restore-portal.md)
- [Recuperação pontual usando a API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx)
- [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)

## Recursos adicionais

- [Restaurar um banco de dados excluído](sql-database-restore-deleted-database.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)

<!---HONumber=AcomDC_0615_2016-->