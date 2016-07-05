<properties 
    pageTitle="Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica (PowerShell) | Microsoft Azure" 
    description="Restaurar um Banco de Dados SQL para um novo servidor de um backup com redundância geográfica" 
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

# Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o PowerShell


> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-restore.md)
- [Portal do Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o PowerShell.

[AZURE.INCLUDE [Iniciar sua sessão do PowerShell](../../includes/sql-database-powershell.md)]

## Restauração geográfica do seu banco de dados em um banco de dados autônomo

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

- Para obter as etapas detalhadas de como restaurar um Banco de Dados SQL do Azure usando o portal do Azure de um backup com redundância geográfica, confira [Geo-Restore using the Azure Portal](sql-database-geo-restore-portal.md) (Restauração geográfica com o Portal do Azure)
- Para obter informações detalhadas sobre como restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica, confira [Geo-Restore using PowerShell](sql-database-geo-restore.md) (Restauração geográfica com o PowerShell)
- Para uma discussão completa sobre como se recuperar de uma interrupção, confira [Recover from an outage](sql-database-disaster-recovery.md) (Recuperação de uma interrupção)


## Recursos adicionais

- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->