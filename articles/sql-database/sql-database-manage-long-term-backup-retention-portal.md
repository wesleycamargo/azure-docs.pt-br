---
title: "Portal do Azure: gerenciar retenção de longo prazo de backup de banco de dados | Microsoft Docs"
description: "Referência rápida sobre como configurar, gerenciar e restaurar de retenção de longo prazo de backups de Banco de Dados SQL do Azure automatizados em um cofre dos Serviços de Recuperação do Azure usando o Portal do Azure"
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
ms.date: 12/22/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: f234e50d194c1b8b8deed8282a6ab1250b53d075
ms.openlocfilehash: d055e40305b277338ae85e8d6c53d3d76a78d235
ms.lasthandoff: 02/16/2017


---
# <a name="configure-manage-and-restore-from-long-term-retention-of-database-backups-in-an-azure-recovery-services-vault-using-the-azure-portal"></a>Configure, gerencie e restaure de retenção de longo prazo de backups de banco de dados em um cofre dos Serviços de Recuperação do Azure usando o Portal do Azure

Neste tópico, você aprenderá a configurar, gerenciar e restaurar retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure usando o Portal do Azure. Você também pode executar essa tarefa usando o [PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).

Para obter mais informações sobre a retenção de backup de longo prazo, consulte [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

> [!TIP]
> Para obter um tutorial, consulte [Introdução ao Backup e Restauração para Proteção e Recuperação dos Dados usando o Portal do Azure](sql-database-get-started-backup-recovery-portal.md).
>

## <a name="configure-long-term-retention-using-the-azure-portal"></a>Configurar a retenção de longo prazo usando o portal do Azure

1. Abra a folha **SQL Server** do servidor.

    ![folha do sql server](./media/sql-database-get-started/sql-server-blade.png)

2. Clique em **Retenção de backup de longo prazo**.

   ![link da retenção de backup de longo prazo](./media/sql-database-get-started-backup-recovery/long-term-backup-retention-link.png)

3. Na folha **Retenção de backup de longo prazo**, examine e aceite os termos da visualização (a menos que você já tenha feito isso ou que esse recurso não esteja mais em visualização).

   ![aceitar os termos da visualização](./media/sql-database-get-started-backup-recovery/accept-the-preview-terms.png)

4. Para configurar a retenção de backup de longo prazo para um banco de dados, selecione o banco de dados na grade e clique em **Configurar** na barra de ferramentas.

   ![selecionar o banco de dados para a retenção de backup de longo prazo](./media/sql-database-get-started-backup-recovery/select-database-for-long-term-backup-retention.png)

5. Na folha **Configurar**, clique em **Configurar as definições necessárias** em **Cofre do serviço de recuperação**.

   ![configurar link do cofre](./media/sql-database-get-started-backup-recovery/configure-vault-link.png)

6. Na folha **Cofre dos Serviços de Recuperação**, selecione um cofre existente, se houver. Caso contrário, se nenhum cofre dos serviços de recuperação for encontrado para sua assinatura, clique para sair do fluxo e crie um cofre dos serviços de recuperação.

   ![criar novo link do cofre](./media/sql-database-get-started-backup-recovery/create-new-vault-link.png)

7. Na folha **Cofres dos Serviços de Recuperação**, clique em **Adicionar**.

   ![adicionar novo link do cofre](./media/sql-database-get-started-backup-recovery/add-new-vault-link.png)
   
8. Na folha **Cofre dos Serviços de Recuperação**, forneça um nome válido para o novo cofre dos Serviços de Recuperação.

   ![novo nome do cofre](./media/sql-database-get-started-backup-recovery/new-vault-name.png)

9. Selecione sua assinatura e grupo de recursos, em seguida, selecione o local do cofre. Quando terminar, clique em **Criar**.

   ![criar novo cofre](./media/sql-database-get-started-backup-recovery/create-new-vault.png)

   > [!IMPORTANT]
   > O cofre deve estar localizado na mesma região do servidor lógico do SQL Azure e deve usar o mesmo grupo de recursos como o servidor lógico.
   >

10. Depois do novo cofre ser criado, execute as etapas necessárias para retornar para a folha **Cofre dos Serviços de Recuperação**.

11. Na folha **Cofre dos serviços de recuperação**, clique no cofre, em seguida, clique em **Selecionar**.

   ![selecionar cofre existente](./media/sql-database-get-started-backup-recovery/select-existing-vault.png)

12. Na folha **Configurar**, forneça um nome válido para a nova política de retenção, modifique a política de retenção padrão conforme apropriado e clique em **OK**.

   ![definir política de retenção](./media/sql-database-get-started-backup-recovery/define-retention-policy.png)

13. Na folha **Retenção de backup de longo prazo**, clique em **Salvar** e em **OK** para aplicar a política de retenção de backup de longo prazo a todos os bancos de dados selecionados.

   ![definir política de retenção](./media/sql-database-get-started-backup-recovery/save-retention-policy.png)

14. Clique em **salvar** para habilitar a retenção de backup a longo prazo, usando essa nova política no cofre de serviços de recuperação do Azure que você configurou.

   ![definir política de retenção](./media/sql-database-get-started-backup-recovery/enable-long-term-retention.png)

14. Após a habilitação da retenção de backup de longo prazo, abra a folha **Cofre dos Serviços de Recuperação** (acesse **Todos os recursos** e selecione-o na lista de recursos de sua assinatura).

   ![exibir cofre dos serviços de recuperação](./media/sql-database-get-started-backup-recovery/view-recovery-services-vault.png)


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
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery-portal.md)

## <a name="next-steps"></a>Próximas etapas

- Para gerenciar backups com retenção de longo prazo usando o PowerShell, consulte [Gerenciar a retenção de backup de longo prazo usando o PowerShell](sql-database-manage-long-term-backup-retention-powershell.md)
- Para saber mais sobre backups automáticos gerados pelo serviço, veja [backups automáticos](sql-database-automated-backups.md)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)
