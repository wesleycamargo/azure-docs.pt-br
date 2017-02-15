---
title: "Exibir os backups no cofre dos Serviços de Recuperação do Azure | Microsoft Docs"
description: "Referência rápida sobre como exibir os backups no cofre dos Serviços de Recuperação do Azure e o espaço usado por esses backups"
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
ms.openlocfilehash: 723be587128c131670c9905572d808c176362fb8


---
# <a name="view-information-about-your-database-backups-in-long-term-backup-retention"></a>Exibir informações sobre os backups de banco de dados na retenção de backup de longo prazo

Neste tópico de instruções, você aprende a exibir informações sobre os backups de banco de dados na retenção de backup de longo prazo.

## <a name="view-long-term-backup-retention-information-using-the-azure-portal"></a>Exibir informações de retenção de backup de longo prazo usando o portal do Azure 

1. Abra a folha do cofre dos Serviços de Recuperação do Azure (acesse **Todos os recursos** e selecione-o na lista de recursos de sua assinatura) para exibir a quantidade de armazenamento usada pelos backups de banco de dados no cofre.

   ![exibir cofre dos serviços de recuperação com backups](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault-with-data.png)

2. Abra a folha **Banco de dados SQL** do banco de dados.

    ![nova folha de banco de dados de exemplo](./media/sql-database-get-started/new-sample-db-blade.png)

3. Na barra de ferramentas, clique em **Restaurar**.

    ![barra de ferramentas de restauração](./media/sql-database-get-started-backup-recovery/restore-toolbar.png)

4. Na folha Restaurar, clique em **Longo prazo**.

5. Nos backups do cofre do Azure, clique em **Selecionar um backup** para exibir os backups do banco de dados disponíveis na retenção de backup de longo prazo.

    ![backups no cofre](./media/sql-database-get-started-backup-recovery/view-backups-in-vault.png)

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Próximas etapas

- Para configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure, consulte [](sql-database-configure-long-term-retention.md)
- Para restaurar um banco de dados de um backup na retenção de backup de longo prazo, consulte [Restore a database from a backup in long-term backup retention](sql-database-restore-from-long-term-retention.md) (Restaurar um banco de dados de um backup na retenção de backup de longo prazo)
- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


