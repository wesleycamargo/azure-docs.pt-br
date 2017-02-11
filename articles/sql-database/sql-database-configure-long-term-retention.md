---
title: "Configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure | Microsoft Docs"
description: "Referência rápida sobre como configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure"
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
ms.openlocfilehash: b395fc5c1deaf04205dcfccacd13d30ec90dd1e7


---
# <a name="configure-long-term-retention-of-automated-backups-in-an-azure-recovery-services-vault"></a>Configurar a retenção de longo prazo dos backups automatizados em um cofre dos Serviços de Recuperação do Azure 

Neste tópico de instruções, você aprende a configurar a retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure.

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


> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery.md)
>

## <a name="next-steps"></a>Próximas etapas

- Para recuperar um banco de dados com base em um backup na retenção de longo prazo, consulte [recover from a backup in long-term retention](sql-database-restore-from-long-term-retention.md) (Recuperar de um backup em retenção de longo prazo)
- Para exibir os backups no cofre dos Serviços de Recuperação do Azure, consulte [view backups in long-term retention](sql-database-view-backups-in-vault.md) (Exibir backups em retenção de longo prazo)
- Para saber mais sobre os backups automáticos gerados pelo serviço, consulte [backups automáticos](: https://azure.microsoft.com/en-us/documentation/articles/)sql-database-automated-backups.MD)
- Para saber mais sobre a retenção de backup de longo prazo, consulte [retenção de backup de longo prazo](sql-database-long-term-retention.md)
- Para saber mais sobre a restauração a partir de backups, consulte [restaurar a partir do backup](sql-database-recovery-using-backups.md)


<!--HONumber=Dec16_HO2-->


