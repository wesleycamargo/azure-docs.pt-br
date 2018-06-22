---
title: Migrar o novo host e o novo host de um aplicativo local Linux VMs do Azure | Microsoft Docs
description: Saiba como Contoso novo host a um aplicativo do Linux local migrando para máquinas virtuais do Azure.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: conceptual
ms.date: 06/19/2018
ms.author: raynew
ms.openlocfilehash: 8f039884ca0ea46c128078984d6cab6fd29ac9af
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/19/2018
ms.locfileid: "36220543"
---
# <a name="contoso-migration-rehost-an-on-premises-linux-app-to-azure-vms"></a>Migração de Contoso: novo host a um aplicativo local Linux VMs do Azure

Este artigo mostra como a Contoso é nova hospedagem um aplicativo de suporte técnico de serviço baseado em Linux local (**osTicket**), para VMs Azure IaaS.

Este documento é a sétima em uma série de artigos de documento como a empresa fictícia Contoso migra seus recursos locais para a nuvem do Microsoft Azure. A série inclui informações básicas e um conjunto de cenários que ilustra como configurar uma infra-estrutura de migração e executar diferentes tipos de migrações. Cenários de crescem em complexidade e incluiremos artigos adicionais ao longo do tempo.

**Artigo** | **Detalhes** | **Status**
--- | --- | ---
[O artigo 1: Visão geral](contoso-migration-overview.md) | Fornece uma visão geral de estratégia de migração da Contoso, a série de artigos e os aplicativos de exemplo que usamos. | Disponível
[Artigo 2: Implantar uma infraestrutura do Azure](contoso-migration-infrastructure.md) | Descreve como Contoso prepara seu local e a infraestrutura do Azure para a migração. A mesma infraestrutura é usada para todos os cenários de migração da Contoso. | Disponível
[Artigo 3: Avaliar recursos locais](contoso-migration-assessment.md)  | Mostra como a Contoso é executado em uma avaliação de seu aplicativo de SmartHotel de duas camadas do local em execução no VMware. Avaliar a VMs de aplicativo com o [migrar do Azure](migrate-overview.md) service e o banco de dados do SQL Server de aplicativo com o [Assistente de migração de banco de dados do Azure](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017). | Disponível
[Artigo 4: Novo host para as VMs do Azure e uma instância gerenciada do SQL](contoso-migration-rehost-vm-sql-managed-instance.md) | Demonstra como Contoso migra o aplicativo SmartHotel no Azure. Elas migram a VM de front-end do aplicativo usando o [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview)e o banco de dados de aplicativo usando o serviço de [Migração de Banco de Dados do Azure](https://docs.microsoft.com/azure/dms/dms-overview), para migrar para uma Instância Gerenciada do SQL. | Disponível
[Artigo 5: Novo host para máquinas virtuais do Azure](contoso-migration-rehost-vm.md) | Mostra como a Contoso migrar seu aplicativo SmartHotel VMs usando apenas o Site Recovery.
[Artigo 6: Novo host para máquinas virtuais do Azure e grupos de disponibilidade do SQL Server](contoso-migration-rehost-vm-sql-ag.md) | Mostra como a Contoso migra o aplicativo SmartHotel. Eles usam o Site Recovery para migrar as máquinas virtuais do aplicativo e o serviço de Migração de Banco de Dados para migrar o banco de dados de aplicativo para um Grupo de Disponibilidade do SQL Server. | Disponível
Artigo 7: Novo host a um aplicativo do Linux para VMs do Azure (neste artigo) | Mostra como a Contoso migra seus aplicativos do Linux osService usando o Azure Site Recovery.
[Artigo 8: Novo host a um aplicativo do Linux para as VMs do Azure e o Azure MySQL Server](contoso-migration-rehost-linux-vm-mysql.md) | Demonstra como Contoso migra o aplicativo do Linux osService, usando a recuperação de Site para migração de VM e MySQL Workbench para migrar (para uma instância do MySQL Server do Azure. | Disponível

Neste artigo, a Contoso migrará duas camadas **osTicket** aplicativo, em execução no Linux Apache MySQL PHP (LÂMPADA) para o Azure. O aplicativo VMs será migrado usando o serviço do Azure Site Recovery. Se você quiser usar este aplicativo de código-fonte aberto, você pode baixá-lo do [GitHub](https://github.com/osTicket/osTicket).

## <a name="business-drivers"></a>Geradores de negócios

A equipe de liderança de TI trabalhou em conjunto com seus parceiros comerciais para entender o que desejam obter com a migração:

- **Endereço de crescimento comercial**: Contoso está crescendo e, como resultado, há pressão sobre a infra-estrutura e os sistemas locais.
- **Limitar o risco**: O serviço de aplicativo de suporte técnico é essencial para a empresa Contoso. Eles querem movê-lo para o Azure com risco zero.
- **Estender**: não quer alterar o aplicativo agora. Eles simplesmente desejam garantir que ele esteja estável.


## <a name="migration-goals"></a>Metas de migração

A equipe de nuvem Contoso foi fixado para baixo objetivos para a migração, para determinar o melhor método de migração:

- Após a migração, o aplicativo no Azure deve ter os mesmos recursos de desempenho de hoje em seu ambiente de VMWare no local.  O aplicativo permanecerá como crítico na nuvem quanto local. 
- A Contoso não quiser investir nesse aplicativo.  É importante para os negócios, mas em sua forma atual, eles simplesmente desejam movê-lo com segurança para a nuvem.
- A Contoso não quer alterar o modelo de operações para este aplicativo. Eles querem interagir com ele na nuvem da mesma maneira que fazem agora.
- A Contoso não quer alterar a funcionalidade do aplicativo. Somente o local do aplicativo será alterado.
- Ter concluído algumas das migrações de aplicativo do Windows, a Contoso deseja aprender a usar uma infraestrutura baseada em Linux no Azure.

## <a name="proposed-architecture"></a>Arquitetura proposta

Neste cenário:

- O aplicativo está em camadas em duas VMs (OSTICKETWEB e OSTICKETMYSQL).
- As VMs estão localizadas no host VMware ESXi **contosohost1.contoso.com** (versão 6.5).
- O ambiente VMware é gerenciado pelo vCenter Server 6.5 (**vcenter.contoso.com**), em execução em uma VM.
- A Contoso tem um datacenter local (**contoso-datacenter**), com um controlador de domínio local (**contosodc1**).
- Como o aplicativo é uma carga de trabalho de produção, as VMs no Azure residirão no grupo de recursos de produção **ContosoRG**.
- As máquinas virtuais serão migradas para a região principal (Leste dos EUA 2) e colocadas na rede de produção (VNET-PROD-EUS2):
    - Web VM residem na sub-rede front-end (FE-PROD-EUS2).
    - O banco de dados de máquina virtual residem na sub-rede de banco de dados (PROD-DB-EUS2).
- As VMs locais no datacenter Contoso, serão descomissionadas após a migração.

![Arquitetura de cenário](./media/contoso-migration-rehost-linux-vm/architecture.png) 

## <a name="migration-process"></a>Processo de migração

A Contoso migrará da seguinte maneira:

1. Como uma primeira etapa, Contoso configura o Azure e a infraestrutura necessária para implantar a recuperação de Site local.
2. Depois de preparar componentes do Azure e no local, eles configurar e habilitar a replicação para máquinas virtuais.
3. Depois que a replicação está funcionando, eles migrar as máquinas virtuais por failover para o Azure.

![Processo de migração](./media/contoso-migration-rehost-linux-vm/migration-process.png)

### <a name="azure-services"></a>Serviços do Azure

**Serviço** | **Descrição** | **Custo**
--- | --- | ---
[Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/) | O serviço coordena e gerencia a migração e a recuperação de desastre para VMs do Azure e servidores físicos e de VMs locais.  | Durante a replicação para o Azure, são gerados encargos do Armazenamento do Azure.  As VMs do Azure são criadas e incorrem em encargos quando ocorre failover. [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre encargos e preços.

 
## <a name="prerequisites"></a>pré-requisitos

Aqui está o que você (e a Contoso) precisam para executar este cenário.

**Requisitos** | **Detalhes**
--- | ---
**Assinatura do Azure** | Você já deve ter criado uma assinatura durante os artigos anteriores nesta série. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> Se você criar uma conta gratuita, será o administrador da assinatura e poderá executar todas as ações.<br/><br/> Se você usar uma assinatura existente e não for o administrador, será necessário trabalhar com o administrador para receber permissões de Proprietário ou de Colaborador.<br/><br/> Se forem necessárias permissões mais granulares, leia [este artigo](../site-recovery/site-recovery-role-based-linked-access-control.md). 
**Infraestrutura do Azure** | Contoso configurar sua infraestrutura do Azure conforme descrito em [infraestrutura do Azure para migração](contoso-migration-infrastructure.md).<br/><br/> Saiba mais sobre específico [rede](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#network) e [armazenamento](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#storage) requisitos para a recuperação de Site.
**Servidores locais** | O servidor do vCenter local deve estar executando a versão 5.5, 6.0 ou 6.5<br/><br/> Um host ESXi que executa a versão 5.5, 6.0 ou 6.5<br/><br/> Uma ou mais VMs do VMware em execução no host ESXi.
**VMs locais** | [Examine máquinas Linux](https://docs.microsoft.com//azure/site-recovery/vmware-physical-azure-support-matrix#replicated-machines) que têm suporte para migração com a recuperação de Site.<br/><br/> Verifique se tem suporte [sistemas de arquivo e armazenamento Linux](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#linux-file-systemsguest-storage).<br/><br/> As VMs devem atender aos [requisitos do Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements).


## <a name="scenario-steps"></a>Etapas do cenário

Aqui está como o Azure será concluída a migração:

> [!div class="checklist"]
> * **Etapa 1: Preparar o Azure para recuperação de Site**: eles criar uma conta de armazenamento do Azure para armazenar os dados replicados e criará um cofre de serviços de recuperação.
> * **Etapa 2: Preparar VMware no local de recuperação de Site**: eles preparar as contas a serem usadas para a instalação de descoberta e agente VM e se preparar para conectar a máquinas virtuais do Azure após o failover.
> * **Etapa 3: VMs de replicar**: configurar o ambiente de migração de origem e de destino, crie uma política de replicação e iniciar a replicação de máquinas virtuais no armazenamento do Azure.
> * **Etapa 4: Migrar as máquinas virtuais com a recuperação de Site**: eles executar um failover de teste para verificar se tudo está funcionando e, em seguida, execute um failover completo para migrar as máquinas virtuais do Azure.


## <a name="step-1-prepare-azure-for-the-site-recovery-service"></a>Etapa 1: Preparar o Azure para o serviço de recuperação de Site

A Contoso precisa de alguns componentes do Azure para recuperação de Site:

- Failover de uma rede virtual nos quais recursos estão localizados (Contoso usará a rede virtual que já implantados de produção)
- Uma nova conta de armazenamento do Azure para armazenar dados replicados. 
- Um cofre de serviços de recuperação no Azure.

Contoso já criada a rede virtual durante [implantação de infraestrutura do Azure](contoso-migration-infrastructure.md), portanto, precisam apenas para criar uma conta de armazenamento e o cofre.

1. A Contoso cria uma conta de armazenamento do Azure (contosovmsacc20180528) na região Leste dos EUA 2.

    - A conta de armazenamento deve estar na mesma região do cofre de Serviços de Recuperação.
    - Eles está usando uma conta de finalidade geral, com padrão de armazenamento e replicação de LRS.

    ![Armazenamento de recuperação de site](./media/contoso-migration-rehost-linux-vm/asr-storage.png)

2. Com a conta de rede e armazenamento no local, a Contoso crie um cofre (ContosoMigrationVault) e colocá-lo a **ContosoFailoverRG** grupo de recursos na região Leste dos EUA 2 primária.

    ![Cofre dos Serviços de Recuperação](./media/contoso-migration-rehost-linux-vm/asr-vault.png)


**Precisa de mais ajuda?**

[Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/tutorial-prepare-azure) a configuração o Azure para o Site Recovery.


## <a name="step-2-prepare-on-premises-vmware-for-site-recovery"></a>Etapa 2: Preparar VMware local para o Site Recovery

A Contoso prepara a infra-estrutura de VMware no local da seguinte maneira:

- Crie uma conta no vCenter server ou vSphere ESXi host, para automatizar a descoberta VM.
- Crie uma conta que permite que a instalação automática do serviço de mobilidade de máquinas virtuais do VMware que você deseja replicar.
- Prepare VMs locais, para que eles possam se conectar a máquinas virtuais do Azure quando eles são criados após a migração.


### <a name="prepare-an-account-for-automatic-discovery"></a>Preparar uma conta para a descoberta automática

O Site Recovery precisa de acesso aos servidores VMware para:

- Descobrir VMs automaticamente. Pelo menos uma conta somente leitura é necessária.
- Orquestrar a replicação, o failover e o failback. É necessária uma conta que possa executar operações como criação e remoção de discos e ativação de VMs.

A Contoso configura a conta da seguinte maneira:

1. A Contoso cria uma função no nível do vCenter.
2. A Contoso, em seguida, atribua essa função as permissões necessárias.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Preparar uma conta para a instalação do serviço de Mobilidade

O serviço de mobilidade deve ser instalado nas VMs do Linux que Contoso da migração:

- O Site Recovery poderá fazer uma instalação automática por push desse componente quando você habilitar a replicação da VM.
- Para instalação automática por push, é necessário preparar uma conta que o Site Recovery usará para acessar a VM.
- Detalhes de contas são inseridos durante a instalação de replicação. 
- A conta pode ser o domínio ou conta local, com permissões para instalar nas máquinas virtuais.


### <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Preparar para conectar VMs do Azure após o failover

Após o failover no Azure, a Contoso deseja ser capaz de se conectar às VMs replicadas no Azure. Para fazer isso, há algumas coisas que precisam para realizar:

- Para acessar a Internet, eles permitem SSH no Linux local VM antes da migração.  Para Ubuntu isso pode ser feito usando o seguinte comando: **apt Sudo get ssh -y instalar**.
- Depois que eles executam a migração (failover), eles devem verificar **diagnósticos de inicialização** para exibir uma captura de tela da VM.
- Se isso não funcionar, eles devem verificar se a VM está em execução e revisar essas [ dicas de solução de problemas ](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx).


**Precisa de mais ajuda?**

- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-automatic-discovery) criar e atribuir uma função para a descoberta automática.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises#prepare-an-account-for-mobility-service-installation) como criar uma conta para a instalação por push do serviço de Mobilidade.


## <a name="step-3-replicate-the-on-premises-vms"></a>Etapa 3: Replicar máquinas virtuais no local

Antes de eles migrar a máquina virtual da web para o Azure, Contoso configura e habilita a replicação.

### <a name="set-a-protection-goal"></a>Definir um objetivo de proteção

1. No cofre, sob o nome do cofre (ContosoVMVault) definir uma meta de replicação (**Introdução** > **recuperação de Site** > **preparar infraestrutura**.
2. Eles especificam que suas máquinas estão localizados no local, eles são VMs VMware e que deseja replicar no Azure.
    ![Meta de replicação](./media/contoso-migration-rehost-linux-vm/replication-goal.png)

### <a name="confirm-deployment-planning"></a>Confirmar planejamento de implantação

Para continuar, eles confirmam que eles concluiu o planejamento da implantação, selecionando **Sim, tiver feito isso**. Contoso são migrar apenas uma única VM nesse cenário e não é necessário planejamento da implantação.

### <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

A Contoso deve configurar seu ambiente de origem. Para fazer isso, eles baixam um modelo OVF e o utilizam para implantar o servidor de configuração do Site Recovery como uma VM VMware local, altamente disponível. Depois que a configuração do servidor tiver sido finalizada, registre-o no cofre.

O servidor de configuração executa vários componentes:

- O componente de servidor de configuração que coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
- Servidor em processo que age como um gateway de replicação. Ele recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.
- O servidor de processo também instala o Serviço de Mobilidade nas VMs que você deseja replicar e executa a descoberta automática de VMs VMware locais.

A Contoso executar essas etapas, da seguinte maneira:

1. Eles baixam o modelo OVF **preparar a infraestrutura** > **fonte** > **servidor de configuração**.
    
    ![Baixe o OVF](./media/contoso-migration-rehost-linux-vm/add-cs.png)

2. Eles importar o modelo para VMware para criar a VM e implante a VM.

    ![Modelo de OVF](./media/contoso-migration-rehost-linux-vm/vcenter-wizard.png)

3. Ao ligar a máquina virtual pela primeira vez, ela é inicializado com uma experiência de instalação do Windows Server 2016. Eles aceitam o contrato de licença e inserir uma senha de administrador.
4. Depois que a instalação for concluída, entrarem na VM como um administrador. Na primeira entrada, a ferramenta de configuração de recuperação de Site do Azure é executado por padrão.
5. Na ferramenta, eles especificam um nome a ser usado para registrar o servidor de configuração no cofre.
6. A ferramenta verifica se a VM pode se conectar ao Azure. Depois que a conexão for estabelecida, eles entrarão na assinatura do Azure. As credenciais devem ter acesso ao cofre no qual você deseja registrar o servidor de configuração.

    ![Registrar servidor de configuração](./media/contoso-migration-rehost-linux-vm/config-server-register2.png)

7. A ferramenta executa algumas tarefas de configuração e, em seguida, é reinicializada.
8. Eles entram na máquina novamente e o Assistente de Gerenciamento do Servidor de Configuração é iniciado automaticamente.
9. No assistente, eles selecionam o NIC para receber o tráfego de replicação. Essa configuração não pode ser alterada depois de ter sido definida.
10. Eles selecionam a assinatura, o grupo de recursos e o cofre no qual registrar o servidor de configuração.

    ![cofre](./media/contoso-migration-rehost-linux-vm/cswiz1.png) 

11. Eles Baixe e instale o MySQL Server e VMWare PowerCLI. 
12. Após a validação, eles especificar o FQDN ou endereço IP do host de servidor ou vSphere do vCenter. Eles deixam a porta padrão e especifique um nome amigável para o servidor do vCenter.
13. Eles especificam a conta que criaram para descoberta automática e as credenciais que devem ser usadas para instalar automaticamente o serviço de mobilidade.

    ![vCenter](./media/contoso-migration-rehost-linux-vm/cswiz2.png)

14. Após a conclusão do registro, no portal do Azure, a Contoso verifica se o servidor de configuração e o servidor do VMware estão listados no **fonte** página no cofre. Descoberta pode levar 15 minutos ou mais. 
15. Recuperação de site conecta-se a servidores VMware e descobre máquinas virtuais.

### <a name="set-up-the-target"></a>Configurar o destino

Agora, a Contoso configura as configurações de replicação de destino.

1. Em **preparar infraestrutura** > **destino**, eles selecionam as configurações de destino.
2. Recuperação de site verifica se há uma conta de armazenamento do Azure e da rede de destino especificado.

### <a name="create-a-replication-policy"></a>Criar uma política de replicação

Depois de origem e destino são configuradas, a Contoso está pronta para criar uma política de replicação.

1. Em **preparar infraestrutura** > **as configurações de replicação** > **política de replicação** >  **criar e Associar**, eles criam uma política **ContosoMigrationPolicy**.
2. Eles usam as configurações padrão:
    - **Limite de RPO**: Padrão de 60 minutos. Esse valor define a frequência em que são criados os pontos de recuperação. Um alerta será gerado se a replicação contínua exceder esse limite.
    - **Retenção de ponto de recuperação**. Padrão de 24 horas. Esse valor Especifica quanto tempo a janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela.
    - **Frequência de instantâneos consistentes com o aplicativo**. Padrão de uma hora. Esse valor especifica a frequência com a qual os instantâneos consistentes com o aplicativo são criados.
 
        ![Criar política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy.png)

5. A política é associada automaticamente ao servidor de configuração. 

    ![Associar política de replicação](./media/contoso-migration-rehost-linux-vm/replication-policy2.png)

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [configurar a recuperação de desastres para máquinas virtuais do VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.

**Precisa de mais ajuda?**

- Você pode ler uma explicação completa de todas essas etapas em [configurar a recuperação de desastres para máquinas virtuais do VMware no local](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial).
- As instruções detalhadas estão disponíveis para ajudá-lo a [configurar o ambiente de origem](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-source), [implantar o servidor de configuração](https://docs.microsoft.com/azure/site-recovery/vmware-azure-deploy-configuration-server), e [definir configurações de replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-replication).
- [Saiba mais](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux) sobre o agente convidado do Azure para Linux.



### <a name="enable-replication-for-osticketweb"></a>Ativar replicação para o OSTICKETWEB

Agora, a Contoso pode iniciar a replicação de **OSTICKETWEB** VM.

1. Em **replicar aplicativo** > **fonte** > **+ replicar** eles selecionar as configurações de fonte.
2. Eles selecionam que deseja habilitar as máquinas virtuais, selecione as configurações de fonte, incluindo o servidor do vCenter e o servidor de configuração.

    ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication-source.png)

3. Eles especificam as configurações de destino, incluindo o grupo de recursos e redes em que a VM do Azure será localizada após o failover e a conta de armazenamento no qual serão armazenados os dados replicados.

     ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication2.png)

3. Seleciona Contoso **OSTICKETWEB** para replicação. 

    - Neste estágio, a Contoso seleciona somente **OSTICKETWEB** porque a rede virtual e a sub-rede devem ser selecionados e as máquinas virtuais não estão na mesma sub-rede.
    - O Site Recovery instala automaticamente o serviço Mobility quando a replicação está habilitada para a VM.

    ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/enable-replication3.png)

4. Nas propriedades da VM, a Contoso seleciona a conta que é usada pelo servidor de processo para instalar automaticamente o serviço de mobilidade no computador.

     ![Serviço de mobilidade](./media/contoso-migration-rehost-linux-vm/linux-mobility.png)

5. em **as configurações de replicação** > **definir configurações de replicação**, eles verificar que a política de replicação correto é aplicado e selecione **habilitar replicação**.
6.  Eles rastreiam o progresso da replicação em **Trabalhos**. Após o trabalho de **Finalizar Proteção** ser executado, o computador estará pronto para failover.



### <a name="enable-replication-for-osticketmysql"></a>Habilitar a replicação para OSTICKETMYSQL

Agora, a Contoso pode iniciar a replicação **OSTICKETMYSQL**.

1. Em **replicar aplicativo** > **fonte** > **+ replicar** eles selecionar as configurações de origem e destino.
2. Seleciona Contoso **OSTICKETMYSQL** para replicação e seleciona a conta a ser usada para a instalação do serviço de mobilidade.

    ![Habilitar a replicação](./media/contoso-migration-rehost-linux-vm/mysql-enable.png)

3. A Contoso se aplica a mesma política de replicação que foi usada para OSTICKETWEB e habilita a replicação.  

**Precisa de mais ajuda?**

Você pode ler uma explicação completa de todas essas etapas em [habilitar replicação](https://docs.microsoft.com/azure/site-recovery/vmware-azure-enable-replication).


## <a name="step-4-migrate-the-vms"></a>Etapa 4: Migrar as VMs 

Contoso para executar um rápido failover de teste e, em seguida, migrar as máquinas virtuais.

### <a name="run-a-test-failover"></a>Execute um teste de failover

Executar um failover de teste ajuda a garantir que tudo está funcionando conforme o esperado antes da migração. 

1. A Contoso executa um failover de teste para o ponto no tempo mais recente disponível (**Último processado**).
2. Eles selecionam **Desligar o computador antes do início do failover**, para que o Site Recovery tente desligar a VM da fonte antes de disparar o failover. O failover continuará mesmo o desligamento falhar. 
3. O failover de Teste executa: 
    - Uma verificação de pré-requisitos será executada para conferir se todas as condições exigidas para a migração foram cumpridas.
    - O failover processa os dados para que uma VM do Azure possa ser criada. Se o ponto de recuperação mais recente for selecionado, um ponto de recuperação é criado dos dados.
    - Uma VM do Azure é criada usando os dados processados na etapa anterior.
3. Após o failover ser concluído, a réplica da VM do Azure aparecerá no portal do Azure. Eles Verifique se a VM é o tamanho apropriado, o que ele está conectado à rede certa, e se ele está em execução. 
4. Depois de verificar, eles limpar o failover e registram e salvar todas as observações.

### <a name="create-and-customize-a-recovery-plan"></a>Criar e personalizar um plano de recuperação

 Depois de verificar se o failover de teste funcionou como esperado, a Contoso criar um plano de recuperação para a migração. 

- Um plano de recuperação Especifica a ordem na qual o failover ocorre, como VMs do Azure serão ativadas no Azure.
- Desde que desejam migrar um aplicativo de duas camadas, eles personalizará o plano de recuperação para que os dados de VM (SQLVM) é iniciado antes do front-end (WEBVM).


1. Em **planos de recuperação (recuperação de Site)** > **+ plano de recuperação**, criam um plano e adicione as máquinas virtuais a ele.

    ![Plano de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-plan.png)

2. Depois de criar o plano, eles selecionarão-lo para personalização (**planos de recuperação** > **OsTicketMigrationPlan** > **personalizar**.
3.  Remover **OSTICKETWEB** de **grupo 1: Iniciar**.  Isso garante que a primeira ação de início afeta **OSTICKETMYSQL** somente.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group1.png)

4.  Em **+ grupo** > **itens protegidos de adicionar**, adicionam **OSTICKETWEB** para **grupo 2: Iniciar**.  A Contoso deve esses dois grupos diferentes.

    ![Grupo de recuperação](./media/contoso-migration-rehost-linux-vm/recovery-group2.png)


### <a name="migrate-the-vms"></a>Migrar as VMs


A Contoso está pronto para executar um failover no plano de recuperação, para migrar as máquinas virtuais.

1. Eles selecionam o plano > **Failover**.
2.  Eles selecionam o failover para o último ponto de recuperação e especificar que a recuperação de Site deve tentar desligar a VM do local antes de acionar o failover. Eles podem acompanhar o progresso do failover na página**Trabalhos**.

    ![Failover](./media/contoso-migration-rehost-vm/failover1.png)

3. Durante o failover, o servidor vCenter emite comandos para parar as duas máquinas virtuais em execução no host ESXi.

    ![Failover](./media/contoso-migration-rehost-linux-vm/vcenter-failover.png)

3. Depois do failover, a Contoso verifica se a VM do Azure aparece conforme o esperado no portal do Azure.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover2.png)  

3. Depois de verificar se a VM no Azure, eles concluírem a migração para concluir o processo de migração para cada VM. Isso interrompe a replicação da VM e interrompe o faturamento do Site Recovery para a VM.

    ![Failover](./media/contoso-migration-rehost-linux-vm/failover3.png)


### <a name="connect-the-vm-to-the-database"></a>Conecte a VM ao banco de dados

Como a etapa final no processo de migração, a Contoso atualizar a cadeia de caracteres de conexão do aplicativo para apontar para o banco de dados de aplicativo com o **OSTICKETMYSQL** VM. 

1. Eles fazer uma conexão SSH para a **OSTICKETWEB** VM usando Putty ou outro cliente SSH. A VM é particular para que se conectar usando o endereço IP privado.

    ![Conectar ao banco de dados](./media/contoso-migration-rehost-linux-vm/db-connect.png)  

    ![Conectar ao banco de dados](./media/contoso-migration-rehost-linux-vm/db-connect2.png)  

2. Eles precisam para se certificar de que o **OSTICKETWEB** VM pode se comunicar com o **OSTICKETMYSQL** VM. Atualmente, a configuração é codificado com o endereço IP local 172.16.0.43.

    **Antes da atualização**
    
    ![Atualizar IP](./media/contoso-migration-rehost-linux-vm/update-ip1.png)  

    **Após a atualização**
    
    ![Atualizar IP](./media/contoso-migration-rehost-linux-vm/update-ip2.png) 
    
3. Reinicie o serviço com **systemctl reiniciar do apache2**.

    ![Reiniciar](./media/contoso-migration-rehost-linux-vm/restart.png) 

4. Por fim, eles atualizar os registros DNS para **OSTICKETWEB** e **OSTICKETMYSQL**, em um dos controladores de domínio Contoso.

    ![Atualizar o DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

    ![Atualizar o DNS](./media/contoso-migration-rehost-linux-vm-mysql/update-dns.png) 

**Precisa de mais ajuda?**

- [Saiba mais](https://docs.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure) sobre a execução de failovers de teste. 
- [Saiba](https://docs.microsoft.com/azure/site-recovery/site-recovery-create-recovery-plans) como criar um plano de recuperação.
- [Saiba mais sobre](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) failover para o Azure.

## <a name="clean-up-after-migration"></a>Limpeza após a migração

Migração concluída, as camadas de aplicativo osTicket agora estão em execução em VMs do Azure.

Agora, a Contoso precisa fazer uma limpeza:  

- Eles remover as VMs no local do inventário do vCenter.
- Eles remover as VMs de local de trabalhos de backup locais.
- Eles atualize sua documentação interna para mostrar o novo local e endereços IP para OSTICKETWEB e OSTICKETMYSQL.
- Eles examinar todos os recursos que interagem com as máquinas virtuais e atualizar as configurações relevantes ou documentação para refletir a nova configuração.
- Contoso usado o serviço de migração do Azure com o mapeamento de dependência para avaliar as máquinas virtuais para migração. Eles devem remover o Microsoft Monitoring Agent e o agente de dependência que instalados para essa finalidade, da VM.

## <a name="review-the-deployment"></a>Revisar a implantação

Com o aplicativo em execução, a Contoso precisa totalmente colocar e proteger sua infraestrutura de novo.

### <a name="security"></a>Segurança

A equipe de segurança da Contoso examine o OSTICKETWEB e OSTICKETMYSQLVMs para determinar os problemas de segurança.

- Eles revisar os grupos de segurança de rede (NSGs) para as VMs controlar o acesso. Os NSGs são usados para garantir que somente o tráfego permitido para o aplicativo pode passar.
- Eles também planejam protegendo os dados nos discos de VM usando a criptografia de disco e Azure KeyVault.

[Leia mais](https://docs.microsoft.com/azure/security/azure-security-best-practices-vms#vm-authentication-and-access-control) sobre as práticas recomendadas de segurança para máquinas virtuais.

### <a name="backups"></a>Backups

A Contoso fazer backup dos dados nas máquinas virtuais usando o serviço de Backup do Azure. [Saiba mais](https://docs.microsoft.com/azure/backup/backup-introduction-to-azure-backup?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="licensing-and-cost-optimization"></a>Licenciamento e otimização de custo

- Após a implantação de recursos, a Contoso atribui marcas do Azure conforme definido durante a [implantação de infraestrutura do Azure](contoso-migration-infrastructure.md#set-up-tagging).
- A Contoso não tem nenhum problema de licenciamento com os servidores do Ubuntu.
- A Contoso permitirá o gerenciamento de custos do Azure licenciado por Cloudyn, uma subsidiária Microsoft. É uma solução de gerenciamento de custo de multi-nuvem que ajuda você a usar e gerenciar o Azure e outros recursos de nuvem.  [Saiba mais](https://docs.microsoft.com/azure/cost-management/overview) sobre o Gerenciamento de Custos do Azure. 


## <a name="next-steps"></a>Próximas etapas

Neste artigo, mostramos como Contoso migrados um local de suporte técnico do serviço de aplicativo em camadas em duas VMs do Linux para VMs de IaaS do Azure, usando o Azure Site Recovery.

O próximo artigo na série, mostraremos como Contoso migrar o mesmo serviço de aplicativo de suporte técnico para o Azure. Neste momento, a Contoso usa recuperação de Site para migrar o front-end VM para o aplicativo e migrar o banco de dados de aplicativo usando o backup e restaurar o banco de dados do Azure para MySQL, usando a ferramenta de workbench MySQL. [Introdução](contoso-migration-rehost-linux-vm-mysql.md).
