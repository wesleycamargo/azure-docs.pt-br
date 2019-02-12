---
title: Fazer backup de bancos de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica como fazer backup do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: raynew
ms.openlocfilehash: fa154b79625fffb8174c510156b3a67df8bff785
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770420"
---
# <a name="back-up-sql-server-databases-to-azure"></a>Fazer backup de bancos de dados do SQL Server para o Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. O Backup do Azure fornece uma solução de backup do SQL Server que não exige infraestrutura: sem servidor de backup complexo, sem agente de gerenciamento e sem armazenamento de backup para gerenciar. O Backup do Azure fornece gerenciamento centralizado para seus backups em todos os servidores SQL que estão executando o SQL Server ou mesmo cargas de trabalho diferentes.

Neste artigo, você aprende:

> [!div class="checklist"]
> * Os pré-requisitos para fazer backup de uma instância do SQL Server no Azure.
> * Como criar e usar um cofre dos Serviços de Recuperação.
> * Como configurar backups do banco de dados do SQL Server.
> * Como definir uma política de backup (ou retenção) para os pontos de recuperação.
> * Como restaurar o banco de dados.

Antes de começar os procedimentos neste artigo, você deve ter uma instância do SQL Server em execução no Azure. [Use as máquinas virtuais do Marketplace do SQL para criar rapidamente uma instância do SQL Server](../sql-database/sql-database-get-started-portal.md).

## <a name="public-preview-limitations"></a>Limitações da visualização pública

Os itens a seguir são as limitações conhecidas da Visualização Pública:

- A VM (máquina virtual) SQL exige conectividade com a Internet para acessar os endereços IP públicos do Azure. Para obter detalhes, veja [Estabelecer conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).
- Proteja até 2 mil bancos de dados SQL em um cofre dos Serviços de Recuperação. Os bancos de dados SQL adicionais devem ser armazenados em um cofre de Serviços de Recuperação separado.
- [Backups de grupos de disponibilidade distribuídos](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/distributed-availability-groups?view=sql-server-2017) têm limitações.
- Não há suporte para FCIs (Instâncias do Cluster de Failover) Always On do SQL Server.
- Use o Portal do Azure para configurar o Backup do Azure para proteger bancos de dados do SQL Server. No momento, não há suporte para o Azure PowerShell, a CLI do Azure e as APIs REST.
- Não há suporte para operações de backup/restauração para bancos de dados de espelho FCI, instantâneos de banco de dados e bancos de dados.
- Banco de dados com um grande número de arquivos não podem ser protegidos. O número máximo de arquivos compatível não é um número muito determinístico, porque ele não depende apenas do número de arquivos, mas também do comprimento do caminho dos arquivos. No entanto, esses casos são menos predominantes. Estamos criando uma solução para lidar com isso.

