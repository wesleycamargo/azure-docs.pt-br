---
title: Fazer backup de bancos de dados SQL Server para o Azure | Microsoft Docs
description: Este tutorial explica como fazer backup do SQL Server para o Azure. O artigo também explica a recuperação do SQL Server.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: raynew
ms.openlocfilehash: d99a3d23959cfdd9bd068fbde3a882eb1bc9b4ae
ms.sourcegitcommit: 04716e13cc2ab69da57d61819da6cd5508f8c422
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2019
ms.locfileid: "58847292"
---
# <a name="about-sql-server-backup-in-azure-vms"></a>Sobre o Backup do SQL Server nas VMs do Azure

Os bancos de dados SQL Server são cargas de trabalho críticas que exigem um baixo RPO (objetivo de ponto de recuperação) e retenção de longo prazo. Faça backup de bancos de dados do SQL Server em execução em VMs do Azure usando o [Backup do Azure](backup-overview.md).

## <a name="backup-process"></a>Processo de backup

Essa solução aproveita as APIs nativas do SQL para fazer backups dos bancos de dados SQL.

* Depois que você especificar a VM do SQL Server que você deseja proteger e consultar para bancos de dados no o departamento de TI, serviço de Backup do Microsoft Azure instalará uma extensão de backup de carga de trabalho na VM pelo nome da extensão `AzureBackupWindowsWorkload` .
* Essa extensão consiste em um coordenador e um plugin do SQL. Ao passo que o coordenador é responsável por disparar fluxos de trabalho para várias operações, como configurar o backup, backup e restauração, o plugin é responsável por fluxo de dados real.
* Para poder descobrir bancos de dados nesta VM, o Backup do Microsoft Azure cria a conta `NT SERVICE\AzureWLBackupPluginSvc`. Essa conta é usada para backup e restauração e exige permissões de sysadmin do SQL. O Backup do Microsoft Azure aproveita a conta  `NT AUTHORITY\SYSTEM`  para descoberta/consulta de banco de dados e, portanto, essa conta precisa ser um logon público no SQL. Se você não criou a VM do SQL Server no Azure Marketplace, talvez você receba um erro  **UserErrorSQLNoSysadminMembership**. Se isso ocorrer,  [siga essas instruções](backup-azure-sql-database.md).
* Depois que o gatilho configurar a proteção nos bancos de dados selecionados, o serviço de backup configura o coordenador com as agendas de backup e outros detalhes da política, que aumenta os caches localmente na VM 
* No horário agendado, o coordenador se comunica com o plugin e ele começa a transmissão dos dados de backup do SQL Server usando o VDI.  
* O plugin envia os dados diretamente para o cofre de serviços de recuperação, eliminando a necessidade de um local de preparo. Os dados são criptografados e armazenados pelo serviço de Backup do Microsoft Azure em contas de armazenamento.
* Quando a transferência de dados for concluída, o coordenador confirma com o serviço de backup.

  ![Arquitetura de backup SQL](./media/backup-azure-sql-database/backup-sql-overview.png)

## <a name="before-you-start"></a>Antes de começar

Antes de começar, verifique o seguinte:

