---
title: Fazer backup do banco de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica o backup do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
services: backup
documentationcenter: ''
author: markgalioto
manager: carmonm
editor: ''
keywords: ''
ms.assetid: ''
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 6/1/2018
ms.author: markgal;anuragm
ms.custom: ''
ms.openlocfilehash: 4ae64fefb58840214104a4e1cb338ec404fac1a8
ms.sourcegitcommit: 4e36ef0edff463c1edc51bce7832e75760248f82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35235406"
---
# <a name="back-up-sql-server-database-in-azure"></a>Fazer backup do banco de dados SQL Server no Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que exigem baixo Objetivo de Ponto de Recuperação (RPO) e retenção de longo prazo. O Backup do Azure fornece uma solução de backup do SQL Server que não exige infraestrutura, ou seja, sem servidor de backup complexo, sem agente de gerenciamento ou armazenamento de backup para gerenciar. O Backup do Azure fornece gerenciamento centralizado para seus backups em todos os servidores SQL ou até mesmo diferentes cargas de trabalho.

 Neste artigo, você aprende:

> [!div class="checklist"]
> * Pré-requisitos para fazer backup do SQL Server no Azure
> * Criar e usar um cofre dos Serviços de Recuperação
> * Configurar backups do banco de dados do SQL Server
> * Definir uma política de backup (ou retenção) para os pontos de recuperação
> * Como restaurar o banco de dados

