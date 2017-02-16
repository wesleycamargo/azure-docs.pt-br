---
title: Restaurar um Banco de Dados SQL do Azure para um ponto anterior no tempo | Microsoft Docs
description: Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: NA
ms.date: 12/08/2016
ms.author: sstein; carlrab
translationtype: Human Translation
ms.sourcegitcommit: 75bf523679c8d8ad6fbe4a8aa8a561d03008e59b
ms.openlocfilehash: 4a3c7ae453ead16aec68d362676aa0a000b647ba


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior 

Este artigo de instruções mostra como restaurar seu banco de dados para um ponto anterior no tempo nos [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md). 

## <a name="restore-to-a-previous-point-in-time-using-the-azure-portal"></a>Restaurar para um ponto anterior no tempo usando o Portal do Azure

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao Backup e Restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

Selecione um banco de dados para restaurar no portal do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **Mais serviços** > **Bancos de dados SQL**.
3. Clique no banco de dados que deseja restaurar.
4. Na parte superior da página do seu banco de dados, selecione **Restaurar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Na página **Restaurar**, selecione a data e hora (em hora UTC) para a qual restaurar o banco de dados e, em seguida, clique em **OK**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

6. Depois de clicar em **OK** na etapa anterior, clique no ícone de notificação na parte superior direita da página e clique na notificação **Restaurando banco de dados SQL** para obter detalhes.
   
    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
7. A página Restaurando banco de dados SQL é aberta com informações sobre o status da restauração. É possível clicar no item de linha para obter mais detalhes:
   
    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="restore-to-a-previous-point-in-time-using-powershell"></a>Restaurar para um ponto anterior no tempo usando o PowerShell

[!INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell-h3.md)]

### <a name="restore-your-database-to-a-point-in-time-as-a-single-database"></a>Restaurar o banco de dados para um ponto no tempo como um banco de dados individual
1. Obtenha o banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure o banco de dados para um determinado momento usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -Edition "Standard" -ServiceObjectiveName "S2"

### <a name="restore-your-database-to-a-point-in-time-into-an-elastic-pool"></a>Restaurar o banco de dados para um ponto no tempo em um pool elástico
1. Obtenha o banco de dados que você deseja restaurar usando o cmdlet [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648\(v=azure.300\).aspx).
   
        $Database = Get-AzureRmSqlDatabase -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"
2. Restaure o banco de dados para um determinado momento usando o cmdlet [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390\(v=azure.300\).aspx).
   
        Restore-AzureRmSqlDatabase -FromPointInTimeBackup -PointInTime UTCDateTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.ServerName -TargetDatabaseName "RestoredDatabase" -ResourceId $Database.ResourceID -ElasticPoolName "elasticpool01"

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para exibir o ponto de restauração mais antigo dos backups de um banco de dados gerados pelo serviço, consulte [View oldest restore point](sql-database-view-oldest-restore-point.md) (Exibir o ponto de restauração mais antigo)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)




<!--HONumber=Dec16_HO3-->


