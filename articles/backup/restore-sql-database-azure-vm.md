---
title: Restauração de backup de bancos de dados do SQL Server em uma VM do Azure com o Backup do Azure | Microsoft Docs
description: Este artigo descreve como restaurar backups de bancos de dados do SQL Server em execução em uma VM do Azure feitos com o Backup do Azure
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 02/19/2018
ms.author: raynew
ms.openlocfilehash: 9b741f8562ae2e81d297357afd3b0e0e3976a248
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56445433"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Restaurar bancos de dados do SQL Server em VMs do Azure 


Este artigo descreve como restaurar um banco de dados do SQL Server em execução em uma VM do Azure do qual foi feito backup em um cofre dos Serviços de Recuperação do Backup do Azure com o serviço [Backup do Azure](backup-overview.md).


> [!NOTE]
> O backup de bancos de dados do SQL Server em execução em uma VM do Azure com o Backup do Azure está atualmente em versão prévia pública.


Este artigo descreve como restaurar bancos de dados do SQL Server. Se você ainda não configurou o backup dos bancos de dados, siga as instruções [neste artigo](backup-azure-sql-database.md)



## <a name="about-restoring-databases"></a>Sobre restauração de bancos de dados

O Backup do Azure pode restaurar bancos de dados do SQL Server em execução em VMs do Azure da seguinte maneira:

- Restaure a uma data e hora específicas (com precisão de segundos), usando backups de log de transações. O Backup do Azure determina automaticamente o backup diferencial completo adequado e a cadeia de backups de log de transações necessários para restaurar com base na hora selecionada.
- Restaure um backup completo ou diferencial específico para restaurar em um ponto de recuperação específico em vez de uma hora específica.


### <a name="prerequisites"></a>Pré-requisitos

Antes de restaurar um banco de dados, observe o seguinte:

- Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure.
- O servidor de destino precisa ser registrado no mesmo cofre que a fonte.
- Para restaurar um banco de dados criptografado por TDE em outro SQL Server, você precisa primeiro [restaurar o certificado no servidor de destino](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
- Antes de disparar uma restauração de banco de dados "mestre", inicie a instância do SQL Server no modo de usuário único com a opção de inicialização **-m AzureWorkloadBackup**.
    - O valor de **-m** é o nome do cliente.
    - Somente o nome do cliente especificado tem permissão para abrir a conexão.
- Para todos os bancos de dados do sistema (modelo, mestre, msdb), interrompa o serviço SQL Agent antes de disparar a restauração.
- Feche os aplicativos que podem tentar roubar uma conexão com um desses bancos de dados.

## <a name="restore-a-database"></a>Restaurar um banco de dados

Para restaurar você precisará das seguintes permissões:

* Permissões de **Operador de Backup** no cofre em que você está fazendo a restauração.
* Acesso de **Colaborador (gravação)** à VM de origem da qual será feito backup.
* Acesso de **Colaborador (gravação)** à VM de destino:
    - Se você estiver restaurando na mesma VM, esta será a VM de origem.
    - Se você estiver restaurando em uma localização alternativa, será a nova VM de destino. 

Restaure da seguinte maneira:
1. Abra o cofre no qual a VM do SQL Server está registrada.
2. No painel do cofre, em **Uso**, selecione **Itens de Backup**.

    ![Abrir o menu Itens de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. Em **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. Selecione o banco de dados a ser restaurado.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. Examine o menu do banco de dados. Ele fornece informações sobre o backup do banco de dados, incluindo: 

    * Os pontos de restauração mais antigo e mais recentes.
    * O status do backup de log de transações nas últimas 24 horas, para bancos de dados em modo de recuperação bulk-logged e completo se configurado para backups de log de transações.

6. Clique em **Restaurar BD**. 

    ![Selecione Restaurar BD](./media/backup-azure-sql-database/restore-db-button.png)

7. Em **Configuração da Restauração**, especifique o local em que deseja restaurar os dados:
    - **Local Alternativo**: Restaure o banco de dados em um local alternativo e retenha o banco de dados de origem original.
    - **Substituir BD**: Restaure os dados para a mesma instância do SQL Server que a fonte original. O efeito dessa opção é substituir o banco de dados original.

    > [!Important]
    > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Somente **Localização Alternativa** está disponível.
    >

    ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

1. No menu **Configuração da Restauração**, em **Onde Restaurar**, selecione **Localização Alternativa**.
2. Selecione o nome do SQL Server e a instância na qual você deseja restaurar o banco de dados.
3. Na caixa **Nome do BD Restaurado**, digite o nome do banco de dados de destino.
4. Se aplicável, selecione **Substituir se o BD com o mesmo nome já existir na instância do SQL selecionada**.
5. Clique em **OK**.

    ![Forneça valores para o menu de Configuração da Restauração](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. Em **Selecionar ponto de restauração**, selecione se deseja [restaurar a um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou restaurar em um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual só está disponível para backups de log de transações para bancos de dados com um modo de recuperação bulk-logged e completo. 


### <a name="restore-and-overwrite"></a>Restaurar e substituir

1. No menu **Configuração da Restauração**, em **Onde Restaurar**, selecione **Substituir BD** > **OK**.

    ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. Em **Selecionar ponto de restauração**, selecione **Logs (Pontual) para [restaurar a um ponto específico no tempo](#restore-to-a-specific-point-in-time) ou **Completo e Diferencial** para restaurar em um [ponto de recuperação específico](#restore-to-a-specific-restore-point).

    > [!NOTE]
    > A restauração pontual só está disponível para backups de log para bancos de dados com um modelo de recuperação bulk-logged e completo. 




    
### <a name="restore-to-a-specific-point-in-time"></a>Restaurar a um ponto específico no tempo

Se você tiver selecionado **Logs (Pontual)** como o tipo de restauração, faça o seguinte:

1.  Em **Data/hora de restauração**, selecione o mini calendário. No **Calendário**, as datas em negrito têm pontos de recuperação e a data atual está realçada.
2. Selecione uma data com pontos de recuperação. Datas sem pontos de recuperação não podem ser selecionadas.

    ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

3. Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.
4. Especifique uma hora para a recuperação usando o gráfico de linha do tempo ou selecione uma hora. Em seguida, clique em **OK**.

    ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

 
4. No menu **Configuração Avançada**, se você quiser manter o banco de dados não operacional após a restauração, habilite **Restaurar com NORECOVERY**.
5. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino.
6. Clique em **OK**.

    ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)


7. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
8. Acompanhe o progresso da restauração na área **Notificações** ou selecione **Restaurar trabalhos** no menu de banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)



### <a name="restore-to-a-specific-restore-point"></a>Restaurar a um ponto de restauração específico

Se você tiver selecionado **Completo e Diferencial** como o tipo de restauração, faça o seguinte:


1. Selecione um ponto de recuperação da lista e clique em **OK** para concluir o procedimento de ponto de restauração.

    ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)
        
2. No menu **Configuração Avançada**, se você quiser manter o banco de dados não operacional após a restauração, habilite **Restaurar com NORECOVERY**.
3. Se quiser alterar a localização de restauração no servidor de destino, insira um novo caminho de destino. 
4. Clique em **OK**.

    ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

7. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração.
8. Acompanhe o progresso da restauração na área **Notificações** ou selecione **Restaurar trabalhos** no menu de banco de dados.

    ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="next-steps"></a>Próximas etapas

[Gerenciar e monitorar](manage-monitor-sql-database-backup.md) backups de bancos de dados do SQL Server feitos pelo Backup do Azure.
