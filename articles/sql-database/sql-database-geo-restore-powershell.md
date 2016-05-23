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
    ms.workload="data-management" 
    ms.date="05/10/2016"
    ms.author="sstein"/>

# Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica com o PowerShell.

A [Restauração Geográfica](sql-database-geo-restore.md) possibilita restaurar um banco de dados por meio de um backup com redundância geográfica para criar um novo banco de dados. O banco de dados pode ser criado em qualquer servidor em qualquer região do Azure. Como usa um backup com redundância geográfica como sua fonte, ele pode ser usado para recuperar um banco de dados mesmo que este esteja inacessível devido a uma interrupção. A restauração geográfica está habilitada automaticamente para todas as camadas de serviço sem custo extra.

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

- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md)


## Recursos adicionais

- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Documentação do Banco de Dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->