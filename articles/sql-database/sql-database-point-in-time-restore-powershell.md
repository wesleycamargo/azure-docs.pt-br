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
    ms.workload="sqldb-bcdr" 
    ms.date="06/17/2016"
    ms.author="sstein"/>

# Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior com o PowerShell

> [AZURE.SELECTOR]
- [Visão geral](sql-database-point-in-time-restore.md)
- [Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um determinado momento anterior dos [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) usando o PowerShell.

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

- Para obter as etapas detalhadas de como recuperar para um determinado momento usando o Portal do Azure, confira [Point-In-Time Restore using the Azure portal](sql-database-point-in-time-restore-portal.md) (Recuperação Pontual usando o Portal do Azure).
- Para obter informações sobre como recuperar para um determinado momento usando a API REST, confira [Point-In-Time Restore using the REST API](https://msdn.microsoft.com/library/azure/mt163685.aspx) (Recuperação Pontual usando a API REST).
- Para obter uma visão geral da Recuperação Pontual, confira [Point-In-Time Restore](sql-database-point-in-time-restore.md) (Restauração Pontual)
- Para uma discussão completa sobre como se recuperar de um erro de usuário ou aplicativo, confira [User error recovery](sql-database-user-error-recovery.md) (Recuperação de erro do usuário).

## Recursos adicionais

- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->