---
title: Gerenciar e monitorar backup de bancos de dados do SQL Server em uma VM do Azure pelo Backup do Azure | Microsoft Docs
description: Este artigo descreve como restaurar backups de bancos de dados do SQL Server em execução em uma VM do Azure feitos com o Backup do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 1c2ce0ba42f0bc3efd1dcc951113b05ab6941b98
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56430950"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>Gerenciar e monitorar backup de bancos de dados do SQL Server 


Este artigo descreve as tarefas comuns para gerenciamento e monitoramento de bancos de dados do SQL Server em execução em uma VM do Azure dos quais foi feito backup no cofre dos Serviços de Recuperação do Backup do Azure pelo serviço [Backup do Azure](backup-overview.md). As tarefas incluem alertas e trabalhos de monitoramento, interrupções e retomada de proteção do banco de dados, executar trabalhos de backup e cancelar o registro de uma VM do backup.


> [!NOTE]
> O backup de bancos de dados do SQL Server em execução em uma VM do Azure com o Backup do Azure está atualmente em versão prévia pública.


Se você ainda não configurou o backup dos bancos de dados do SQL Server, siga as instruções [neste artigo](backup-azure-sql-database.md)

## <a name="monitor-backup-jobs"></a>Monitorar trabalhos de backup

###  <a name="monitor-ad-hoc-jobs-in-the-portal"></a>Monitorar trabalhos ad-hoc no portal

O Backup do Azure mostra todos os trabalhos disparados manualmente no portal **Trabalhos de backup**, incluindo a descoberta e registro de bancos de dados e operações de backup e restauração.

![Portal de trabalhos de backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Trabalhos de backup agendados não são mostrados no portal **Trabalhos de backup**. Use o SQL Server Management Studio para monitorar trabalhos de backup agendados, conforme descrito na próxima seção.
>

### <a name="monitor-backup-jobs-with-sql-server-management-studio"></a>Monitorar trabalhos de backup com o SQL Server Management Studio 

O Backup do Azure usa APIs nativas do SQL para todas as operações de backup.

Use as APIs nativas para buscar todas as informações de trabalho na [Tabela de conjunto de backup do SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) no banco de dados msdb.

O exemplo a seguir é uma consulta que busca todos os trabalhos de backup de um banco de dados chamado **DB1**. Personalize a consulta para monitoramento avançado.

```
select CAST (
Case type
                when 'D' 
                                 then 'Full'
                when  'I'
                               then 'Differential' 
                ELSE 'Log'
                END         
                AS varchar ) AS 'BackupType',
database_name, 
server_name,
machine_name,
backup_start_date,
backup_finish_date,
DATEDIFF(SECOND, backup_start_date, backup_finish_date) AS TimeTakenByBackupInSeconds,
backup_size AS BackupSizeInBytes
  from msdb.dbo.backupset where user_name = 'NT SERVICE\AzureWLBackupPluginSvc' AND database_name =  <DB1>  

```

## <a name="view-backup-alerts"></a>Exibir alertas de backup

Uma vez que os backups de log ocorrem a cada 15 minutos, monitorar os trabalhos de backup pode ser entediante. O Backup do Azure facilita o monitoramento com alertas por email.

- Os alertas são disparados para todas as falhas de backup.
- Os alertas são consolidados no nível do banco de dados por código de erro.
- Um alerta por email é enviado somente para a primeira falha de backup para um banco de dados. 

Para monitorar os alertas de backup:

1. Entre em sua assinatura do Azure no [portal do Azure](https://portal.azure.com) para monitorar alertas de banco de dados.

2. No painel de cofres, selecione **Alertas e Eventos**.

   ![Selecionar Alertas e Eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. Em **Alertas e Eventos**, selecione **Alertas de Backup**.

   ![Selecionar Alertas de Backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção para um banco de dados do SQL Server

Você pode interromper o backup de um banco de dados do SQL Server de duas maneiras:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interromper todos os trabalhos de backup futuros, mas deixar os pontos de recuperação intactos.

Observe que:

Se você deixar os pontos de recuperação, eles serão apagados de acordo com a política de backup. Você incorre em encargos da instância protegida e do armazenamento consumido até que todos os pontos de recuperação sejam apagados. [ Saiba mais ](https://azure.microsoft.com/pricing/details/backup/) sobre preços.
- Ao deixar os pontos de recuperação intactos, embora eles expirem de acordo com a política de retenção, o Backup do Azure sempre mantém um último ponto de recuperação até que você exclua explicitamente os dados de backup.
- Se você excluir uma fonte de dados sem interromper o backup, novos backups começarão a falhar. Novamente, os pontos de recuperação antigos expirarão de acordo com a política, mas um último ponto de recuperação sempre será retido até que você pare o backup e exclua os dados.
- Não é possível parar o backup de um banco de dados habilitado para proteção automática até que a proteção automática seja desabilitada.

Para interromper a proteção para um banco de dados:

1. No painel do cofre, em **Uso**, selecione **Itens de Backup**.

    ![Abrir o menu Itens de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

2. Em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)


3. Selecione o banco de dados para o qual você deseja interromper a proteção.

    ![Selecionar o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)


5. No menu de banco de dados, selecione **Parar backup**.

    ![Selecionar Parar Backup](./media/backup-azure-sql-database/stop-db-button.png)


6. No menu **Parar Backup**, selecione se deseja reter ou excluir dados. Opcionalmente, forneça um motivo e um comentário.

    ![Interromper o menu de Backup](./media/backup-azure-sql-database/stop-backup-button.png)

7. Clique em **Parar backup**.

  

### <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Se a opção **Reter Dados de Backup** tiver sido selecionada quando a proteção para o banco de dados SQL foi interrompida, você poderá retomar a proteção. Se os dados de backup não tiverem sido mantidos, não será possível retomar a proteção.

1. Para retomar a proteção para o banco de dados SQL, abra o item de backup e selecione **Retomar backup**.

    ![Selecionar Retomar backup para retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

2. No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

Você pode executar diferentes tipos de backups sob demanda:

* Backup completo
* Backup completo somente de cópia
* Backup diferencial
* Backup de log

[Saiba mais](backup-architecture.md#sql-server-backup-types) tipos de backup do SQL Server.

## <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância do SQL Server

Cancele o registro de uma instância do SQL Server depois de desabilitar a proteção, mas antes você deve excluir o cofre:

1. No painel de cofres, em **Gerenciar**, selecione **Infraestrutura de Backup**.  

   ![Selecionar Infraestrutura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. Em **Servidores de Gerenciamento**, selecione **Servidores Protegidos**.

   ![Selecionar Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)


3. Em **Servidores Protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

4. Clique com o botão direito do mouse no servidor protegido > **Excluir**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.png)


## <a name="next-steps"></a>Próximas etapas

[Examine](backup-sql-server-azure-troubleshoot.md) solução de problemas de informações de backup de banco de dados do SQL Server.