Antes de iniciar os procedimentos neste artigo, você deverá ter um SQL Server em execução no Azure. [Você pode usar máquinas virtuais do marketplace do SQL para criar rapidamente um SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limitações da visualização pública

Os itens a seguir são as limitações conhecidas para a Visualização Pública.

- A máquina virtual SQL exige conectividade com a Internet para acessar os endereços IP públicos do Azure. Para obter mais detalhes, veja a seção [Estabelecer conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).
- Você pode proteger até 2000 bancos de dados SQL em um cofre dos Serviços de Recuperação. Os bancos de dados SQL adicionais devem ser armazenados em um cofre de Serviços de Recuperação separado.
- [O backup de grupos de disponibilidade distribuída tem limitações](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017).
- Não há suporte para instâncias de Cluster de Failover do SQL (FCI).
- Use o Portal do Azure para configurar o Backup do Azure para proteger bancos de dados do SQL Server. O suporte para o Azure PowerShell, CLI e APIs REST não está disponível no momento.

## <a name="supported-azure-geos"></a>Áreas geográficas do Azure com suporte

- Sudeste da Austrália (ASE) 
- Sul do Brasil (BRS)
- Central do Canadá (CNC)
- Leste do Canadá (CE)
- Central dos EUA (CUS)
- Ásia Oriental (EA)
- Leste da Austrália (AE) 
- Leste dos EUA (EUS)
- Leste dos EUA 2 (EUS2)
- Leste do Japão (JPE)
- Oeste do Japão (JPW)
- Índia Central (INC) 
- Sul da Índia (INS)
- Coreia Central (KRC)
- Sul da Coreia (KRS)
- Centro-Norte dos EUA (NCUS) 
- Europa Setentrional (NE) 
- Centro-Sul dos EUA (SCUS) 
- Sudeste Asiático (SEA)
- Sul do Reino Unido (UKS) 
- Oeste do Reino Unido (UKW) 
- Europa Ocidental (WE) 
- Oeste dos EUA (WUS)
- Centro-Oeste dos EUA (WCUS)
- Oeste dos EUA 2 (WUS 2) 

## <a name="supported-operating-systems-and-versions-of-sql-server"></a>Sistemas operacionais com suporte e versões do SQL server

Os seguintes sistemas operacionais e versões do SQL Server aplicam-se a máquinas virtuais do marketplace do SQL do Azure e máquinas virtuais que não são do marketplace (onde o SQL Server é instalado manualmente).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

O Linux não tem suporte no momento.

### <a name="supported-versionseditions-of-sql-server"></a>Versões/edições com suporte do SQL Server

- SQL 2012 Enterprise, Standard, Web, Developer, Express
- SQL 2014 Enterprise, Standard, Web, Developer, Express
- SQL 2016 Enterprise, Standard, Web, Developer, Express
- SQL 2017 Enterprise, Standard, Web, Developer, Express


## <a name="prerequisites-for-using-azure-backup-to-protect-sql-server"></a>Pré-requisitos para usar o Backup do Azure para proteger o SQL Server 

Antes de fazer backup de seu banco de dados do SQL Server, verifique as condições a seguir. :

- Identifique ou [crie um cofre de Serviços de Recuperação](backup-azure-sql-database.md#create-a-recovery-services-vault) na mesma região ou localidade, que a máquina virtual que hospeda o SQL Server.
- [Verifique as permissões na máquina virtual](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) necessárias para fazer backup dos bancos de dados SQL.
- [As máquinas virtuais SQL precisam de conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).

> [!NOTE]
> Você pode ter apenas uma solução de backup de cada vez para bancos de dados do backup do SQL Server. Desabilite qualquer outro backup do SQL antes de usar esse recurso, porque os outros backups poderão interferir e causa falhas. Você pode habilitar um Backup do Azure para VM de IaaS junto com o backup do SQL sem qualquer conflito 
>

Se essas condições existem em seu ambiente, vá até a seção [Configurar seu cofre para proteger um banco de dados SQL](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database). Se algum dos pré-requisitos não existir, continue a leitura desta seção.


## <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual SQL precisa de conectividade para os endereços IP públicos do Azure. As operações de máquina virtual do SQL (como descoberta de banco de dados, configuração de backup, backups agendados, restauração de pontos de recuperação e assim por diante) falham sem conectividade com os endereços IP públicos. Use uma das opções a seguir para fornecer um caminho claro para o tráfego de backup:

- Coloque os intervalos IP do datacenter do Azure na lista de permissões. Para colocar os intervalos IP do datacenter do Azure na lista de permissões, use a [página do centro de download para obter detalhes sobre os intervalos IP e as instruções](https://www.microsoft.com/download/details.aspx?id=41653). 
- Implante um servidor proxy HTTP para rotear o tráfego. Quando você estiver fazendo backup de um banco de dados SQL em uma VM, a extensão de backup na VM usa as APIs de HTTPS para enviar comandos de gerenciamento para o Backup do Azure e dados para o armazenamento do Azure. A extensão de backup também usa o Azure Active Directory (AAD) para autenticação. Roteie o tráfego da extensão de backup para esses três serviços por meio do proxy HTTP, pois ele é o único componente configurado para acesso à Internet pública.

As compensações entre as opções são: capacidade de gerenciamento, controle granular e custo.

> [!NOTE]
>As marcas de serviço para Backup do Azure devem estar disponíveis por meio de Disponibilidade Geral.
>

| Opção | Vantagens | Desvantagens |
| ------ | ---------- | ------------- |
| Intervalos de IPs na lista de autorizados | Sem custo adicional. <br/> Para habilitar o acesso em NSG, use o cmdlet **Set-AzureNetworkSecurityRule**. | É complexo para gerenciar, já que os intervalos de IP afetados mudam com o tempo. <br/>Fornece acesso ao Azure por completo, não somente ao armazenamento.|
| Usar um proxy HTTP   | É permitido o controle granular no proxy em relação às URLs de armazenamento. <br/>Único ponto de acesso à Internet para VMs. <br/> Não está sujeito a alterações do endereço IP do Azure | Custos adicionais para a execução de uma VM com o software do proxy |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Definir permissões para VMs do SQL que não são do marketplace

Para fazer backup de uma máquina virtual, o Backup do Azure requer a extensão **AzureBackupWindowsWorkload** instalada. Se você estiver usando máquinas virtuais do marketplace do Azure, vá até [Descobrir bancos de dados SQL](backup-azure-sql-database.md#discover-sql-server-databases). Se a máquina virtual que hospeda os bancos de dados SQL não tiver sido criada no marketplace do Azure, conclua a seção a seguir para instalar a extensão e definir as permissões apropriadas. Além da extensão **AzureBackupWindowsWorkload**, o Backup do Azure requer privilégios de sysadmin do SQL para proteger bancos de dados SQL. Ao descobrir bancos de dados na máquina virtual, o Backup do Azure cria uma conta, NT Service\AzureWLBackupPluginSvc. Para Backup do Windows Azure para descobrir bancos de dados SQL, a conta NT Service\AzureWLBackupPluginSvc deve ter permissões de sysadmin do SQL e de login do SQL. O procedimento a seguir explica como fornecer essas permissões.

Para configurar permissões:

1. No [Portal do Azure](https://portal.azure.com), abra o cofre de Serviços de Recuperação que você está usando para proteger bancos de dados SQL.
2. No menu do painel do cofre, clique em **+ Backup** para abrir o menu **Meta de Backup**.

   ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. No menu **Meta de Backup** no menu **Onde sua carga de trabalho é executada?**, deixe **Azure** como o padrão.

4. No menu **Do que você deseja fazer backup**, expanda o menu suspenso e selecione **SQL Server na VM do Azure**.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    O menu **Meta de Backup** exibe duas novas etapas: **Descobrir Bancos de Dados em VMs** e **Configurar Backup**. **Descobrir Bancos de Dados em VMs** inicia uma pesquisa para máquinas virtuais do Azure.

    ![Mostra as novas etapas de meta de Backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Clique em **Iniciar Descoberta** para procurar máquinas virtuais sem proteção na assinatura. Dependendo do número de máquinas virtuais sem proteção na assinatura, pode levar algum tempo para percorrer todas as máquinas virtuais.

    ![Backup pendente](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Quando uma máquina virtual desprotegida é descoberta, ela aparece na lista. As máquinas virtuais desprotegidas são listadas por seu nome da máquina virtual e grupo de recursos. É possível que várias máquinas virtuais tenham o mesmo nome. No entanto, as máquinas virtuais com o mesmo nome pertencem a diferentes grupos de recursos. Se uma máquina virtual esperada não aparecer na lista, veja se a máquina virtual já está protegida em um cofre.

6. Na lista de máquinas virtuais, selecione a VM que contém o banco de dados SQL que você deseja fazer backup e, em seguida, clique em **Descobrir Bancos de Dados**. 

    O processo de descoberta instala a extensão **AzureBackupWindowsWorkload** na máquina virtual. A extensão permite que o serviço de Backup do Azure se comunique com a máquina virtual para que ela possa fazer backup dos bancos de dados SQL. Depois que a extensão é instalada, o Backup do Azure cria a conta de serviço virtual do Windows, **NT Service\AzureWLBackupPluginSvc**, na máquina virtual. A conta de serviço virtual requer a permissão de sysadmin do SQL. Durante o processo de instalação de conta serviço virtual, se você vir o erro **UserErrorSQLNoSysadminMembership**, veja a seção [Como corrigir permissões de sysadmin do SQL](backup-azure-sql-database.md#fixing-sql-sysadmin-permissions).

    A área de notificações mostra o progresso da descoberta de banco de dados. Dependendo de quantos bancos de dados estão na máquina virtual, pode levar um tempo para o trabalho ser concluído. Quando bancos de dados selecionados tiverem sido descobertos, será exibida uma mensagem de êxito.

    ![mensagem de notificação de êxito da implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

Depois de associar o banco de dados com o cofre de Serviços de Recuperação, a próxima etapa é [Configurar o Backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="fixing-sql-sysadmin-permissions"></a>Como corrigir permissões de sysadmin do SQL

Durante o processo de instalação, se você vir o erro **UserErrorSQLNoSysadminMembership**, entre no SQL Server Management Studio (SSMS) com uma conta que tenha permissão de sysadmin do SQL. A menos que você exija permissões especiais, você poderá usar a autenticação do Windows para reconhecer a conta.

1. No SQL Server, abra a pasta **Segurança/Logons**.

    ![Abrir as pastas do SQL Server e segurança e logon para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

2. Na pasta de logons, clique com o botão direito do mouse e selecione **Novo Logon** e, na caixa de diálogo Logon - Novo, clique em **Pesquisar**

    ![Abrir Pesquisar na caixa de diálogo Logon - Novo](./media/backup-azure-sql-database/new-login-search.png)

3. Desde a conta de serviço virtual do Windows, **NT Service\AzureWLBackupPluginSvc** já foi criado durante a fase de descoberta do SQL e registro da máquina virtual, insira o nome da conta como ele aparece na caixa de diálogo **Insira o nome do objeto para selecionar**. Clique em **Verificar Nomes** para resolver o nome. 

    ![Clique no botão Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

4. Clique em **OK** para fechar a caixa de diálogo Selecionar Usuário ou Grupo.

5. Na caixa de diálogo **Funções de Servidor**, verifique se a função **sysadmin** está selecionada. Em seguida, clique em **OK** para fechar **Logon - Novo**.

    ![Verifique se a função de servidor sysadmin está selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

    As permissões necessárias agora devem existir.

6. Embora você tenha corrigido o erro de permissões, ainda precisará associar o banco de dados com o cofre de Serviços de Recuperação. No Portal do Azure, na lista **Servidores Protegidos**, clique com o botão direito do mouse no servidor no erro e selecione **Redescobrir Bancos de Dados**.

    ![Verifique se o servidor tem as permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

    A área de notificações mostra o progresso da descoberta de banco de dados. Dependendo de quantos bancos de dados estão na máquina virtual, pode levar um tempo para o trabalho ser concluído. Quando os bancos de dados selecionados tiverem sido encontrados, uma mensagem de êxito será exibida na área de notificações.

    ![mensagem de notificação de êxito da implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

Depois de associar o banco de dados com o cofre de Serviços de Recuperação, a próxima etapa é [Configurar o Backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

[!INCLUDE [Section explaining how to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

O Backup do Azure pode descobrir todos os bancos de dados em uma instância do SQL Server para protegê-los de acordo com seus requisitos de backup. Use o procedimento a seguir para identificar a máquina virtual que hospeda os bancos de dados SQL. Depois de identificar a máquina virtual, o Backup do Azure instalará uma extensão leve para descobrir os bancos de dados do SQL server.

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).
2. No menu esquerdo, selecione **Todos os Serviços**.

    ![Escolha a opção Todos os Serviços no menu principal](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Na caixa de diálogo Todos os Serviços, digite *Serviços de Recuperação*. Conforme você começa a digitar, sua entrada filtra a lista de recursos. Ao ver a opção, selecione **Cofres dos Serviços de Recuperação**.

    ![Digite Serviços de Recuperação na caixa de diálogo Todos os serviços](./media/backup-azure-sql-database/all-services.png) <br/>

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá. 

4. Na lista de cofre de Serviços de Recuperação, selecione o cofre que você deseja usar para proteger bancos de dados SQL.

5. No menu do painel do cofre, clique em **+ Backup** para abrir o menu **Meta de Backup**.

   ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

6. No menu **Meta de Backup** no menu **Onde sua carga de trabalho é executada?**, deixe **Azure** como o padrão.

7. No menu **Do que você deseja fazer backup**, expanda o menu suspenso e selecione **SQL Server na VM do Azure**.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Depois de selecionado, o menu **Meta de Backup** exibe duas etapas: Descobrir Bancos de Dados em VMs e Configurar Backup. 

    ![Mostra as novas etapas de meta de Backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Clique em **Iniciar Descoberta** para procurar máquinas virtuais sem proteção na assinatura. Dependendo do número de máquinas virtuais sem proteção na assinatura, pode levar algum tempo para percorrer todas as máquinas virtuais.

    ![Backup pendente](./media/backup-azure-sql-database/discovering-sql-databases.png)
 
    Quando uma máquina virtual desprotegida é descoberta, ela aparece na lista. Várias máquinas virtuais podem ter o mesmo nome. No entanto, várias máquinas virtuais com o mesmo nome pertencem a diferentes grupos de recursos. As máquinas virtuais desprotegidas são listadas por seu nome da máquina virtual e grupo de recursos. Se uma máquina virtual esperada não estiver listada, veja se essa máquina virtual já está protegida em um cofre.

9. Na lista de máquinas virtuais, marque a caixa de seleção da máquina virtual que contém os bancos de dados SQL que deseja proteger e clique em **Descobrir Bancos de Dados**.

    O Backup do Azure descobre todos os bancos de dados SQL na máquina virtual. Para saber mais sobre o que acontece durante a fase de descoberta do banco de dados, veja a seção a seguir, [Operações de back-end ao descobrir bancos de dados SQL](backup-azure-sql-database.md#backend-operations-when-discovering-sql-databases). Depois de descobrir os bancos de dados SQL, você estará pronto para [configurar o trabalho de backup](backup-azure-sql-database.md#configure-your-vault-to-protect-a-sql-database).

### <a name="backend-operations-when-discovering-sql-databases"></a>Operações de back-end ao descobrir bancos de dados SQL

Quando você usa a ferramenta **Descobrir Bancos de Dados**, o Backup do Azure executa as seguintes operações em segundo plano:

- registra a máquina virtual com o cofre de Serviços de Recuperação para o backup de cargas de trabalho. Todos os bancos de dados na máquina virtual registrada somente podem ter o backup feito para este cofre de Serviços de Recuperação. 

- instala a extensão **AzureBackupWindowsWorkload** na máquina virtual. Fazer backup de um banco de dados SQL é uma solução sem agente, ou seja, com a extensão instalada na máquina virtual, nenhum agente está instalado no banco de dados SQL.

- cria a conta de serviço, **NT Service\AzureWLBackupPluginSvc**, na máquina virtual. Todas as operações de backup e restauração usam a conta de serviço. **NT Server\AzureWLBackupPluginSvc** precisa de permissões de sysadmin do SQL. Todas as máquinas virtuais do Marketplace do SQL vêm com o SqlIaaSExtension instalado e AzureBackupWindowsWorkload usa SQLIaaSExtension para obter automaticamente as permissões necessárias. Se sua máquina virtual não tem SqlIaaSExtension instalado, a operação de banco de dados de descoberta falhará e você receberá a mensagem de erro **UserErrorSQLNoSysAdminMembership**. Para adicionar a permissão de sysadmin para backup, siga as instruções em [Como definir as permissões de Backup do Azure para VMs do SQL que não são do marketplace](backup-azure-sql-database.md#set-permissions-for-non--marketplace-sql-vms).

    ![selecionar a vm e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-database"></a>Configurar backup para o banco de dados do SQL Server

O Backup do Azure fornece serviços de gerenciamento para proteger seus bancos de dados do SQL Server e gerenciar trabalhos de backup. Os recursos de gerenciamento e monitoramentos dependem de seu cofre de Serviços de Recuperação. 

> [!NOTE]
> Você pode ter apenas uma solução de backup de cada vez para bancos de dados do backup do SQL Server. Desabilite qualquer outro backup do SQL antes de usar esse recurso, porque os outros backups poderão interferir e causa falhas. Você pode habilitar um Backup do Azure para VM de IaaS junto com o backup do SQL sem qualquer conflito 
>

Para configurar a proteção para o banco de dados SQL:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No menu do painel do cofre, clique em **+ Backup** para abrir o menu **Meta de Backup**.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. No menu **Meta de Backup** no menu **Onde sua carga de trabalho é executada?**, deixe **Azure** como o padrão.

4. No menu **Do que você deseja fazer backup**, expanda o menu suspenso e selecione **SQL Server na VM do Azure**.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    Depois de selecionado, o menu **Meta de Backup** exibe duas etapas: Descobrir Bancos de Dados em VMs e Configurar Backup. Se você seguir este artigo na ordem, já terá descoberto as máquinas virtuais desprotegidas e o cofre estará registrado com uma máquina virtual. Agora você está pronto para configurar a proteção para os bancos de dados SQL.

5. No menu Meta de Backup, clique em **Configurar Backup**.

    ![Mostra as novas etapas de meta de Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    O serviço de Backup do Azure exibe todas as instâncias do SQL com bancos de dados autônomos, bem como grupos de disponibilidade AlwaysOn do SQL. Para exibir os bancos de dados autônomos na instância do SQL, clique na divisa ao lado do nome de instância para exibir os bancos de dados. As imagens a seguir mostram exemplos de uma instância autônoma e um grupo de disponibilidade AlwaysOn.

    > [!NOTE]
    > Os backups completos e diferenciais ocorrem no nó principal, porque a plataforma do SQL tem essa limitação. O backup de log pode ocorrer com base em sua preferência de backup. Devido a essa limitação, o nó primário deve ser registrado.
    >

    ![Lista de bancos de dados na instância do SQL](./media/backup-azure-sql-database/discovered-databases.png)

    Clique na divisa ao lado de grupos de disponibilidade AlwaysOn para exibir a lista de bancos de dados.

    ![Lista de bancos de dados no grupo de disponibilidade AlwaysOn](./media/backup-azure-sql-database/discovered-database-availability-group.png)

6. Na lista de bancos de dados, selecione tudo o que você deseja proteger e clique em **OK**.

    ![selecione vários bancos de dados para protegê-los](./media/backup-azure-sql-database/select-multiple-database-protection.png)

    Você pode selecionar até 50 bancos de dados ao mesmo tempo. Se você deseja proteger mais de 50 bancos de dados, faça várias passagens. Depois que você protege os primeiros 50 bancos de dados, repita essa etapa para proteger o próximo conjunto de bancos de dados.
    > [!Note] 
    > Para otimizar cargas de backup, o Backup do Azure divide grandes trabalhos de backup em vários lotes. O número máximo de bancos de dados em um trabalho de backup é 50.
    >
    >

7. Para criar ou escolher uma política de backup no menu **Backup**, selecione **Política de Backup** para abrir o menu.

    ![selecionar a opção de política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

8. No menu suspenso **Escolher política de backup**, escolha uma política de backup e clique em **OK**. Para saber mais sobre como criar sua própria política de backup, veja a seção [Definir uma política de backup](backup-azure-sql-database.md#define-a-backup-policy).

    ![escolha uma política de backup no menu suspenso](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    No menu Política de Backup, no menu suspenso **Escolher política de backup**, escolha: 
    - a política de HourlyLogBackup padrão, 
    - uma política de backup criada anteriormente para SQL,
    - para [definir uma nova política](backup-azure-sql-database.md#define-a-backup-policy) com base em seu objetivo de ponto de recuperação (RPO) e o período de retenção. 

    > [!Note]
    > O Backup do Azure oferece suporte à retenção de longo prazo com base no esquema de backup avô-pai-filho para otimizar o consumo de armazenamento de back-end ao atender às necessidades de conformidade.
    >

9. Depois de ter escolhido uma política de backup, no **Menu Backup** clique em **Habilitar backup**.

    ![habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

    Você pode acompanhar o progresso da configuração na área de notificações do portal.

    ![área de exibição de notificação](./media/backup-azure-sql-database/notifications-area.png)


### <a name="define-a-backup-policy"></a>Definir uma política de backup

Uma política de backup define uma matriz de quando os backups são obtidos e por quanto tempo os backups são mantidos. Você pode usar o Backup do Azure para agendar três tipos de backup para bancos de dados SQL:

* Backup completo - um backup completo do banco de dados faz backup do banco de dados inteiro. Um backup completo contém todos os dados em um banco de dados específico ou um conjunto de grupos de arquivos ou arquivos e log suficiente para recuperar esses dados. No máximo, você pode acionar um backup completo por dia. Você pode optar por fazer um backup completo em um intervalo diário ou semanal. 
* Backup diferencial - um backup diferencial é baseado no backup de dados completo anterior mais recente. Um backup diferencial captura apenas os dados que foram alterados desde o backup completo. No máximo, você pode acionar um backup diferencial por dia. Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
* Backup de log de transações - um backup de log permite a restauração pontual até um determinado segundo. No máximo, você pode configurar backups de log de transações a cada 15 minutos.

A política é criada no nível do cofre de Serviços de Recuperação. Se você tiver vários cofres, os cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup para cada cofre. Ao criar uma política de backup diário, o Backup Completo é o padrão. Você pode adicionar um Backup Diferencial, mas somente se você alternar os Backups Completos para ocorrerem semanalmente. O procedimento a seguir explica como criar uma política de backup para um servidor SQL em uma máquina virtual do Azure.

Para criar uma política de backup

1. No menu Política de Backup, no menu suspenso **Escolher política de backup**, selecione **Criar Nova**.

   ![criar uma nova política de backup](./media/backup-azure-sql-database/create-new-backup-policy.png)

    O menu de política de Backup alterna para fornecer os campos necessários para qualquer nova política de backup de servidor SQL.

   ![novos campos de política de backup](./media/backup-azure-sql-database/blank-new-policy.png)

2. Em **Nome da política**, forneça um nome. 

3. Um Backup Completo é obrigatório. Você pode aceitar os valores padrão para o Backup Completo ou clique em **Backup Completo** para editar a política.

    ![novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)

    Dentro da política de Backup Completo, escolha Diariamente ou Semanalmente para a frequência. Se você escolher Diariamente, escolha a hora e o fuso horário de início do trabalho de backup. Se você escolher Backups Completos diários, você não poderá criar Backups Diferenciais.

   ![configuração do intervalo diário](./media/backup-azure-sql-database/daily-interval.png)

    Se você escolher Semanalmente, escolha o dia da semana, a hora e o fuso horário de início do trabalho de backup.

   ![configuração do intervalo semanal](./media/backup-azure-sql-database/weekly-interval.png)

4. Por padrão, todas as opções de Intervalo de Retenção (diário, semanal, mensal e anual) são selecionadas. Desmarque qualquer limite do intervalo de retenção que você não deseja usar e defina os intervalos desejados. No menu de política de Backup Completo, clique em **OK** para aceitar as configurações.

   ![configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

    Os pontos de recuperação estão marcados para retenção, com base em seu período de retenção. Por exemplo, se você selecionar um diário ou backup completo, apenas um backup completo será acionado por dia. Dependendo de sua retenção semanal, o backup do dia específico será marcado e mantido com base no período de retenção semanal. O período de retenção mensal e anual comporta-se da mesma forma.

5. Para adicionar uma política de backup diferencial, clique em **Backup Diferencial** para abrir o menu. 

   ![abrir a política diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    No menu de política Backup diferencial, selecione **Habilitar** para abrir os controles de retenção e frequência. Você pode acionar, no máximo, um backup diferencial por dia.
    > [!Important] 
    > No máximo, backups diferenciais podem ser mantidos por 180 dias. Se você precisar de uma retenção mais longa, deverá usar os backups completos, não poderá usar backups diferenciais.
    >

   ![editar a política diferencial](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Clique em **OK** para salvar a política e retornar para o menu principal da Política de Backup.

6. Para adicionar uma política de Backup de Log transacional, clique em **Backup de Log** para abrir o menu. No menu de Backup de Log, selecione **Habilitar**e defina os controles de retenção e frequência. Os Backups de log podem ocorrer a cada 15 minutos e podem ser mantidos por 35 dias. Clique em **OK** para salvar a política e retornar para o menu principal da Política de Backup.

   ![editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

7. Escolha se deseja habilitar a compactação de Backup do SQL. A compactação está desabilitada por padrão.

    No back-end, o Backup do Azure usa compactação de backup nativo do SQL.

8. Quando todas as edições tiverem sido feitas para a política de Backup, clique em **OK**. 

   ![período de retenção diferencial](./media/backup-azure-sql-database/differential-backup-policy.png)

## <a name="restore-a-sql-database"></a>Restaurar um Banco de Dados SQL

O Backup do Azure fornece funcionalidade para restaurar bancos de dados individuais para uma data e hora específicas, até um determinado segundo, usando backups de log de transações. Com base nos tempos de restauração que você fornecer, o Backup do Azure determinará automaticamente os backups apropriados completo, diferencial e a cadeia de log necessários para restaurar seus dados.

Como alternativa, você pode selecionar um backup Completo ou Diferencial específico para restaurar para um ponto de recuperação específico em vez de um momento específico.

Para restaurar um banco de dados

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel do cofre, selecione **Uso** dos Itens de Backup para abrir o menu Itens de Backup.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. No menu **Itens de Backup**, selecione o tipo de gerenciamento de backup, **SQL na VM do Azure**. 

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A lista de itens de Backup será ajustada para mostrar a lista de bancos de dados SQL. 

4. Na lista de bancos de dados SQL, selecione o banco de dados que você deseja restaurar.

    ![selecione SQL na VM do Azure na lista](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando você seleciona o banco de dados, o menu é aberto. Esse menu fornece os detalhes do backup para o banco de dados incluindo:

    * os pontos de restauração mais antigo e mais recente,
    * status de backup de log para as últimas 24 horas (para bancos de dados em modelo de recuperação conectado Completo e Em massa, se estiver configurado para backups de log transacional)

5. No menu de banco de dados selecionado, clique em **Restaurar Banco de Dados** para abrir o menu de restauração.

    ![selecione restaurar o banco de dados](./media/backup-azure-sql-database/restore-db-button.png)

    O menu **Restaurar** é aberto e também o menu **Configuração de Restauração**. O menu **Configuração de Restauração** é a primeira etapa na configuração de restauração. Nesse menu, selecione onde você deseja restaurar os dados. As opções são:
    * Local Alternativo - use essa opção se você deseja restaurar o banco de dados para um local alternativo mantendo o banco de dados fonte de origem.
    * Substituir Banco de Dados – restaura os dados para a mesma instância do SQL Server como a fonte original. O efeito disso é você substituir o banco de dados original.

    > [!Important]
    > Se o banco de dados selecionado pertence a um grupo de disponibilidade AlwaysOn, o SQL não permite que o banco de dados seja substituído. Nesse caso, somente a opção **Local Alternativo** está habilitada.
    >

    ![clique em Configurar para abrir o menu de configuração Restaurar](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

Esse procedimento orienta a restauração de dados para um local alternativo. Se você deseja substituir o banco de dados ao restaurar, vá para a seção [Restaurar e substituir o banco de dados](backup-azure-sql-database.md#restore-and-overwrite-the-database). Este procedimento pressupõe que você abriu seu cofre de Serviços de Recuperação e está no menu Configuração de Restauração. Se não estiver, inicie com a seção [Restaurar um banco de dados SQL](backup-azure-sql-database.md#restore-a-sql-database).

O menu suspenso **Servidor** mostra apenas os servidores SQL registrados com o cofre de Serviços de Recuperação. Se o servidor que você deseja não está na lista **Servidor**, veja a seção [Descobrir bancos de dados de servidor SQL](backup-azure-sql-database.md#discover-sql-server-databases) para localizar o servidor. Durante o processo de banco de dados de descoberta, novos servidores são registrados no cofre de Serviços de Recuperação.

1. No menu **Configuração de Restauração**:

    * selecione **Local Alternativo**,
    * para **Servidor**, escolha o servidor SQL em que você deseja restaurar o banco de dados.
    * no menu suspenso **Instância**, escolha uma instância do SQL
    * na caixa de diálogo **Nome do Banco de Dados Restaurado**, forneça o nome do banco de dados de destino.
    * se aplicável, selecione **Substituir se o banco de dados com o mesmo nome já existir na instância do SQL selecionada**.
    * clique em **OK** para concluir a configuração do destino e escolha um ponto de restauração.

    ![selecione o local alternativo, a instância e o nome no menu de configuração Restaurar](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. No menu **Selecionar ponto de restauração**, você pode escolher um ponto de restauração Logs (pontual) ou Completo e Diferencial. Se você deseja restaurar para um log pontual específico, continue com essa etapa. Se você deseja restaurar um ponto de restauração Completo ou Diferencial, vá para a etapa 3.

    ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    A restauração pontual só está disponível para backups de log para bancos de dados com modelo de recuperação conectado Completo e Em massa. Para restaurar para um ponto específico:

    1. Selecione **Logs (Pontual)** como a opção de restauração.

        ![escolha o tipo de ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Em **Restaurar Data/Hora**, clique no ícone de calendário para abrir o calendário. Datas em negrito contêm pontos de recuperação e a data atual é realçada. Selecione uma data no calendário com pontos de recuperação. Não é possível selecionar datas sem pontos de recuperação.

        ![abrir calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Quando você seleciona uma data, o gráfico de linha do tempo exibe os pontos de recuperação disponíveis em um intervalo contínuo.

    3. Usando o gráfico de linha do tempo ou a caixa de diálogo de tempo, especifique um tempo específico para o ponto de recuperação e clique em **OK** para concluir a etapa de ponto de restauração.
    
       ![abrir calendário](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        O menu **Selecionar ponto de restauração** é fechado e o menu **Configuração Avançada** é aberto.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, no menu **Restaurar com NORECOVERY**, selecione **Habilitado**.
        * Se você quiser alterar o local de restauração no servidor de destino, forneça um novo caminho na coluna **Destino**.
        * Clique em **OK** para aprovar as configurações e fechar **Configuração Avançada**.

    5. No menu **Restaurar**, clique em **Restaurar** para iniciar o trabalho de restauração. Na área de notificações, você pode acompanhar o progresso. Você também pode acompanhar o progresso nos trabalhos de restauração de banco de dados.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-job-notification.png)

3. No menu **Selecionar ponto de restauração**, escolha um ponto de recuperação. Você pode escolher Logs (Pontual) ou Completo e Diferencial. Se você quiser restaurar um log pontual, volte para a etapa 2. Essa etapa restaura um ponto de restauração Completo ou Diferencial específico. Com essa opção, você pode ver todos os pontos de recuperação Completo e Diferencial para os últimos 30 dias. Se quiser ver os pontos de recuperação com mais de 30 dias, clique em **Filtrar** para abrir o menu **Filtrar pontos de restauração**. Se escolher um ponto de recuperação Diferencial, o Backup do Azure primeiro restaura o ponto de recuperação Completo apropriado e, em seguida, aplica o ponto de recuperação Diferencial selecionado.

    ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. No menu **Selecionar ponto de restauração**, selecione **Completo e Diferencial**.

       ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A lista de pontos de recuperação disponíveis é exibida.

    2. Na lista de pontos de recuperação, selecione um ponto de recuperação e clique em **OK** para concluir o procedimento de ponto de restauração. 

        ![escolha o ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        O menu **Ponto de Restauração** é fechado e o menu **Configuração Avançada** é aberto.

        ![menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, no menu **Restaurar com NORECOVERY**, selecione **Habilitado**. **Restaurar com NORECOVERY** é desabilitado por padrão.
        * Se você quiser alterar o local de restauração no servidor de destino, forneça um novo caminho na coluna **Destino**.
        * Clique em **OK** para aprovar as configurações e fechar **Configuração Avançada**.

    4. No menu **Restaurar**, clique em **Restaurar** para iniciar o trabalho de restauração. Na área de notificações, você pode acompanhar o progresso. Você também pode acompanhar o progresso nos trabalhos de restauração de banco de dados.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Restaurar e substituir o banco de dados

Este procedimento explica como restaurar dados e substituir o banco de dados. Se você deseja restaurar para um local alternativo, vá para a seção [Restaurar para um local alternativo](backup-azure-sql-database.md#restore-to-an-alternate-location). Este procedimento pressupõe que você abriu seu cofre de Serviços de Recuperação e está no menu **Configuração de Restauração** (veja a imagem a seguir). Se não estiver, inicie com a seção [Restaurar um banco de dados SQL](backup-azure-sql-database.md#restore-a-sql-database).

![clique em Configurar para abrir o menu de configuração Restaurar](./media/backup-azure-sql-database/restore-overwrite-db.png)

O menu suspenso **Servidor** mostra apenas os servidores SQL registrados com o cofre de Serviços de Recuperação. Se o servidor que você deseja não está na lista **Servidor**, veja a seção [Descobrir bancos de dados de servidor SQL](backup-azure-sql-database.md#discover-sql-server-databases) para localizar o servidor. Durante o processo de banco de dados de descoberta, novos servidores são registrados no cofre de Serviços de Recuperação.

1. No menu **Configuração de Restauração**, selecione **Substituir Banco de Dados** e clique em **OK** para concluir a configuração de destino. 

   ![clique em Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    As caixas de diálogo **Servidor**, **Instância** e **Nome do Banco de Dados Restaurado** não são necessárias.

2. No menu **Selecionar ponto de restauração**, você pode escolher um ponto de restauração Logs (pontual) ou Completo e Diferencial. Se você deseja restaurar para um log pontual, continue com essa etapa. Se você deseja restaurar um ponto de restauração Completo e Diferencial, vá para a etapa 3.

    ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    A restauração pontual só está disponível para backups de log para bancos de dados com modelo de recuperação conectado Completo e Em massa. Para escolher uma restauração pontual para um determinado segundo:

    1. Selecione **Logs (Pontual)** como a opção de restauração.

        ![escolha o tipo de ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Em **Restaurar Data/Hora**, clique no ícone de calendário para abrir o calendário. Datas em negrito contêm pontos de recuperação e a data atual é realçada. Selecione uma data no calendário com pontos de recuperação. Não é possível selecionar datas sem pontos de recuperação.

        ![abrir calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Quando você seleciona uma data, o gráfico de linha do tempo exibe os pontos de recuperação disponíveis.

    3. Usando o gráfico de linha do tempo ou a caixa de diálogo de tempo, especifique um tempo específico para o ponto de recuperação e clique em **OK** para concluir a etapa de ponto de restauração.
    
       ![abrir calendário](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        O menu **Selecionar ponto de restauração** é fechado e o menu **Configuração Avançada** é aberto.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, no menu **Restaurar com NORECOVERY**, selecione **Habilitado**.
        * Se você quiser alterar o local de restauração no servidor de destino, forneça um novo caminho na coluna **Destino**.
        * Clique em **OK** para aprovar as configurações e fechar **Configuração Avançada**.

    5. No menu **Restaurar**, clique em **Restaurar** para iniciar o trabalho de restauração. Na área de notificações, você pode acompanhar o progresso. Você também pode acompanhar o progresso nos trabalhos de restauração de banco de dados.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-job-notification.png)

3. No menu **Selecionar ponto de restauração**, escolha um ponto de recuperação. Você pode escolher Logs (Pontual) ou Completo e Diferencial. Se você quiser restaurar um log pontual, volte para a etapa 2. Essa etapa restaura um ponto de restauração Completo ou Diferencial específico. Com essa opção, você pode ver todos os pontos de recuperação Completo e Diferencial para os últimos 30 dias. Se quiser ver os pontos de recuperação com mais de 30 dias, clique em **Filtrar** para abrir o menu **Filtrar pontos de restauração**. Se escolher um ponto de recuperação Diferencial, o Backup do Azure primeiro restaura o ponto de recuperação Completo apropriado e, em seguida, aplica o ponto de recuperação Diferencial selecionado.

    ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. No menu **Selecionar ponto de restauração**, selecione **Completo e Diferencial**.

       ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        A lista de pontos de recuperação disponíveis é exibida.

    2. Na lista de pontos de recuperação, selecione um ponto de recuperação e clique em **OK** para concluir o procedimento de ponto de restauração. 

        ![escolha o ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        O menu **Ponto de Restauração** é fechado e o menu **Configuração Avançada** é aberto.

        ![menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, no menu **Restaurar com NORECOVERY**, selecione **Habilitado**. **Restaurar com NORECOVERY** é desabilitado por padrão.
        * Se você quiser alterar o local de restauração no servidor de destino, forneça um novo caminho na coluna **Destino**.
        * Clique em **OK** para aprovar as configurações e fechar **Configuração Avançada**.

    4. No menu **Restaurar**, clique em **Restaurar** para iniciar o trabalho de restauração. Na área de notificações, você pode acompanhar o progresso. Você também pode acompanhar o progresso nos trabalhos de restauração de banco de dados.

       ![menu de configuração avançada](./media/backup-azure-sql-database/restore-job-notification.png)


## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gerenciar operações de Backup do Azure para SQL em VMs do Azure

Esta seção fornece informações sobre as várias operações de gerenciamento de Backup do Azure disponíveis para SQL em máquinas virtuais do Azure. Existem as seguintes operações de alto nível:

* Monitorar trabalhos
* Alertas de Backup
* Interromper a proteção em um banco de dados SQL
* Retomar a proteção para um banco de dados SQL
* Acionar um trabalho de backup ad hoc
* Cancelar o registro de um servidor SQL

### <a name="monitor-jobs"></a>Monitorar trabalhos

O Backup do Azure usa APIs nativas do SQL para todas as operações de backup. Usando as APIs nativas, você pode buscar todas as informações de trabalho da [tabela de conjunto de backup do SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) no banco de dados msdb. Além disso, o Backup do Azure mostra todos os trabalhos acionados manualmente ou ad hoc no portal de trabalhos de backup. Os trabalhos disponíveis no portal incluem: todas as operações de configuração de backup, as operações de restauração, operações de registro e descoberta de banco de dados e operações de interrupção de backup. Todos os trabalhos agendados também podem ser monitorados com análise de logs do OMS. Usar a análise de log remove a desordem de trabalhos e fornece flexibilidade granular para monitoramento ou filtragem de trabalhos específicos.

![menu de configuração avançada](./media/backup-azure-sql-database/jobs-list.png)

### <a name="backup-alerts"></a>Alertas de Backup

Com backups de log que ocorrem a cada 15 minutos, monitorar ocasionalmente os trabalhos de backup pode ser entediante. O Backup do Azure planejado para essa situação potencialmente entediante é fornecer alertas por email acionados por qualquer falha de backup. Os alertas são consolidados no nível do banco de dados por código de erro. Por exemplo, se um banco de dados tem várias falhas de backup, em vez de receber um alerta para cada falha, você recebe um email para a primeira falha. Em seguida, você pode entrar no Portal do Azure para monitorar as falhas subsequentes para esse banco de dados. 

Para monitorar os alertas de backup:

1. Entre em sua assinatura do Azure no [Portal do Azure](https://portal.azure.com).

2. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

3. No menu de cofre de Serviços de Recuperação, selecione **Alertas e Eventos**. 

   ![menu de configuração avançada](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. No menu **Alertas e Eventos**, selecione **Alertas de Backup** para exibir a lista de alertas.

   ![menu de configuração avançada](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-on-a-sql-server-database"></a>Interromper a proteção em um banco de dados do SQL Server

Se você parar de proteger um banco de dados do SQL Server, o Backup do Azure perguntará se você deseja manter os pontos de recuperação. Há duas maneiras para interromper a proteção de banco de dados SQL:

* Parar todos os trabalhos de backup futuros e excluir todos os pontos de recuperação,
* Parar todos os trabalhos de backup futuros, mas deixar os pontos de recuperação 

Deixar os pontos de recuperação acarreta um custo porque os pontos de recuperação de SQL realizam a cobrança de valores da instância protegida do SQL, além do armazenamento consumido. Para saber mais sobre os preços de Backup do Azure para SQL, veja a [página de preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/). Para interromper a proteção para o banco de dados:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel do cofre, selecione **Uso** dos Itens de Backup para abrir o menu Itens de Backup.

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. No menu **Itens de Backup**, selecione o tipo de gerenciamento de backup, **SQL na VM do Azure**. 

    ![Clique em + Backup para abrir o menu de meta de Backup](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    A lista de itens de Backup será ajustada para mostrar a lista de bancos de dados SQL. 

4. Na lista de bancos de dados SQL, selecione o banco de dados de que você deseja interromper a proteção.

    ![selecione SQL na VM do Azure na lista](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando você seleciona o banco de dados, o menu é aberto. 

5. No menu de banco de dados selecionado, clique em **Parar backup** para parar de proteger o banco de dados.

    ![selecione restaurar o banco de dados](./media/backup-azure-sql-database/stop-db-button.png)

    O menu **Parar Backup** é aberto.

6. No menu **Parar Backup**, escolha Reter Dados de Backup ou Excluir Dados de Backup. Opcionalmente, você pode fornecer um motivo para interromper a proteção e um comentário.

    ![selecione restaurar o banco de dados](./media/backup-azure-sql-database/stop-backup-button.png)

7. Clique em **Parar Backup** para interromper a proteção no banco de dados. 

### <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Se a opção **Reter Dados do Backup** estiver selecionada ao parar a proteção do banco de dados SQL, será possível retomar a proteção. Se os dados de backup não tiverem sido mantidos, não será possível retomar a proteção. 

1. Para retomar a proteção para o banco de dados SQL, abra o item de backup e clique em **Retomar Backup**.

    ![retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

   O menu Política de Backup é aberto.

2. No menu **Política de Backup**, selecione uma política e clique em **Salvar**.

### <a name="trigger-an-adhoc-backup"></a>Acionar um backup ad hoc

Sempre que quiser, você pode acionar um backup ad hoc. Há quatro tipos de backup ad hoc. Para obter detalhes sobre cada tipo, veja o artigo [Tipos de backups do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

* Backup completo
* Backup Completo de Copiar Somente
* Backup Diferencial
* Backup de Log

### <a name="unregister-a-sql-server"></a>Cancelar o registro de um SQL Server

Para cancelar o registro de um servidor SQL após a remoção da proteção, mas antes de excluir o cofre

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. Na seção **Gerenciar** do menu do cofre, clique em **Infraestrutura de Backup**.  

   ![retomar a proteção do banco de dados](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Na seção **Servidores de Gerenciamento**, clique em **Servidores Protegidos**.

   ![retomar a proteção do banco de dados](./media/backup-azure-sql-database/protected-servers.png)

    O menu de servidores protegidos é aberto. 

4. No menu **Servidores Protegidos**, selecione o servidor de que deseja cancelar o registro. Se você deseja excluir o cofre, deverá cancelar o registro de todos os servidores.

5. No menu Servidores Protegidos, clique com o botão direito do mouse no servidor protegido e selecione **Excluir**. 

   ![retomar a proteção do banco de dados](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Backup do Azure, consulte o exemplo do PowerShell para fazer backup de máquinas virtuais criptografadas.

> [!div class="nextstepaction"]
> [Fazer backup da VM criptografada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
