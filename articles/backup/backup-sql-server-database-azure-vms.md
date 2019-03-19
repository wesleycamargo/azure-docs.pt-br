---
title: Fazer backup de bancos de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica como fazer backup do SQL Server para o Azure.
services: backup
author: sachdevaswati
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sachdevaswati
ms.openlocfilehash: 5fe4161c688570cc3adaacc79a0dd1fe38460142
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/16/2019
ms.locfileid: "58118894"
---
# <a name="back-up-sql-server-databases-in-azure-vms"></a>Fazer backup de bancos de dados do SQL Server em VMs do Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. Faça backup de bancos de dados do SQL Server em execução em VMs do Azure usando o [Backup do Azure](backup-overview.md).

Este artigo mostra como fazer backup de um banco de dados do SQL Server em execução em uma VM do Azure em um cofre dos Serviços de Recuperação do Backup do Azure. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar e configurar um cofre.
> * Descobrir bancos de dados e configurar backups.
> * Configurar a proteção automática para bancos de dados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de seu banco de dados do SQL Server, verifique as condições a seguir:

1. Identificar ou [criar](backup-azure-sql-database.md#create-a-recovery-services-vault) um cofre de serviços de recuperação na mesma região ou a localidade que a VM que hospeda a instância do SQL Server.
2. [Verifique as permissões da VM](#fix-sql-sysadmin-permissions) necessárias para fazer backup dos bancos de dados SQL.
3. Verifique se a VM tem [conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).
4. Verifique se os bancos de dados do SQL Server estão nomeados de acordo com as [diretrizes de nomenclatura](#verify-database-naming-guidelines-for-azure-backup) do Backup do Azure.
5. Verifique se não há nenhuma outra solução de backup habilitada para o banco de dados. Desabilite todos os outros backups do SQL Server antes de configurar esse cenário. Você pode habilitar o Backup do Azure para uma VM do Azure, juntamente com o Backup do Azure para um banco de dados do SQL Server em execução na VM sem nenhum conflito.


### <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual (VM) do SQL Server precisa ter conectividade com os endereços IP públicos do Azure. Operações de VM (descoberta de banco de dados, configurar backups, agendar backups, restaurar os pontos de recuperação e assim por diante) falhar sem conectividade com os endereços IP públicos. Estabeleça a conectividade com uma destas opções:

- **Permitir os intervalos de IP do datacenter do Azure**: Permita os [intervalos de IP](https://www.microsoft.com/download/details.aspx?id=41653) no download. Para acessar o grupo de segurança de rede (NSG), use o **Set-AzureNetworkSecurityRule** cmdlet.
- **Implantar um servidor proxy HTTP para rotear o tráfego**: Quando você faz backup de um banco de dados do SQL Server em uma VM do Azure, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o Backup do Azure e dados para o Armazenamento do Azure. A extensão de backup também usa o Azure AD (Azure Active Directory) para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. A extensão é o único componente que está configurado para acesso à internet pública.

Cada opção traz vantagens e desvantagens

**Opção** | **Vantagens** | **Desvantagens**
--- | --- | ---
Permitir intervalos de IP | Sem custo adicional. | É complexa de ser gerenciada porque os intervalos de endereços IP mudam com o tempo. <br/><br/> Fornece acesso a todo o Azure, não somente ao Armazenamento do Azure.
Usar um proxy HTTP   | É permitido o controle granular no proxy em relação às URLs de armazenamento. <br/><br/> Único ponto de acesso à Internet para VMs. <br/><br/> Não está sujeito a alterações do endereço IP do Azure | Custos adicionais para executar uma VM com o software de proxy.

### <a name="set-vm-permissions"></a>Definir permissões da VM

O Backup do Azure realiza uma série de ações quando você configura o backup de um banco de dados do SQL Server:

- Adiciona a extensão **AzureBackupWindowsWorkload**.
- Para descobrir bancos de dados na máquina virtual, o Backup do Azure cria uma conta **NT SERVICE\AzureWLBackupPluginSvc**. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL.
- O Backup do Azure aproveita a conta **NT AUTHORITY\SYSTEM** para descoberta/consulta de banco de dados e, portanto, essa conta precisa ser um logon público no SQL.

Se você não criou a VM do SQL Server no Azure Marketplace, talvez você receba um erro **UserErrorSQLNoSysadminMembership**. Se isso ocorrer, [siga estas instruções](#fix-sql-sysadmin-permissions).

### <a name="verify-database-naming-guidelines-for-azure-backup"></a>Verificar as diretrizes de nomenclatura de banco de dados do Backup do Azure

Evite usar o seguinte em nomes de banco de dados:

  * Espaços à esquerda ou à direita
  * '!' à direita
  * Colchete de fechamento ‘]’

Temos nomes alternativos para caracteres não compatíveis com a tabela do Azure, mas recomendamos evitá-los. [Saiba mais](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).


[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

Descubra os bancos de dados em execução na VM.

1. No [portal do Azure](https://portal.azure.com), abra o cofre dos Serviços de Recuperação usado para fazer backup do banco de dados.

2. No painel **Cofre dos serviços de recuperação**, selecione **Backup**.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. Em **Meta de Backup**, defina **Localização na qual a carga de trabalho está sendo executada** como **Azure** (o padrão).

4. Em **Do que você deseja fazer backup**, selecione **SQL Server em uma VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

5. Em **Meta de Backup** > **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar as VMs não protegidas na assinatura. Isso poderá levar alguns instantes, dependendo do número de máquinas virtuais não protegidas na assinatura.

   - As VMs não protegidas devem ser exibidas na lista após a descoberta, listadas por nome e grupo de recursos.
   - Se uma VM não estiver listada conforme o esperado, verifique se ela já foi copiada em backup em um cofre.
   - Várias VMs podem ter o mesmo nome, mas elas pertencerão a diferentes grupos de recursos.

     ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de VM, selecione a VM que executa o banco de dados do SQL Server > **Descobrir BDs**.

7. Acompanhe a descoberta de banco de dados na área **Notificações**. Poderá levar alguns instantes para o trabalho ser concluído, dependendo de quantos bancos de dados existem na VM. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

8. O Backup do Azure descobre todos os bancos de dados do SQL Server na VM. Durante a descoberta, ocorre o seguinte em segundo plano:

    - O Backup do Azure registra a VM no cofre para backup da carga de trabalho. Todos os bancos de dados na VM registrada só podem ser copiados em backup nesse cofre.
    - O Backup do Azure instala a extensão **AzureBackupWindowsWorkload** na VM. Nenhum agente é instalado no Banco de Dados SQL.
    - O Backup do Azure cria a conta de serviço **NT Service\AzureWLBackupPluginSvc** na VM.
      - Todas as operações de backup e restauração usam a conta de serviço.
      - **NT Service\AzureWLBackupPluginSvc** precisa de permissões de sysadmin do SQL. Todas as VMs do SQL Server criadas no Azure Marketplace são fornecidas com a **SqlIaaSExtension** instalada. A extensão **AzureBackupWindowsWorkload** usa a **SQLIaaSExtension** para obter automaticamente as permissões necessárias.
    - Se você não criou a VM por meio do marketplace, a VM não tem a **SqlIaaSExtension** instalada e a operação de descoberta falha com a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Siga as instruções em [#fix-sql-sysadmin-permissions] para corrigir esse problema.

        ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup"></a>Configurar o backup  

Configure o backup da seguinte maneira:

1. Em **Meta de Backup**, selecione **Configurar Backup**.

   ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

2. Clique em **configurar o Backup**, o **selecionar itens para backup** folha é exibida. Isso lista todos os grupos de disponibilidade registrado e autônomo SQL Servers. A divisa de expansão à esquerda da linha para ver todos os bancos de dados desprotegidos nessa instância ou AlwaysOn AG.  

    ![Exibindo todas as Instâncias do SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

3. Selecione todos os bancos de dados que deseja proteger > **OK**.

   ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

   Para otimizar as cargas de backup, o Backup do Azure define um número máximo de bancos de dados em um trabalho de backup como 50.

     * Para proteger mais de 50 bancos de dados, configure vários backups.
     * Como alternativa, você pode habilitar a [proteção automática](#enable-auto-protection) no grupo instância inteira ou grupo de disponibilidade Always On selecionando a opção **ATIVAR** na lista suspensa correspondente na coluna **PROTEÇÃO AUTOMÁTICA**. O recurso de [proteção automática](#enable-auto-protection) não só permite a proteção em todos os bancos de dados existentes de uma só vez, mas protege automaticamente quaisquer novos bancos de dados que serão adicionados no futuro para essa instância ou o grupo de disponibilidade.  

4. Clique em **Okey** para abrir o **política de Backup** folha.

    ![Desabilite a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

5. Na **escolher política de backup**, selecione uma política e clique em **Okey**.

   - Selecionar a política padrão: HourlyLogBackup.
   - Escolher uma política de backup existente criada anteriormente para SQL.
   - [Definir uma nova política](#configure-a-backup-policy) baseada no seu período de retenção e o RPO.

     ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

6. Na **Backup** menu, selecione **habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

7. Acompanhar o progresso da configuração na **notificações** área do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)

### <a name="create-a-backup-policy"></a>Criar uma política de backup

Uma política de backup define quando os backups são feitos e por quanto tempo eles são mantidos.

- Uma política é criada no nível do cofre.
- Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre.
- Quando você cria uma política de backup, um backup completo diário é o padrão.
- Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente.
- [Saiba mais sobre](backup-architecture.md#sql-server-backup-types) os diferentes tipos de políticas de backup.

Para criar uma política de backup:

1. No cofre, clique em **Políticas de backup** > **Adicionar**.
2. No menu **Adicionar**, clique em **SQL Server em uma VM do Azure**. Isso define o tipo de política.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

3. Em **Nome da política**, insira um nome para a nova política.
4. Em **Política de Backup Completo**, selecione uma **Frequência de Backup** escolhendo **Diária** ou **Semanal**.

   - Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar.
   - É necessário executar um backup completo, não sendo possível desligar a opção **Backup Completo**.
   - Clique em **Backup Completo** para exibir a política.
   - Você não pode criar backups diferenciais para backups diários completos.
   - Para **Semanal**, selecione o dia da semana, a hora e o fuso horário do início do trabalho de backup.

     ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)  

5. Em **Período de Retenção**, por padrão, todas as opções estão selecionadas. Desmarque os limites de período de retenção que não deseja usar e defina os intervalos a serem usados.

    - Período de retenção mínimo para qualquer tipo de backup (completo/diferencial/log) é 7 dias.
    - Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia.
    - O backup para um dia específico é marcado e mantido com base no intervalo de retenção semanal e sua configuração de retenção semanal.
    - Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

   ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

6. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.
7. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**.

   ![Configurações do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)
   ![Abrir o menu da política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

8. Em **Política de Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência.

    - No máximo, você pode acionar um backup diferencial por dia.
    - Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.

9. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

10. Para adicionar uma política de backup de log transacional, selecione **Backup de Log**.
11. Em **Backup de Log**, selecione **Habilitar** e, em seguida, defina os controles de retenção e frequência. Os Backups de log podem ocorrer a cada 15 minutos e podem ser mantidos por 35 dias.
12. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

    ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

13. No menu **Política de backup**, escolha se deseja habilitar a **Compactação de Backup SQL**.
    - A compactação está desabilitada por padrão.
    - No back-end, o Backup do Azure usa compactação de backup nativo do SQL.

14. Depois de concluir as edições à política de backup, selecione **OK**.


### <a name="modify-policy"></a>Modificar política
Modificar a política para alterar o intervalo de frequência ou retenção de backup.

> [!NOTE]
> Qualquer alteração no período de retenção será aplicada retrospectively para todos os pontos de recuperação mais antigos além de novos.

No painel do cofre, acesse **Manage** > **políticas de Backup** e escolha a política que você deseja editar.

  ![Gerenciar política de backup](./media/backup-azure-sql-database/modify-backup-policy.png)


## <a name="enable-auto-protection"></a>Habilitar a proteção automática  

Habilite a proteção automática para fazer backup automaticamente de todos os bancos de dados existentes e dos bancos de dados que serão adicionados no futuro a uma Instância autônoma do SQL Server ou a um Grupo de Disponibilidade Always On do SQL Server.

  - Quando você ativar a proteção automática e selecionar uma política, os bancos de dados protegidos existentes continuarão usando a política anterior.
  - Não há nenhum limite no número de bancos de dados que podem ser selecionados para proteção automática de uma só vez.

Habilite a proteção automática da seguinte maneira:

  1. Em **Itens para backup**, selecione a instância na qual deseja habilitar a proteção automática.
  2. Selecione a lista suspensa em **Proteção automática** e defina essa opção como **Ativado**. Em seguida, clique em **OK**.

      ![Habilite a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

  3. O backup é configurado para todos os bancos de dados juntos e pode ser acompanhado em **Trabalhos de Backup**.

Se você precisar desativar proteção automática, clique no nome de instância sob **configurar o Backup**e selecione **desabilitar proteger automaticamente** para a instância. Todos os bancos de dados continuarão sendo copiados em backup. No entanto, os bancos de dados futuros não serão protegidos automaticamente.


## <a name="fix-sql-sysadmin-permissions"></a>Corrigir permissões de sysadmin do SQL

  Caso precise corrigir as permissões devido a um erro **UserErrorSQLNoSysadminMembership**, faça o seguinte:

  1. Use uma conta com permissões de sysadmin do SQL Server para entrar no SSMS (SQL Server Management Studio). A menos que você precise de permissões de acesso especiais, a autenticação do Windows deverá funcionar.
  2. No SQL Server, abra a pasta **Segurança/Logons**.

      ![Abra a pasta Segurança/Logons para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

  3. Clique com o botão direito do mouse na pasta **Logons** e selecione **Novo Logon**. Em **Logon – Novo**, selecione **Pesquisar**.

      ![Na caixa de diálogo Logon – Novo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

  4. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante a fase de descoberta do SQL e do registro da máquina virtual. Insira o nome da conta, conforme mostrado em **Inserir o nome do objeto a ser selecionado**. Selecione **Verificar Nomes** para resolver o nome. Clique em **OK**.

      ![Selecione Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

  5. Em **Funções de Servidor**, verifique se a função **sysadmin** está selecionada. Clique em **OK**. As permissões necessárias agora devem existir.

      ![Verifique se a função de servidor sysadmin está selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

  6. Agora, associe o banco de dados ao cofre dos Serviços de Recuperação. No portal do Azure, na lista **Servidores Protegidos**, clique com o botão direito do mouse no servidor que está em um estado de erro > **Redescobrir BDs**.

      ![Verifique se o servidor tem as permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

  7. Verifique o progresso na área **Notificações**. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

      ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

 
## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre](restore-sql-database-azure-vm.md) como restaurar bancos de dados do SQL Server copiados em backup.
- [Saiba mais sobre](manage-monitor-sql-database-backup.md) como gerenciar bancos de dados do SQL Server copiados em backup.
