---
title: Restaurar um Banco de Dados SQL do Azure para um determinado momento anterior (Portal do Azure) | Microsoft Docs
description: Restaurar um banco de dados SQL do Azure para um determinado momento anterior.
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: d1822905-a11f-4c42-8940-98c6b81aed20
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6feaea525ae1fceff5acdc95fefa115939d5b1da


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurar um banco de dados SQL do Azure para um determinado momento anterior com o Portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md)
> * [Restauração Pontual: PowerShell](sql-database-point-in-time-restore-powershell.md)
> 
> 

Este artigo mostra como restaurar seu banco de dados para determinado momento anterior por meio de [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) usando o portal do Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurar um banco de dados SQL para um ponto anterior no tempo
Selecione um banco de dados para restaurar no portal do Azure:

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **Mais serviços** > **Bancos de dados SQL**.
3. Clique no banco de dados que deseja restaurar.
4. Na parte superior da página do seu banco de dados, selecione **Restaurar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore.png)
5. Na página **Restaurar**, selecione a data e hora (em hora UTC) para a qual restaurar o banco de dados e, em seguida, clique em **OK**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Monitorar a operação de restauração
1. Depois de clicar em **OK** na etapa anterior, clique no ícone de notificação na parte superior direita da página e clique na notificação **Restaurando banco de dados SQL** para obter detalhes.
   
    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/notification-icon.png)
2. A página Restaurando banco de dados SQL é aberta com informações sobre o status da restauração. É possível clicar no item de linha para obter mais detalhes:
   
    ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-point-in-time-restore-portal/inprogress.png)

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