Consulte a [Seção de Perguntas Frequentes](https://docs.microsoft.com/azure/backup/backup-azure-sql-database#faq) para obter mais detalhes sobre o suporte/cenários sem suporte.

## <a name="support-for-azure-geos"></a>Suporte para áreas geográficas do Azure

Há suporte para o Backup do Azure nas áreas geográficas a seguir:

- Sudeste da Austrália (ASE)
- Sul do Brasil (BRS)
- Canadá Central (CNC)
- Leste do Canadá (CE)
- Central dos EUA (CUS)
- Ásia Oriental (EA)
- Leste da Austrália (AE)
- Leste dos EUA (EUS)
- Leste dos EUA 2 (EUS2)
- Índia Central (INC)
- Sul da Índia (INS)
- Leste do Japão (JPE)
- Oeste do Japão (JPW)
- Coreia Central (KRC)
- Sul da Coreia (KRS)
- Centro-Norte dos EUA (NCUS)
- Europa Setentrional (NE)
- Centro-Sul dos EUA (SCUS)
- Sudeste Asiático (SEA)
- Sul do Reino Unido (UKS)
- Oeste do Reino Unido (UKW)
- Centro-Oeste dos EUA (WCUS)
- Europa Ocidental (WE)
- Oeste dos EUA (WUS)
- Oeste dos EUA 2 (WUS 2)

## <a name="support-for-operating-systems-and-sql-server-versions"></a>Suporte para sistemas operacionais e versões do SQL Server

Esta seção descreve o suporte de Backup do Azure para sistemas operacionais e versões do SQL Server. Há suporte para máquinas virtuais do Azure no Marketplace do SQL e para máquinas virtuais não do Marketplace (em que o SQL Server é instalado manualmente).

### <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

- Windows Server 2012
- Windows Server 2012 R2
- Windows Server 2016

Não há suporte para Linux no momento.

### <a name="supported-sql-server-versions-and-editions"></a>Edições e versões do SQL Server com suporte

- SQL Server 2012 Enterprise, Standard, Web, Developer, Express
- SQL Server 2014 Enterprise, Standard, Web, Developer, Express
- SQL Server 2016 Enterprise, Standard, Web, Developer, Express
- SQL Server 2017 Enterprise, Standard, Web, Developer, Express

## <a name="prerequisites"></a>Pré-requisitos

Antes de fazer backup de seu banco de dados do SQL Server, verifique as condições a seguir:

- Identifique ou [crie um cofre de Serviços de Recuperação](backup-azure-sql-database.md#create-a-recovery-services-vault) na mesma região ou localidade, que a máquina virtual que hospeda a sua instância do SQL Server.
- [Verifique as permissões na máquina virtual](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms) que são necessárias para fazer backup dos bancos de dados SQL.
- Verifique se que a [máquina virtual SQL tem conectividade de rede](backup-azure-sql-database.md#establish-network-connectivity).
- Verifique se os bancos de dados SQL são nomeados de acordo com as [diretrizes de nomenclatura](backup-azure-sql-database.md#sql-database-naming-guidelines-for-azure-backup) para o Backup do Azure para fazer backups bem-sucedidos.

> [!NOTE]
> Você pode ter apenas uma solução de backup de cada vez para fazer o backup de bancos de dados do SQL Server. Desabilite todos os outros backups SQL antes de usar esse recurso; caso contrário, os backups causarão interferência e falharão. Você pode habilitar um Backup do Azure para VM de IaaS junto com o backup do SQL sem qualquer conflito.
>

Se essas condições existirem em seu ambiente, vá para [Configurar o backup para bancos de dados do SQL Server](backup-azure-sql-database.md#configure-backup-for-sql-server-databases). Se algum dos pré-requisitos não existir, continue lendo.


## <a name="establish-network-connectivity"></a>Estabelecer conectividade de rede

Para todas as operações, a máquina virtual SQL precisa de conectividade para os endereços IP públicos do Azure. As operações de máquina virtual SQL (como descoberta de banco de dados, configuração de backup, agendamento de backups, restauração de pontos de recuperação e assim por diante) falharão sem conectividade com os endereços IP públicos. Use uma das opções a seguir para fornecer um caminho claro para o tráfego de backup:

- Colocar os intervalos de IP do datacenter do Azure na lista de permissões: Para colocar os intervalos IP do datacenter do Azure na lista de permissões, use a [página do Centro de Download para obter detalhes sobre os intervalos IP e as instruções](https://www.microsoft.com/download/details.aspx?id=41653).
- Implantar um servidor proxy HTTP para rotear o tráfego: Quando você fizer o backup de um banco de dados SQL em uma VM, a extensão de backup na VM usa as APIs HTTPS para enviar comandos de gerenciamento para o Backup do Azure e dados para o Armazenamento do Azure. A extensão de backup também usa o Azure AD (Azure Active Directory) para autenticação. Roteie o tráfego de extensão de backup para esses três serviços por meio do proxy HTTP. A extensão é o único componente que está configurado para acesso à Internet pública.

As compensações entre as opções são capacidade de gerenciamento, controle granular e custo.

> [!NOTE]
> As marcas de serviço para Backup do Azure devem estar disponíveis por meio de Disponibilidade Geral.
>

| Opção | Vantagens | Desvantagens |
| ------ | ---------- | ------------- |
| Intervalos de IPs na lista de autorizados | Sem custo adicional. <br/> Para o acesso abrir em um NSG, use o cmdlet **Set-AzureNetworkSecurityRule**. | É complexo de gerenciar porque os intervalos de IP afetados mudam com o tempo. <br/>Fornece acesso a todo o Azure, não somente ao Armazenamento do Azure.|
| Usar um proxy HTTP   | É permitido o controle granular no proxy em relação às URLs de armazenamento. <br/>Único ponto de acesso à Internet para VMs. <br/> Não está sujeito a alterações do endereço IP do Azure | Custos adicionais para executar uma VM com o software de proxy. |

## <a name="set-permissions-for-non-marketplace-sql-vms"></a>Definir permissões para VMs do SQL que não são do Marketplace

Para fazer backup de uma máquina virtual, o Backup do Azure exibe que a extensão **AzureBackupWindowsWorkload** esteja instalada. Se você usar máquinas virtuais do Azure Marketplace, prossiga para [Descobrir bancos de dados do SQL Server](backup-azure-sql-database.md#discover-sql-server-databases). Se a máquina virtual que hospeda os bancos de dados SQL não tiver sido criada no Azure Marketplace, conclua o procedimento a seguir para instalar a extensão e definir as permissões apropriadas. Além da extensão **AzureBackupWindowsWorkload**, o Backup do Azure requer privilégios de sysadmin do SQL para proteger bancos de dados SQL. Para descobrir bancos de dados na máquina virtual, o Backup do Azure cria uma conta **NT SERVICE\AzureWLBackupPluginSvc**. Essa conta é usada para backup e restauração e precisa ter permissão de sysadmin do SQL. Além disso, o Backup do Azure aproveitará **NT AUTHORITY\SYSTEM** de conta para descoberta/consulta de banco de dados, portanto, essa conta precisa ser um logon público no SQL.

Para configurar permissões:

1. No [portal do Azure](https://portal.azure.com), abra o cofre de Serviços de Recuperação que você pode usar para proteger os bancos de dados SQL.

2. No painel **Cofre dos serviços de recuperação**, selecione **Backup**. O menu **Meta de Backup** é aberto.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. No menu **Meta de Backup**, defina o **Local em que sua carga de trabalho está sendo executada** para o padrão: **Azure**.

4. Expanda a caixa de listagem suspensa **Do que você deseja fazer backup** e selecione **SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    O menu **Meta de Backup** exibe duas etapas: **Descobrir Bancos de Dados em VMs** e **Configurar Backup**. A etapa **Descobrir Bancos de Dados em VMs** inicia uma pesquisa para máquinas virtuais do Azure.

    ![Examine as duas etapas de Meta de Backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

5. Em **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar máquinas virtuais desprotegidas na assinatura. Pode levar um tempo para pesquisar todas as máquinas virtuais. O tempo de pesquisa depende do número de máquinas virtuais desprotegidas na assinatura.

    ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

6. Na lista de máquinas virtuais, selecione a VM que tem o banco de dados SQL para fazer backup e, em seguida, selecione **Descobrir BDs**.

    O processo de descoberta instala a extensão **AzureBackupWindowsWorkload** na máquina virtual. A extensão permite que o serviço de Backup do Azure se comunique com a máquina virtual para que ela possa fazer backup dos bancos de dados SQL. Depois que a extensão for instalada, o Backup do Azure criará a conta de serviço virtual do Windows, **NT Service\AzureWLBackupPluginSvc**, na máquina virtual. A conta de serviço virtual requer a permissão de sysadmin do SQL. Durante o processo de instalação da conta de serviço virtual, se você receber o erro `UserErrorSQLNoSysadminMembership`, veja [Corrigir permissões de sysadmin do SQL](backup-azure-sql-database.md#fix-sql-sysadmin-permissions).

    A área **Notificações** mostra o progresso da descoberta de banco de dados. Poderá levar algum tempo para que o trabalho seja concluído. O tempo de trabalho depende de quantos bancos de dados estão na máquina virtual. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

Depois de associar o banco de dados ao cofre de Serviços de Recuperação, a próxima etapa é [configurar o trabalho de backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="fix-sql-sysadmin-permissions"></a>Corrigir permissões de sysadmin do SQL

Durante o processo de instalação, caso receba o erro `UserErrorSQLNoSysadminMembership`, use uma conta com permissões de sysadmin do SQL Server para entrar no SSMS (SQL Server Management Studio). A menos que você precise de permissões de acesso especiais, a autenticação do Windows deverá funcionar.

1. No SQL Server, abra a pasta Segurança/Logons.

    ![Abra a pasta Segurança/Logons para ver as contas](./media/backup-azure-sql-database/security-login-list.png)

2. Clique com o botão direito do mouse na pasta Logons e selecione **Novo Logon**. Na caixa de diálogo **Logon – Novo**, selecione **Pesquisar**.

    ![Na caixa de diálogo Logon – Novo, selecione Pesquisar](./media/backup-azure-sql-database/new-login-search.png)

3. A conta de serviço virtual do Windows **NT SERVICE\AzureWLBackupPluginSvc** foi criada durante a fase de descoberta do SQL e do registro da máquina virtual. Insira o nome da conta conforme mostrado na caixa **Inserir o nome do objeto a selecionar**. Selecione **Verificar Nomes** para resolver o nome.

    ![Selecione Verificar Nomes para resolver o nome de serviço desconhecido](./media/backup-azure-sql-database/check-name.png)

4. Selecione **OK** para fechar a caixa de diálogo.

5. Na caixa de diálogo **Funções de Servidor**, verifique se a função **sysadmin** está selecionada. Selecione **OK** para fechar a caixa de diálogo.

    ![Verifique se a função de servidor sysadmin está selecionada](./media/backup-azure-sql-database/sysadmin-server-role.png)

    As permissões necessárias agora devem existir.

6. Embora você tenha corrigido o erro de permissões, precisa associar o banco de dados ao cofre dos Serviços de Recuperação. No portal do Azure, na lista **Servidores Protegidos**, clique com o botão direito do mouse no servidor que está em estado de erro e selecione **Redescobrir Bancos de Dados**.

    ![Verifique se o servidor tem as permissões apropriadas](./media/backup-azure-sql-database/check-erroneous-server.png)

    A área **Notificações** mostra o progresso da descoberta de banco de dados. Poderá levar algum tempo para que o trabalho seja concluído. O tempo de trabalho depende de quantos bancos de dados estão na máquina virtual. Quando os bancos de dados selecionados são descobertos, é exibida uma mensagem de êxito.

    ![Mensagem de êxito na implantação](./media/backup-azure-sql-database/notifications-db-discovered.png)

Depois de associar o banco de dados ao cofre de Serviços de Recuperação, a próxima etapa é [configurar o trabalho de backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

## <a name="sql-database-naming-guidelines-for-azure-backup"></a>Diretrizes de nomenclatura de banco de dados SQL para o Backup do Azure
Para garantir backups estáveis usando o Backup do Azure para SQL Server em VM IaaS, evite o seguinte ao nomear os bancos de dados:

  * Espaços à esquerda ou à direita
  * '!' à direita
  * Colchete de fechamento ‘]’

Temos o uso de alias para caracteres não compatíveis com a Tabela do Azure, mas é recomendável evitá-los também. Para obter mais informações, veja este [artigo](https://docs.microsoft.com/rest/api/storageservices/Understanding-the-Table-Service-Data-Model?redirectedfrom=MSDN).

[!INCLUDE [How to create a Recovery Services vault](../../includes/backup-create-rs-vault.md)]

## <a name="discover-sql-server-databases"></a>Descobrir bancos de dados do SQL Server

O Backup do Azure descobre todos os bancos de dados em uma instância do SQL Server. Você pode proteger os bancos de dados de acordo com as suas necessidades de backup. Use o procedimento a seguir para identificar a máquina virtual que hospeda os bancos de dados SQL. Depois de identificar a máquina virtual, o Backup do Azure instalará uma extensão leve para descobrir os bancos de dados do SQL Server.

1. Entre na sua assinatura no [Portal do Azure](https://portal.azure.com/).

2. No menu esquerdo, selecione **Todos os serviços**.

    ![Selecionar Todos os serviços](./media/backup-azure-sql-database/click-all-services.png) <br/>

3. Na caixa de diálogo **Todos os serviços**, insira **Serviços de Recuperação**. Conforme você digita, sua entrada filtra a lista de recursos. Selecione **cofres dos Serviços de Recuperação** na lista.

  ![Insira e escolha os cofres dos Serviços de Recuperação](./media/backup-azure-sql-database/all-services.png) <br/>

    A lista de cofres de Serviços de Recuperação na assinatura aparecerá.

4. Na lista de cofre de Serviços de Recuperação, selecione o cofre que você deseja usar para proteger bancos de dados SQL.

5. No painel **Cofre dos serviços de recuperação**, selecione **Backup**. O menu **Meta de Backup** é aberto.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

6. No menu **Meta de Backup**, defina o **Local em que sua carga de trabalho está sendo executada** para o padrão: **Azure**.

7. Expanda a caixa de listagem suspensa **Do que você deseja fazer backup** e selecione **SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    O menu **Meta de Backup** exibe duas etapas: **Descobrir Bancos de Dados em VMs** e **Configurar Backup**.

    ![Examine as duas etapas de Meta de Backup](./media/backup-azure-sql-database/backup-goal-menu-step-one.png)

8. Em **Descobrir BDs em VMs**, selecione **Iniciar Descoberta** para pesquisar máquinas virtuais desprotegidas na assinatura. Pode levar um tempo para pesquisar em todas as máquinas virtuais. O tempo de pesquisa depende do número de máquinas virtuais desprotegidas na assinatura.

    ![O backup está pendente durante a pesquisa por BDs em VMs](./media/backup-azure-sql-database/discovering-sql-databases.png)

    Depois que uma máquina virtual não protegida é descoberta, ela aparece na lista. Várias máquinas virtuais podem ter o mesmo nome. No entanto, as máquinas virtuais com o mesmo nome pertencem a diferentes grupos de recursos. As máquinas virtuais desprotegidas são listadas por seu nome da máquina virtual e grupo de recursos. Se uma máquina virtual esperada não estiver listada, veja se ela já está protegida em um cofre.

9. Na lista de máquinas virtuais, selecione a VM que tem o banco de dados SQL para fazer backup e, em seguida, selecione **Descobrir BDs**.

    O Backup do Azure descobre todos os bancos de dados SQL na máquina virtual. Para obter informações sobre o que acontece durante a fase de descoberta do banco de dados, veja [Operações em segundo plano](backup-azure-sql-database.md#background-operations). Após a descoberta de bancos de dados SQL, você está pronto para [configurar o trabalho de backup](backup-azure-sql-database.md#configure-backup-for-sql-server-databases).

### <a name="background-operations"></a>Operações em segundo plano

Quando você usa a ferramenta **Descobrir Bancos de Dados**, o Backup do Azure executa as seguintes operações em segundo plano:

- Registra a máquina virtual com o cofre dos Serviços de Recuperação para o backup de cargas de trabalho. O backup de todos os bancos de dados na máquina virtual registrada pode ser feito somente para este cofre dos Serviços de Recuperação.

- Instale a extensão **AzureBackupWindowsWorkload** na máquina virtual. Fazer o backup de um banco de dados SQL é uma solução sem agente. A extensão é instalada na máquina virtual e nenhum agente é instalado no banco de dados SQL.

- Crie a conta de serviço **NT Service\AzureWLBackupPluginSvc** na máquina virtual. Todas as operações de backup e restauração usam a conta de serviço. **NT Service\AzureWLBackupPluginSvc** precisa de permissões de sysadmin do SQL. Todas as máquinas virtuais do Marketplace SQL são fornecidas com o **SqlIaaSExtension** instalado. A extensão **AzureBackupWindowsWorkload** usa a **SQLIaaSExtension** para obter automaticamente as permissões necessárias. Se sua máquina virtual não tiver a **SqlIaaSExtension** instalada, a operação Descobrir Banco de Dados falhará com a mensagem de erro `UserErrorSQLNoSysAdminMembership`. Para adicionar a permissão sysadmin para backup, siga as instruções em [Configurar permissões de Backup do Azure para VMs SQL não do Marketplace](backup-azure-sql-database.md#set-permissions-for-non-marketplace-sql-vms).

    ![Selecionar a VM e o banco de dados](./media/backup-azure-sql-database/registration-errors.png)

## <a name="configure-backup-for-sql-server-databases"></a>Configurar backup para o banco de dados do SQL Server

O Backup do Azure fornece serviços de gerenciamento para proteger seus bancos de dados do SQL Server e gerenciar trabalhos de backup. As funções de gerenciamento e monitoramentos dependem de seu cofre de Serviços de Recuperação.

> [!NOTE]
> Você pode ter apenas uma solução de backup de cada vez para fazer o backup de bancos de dados do SQL Server. Desabilite todos os outros backups SQL antes de usar esse recurso; caso contrário, os backups causarão interferência e falharão. Você pode habilitar um Backup do Azure para VM de IaaS junto com o backup do SQL sem qualquer conflito.
>

Para configurar a proteção para o banco de dados SQL:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel **Cofre dos serviços de recuperação**, selecione **Backup**. O menu **Meta de Backup** é aberto.

   ![Selecione Backup para abrir o menu Meta de Backup](./media/backup-azure-sql-database/open-backup-menu.png)

3. No menu **Meta de Backup**, defina o **Local em que sua carga de trabalho está sendo executada** para o padrão: **Azure**.

4. Expanda a caixa de listagem suspensa **Do que você deseja fazer backup** e selecione **SQL Server na VM do Azure**.

    ![Selecione o SQL Server na VM do Azure para o backup](./media/backup-azure-sql-database/choose-sql-database-backup-goal.png)

    O menu **Meta de Backup** exibe duas etapas: **Descobrir Bancos de Dados em VMs** e **Configurar Backup**.

    Se você concluiu as etapas neste artigo na ordem, descobriu as máquinas virtuais desprotegidas e o cofre está registrado com uma máquina virtual. Agora você está pronto para configurar a proteção para os bancos de dados SQL.

5. No menu **Meta de Backup**, selecione **Configurar o Backup**.

    ![Selecione Configurar Backup](./media/backup-azure-sql-database/backup-goal-configure-backup.png)

    O serviço de Backup do Azure exibe todas as instâncias do SQL Server com bancos de dados autônomos e grupos de disponibilidade Always On do SQL Server. Para exibir os bancos de dados autônomos na instância do SQL Server, selecione a divisa à esquerda do nome da instância. Da mesma forma, selecione a divisa à esquerda do grupo de disponibilidade Always On para exibir a lista de bancos de dados. A imagem a seguir mostra exemplos de uma instância autônoma e um grupo de disponibilidade Always On.

      ![Exibindo todas as Instâncias do SQL Server com bancos de dados autônomos](./media/backup-azure-sql-database/list-of-sql-databases.png)

6. Na lista de bancos de dados, selecione todos os banco de dados o que você deseja proteger e clique em **OK**.

    ![Proteção do banco de dados](./media/backup-azure-sql-database/select-database-to-protect.png)

    Você pode selecionar até 50 bancos de dados ao mesmo tempo de uma vez. Para proteger mais de 50 bancos de dados, faça várias passagens. Depois que você protege os primeiros 50 bancos de dados, repita essa etapa para proteger o próximo conjunto de bancos de dados.

    > [!Note]
    > Para otimizar cargas de backup, o Backup do Azure divide grandes trabalhos de backup em vários lotes. O número máximo de bancos de dados em um trabalho de backup é 50.
    >

      Como alternativa, você pode habilitar a [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) no grupo instância inteira ou grupo de disponibilidade Always On selecionando a opção **ATIVAR** na lista suspensa correspondente na coluna **PROTEÇÃO AUTOMÁTICA**. O recurso de [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) não só permite a proteção em todos os bancos de dados existentes de uma só vez, mas protege automaticamente quaisquer novos bancos de dados que serão adicionados no futuro para essa instância ou o grupo de disponibilidade.  

      ![Habilite a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

7. Para criar ou escolher uma política de backup, no menu **Backup**, selecione **Política de backup**. O menu **Política de Backup** é aberto.

    ![Selecionar a Política de backup](./media/backup-azure-sql-database/select-backup-policy.png)

8. Na lista suspensa **Escolher política de backup**, escolha uma política de backup selecione **OK**. Para obter informações sobre como criar uma política de backup, veja [Definir uma política de backup](backup-azure-sql-database.md#define-a-backup-policy).

   > [!NOTE]
   > Durante a visualização, é possível editar políticas de Backup. Se você quiser uma política diferente do que está disponível na lista, você deve criar essa política. Para saber mais sobre como criar uma nova política de backup, veja a seção [Definir uma política de backup](backup-azure-sql-database.md#define-a-backup-policy).

    ![Escolha uma política de backup na lista](./media/backup-azure-sql-database/select-backup-policy-steptwo.png)

    No menu **Política de backup**, na caixa de listagem suspensa **Escolher política de backup**, você pode:
    - Selecionar a política padrão: **HourlyLogBackup**.
    - Escolher uma política de backup existente criada anteriormente para SQL.
    - [Definir uma nova política](backup-azure-sql-database.md#define-a-backup-policy) baseada no seu período de retenção e o RPO.

    > [!Note]
    > O Backup do Azure dá suporte para retenção de longo prazo com base no esquema de backup avô-pai-filho. O esquema otimiza o consumo de armazenamento de back-end enquanto cumpre as necessidades de conformidade.
    >

9. Depois de você escolher uma política de backup, no **menu Backup**, selecione **Habilitar backup**.

    ![Habilitar a política de backup escolhida](./media/backup-azure-sql-database/enable-backup-button.png)

    Acompanhe o progresso da configuração na área **Notificações** do portal.

    ![Área de notificação](./media/backup-azure-sql-database/notifications-area.png)


## <a name="auto-protect-sql-server-in-azure-vm"></a>Proteger automaticamente o SQL Server na VM do Azure  

A proteção automática permite proteger automaticamente todos os bancos de dados existentes e aqueles que você adicionaria no futuro a uma instância autônoma do SQL Server ou a um grupo de disponibilidade do SQL Server Always On. **ATIVAR** a proteção automática e escolher uma política de backup serão aplicadas são ações que estarão em vigor para bancos de dados protegidos desse momento em diante, os bancos de dados protegidos existentes continuarão a usar a política anterior.

![Habilite a proteção automática no grupo de disponibilidade Always On](./media/backup-azure-sql-database/enable-auto-protection.png)

Não há nenhum limite no número de bancos de dados que foi selecionado de uma só vez usando o recurso de proteção automática. A configuração do backup é disparada para todos os bancos de dados simultaneamente e pode ser controlada nos **Trabalhos de Backup**.

Se por algum motivo você precisar desabilitar a proteção automática em uma instância, clique no nome de instância em **Configurar o Backup** para abrir o painel de informações do lado direito, que tem a opção **Desabilitar Proteção Automática** na parte superior. Clique em **Desabilitar Proteção automática** para desabilitar a proteção automática nessa instância.

![Desabilite a proteção automática nessa instância](./media/backup-azure-sql-database/disable-auto-protection.png)

Todos os bancos de dados nessa instância continuarão a ser protegidos. No entanto, essa ação vai desabilitar a proteção automática em qualquer banco de dados que serão adicionados no futuro.

### <a name="define-a-backup-policy"></a>Definir uma política de backup

Uma política de backup define uma matriz de quando os backups são obtidos e por quanto tempo eles são mantidos. Use o Backup do Azure para agendar três tipos de backup para bancos de dados SQL:

* Backup completo: Um backup completo do banco de dados faz backup do banco de dados inteiro. Um backup completo contém todos os dados em um banco de dados específico ou um conjunto de arquivos ou grupos de arquivos e log suficiente para recuperar esses dados. No máximo, você pode acionar um backup completo por dia. Você pode optar por fazer um backup completo em um intervalo diário ou semanal.
* Backup diferencial: Um backup diferencial é baseado no backup de dados completo anterior mais recente. Um backup diferencial captura apenas os dados que foram alterados desde o backup completo. No máximo, você pode acionar um backup diferencial por dia. Você não pode configurar um backup completo e um backup diferencial no mesmo dia.
* Backup de log de transações: Um backup de log permite a restauração pontual até um determinado segundo. No máximo, você pode configurar backups de log de transações a cada 15 minutos.

A política é criada no nível do cofre de Serviços de Recuperação. Vários cofres podem usar a mesma política de backup, mas você deve aplicar a política de backup a cada cofre. Ao criar uma política de backup, o backup completo diário é o padrão. Você poderá adicionar um backup diferencial, mas somente se configurar backups completos para que ocorram semanalmente. O procedimento a seguir explica como criar uma política de backup para uma instância do SQL Server em uma máquina virtual do Azure.

> [!NOTE]
> Na versão prévia, é possível editar políticas de Backup. Em vez disso, você deve criar uma nova política com os detalhes desejados.  

Para criar uma política de backup:

1. No cofre de Serviços de Recuperação do Microsoft Azure que protege o banco de dados SQL, clique em **Políticas de Backup**e, em seguida, clique em **Adicionar**.

   ![Abra a caixa de diálogo Criar nova política de backup](./media/backup-azure-sql-database/new-policy-workflow.png)

   O menu **Adicionar** é exibido.

2. No menu **Adicionar**, clique em **SQL Server na VM do Azure**.

   ![Escolha um tipo de política para a nova política de backup](./media/backup-azure-sql-database/policy-type-details.png)

   Selecionar SQL Server na VM do Azure define o tipo de política e abre o menu de política de Backup. O menu **Política de backup** mostra os campos necessários para uma nova política de backup do SQL Server.

3. Em **Nome da política**, insira um nome para a nova política.

4. Um backup completo é obrigatório, não é possível desativar a opção **Backup completo**. Clique em **Backup completo** para exibir e editar a política. Mesmo se você não alterar a política de Backup, você deve exibir os detalhes da política.

    ![Novos campos de política de backup](./media/backup-azure-sql-database/full-backup-policy.png)

    No menu **Política de Backup Completo**, para **Frequência de Backup**, escolha **Diária** ou **Semanal**. Para **Diária**, selecione a hora e fuso horário quando o trabalho de backup começar. Você não pode criar backups diferenciais para backups diários completos.

   ![Configuração do intervalo diário](./media/backup-azure-sql-database/daily-interval.png)

    Para **Semanal**, selecione o dia da semana, a hora e o fuso horário do início do trabalho de backup.

   ![Configuração de intervalo semanal](./media/backup-azure-sql-database/weekly-interval.png)

5. Por padrão, todas as opções de **Intervalo de Retenção** são selecionadas: diário, semanal, mensal e anual. Desmarque os limites de intervalo de retenção indesejados. Defina os intervalos a usar. No menu de **política de Backup Completo**, clique em **OK** para aceitar as configurações.

   ![Configuração de intervalo do período de retenção](./media/backup-azure-sql-database/retention-range-interval.png)

    Os pontos de recuperação são marcados para retenção com base em seu intervalo de retenção. Por exemplo, se você selecionar um backup completo diário, apenas um backup completo será disparado a cada dia. O backup para um dia específico é marcado e mantido com base no intervalo de retenção semanal e sua configuração de retenção semanal. Os intervalos de retenção mensal e anual comportam-se de maneira semelhante.

6. Para adicionar uma política de backup diferencial, selecione **Backup Diferencial**. O menu **Política de Backup Diferencial** é aberto.

   ![Abrir o menu de política de backup diferencial](./media/backup-azure-sql-database/backup-policy-menu-choices.png)

    No menu **Política Backup Diferencial**, selecione **Habilitar** para abrir os controles de retenção e frequência. No máximo, você pode acionar um backup diferencial por dia.

    > [!Important]
    > Backups diferenciais podem ser retidos por até 180 dias. Se você precisar de retenção mais longa, deverá usar os backups completos.
    >

   ![Editar a política de backup diferencial](./media/backup-azure-sql-database/enable-differential-backup-policy.png)

    Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

7. Para adicionar uma política de backup de log transacional, selecione **Backup de Log**. O menu **Backup de Log** é aberto.

    No menu de **Backup de Log**, selecione **Habilitar** e defina os controles de retenção e frequência. Os Backups de log podem ocorrer a cada 15 minutos e podem ser mantidos por 35 dias. Selecione **OK** para salvar a política e retornar para o menu principal da **Política de backup**.

   ![Editar a política de backup de log](./media/backup-azure-sql-database/log-backup-policy-editor.png)

8. No menu **Política de backup**, escolha se deseja habilitar a **Compactação de Backup SQL**. A compactação está desabilitada por padrão.

    No back-end, o Backup do Azure usa compactação de backup nativo do SQL.

9. Depois de concluir as edições à política de backup, selecione **OK**.

   ![Aceitar a nova política de backup](./media/backup-azure-sql-database/backup-policy-click-ok.png)

## <a name="restore-a-sql-database"></a>Restaurar um Banco de Dados SQL
O Backup do Azure fornece funcionalidade para restaurar bancos de dados individuais para uma data ou hora específica (até um determinado segundo) usando backups de log de transações. O Backup do Azure determina automaticamente o backup diferencial completo adequado e a cadeia de backups de log necessários para restaurar os dados com base nos tempos de restauração.

Você também pode selecionar um backup completo ou diferencial específico para restaurar para um ponto de recuperação específico em vez de um momento específico.

### <a name="pre-requisite-before-triggering-a-restore"></a>Pré-requisito antes de disparar uma restauração

1. Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure. O servidor de destino precisa ser registrado no mesmo cofre dos Serviços de Recuperação que a fonte.  
2. Para restaurar um banco de dados criptografado TDE para outro SQL Server, primeiro restaure o certificado para o servidor de destino, siga as etapas documentadas [aqui](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017).
3. Antes de disparar uma restauração de banco de dados "mestre", inicie a instância do SQL Server no modo de usuário único com a opção de inicialização `-m AzureWorkloadBackup`. O argumento para a opção `-m` é o nome do cliente. Apenas esse cliente tem permissão para abrir a conexão. Para todos os bancos de dados do sistema (modelo, mestre, msdb), interrompa o serviço SQL Agent antes de disparar a restauração. Feche os aplicativos que podem tentar roubar uma conexão com um desses bancos de dados.

### <a name="steps-to-restore-a-database"></a>Etapas para restaurar um banco de dados:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel **Cofre dos Serviços de Recuperação**, em **Uso**, selecione **Itens de Backup** para abrir o menu **Itens de Backup**.

    ![Abrir o menu Itens de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. No menu **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione o **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    O menu **Itens de Backup** mostra a lista de bancos de dados SQL.

4. Na lista de bancos de dados SQL, selecione o banco de dados a restaurar.

    ![Selecionar o banco de dados a ser restaurado](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando você seleciona o banco de dados, o menu é aberto. O menu fornece os detalhes do backup para o banco de dados incluindo:

    * Os pontos de restauração mais antigo e mais recentes.
    * Status de backup de log para as últimas 24 horas (para bancos de dados em modelo de recuperação bulk-logged e completo, se configurado para backups de log de transações).

5. No menu para o banco de dados selecionado, selecione **Restaurar Banco de Dados**. O menu **Restaurar** é aberto.

    ![Selecione Restaurar BD](./media/backup-azure-sql-database/restore-db-button.png)

    Quando o menu **Restaurar** é aberto, o menu **Configuração da Restauração** também é aberto. O menu **Configuração da Restauração** é a primeira etapa para configurar a restauração. Use este menu para selecionar o local restaurar os dados. As opções são:
    - **Local Alternativo**: Restaure o banco de dados em um local alternativo e retenha o banco de dados de origem original.
    - **Substituir BD**: Restaure os dados para a mesma instância do SQL Server que a fonte original. O efeito dessa opção é substituir o banco de dados original.

    > [!Important]
    > Se o banco de dados selecionado pertencer a um grupo de disponibilidade Always On, o SQL Server não permitirá que o banco de dados seja substituído. Nesse caso, somente a opção **Local Alternativo** está habilitada.
    >

    ![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>Restaurar para um local alternativo

Esse procedimento o conduz pela restauração dos dados para um local alternativo. Para substituir o banco de dados durante a restauração, continue para [Restaurar e substituir o banco de dados](backup-azure-sql-database.md#restore-and-overwrite-the-database). Neste estágio, o cofre de Serviços de Recuperação é aberto e o menu **Configuração de Restauração** está visível. Se você não estiver neste estágio, comece [restaurando um banco de dados SQL](backup-azure-sql-database.md#restore-a-sql-database).

> [!NOTE]
> Você pode restaurar o banco de dados para uma instância de um SQL Server na mesma região do Azure. O servidor de destino precisa ser registrado no cofre dos Serviços de Recuperação.
>

No menu **Restaurar Configuração**, a caixa de listagem suspensa **Servidor** mostra somente as instâncias do SQL Server registradas com o cofre dos Serviços de Recuperação. Se o servidor que você quiser não estiver na lista, veja [Descobrir bancos de dados do SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) para localizar o servidor. Durante o processo de descoberta, novos servidores são registrados no cofre dos Serviços de Recuperação.<br>
Para restaurar um banco de dados SQL, você precisa das seguintes permissões:

* Permissões de **Operador de Backup** no **Cofre** dos Serviços de Recuperação no qual você está fazendo a restauração.
* Acesso de **Colaborador (gravação)** à **VM do SQL de Origem** (a VM da qual é feito o backup e da qual você está tentando restaurar).
* Acesso de **Colaborador (gravação)** à VM SQL de destino (a VM para a qual você está restaurando; será ser a mesma VM que a VM de origem no caso de OLR (Recuperação de Localização Original)).

Para restaurar para uma localização alternativa:

1. No menu **Configuração de Restauração**:

    * Em **Em que Local Restaurar**, selecione **Local Alternativo**.
    * Abra a caixa de listagem suspensa **Servidor** e escolha a instância do SQL Server para restaurar o banco de dados.
    * Abra a caixa de listagem suspensa **Instância** e escolha uma instância do SQL Server.
    * Na caixa **Nome do Banco de Dados Restaurado**, digite o nome do banco de dados de destino.
    * Conforme aplicável, selecione **Substituir se o banco de dados com o mesmo nome já existir na instância do SQL selecionada**.
    * Selecione **OK** para concluir a configuração do destino e continuar para escolher um ponto de restauração.

    ![Forneça valores para o menu de Configuração da Restauração](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. No menu **Selecionar ponto de restauração**, escolha **Logs (Ponto no Tempo)** ou **Completo e Diferencial** como o ponto de restauração. Para restaurar para um log pontual específico, continue com esta etapa. Para restaurar um ponto de restauração completo e um ponto de restauração diferencial, prossiga para a etapa 3.

    ![Selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    A restauração pontual só está disponível para backups de log para bancos de dados com um modelo de recuperação bulk-logged e completo. Para restaurar para um ponto específico no tempo:

    1. Selecione **Logs (Pontual)** no tipo de restauração.

        ![Escolher o tipo de ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Em **Data/Hora da Restauração**, selecione o minicalendário para abrir o **Calendário**. Em **Calendário**, as datas em negrito têm pontos de recuperação e a data atual é realçada. Selecione uma data com pontos de recuperação. Datas sem pontos de recuperação não podem ser selecionadas.

        ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis em um intervalo contínuo.

    3. Use o gráfico de linha do tempo ou a caixa de diálogo **Tempo** para especificar um horário específico para o ponto de recuperação. Selecione **OK** para concluir a etapa de ponto de restauração.

       ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        O menu **Selecionar ponto de restauração** é fechado e o menu **Configuração Avançada** é aberto.

       ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, defina **Restaurar com NORECOVERY** como **Habilitado**.
        * Para alterar o local de restauração no servidor de destino, insira um novo caminho na coluna **Destino**.
        * Selecione **OK** para aprovar as configurações. Feche o menu **Configuração Avançada**.

    5. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração. Acompanhe o progresso da restauração na área **Notificações** ou selecione **Restaurar trabalhos** no menu de banco de dados.

       ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

3. No menu **Selecionar ponto de restauração**, escolha **Logs (Ponto no Tempo)** ou **Completo e Diferencial** como o ponto de restauração. Para restaurar um log pontual, volte para a etapa 2. Essa etapa restaura um ponto de restauração completo ou diferencial específico. Você pode ver todos os pontos de recuperação diferenciais para os últimos 30 dias. Para ver os pontos de recuperação com mais de 30 dias, selecione **Filtrar** para abrir o menu **Filtrar pontos de restauração**. Para um ponto de recuperação diferencial, o Backup do Azure primeiro restaura o ponto de recuperação completo apropriado e, em seguida, aplica o ponto de recuperação diferencial selecionado.

    ![Selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. No menu **Selecionar ponto de restauração**, selecione **Completo e Diferencial**.

       ![Selecionar completo e diferencial](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        O menu mostra a lista de pontos de recuperação disponíveis.

    2. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

        ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        O menu **Ponto de Restauração** é fechado e o menu **Configuração Avançada** é aberto.

        ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, defina **Restaurar com NORECOVERY** como **Habilitado**. **Restaurar com NORECOVERY** é desabilitado por padrão.
        * Para alterar o local de restauração no servidor de destino, insira um novo caminho na coluna **Destino**.
        * Selecione **OK** para aprovar as configurações. Feche o menu **Configuração Avançada**.

    4. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração. Acompanhe o progresso da restauração na área **Notificações** ou selecione **Restaurar trabalhos** no menu de banco de dados.

       ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-and-overwrite-the-database"></a>Restaurar e substituir o banco de dados

Esse procedimento o conduz pela restauração dos dados e substituição de um banco de dados. Para restaurar para um local alternativo, continue para [Restaurar para um local alternativo](backup-azure-sql-database.md#restore-to-an-alternate-location). Neste estágio, o cofre de Serviços de Recuperação é aberto e o menu **Configuração de Restauração** está visível (veja a imagem a seguir). Se você não estiver neste estágio, comece [restaurando um banco de dados SQL](backup-azure-sql-database.md#restore-a-sql-database).

![Menu Restaurar Configuração](./media/backup-azure-sql-database/restore-overwrite-db.png)

No menu **Restaurar Configuração**, a caixa de listagem suspensa **Servidor** mostra somente as instâncias do SQL Server registradas com o cofre dos Serviços de Recuperação. Se o servidor que você quiser não estiver na lista, veja [Descobrir bancos de dados do SQL Server](backup-azure-sql-database.md#discover-sql-server-databases) para localizar o servidor. Durante o processo de descoberta, novos servidores são registrados no cofre dos Serviços de Recuperação.

1. No menu **Configuração da Restauração**, selecione **Substituir Banco de Dados** e, em seguida, selecione **OK** para concluir a configuração do destino.

   ![Selecione Substituir Banco de Dados](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

    As configurações de **Servidor**, **Instância** e **Nome do Banco de Dados Restaurado** não são necessárias.

2. No menu **Selecionar ponto de restauração**, escolha **Logs (Ponto no Tempo)** ou **Completo e Diferencial** como o ponto de restauração. Para restaurar para um log pontual específico, continue com esta etapa. Para restaurar um ponto de restauração completo e um ponto de restauração diferencial, prossiga para a etapa 3.

    ![selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    A restauração pontual só está disponível para backups de log para bancos de dados com um modelo de recuperação bulk-logged e completo. Para restaurar para um segundo específico:

    1. Selecione **Logs (Pontual)** como o ponto de restauração.

        ![Escolher o tipo de ponto de restauração](./media/backup-azure-sql-database/recovery-point-logs.png)

    2. Em **Data/Hora da Restauração**, selecione o minicalendário para abrir o **Calendário**. Em **Calendário**, as datas em negrito têm pontos de recuperação e a data atual é realçada. Selecione uma data com pontos de recuperação. Datas sem pontos de recuperação não podem ser selecionadas.

        ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

        Depois de você selecionar uma data, o gráfico de linha do tempo exibirá os pontos de recuperação disponíveis.

    3. Use o gráfico de linha do tempo ou a caixa de diálogo **Tempo** para especificar um horário específico para o ponto de recuperação. Selecione **OK** para concluir a etapa de ponto de restauração.

       ![Abrir o Calendário](./media/backup-azure-sql-database/recovery-point-logs-graph.png)

        O menu **Selecionar ponto de restauração** é fechado e o menu **Configuração Avançada** é aberto.

       ![Menu de configuração avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    4. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, defina **Restaurar com NORECOVERY** como **Habilitado**.
        * Para alterar o local de restauração no servidor de destino, insira um novo caminho na coluna **Destino**.
        * Selecione **OK** para aprovar as configurações. Feche o menu **Configuração Avançada**.

    5. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração. Acompanhe o progresso da restauração na área **Notificações** ou selecione **Restaurar trabalhos** no menu de banco de dados.

       ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

3. No menu **Selecionar ponto de restauração**, escolha **Logs (Ponto no Tempo)** ou **Completo e Diferencial** como o ponto de restauração. Para restaurar um log pontual, volte para a etapa 2. Essa etapa restaura um ponto de restauração completo ou diferencial específico. Você pode ver todos os pontos de recuperação diferenciais para os últimos 30 dias. Para ver os pontos de recuperação com mais de 30 dias, selecione **Filtrar** para abrir o menu **Filtrar pontos de restauração**. Para um ponto de recuperação diferencial, o Backup do Azure primeiro restaura o ponto de recuperação completo apropriado e, em seguida, aplica o ponto de recuperação diferencial selecionado.

    ![Selecione o menu do ponto de restauração](./media/backup-azure-sql-database/recovery-point-menu.png)

    1. No menu **Selecionar ponto de restauração**, selecione **Completo e Diferencial**.

       ![Selecionar completo e diferencial](./media/backup-azure-sql-database/recovery-point-logs-fd.png)

        O menu mostra a lista de pontos de recuperação disponíveis.

    2. Selecione um ponto de recuperação da lista e selecione **OK** para concluir o procedimento de ponto de restauração.

        ![Escolha um ponto de recuperação completo](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

        O menu **Ponto de Restauração** é fechado e o menu **Configuração Avançada** é aberto.

        ![Menu de Configuração Avançada](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

    3. No menu **Configuração Avançada**:

        * Para manter o banco de dados não operacional após a restauração, defina **Restaurar com NORECOVERY** como **Habilitado**. **Restaurar com NORECOVERY** é desabilitado por padrão.
        * Para alterar o local de restauração no servidor de destino, insira um novo caminho na coluna **Destino**.
        * Selecione **OK** para aprovar as configurações. Feche o menu **Configuração Avançada**.

    4. No menu **Restaurar**, selecione **Restaurar** para iniciar o trabalho de restauração. Acompanhe o progresso da restauração na área **Notificações** ou selecionando **Trabalhos de restauração** no menu de banco de dados.

       ![Progresso do trabalho de restauração](./media/backup-azure-sql-database/restore-job-notification.png)

## <a name="manage-azure-backup-operations-for-sql-on-azure-vms"></a>Gerenciar operações de Backup do Azure para SQL em VMs do Azure

Esta seção fornece informações sobre as várias operações de gerenciamento de Backup do Azure disponíveis para SQL em máquinas virtuais do Azure. Existem as seguintes operações de alto nível:

* Monitorar trabalhos
* Alertas de Backup
* Interromper a proteção em um banco de dados SQL
* Retomar a proteção para um banco de dados SQL
* Disparar um trabalho de backup ad hoc
* Cancelar o registro de um servidor que está executando o SQL Server

### <a name="monitor-backup-jobs"></a>Monitorar trabalhos de backup
O Backup do Azure é uma solução de classe empresarial que fornece alertas de backup avançados e notificações de falhas. (veja [Exibir alertas de backup](backup-azure-sql-database.md#view-backup-alerts).) Para monitorar trabalhos específicos, use qualquer uma das seguintes opções de acordo com suas necessidades.

#### <a name="use-the-azure-portal-for-adhoc-operations"></a>Usar o portal do Azure para operações ad hoc
O Backup do Azure mostra todos os trabalhos disparados manualmente ou ad hoc no portal de **Trabalhos de backup**. Os trabalhos disponíveis no portal **Trabalhos de backup** incluem:
- Todas as operações de configurar backup.
- Operações de backup disparadas manualmente.
- Operações de restauração.
- Operações de registro e descoberta de banco de dados.
- Operações de interromper backup.

![Portal de trabalhos de backup](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> Nenhum dos trabalhos de backup agendados, incluindo completos, diferenciais e backups de log, é exibido no portal **Trabalhos de backup**. Use o SQL Server Management Studio para monitorar trabalhos de backup agendados, conforme descrito na próxima seção.
>

#### <a name="use-sql-server-management-studio-for-backup-jobs"></a>Usar o SQL Server Management Studio para trabalhos de backup
O Backup do Azure usa APIs nativas do SQL para todas as operações de backup. Use as APIs nativas para buscar todas as informações de trabalho na [Tabela de conjunto de backup do SQL](https://docs.microsoft.com/sql/relational-databases/system-tables/backupset-transact-sql?view=sql-server-2017) no banco de dados msdb.

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

### <a name="view-backup-alerts"></a>Exibir alertas de backup

Uma vez que os backups de log ocorrem a cada 15 minutos, às vezes monitorar os trabalhos de backup pode ser entediante. O Backup do Azure fornece ajuda nessa situação. Alertas por email são disparados para todas as falhas de backup. Os alertas são consolidados no nível do banco de dados por código de erro. Um alerta por email é enviado somente para a primeira falha de backup para um banco de dados. Entre no portal do Azure para monitorar todas as falhas para um banco de dados.

Para monitorar os alertas de backup:

1. Entre em sua assinatura do Azure no [portal do Azure](https://portal.azure.com).

2. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

3. No painel **cofre dos Serviços de Recuperação**, selecione **Alertas e Eventos**.

   ![Selecionar Alertas e Eventos](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

4. No menu **Alertas e Eventos**, selecione **Alertas de Backup** para exibir a lista de alertas.

   ![Selecionar Alertas de Backup](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

### <a name="stop-protection-for-a-sql-server-database"></a>Interromper a proteção para um banco de dados do SQL Server

Quando você interrompe a proteção para um banco de dados do SQL Server, o Backup do Azure consulta se deve manter os pontos de recuperação. Há duas maneiras de interromper a proteção para um banco de dados SQL:

* Interromper todos os trabalhos de backup futuros e excluir todos os pontos de recuperação.
* Interromper todos os trabalhos de backup futuros, mas deixar os pontos de recuperação.

Se você optar por Parar o backup com retenção de dados, pontos de recuperação serão removidos de acordo com a política de backup. Você incorrerá em encargo de preço de instância protegida SQL, além do armazenamento consumido, até que todos os pontos de recuperação sejam removidos. Para saber mais sobre os preços de Backup do Azure para SQL, veja a [página de preços do Backup do Azure](https://azure.microsoft.com/pricing/details/backup/).

Sempre que você Parar o Backup com retenção de dados, os pontos de recuperação expirarão de acordo com a política de retenção, mas o Backup do Azure sempre manterá um último ponto de recuperação até que você exclua explicitamente os dados de backup. Da mesma forma, se você excluir uma fonte de dados sem executar Parar Backup, os novos backups começarão a falhar e os pontos de recuperação antigos expirarão de acordo com a política de retenção. No entanto, um último ponto de recuperação sempre será retido até que você execute Parar Backup com exclusão de dados.

Para interromper a proteção para um banco de dados:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel **Cofre dos Serviços de Recuperação**, em **Uso**, selecione **Itens de Backup** para abrir o menu **Itens de Backup**.

    ![Abrir o menu Itens de Backup](./media/backup-azure-sql-database/restore-sql-vault-dashboard.png).

3. No menu **Itens de Backup**, em **Tipo de Gerenciamento de Backup**, selecione o **SQL na VM do Azure**.

    ![Selecionar SQL na VM do Azure](./media/backup-azure-sql-database/sql-restore-backup-items.png)

    O menu **Itens de Backup** mostra a lista de bancos de dados SQL.

4. Na lista de bancos de dados SQL, selecione o banco de dados para interromper a proteção.

    ![Selecionar o banco de dados para interromper a proteção](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

    Quando você seleciona o banco de dados, o menu é aberto.

5. No menu para o banco de dados selecionado, selecione **Interromper backup**.

    ![Selecionar Parar Backup](./media/backup-azure-sql-database/stop-db-button.png)

    O menu **Parar Backup** é aberto.

6. No menu **Interromper Backup**, escolha **Reter Dados de Backup** ou **Excluir Dados de Backup**. Como opção, informe um motivo para interromper a proteção e um comentário.

    ![Interromper o menu de Backup](./media/backup-azure-sql-database/stop-backup-button.png)

7. Selecione **Interromper backup** para interromper a proteção no banco de dados.

  Observe que a opção **Parar Backup** não funcionará para um banco de dados em uma instância [protegida automaticamente](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). A única maneira de interromper a proteção deste banco de dados é desabilitar a [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) na instância temporariamente e, em seguida, escolher a opção **Parar Backup** nos **Itens de Backup** desse banco de dados.<br>
  Depois que você tiver desabilitado a proteção automática, você pode **Parar Backup** para o banco de dados em **itens de Backup**. A instância pode ser habilitada novamente para proteção automática agora.


### <a name="resume-protection-for-a-sql-database"></a>Retomar a proteção para um banco de dados SQL

Se a opção **Reter Dados de Backup** tiver sido selecionada quando a proteção para o banco de dados SQL foi interrompida, você poderá retomar a proteção. Se os dados de backup não tiverem sido mantidos, não será possível retomar a proteção.

1. Para retomar a proteção para o banco de dados SQL, abra o item de backup e selecione **Retomar backup**.

    ![Selecionar Retomar backup para retomar a proteção do banco de dados](./media/backup-azure-sql-database/resume-backup-button.png)

   O menu **Política de Backup** é aberto.

2. No menu **Política de backup**, selecione uma política e, em seguida, selecione **Salvar**.

### <a name="trigger-an-adhoc-backup"></a>Acionar um backup ad hoc

Dispare backups ad hoc conforme necessário. Há quatro tipos de backup ad hoc:

* Backup completo
* Backup completo somente de cópia
* Backup diferencial
* Backup de log

Para obter detalhes sobre cada tipo, veja [Tipos de backups do SQL](https://docs.microsoft.com/sql/relational-databases/backup-restore/backup-overview-sql-server?view=sql-server-2017#types-of-backups).

### <a name="unregister-a-sql-server-instance"></a>Cancelar o registro de uma instância do SQL Server

Para cancelar o registro de uma instância do SQL Server depois de remover a proteção, mas antes de excluir o cofre:

1. Abra o cofre de Serviços de Recuperação registrado com a máquina virtual SQL.

2. No painel **Cofre dos Serviços de Recuperação**, em **Gerenciar**, selecione **Infraestrutura de Backup**.  

   ![Selecionar Infraestrutura de Backup](./media/backup-azure-sql-database/backup-infrastructure-button.png)

3. Em **Servidores de Gerenciamento**, selecione **Servidores Protegidos**.

   ![Selecionar Servidores Protegidos](./media/backup-azure-sql-database/protected-servers.png)

    O menu **Servidores Protegidos** é aberto.

4. No menu **Servidores Protegidos**, selecione o servidor para cancelar o registro. Para excluir o cofre, você deve cancelar o registro de todos os servidores.

5. No menu **Servidores Protegidos**, clique com o botão direito do mouse no servidor protegido e selecione **Excluir**.

   ![Selecione Excluir](./media/backup-azure-sql-database/delete-protected-server.png)

## <a name="faq"></a>Perguntas frequentes

A seção a seguir fornece mais informações sobre o backup do banco de dados SQL.

### <a name="can-i-throttle-the-speed-of-the-sql-server-backup-policy"></a>É possível limitar a velocidade da política de backup do SQL Server?

Sim. Você pode limitar a taxa com que a política de backup é executada para minimizar o impacto em uma instância do SQL Server.
Para alterar a configuração:
1. Na instância do SQL Server, na pasta *C:\Arquivos de Programas\Azure Workload Backup\bin*, crie o arquivo **ExtensionSettingsOverrides.json**.
2. No arquivo **ExtensionSettingsOverrides.json**, altere a configuração **DefaultBackupTasksThreshold** para um valor mais baixo (por exemplo, 5). <br>
  ` {"DefaultBackupTasksThreshold": 5}`

3. Salve suas alterações. Feche o arquivo.
4. Na instância do SQL Server, abra **Gerenciador de Tarefas**. Reinicie o serviço **AzureWLBackupCoordinatorSvc**.

### <a name="can-i-run-a-full-backup-from-a-secondary-replica"></a>Posso executar um backup completo usando uma réplica secundária?
 Não. Não há suporte para esse recurso.

### <a name="do-successful-backup-jobs-create-alerts"></a>Trabalhos de backup bem-sucedidos criam alertas?

 Não. Trabalhos de backup bem-sucedidos não geram alertas. Os alertas são enviados somente para trabalhos de backup com falha.

### <a name="can-i-see-details-for-scheduled-backup-jobs-in-the-jobs-menu"></a>Posso ver os detalhes de trabalhos de backup agendados no menu Trabalhos?

 Não. O menu **Trabalhos de Backup** mostra detalhes do trabalho ad hoc, mas não trabalhos de backup agendados. Se algum trabalho de backup agendado falhar, os detalhes estarão disponíveis nos alertas de trabalho com falha. Para monitorar todos os trabalhos de backup ad hoc e agendados, use o [SQL Server Management Studio](backup-azure-sql-database.md#use-sql-server-management-studio-for-backup-jobs).

### <a name="when-i-select-a-sql-server-instance-are-future-databases-automatically-added"></a>Quando eu seleciono uma instância do SQL Server, bancos de dados futuros são adicionados automaticamente?

 Não. Quando você configura a proteção para uma instância do SQL Server, se você seleciona a opção de nível de servidor, todos os bancos de dados são adicionados. Se você adiciona bancos de dados a uma instância do SQL Server depois de configurar a proteção, deve adicionar manualmente novos bancos de dados para protegê-los. Os bancos de dados não são incluídos automaticamente na proteção configurada.

### <a name="if-i-change-the-recovery-model-how-do-i-restart-protection"></a>Se eu alterar o modelo de recuperação, como faço para reiniciar a proteção?

Dispare um backup completo. Os backups de log começam conforme o esperado.

### <a name="can-i-protect-sql-always-on-availability-groups-where-the-primary-replica-is-on-premises"></a>Posso proteger os Grupos de Disponibilidade Always On do SQL quando a réplica primária for local?

 Não. O Backup do Azure protege os SQL Servers em execução no Azure. Se um AG (Grupo de Disponibilidade) é distribuído entre computadores locais e do Azure, o grupo de disponibilidade pode ser protegido somente se a réplica primária está em execução no Azure. Além disso, o Backup do Azure protege apenas os nós em execução na mesma região do Azure que o cofre de Serviços de Recuperação.

### <a name="can-i-protect-sql-always-on-availability-groups-which-are-spread-across-azure-regions"></a>Posso proteger Grupos de Disponibilidade AlwaysOn do SQL que são distribuídos entre regiões do Azure?

O Cofre dos Serviços de Recuperação do Backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o Cofre dos Serviços de Recuperação. Se tiver um Grupo de Disponibilidade Always On do SQL que abranja várias regiões do Azure, você precisará configurar o backup da região que tem o nó primário. O Backup do Azure será capaz de detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a preferência de backup. Se a preferência de backup não for atendida, os backups falharão e você receberá o alerta de falha.

### <a name="while-i-want-to-protect-most-of-the-databases-in-an-instance-i-would-like-to-exclude-a-few-is-it-possible-to-still-use-the-auto-protection-feature"></a>Enquanto eu quiser proteger a maior parte dos bancos de dados em uma instância, eu gostaria de excluir alguns. É possível ainda usar o recurso de proteção automática?

Não, a [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) aplica-se à instância inteira. Você seletivamente não pode proteger os bancos de dados de uma instância usando a proteção automática.

### <a name="can-i-have-different-policies-for-different-databases-in-an-auto-protected-instance"></a>Posso ter diferentes políticas para diferentes bancos de dados em uma instância protegida automaticamente?

Se você já tiver alguns bancos de dados protegidos em uma instância, eles continuarão a ser protegidos com suas respectivas políticas mesmo depois que você **ATIVAR** a opção de [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm). No entanto, todos os bancos de dados desprotegidos junto com aqueles que você adicionaria futuramente, terão apenas uma única política que você define em **Configurar Backup** depois que o banco de dados for selecionado. Na verdade, ao contrário de outros bancos de dados protegidos, é possível até mesmo alterar a política para um banco de dados em uma instância protegida automaticamente.
Se você quiser fazer isso, a única maneira é desabilitar a proteção automática na instância por enquanto e, em seguida, alterar a política para esse banco de dados. Agora você pode reabilitar a proteção automática para esta instância.

### <a name="if-i-delete-a-database-from-an-auto-protected-instance-will-the-backups-for-that-database-also-stop"></a>Se eu excluir um banco de dados de uma instância protegida automaticamente, os backups do banco de dados também serão interrompidos?

Não, se um banco de dados for descartado de uma instância protegida automaticamente, os backups de banco de dados ainda serão tentados. Isso implica que o banco de dados excluído começa a ser exibido como não íntegro em **Itens de Backup** e ainda é tratado como protegido.

A única maneira de interromper a proteção deste banco de dados é desabilitar a [proteção automática](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) na instância temporariamente e, em seguida, escolher a opção **Parar Backup** nos **Itens de Backup** desse banco de dados. Agora você pode reabilitar a proteção automática para esta instância.

###  <a name="why-cant-i-see-the-newly-added-database-to-an-auto-protected-instance-under-the-protected-items"></a>Por que não consigo ver o banco de dados recém adicionado a uma instância protegida automaticamente em itens protegidos?

Você não poderá ver um banco de dados recém adicionado a uma instância [protegida automaticamente](backup-azure-sql-database.md#auto-protect-sql-server-in-azure-vm) como protegido instantaneamente. Isso ocorre porque a descoberta normalmente é executada a cada 8 horas. No entanto, o usuário pode executar uma descoberta manual usando a opção **Recuperar bancos de dados** para descobrir e proteger novos bancos de dados imediatamente, conforme mostrado na imagem abaixo:

  ![Exibir banco de dados adicionado recentemente](./media/backup-azure-sql-database/view-newly-added-database.png)


## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre o Backup do Azure, consulte a amostra do Azure PowerShell para fazer backup de máquinas virtuais criptografadas.

> [!div class="nextstepaction"]
> [Fazer backup de uma VM criptografada](./scripts/backup-powershell-sample-backup-encrypted-vm.md)
