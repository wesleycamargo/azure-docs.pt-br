---
title: 'Portal do Azure: restaurar um Banco de Dados SQL para um ponto no tempo | Microsoft Docs'
description: Restaurar um Banco de Dados SQL do Azure para um ponto anterior no tempo usando o Portal do Azure
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
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 120075710d466a10233aadfc1f7e29da871f8f83
ms.openlocfilehash: 342df453a2e4cc573117fd59fd5c3aa899295439
ms.lasthandoff: 02/16/2017


---
# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurar um Banco de Dados SQL do Azure para um ponto anterior no tempo com o Portal do Azure

Este artigo mostra como restaurar seu banco de dados para um ponto anterior no tempo dos [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md). usando o PowerShell. Essa tarefa também pode ser feita [com o PowerShell](sql-database-point-in-time-restore-powershell.md).

## <a name="restore-to-a-previous-point-in-time"></a>Restaurar para um ponto anterior no tempo 

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

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre como usar backups automatizados de recuperação, veja [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)