1. Garanta que você tenha uma Instância do SQL Server em execução no Azure. Você pode [criar uma Instância do SQL Server rapidamente](../virtual-machines/windows/sql/quickstart-sql-vm-create-portal.md) no marketplace.
2. Examine os [recurso consideração](#feature-consideration-and-limitations) e [suporte a cenários](#scenario-support).
3. [Examine as perguntas comuns](faq-backup-sql-server.md) sobre esse cenário.

## <a name="scenario-support"></a>Suporte ao cenário

**Suporte** | **Detalhes**
--- | ---
**Implantações com suporte** | Há suporte para VMs do Azure no Marketplace do SQL e VMs que não são do Marketplace (do SQL Server instaladas manualmente).
**Áreas geográficas com suporte** | Sudeste da Austrália (ASE), Leste da Austrália (AE) <br> Sul do Brasil (BRS)<br> Canadá Central (CNC), Leste do Canadá (CE)<br> Sudeste Asiático (SEA), Ásia Oriental (EA) <br> Leste dos EUA (EUS), Leste dos EUA 2 (EUS2), Centro-Oeste dos EUA (WCUS), Oeste dos EUA (WUS); Oeste dos EUA 2 (WUS 2) Centro-Norte dos EUA (NCUS) EUA Central (CUS) Centro-Sul dos EUA (SCUS) <br> Índia Central (INC), Sul da Índia (INS) <br> Oeste do Japão (JPE), Leste do Japão (JPW) <br> Coreia Central (KRC), Sul da Coreia (KRS) <br> Norte da Europa (NE), Oeste da Europa <br> Sul do Reino Unido (UKS), Oeste do Reino Unido (UKW)
**Sistemas operacionais com suporte** | Windows Server 2016, Windows Server 2012 R2 e Windows Server 2012<br/><br/> Não há suporte para Linux no momento.
**Versões do SQL Server com suporte** | SQL Server 2017, SQL Server 2016, SQL Server 2014 e SQL Server 2012.<br/><br/> Enterprise, Standard, Web, Developer e Express.
**Versões do .NET com suporte** | .NET Framework 4.5.2 e superior instalado na VM

## <a name="feature-consideration-and-limitations"></a>Considerações e limitações de recurso

- O Backup do SQL Server pode ser configurado no portal do Azure ou **PowerShell**. Não oferecemos CLI de suporte.
- VM que executa o SQL Server exige conectividade com a Internet para acessar os endereços IP públicos do Azure.
- Não há suporte para o SQL Server **FCI (Instância de Cluster de Failover)** e o SQL Server Always On na Instância de Cluster de Failover.
- Não há suporte para operações de backup e restauração para bancos de dados de espelho e instantâneos do banco de dados.
- Usar mais de uma solução de backup para fazer backup de sua instância do SQL Server ou SQL Always no grupo de disponibilidade pode levar à falha de backup. Evite fazer isso.
- Fazer backup de dois nós de um grupo de disponibilidade individualmente com soluções iguais ou diferentes, também poderá levar à falha de backup. O Backup do Azure pode detectar e proteger todos os nós que estão na mesma região que o cofre. Se o SQL Server Always no grupo de disponibilidade abranger várias regiões do Azure, configure o backup da região que tem o nó primário. O Backup do Microsoft Azure poderá detectar e proteger todos os bancos de dados no grupo de disponibilidade, de acordo com a sua preferência de backup.  
- Backup do Azure dá suporte a apenas tipos de backup Completo e Somente Cópia para banco de dados **Somente leitura**
- Bancos de dados com um grande número de arquivos não podem ser protegidos. O número máximo de arquivos com suporte não é **~1000**.  
- É possível fazer backup de até **~2000** bancos de dados do SQL Server em um cofre. Caso você tenha um número maior de bancos de dados, poderá criar vários cofres.
- Você pode configurar o backup de até **50** bancos de dados de uma vez; essa restrição ajuda a otimizar cargas de backup.
- Damos suporte a bancos de dados de até **2TB** em tamanho; para tamanhos maiores do que isso, poderá haver problemas de desempenho.
- Para ter uma ideia de sobre quantos bancos de dados podem ser protegidos por servidor, precisamos considerar fatores como a largura de banda, tamanho da VM, frequência de backup, tamanho do banco de dados, etc. Estamos trabalhando em um planejador que ajudará a calcular esses número que você possui. Publicaremos em breve.
- No caso de grupos de disponibilidade, os backups são feitos de nós diferentes com base em alguns fatores. O comportamento de backup para um grupo de disponibilidade está resumido abaixo.

### <a name="backup-behavior-in-case-of-always-on-availability-groups"></a>O comportamento do backup no caso de Always em grupos de disponibilidade

Dependendo da preferência de backup e os tipos de backups (completo/diferencial/log/somente cópia completos), os backups serão feitos em um nó específico (primário/secundário).

- **Preferência de Backup: Primário**

**Tipo de backup** | **Node**
    --- | ---
    Completo | Primário
    Diferencial | Primário
    Registro |  Primário
    Completo somente de cópia |  Primário

- **Preferência de Backup: Apenas secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

- **Preferência de Backup: Secundário**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

- **Nenhuma preferência de Backup**

**Tipo de backup** | **Node**
--- | ---
Completo | Primário
Diferencial | Primário
Registro |  Secundário
Completo somente de cópia |  Secundário

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

- [Saiba mais sobre](backup-sql-server-database-azure-vms.md) como realizar backup de bancos de dados do SQL Server.
- [Saiba mais sobre](restore-sql-database-azure-vm.md) como restaurar bancos de dados do SQL Server copiados em backup.
- [Saiba mais sobre](manage-monitor-sql-database-backup.md) como gerenciar bancos de dados do SQL Server copiados em backup.
