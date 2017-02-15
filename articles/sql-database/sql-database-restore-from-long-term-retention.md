---
title: "Restaurar um banco de dados de um backup no cofre dos Serviços de Recuperação do Azure | Microsoft Docs"
description: "Referência rápida de como restaurar com banco de dados de um backup no cofre dos Serviços de Recuperação do Azure e o espaço usado por esses backups"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
ms.date: 12/07/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 6d729f89322b67fe75d2c0ad001e70b42ccedb7e
ms.openlocfilehash: cdb898ef86517b6eb6eed4b2ae38c4e327e78181


---
# <a name="restore-a-database-from-a-backup-in-long-term-backup-retention"></a>Restaurar um banco de dados a partir de um backup na retenção de backup de longo prazo

Neste tópico de tutorial, você aprende a restaurar um banco de dados de um backup na retenção de backup de longo prazo.

## <a name="restore-from-long-term-backup-retention-using-the-azure-portal"></a>Restaurar da retenção de backup de longo prazo usando o portal do Azure

1. Na folha **Backups do cofre do Azure**, clique no backup para restaurar, em seguida, clique em **Selecionar**.

    ![selecionar backup no cofre](./media/sql-database-get-started-backup-recovery/select-backup-in-vault.png)

2. Na caixa de texto **Nome do banco de dados**, forneça o nome do banco de dados restaurado.

    ![novo nome do banco de dados](./media/sql-database-get-started-backup-recovery/new-database-name.png)

3. Clique em **OK** para restaurar seu banco de dados a partir do backup no cofre para o novo banco de dados.

4. Na barra de ferramentas, clique no ícone de notificação para exibir o status do trabalho de restauração.

    ![progresso do trabalho de restauração no cofre](./media/sql-database-get-started-backup-recovery/restore-job-progress-long-term.png)

5. Quando o trabalho de restauração for concluído, abra a folha **Bancos de dados SQL** para exibir o banco de dados recém-restaurado.

    ![banco de dados restaurado a partir do cofre](./media/sql-database-get-started-backup-recovery/restored-database-from-vault.png)

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Próximas etapas

- Para configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure, consulte [Configure long-term backup retention](sql-database-configure-long-term-retention.md) (Configurar a retenção de backups de longo prazo)
- Para exibir os backups no cofre dos Serviços de Recuperação do Azure, consulte [view backups in long-term retention](sql-database-view-backups-in-vault.md) (Exibir backups em retenção de longo prazo)
- Para saber mais sobre os backups automáticos gerados pelo serviço, consulte [backups automáticos](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


