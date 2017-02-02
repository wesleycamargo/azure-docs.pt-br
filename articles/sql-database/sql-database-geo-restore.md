---
title: "Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica | Microsoft Docs"
description: "Restaurar um Banco de Dados SQL do Azure em um novo servidor de um backup com redundância geográfica usando o Portal do Azure ou o PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 4b42bffa-f98c-406a-9a96-51721cc423d4
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/19/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6104936089ac9a1a6bbc08a345105e5fa4ae8be7
ms.openlocfilehash: 0ab7090ba7b37b3447da95a3d577cfe82ea8003e


---
# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica

Este artigo mostra como restaurar seu banco de dados para um novo servidor usando a restauração geográfica. Isso pode ser feito por meio do Portal do Azure ou usando o PowerShell.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o Portal do Azure

Para restaurar geograficamente um banco de dados no Portal do Azure, execute as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **+Novo** > **Bancos de dados** > **Banco de dados SQL**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selecione **Backup** como a origem e o backup que você deseja restaurar. Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em **Criar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitore o status da operação de restauração clicando no ícone de notificação na parte superior direita da página.

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica usando o PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="geo-restore-your-database-into-a-standalone-database"></a>Restaurar geograficamente seu banco de dados em um banco de dados autônomo
1. Obtenha o backup geograficamente redundante do banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie a restauração do backup geograficamente redundante usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"

### <a name="geo-restore-your-database-into-an-elastic-pool"></a>Restaurar geograficamente seu banco de dados em um pool elástico
1. Obtenha o backup geograficamente redundante do banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388\(v=azure.300\).aspx).
   
        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Inicie a restauração do backup geograficamente redundante usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx). Especifique o nome do pool no qual você deseja restaurar o banco de dados.
   
        Restore-AzureRmSqlDatabase -FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" -ResourceId $GeoBackup.ResourceID -ElasticPoolName "elasticpool01"  

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
* Para saber mais sobre como usar backups automatizados de recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md).
* Para saber mais sobre opções de recuperação mais rápidas, veja [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md).  
* Para saber mais sobre o uso de backups automatizados para arquivamento, veja [Cópia de Banco de Dados](sql-database-copy.md).




<!--HONumber=Dec16_HO3-->


