---
title: "Restaurar um banco de dados SQL do Azure de um backup automático (Portal do Azure) | Microsoft Docs"
description: "Restaurar um banco de dados SQL do Azure de um backup automático (Portal do Azure)."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 62d94085-d7e7-4f08-bb83-404cf866a6c1
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/18/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 85f8f2473e372a72c66153cd4131ba46e1c8c741


---
# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restaurar um banco de dados SQL do Azure de um backup automático usando o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-recovery-using-backups.md#geo-restore)
> * [Restauração geográfica: PowerShell](sql-database-geo-restore-powershell.md)
> 
> 

Este artigo mostra como restaurar seu banco de dados por meio de um [backup automático](sql-database-automated-backups.md) em um novo servidor com a [restauração geográfica](sql-database-recovery-using-backups.md#geo-restore) usando o Portal do Azure.

## <a name="select-a-database-to-restore"></a>Selecionar um banco de dados para ser restaurado
Para restaurar um banco de dados no portal do Azure, faça o seguinte:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **+Novo** > **Bancos de dados** > **Banco de dados SQL**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selecione **Backup** como a origem e o backup que você deseja restaurar. Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em **Criar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

Monitore o status da operação de restauração clicando no ícone de notificação na parte superior direita da página.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)




<!--HONumber=Nov16_HO3-->


