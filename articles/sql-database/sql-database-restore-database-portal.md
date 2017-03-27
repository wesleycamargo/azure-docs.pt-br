---
title: 'Portal do Azure: restaurar um Banco de Dados SQL do Azure | Microsoft Docs'
description: Restaure um Banco de Dados SQL do Azure (Portal do Azure).
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 33b0c9e6-1cd2-4fd9-9b0d-70ecf6e54821
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: NA
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 49800be1a61e45f64a77f8d2662ca9edeae29de9
ms.lasthandoff: 03/10/2017


---
# <a name="restore-an-azure-sql-database-using-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure usando o Portal do Azure

As etapas a seguir mostram como restaurar um Banco de Dados SQL do Azure para um ponto no tempo, tanto de um banco de dados excluído quanto de um backup com redundância geográfica.

## <a name="restore-an-azure-sql-database-to-a-previous-point-in-time"></a>Restaurar um Banco de Dados SQL do Azure para um ponto anterior no tempo 

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao Backup e Restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery-portal.md)
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


## <a name="restore-a-deleted-azure-sql-database-from-backups"></a>Restaurar um Banco de Dados SQL do Azure de backups
Para restaurar um banco de dados excluído com o Portal do Azure:

1. No [Portal do Azure](https://portal.azure.com), clique em **Mais serviços** > **Servidores SQL**.
2. Selecione o servidor que continha o banco de dados que você deseja restaurar.
3. Role para baixo até a seção **operações** da folha do seu servidor e selecione **Bancos de dados excluídos**: ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
4. Selecione o banco de dados que você deseja restaurar.
5. Especifique um nome de banco de dados e clique em **OK**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup"></a>Restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica

Para restaurar geograficamente um banco de dados no Portal do Azure, execute as seguintes etapas:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. No lado esquerdo da tela, selecione **+Novo** > **Bancos de dados** > **Banco de dados SQL**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/new-sql-database.png)
3. Selecione **Backup** como a origem e o backup que você deseja restaurar. Especifique um nome de banco de dados, um servidor para o qual você deseja restaurar o banco de dados e clique em **Criar**:
   
   ![Restaurar um Banco de Dados SQL do Azure](./media/sql-database-geo-restore-portal/geo-restore.png)

4. Monitore o status da operação de restauração clicando no ícone de notificação na parte superior direita da página.

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados para recuperação, confira [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)


