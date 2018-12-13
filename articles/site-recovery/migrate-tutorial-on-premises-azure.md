---
title: Migrar máquinas locais para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar máquinas locais para o Azure, usando o Azure Site Recovery.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 11/27/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 1df09a885d6c636ff6bd4bcbec03d27ff7b44ff9
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52836977"
---
# <a name="migrate-on-premises-machines-to-azure"></a>Migrar máquinas locais para o Azure

Além de usar o serviço [Azure Site Recovery](site-recovery-overview.md) para gerenciar e coordenar a recuperação de desastres de máquinas locais e VMs do Azure para fins de continuidade de negócios e recuperação de desastres (BCDR), você também pode usar o Site Recovery para gerenciar a migração de computadores locais para o Azure.


Este tutorial mostra como migrar VMs locais e servidores físicos para o Azure. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Selecione uma meta de replicação
> * Configure o ambiente de origem e de destino
> * Configurar uma política de replicação
> * Habilitar a replicação
> * Execute uma migração de teste para verificar se tudo está funcionando como esperado
> * Executar um failover avulso para o Azure

Este é o terceiro tutorial de uma série. Este tutorial presume que você já tenha concluído as tarefas nos tutoriais anteriores:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. Preparar servidores [VMware](vmware-azure-tutorial-prepare-on-premises.md) ou [Hyper-V](hyper-v-prepare-on-premises-tutorial.md) locais.

Antes de começar, é aconselhável examinar as arquiteturas do [VMware](vmware-azure-architecture.md) ou [Hyper-V](hyper-v-azure-architecture.md) quanto à recuperação de desastre.


## <a name="prerequisites"></a>Pré-requisitos

Não há suporte para dispositivos exportados por drivers paravirtualizados.


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

1. Entre no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **Criar um recurso** > **Monitoramento e Gerenciamento** > **Backup e Site Recovery**.
3. Em **Nome**, especifique o nome amigável **ContosoVMVault**. Se você tiver mais de uma assinatura, selecione uma delas.
4. Crie um grupo de recursos **ContosoRG**.
5. Especifique uma região do Azure. Para verificar as regiões com suporte, confira a disponibilidade geográfica nos [Detalhes dos Preços de Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para acessar rapidamente o cofre no painel, clique em **Fixar no painel** e em **Criar**.

   ![Novo cofre](./media/migrate-tutorial-on-premises-azure/onprem-to-azure-vault.png)

O novo cofre é adicionado ao **Painel** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.



## <a name="select-a-replication-goal"></a>Selecione uma meta de replicação

Selecione o que você deseja replicar e para onde deseja replicar.
1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Objetivo de proteção**, selecione o que você deseja migrar.
    - **VMware**: selecione **Para o Azure** > **Sim, com o VMWare vSphere Hypervisor**.
    - **Computador físico**: selecione **Para o Azure** > **Não virtualizados/outros**.
    - **Hyper-V**: selecione **Para o Azure** > **Sim, com o Hyper-V**. Se as VMs do Hyper-V são gerenciadas pelo VMM, selecione **Sim**.


## <a name="set-up-the-source-environment"></a>Configurar o ambiente de origem

- [Configure](vmware-azure-tutorial.md#set-up-the-source-environment) o ambiente de origem das VMs VMware.
- [Configure](physical-azure-disaster-recovery.md#set-up-the-source-environment) o ambiente de origem para os servidores físicos.
- [Configure](hyper-v-azure-tutorial.md#set-up-the-source-environment) o ambiente de origem para VMs do Hyper-V.

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de origem

Selecione e verifique os recursos de destino.

1. Clique em **Preparar infraestrutura** > **Destino** e selecione a assinatura do Azure que você deseja usar.
2. Especifique o modelo de implantação do Gerenciador de Recursos.
3. A Recuperação de Site verifica se você tem uma ou mais contas de armazenamento e redes do Azure compatíveis.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

- [Configure uma política de replicação](vmware-azure-tutorial.md#create-a-replication-policy) para VMs VMware.
- [Configure uma política de replicação](physical-azure-disaster-recovery.md#create-a-replication-policy) para servidores físicos.
- [Configure uma política de replicação](hyper-v-azure-tutorial.md#set-up-a-replication-policy) para VMs do Hyper-V.


## <a name="enable-replication"></a>Habilitar a replicação

- [Habilite a replicação](vmware-azure-tutorial.md#enable-replication) para VMs VMware.
- [Habilite a replicação](physical-azure-disaster-recovery.md#enable-replication) para servidores físicos.
- Habilite a replicação para VMs Hyper-V [com](hyper-v-vmm-azure-tutorial.md#enable-replication) ou [sem VMM](hyper-v-azure-tutorial.md#enable-replication).


## <a name="run-a-test-migration"></a>Execute um teste de migração

Execute um [failover de teste](tutorial-dr-drill-azure.md) para o Azure, para verificar se tudo está funcionando conforme o esperado.


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute um failover para as máquinas que você deseja migrar.

1. Em **Configurações** > **Itens replicados** clique no computador > **Failover**.
2. Em **Failover**, selecione um **Ponto de Recuperação** para executar o failover. Selecione o ponto de recuperação mais recente.
3. A configuração de chave de criptografia não é relevante para esse cenário.
4. Selecione **Desligar o computador antes do início do failover**. O Site Recovery tentará desligar máquinas virtuais antes de disparar o failover. O failover continuará mesmo o desligamento falhar. Você pode acompanhar o progresso do failover na página **Trabalhos** .
5. Verifique se a VM do Azure aparece no Azure, conforme o esperado.
6. Em **Itens replicados**, clique com o botão direito do mouse em VM > **Concluir Migração**. Isso faz o seguinte:

    - Conclui o processo de migração, interrompe a replicação da VM do AWS e interrompe a cobrança do Site Recovery para a VM.
    - Esta etapa limpa os dados de replicação. Ela não exclui as VMs migradas.

    ![Migração completa](./media/migrate-tutorial-on-premises-azure/complete-migration.png)


> [!WARNING]
> **Não cancelar um failover em andamento**: a replicação da VM é interrompida antes do início do failover. Se você cancelar um failover em andamento, o failover será interrompido, mas a VM não será replicada novamente.

Em alguns cenários, o failover requer um processamento adicional que leva cerca de oito a dez minutos para ser concluído. É possível observar um tempo de failover de teste mais longo para servidores físicos, máquinas VMware Linux, VMs VMware que não têm o serviço DHCP habilitado e VMs VMware que não têm os seguintes drivers de inicialização: storvsc, vmbus, storflt, intelide, atapi.

## <a name="after-migration"></a>Após a migração

Depois que as máquinas são migradas para o Azure, há uma série de etapas que devem ser concluídas.

Algumas etapas podem ser automatizadas como parte do processo de migração usando a funcionalidade dos scripts de automação internos nos [planos de recuperação]( https://docs.microsoft.com/azure/site-recovery/site-recovery-runbook-automation)   


### <a name="post-migration-steps-in-azure"></a>Etapas pós-migração no Azure

- Execute todos os ajustes no aplicativo após a migração, como atualizar as cadeias de conexão de banco de dados e as configurações do servidor Web. 
- Execute o aplicativo final e o teste de aceitação da migração no aplicativo migrado que está sendo executado no Azure.
- O [agente de VM do Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows) gerencia a interação entre uma VM do Azure e o controlador de malha do Azure. É necessário para alguns serviços do Azure, como o Backup do Azure, Site Recovery e Segurança do Azure.
    - Se você estiver migrando máquinas VMware e servidores físicos, o instalador do Serviço de Mobilidade instalará o agente de VM do Azure disponível nos computadores Windows. Em VMs do Linux, é recomendável instalar o agente após o failover. a
    - Se você estiver migrando VMs do Azure para uma região secundária, o agente de VM do Azure precisará ser provisionado na VM antes da migração.
    - Se você estiver migrando VMs Hyper-V para o Azure, instale o agente de VM do Azure na VM do Azure após a migração.
- Remova manualmente todos os provedores/agentes do Site Recovery da VM. Se você migrar VMs VMware ou servidores físicos, [desinstale o Serviço de Mobilidade][vmware-azure-install-mobility-service.md#uninstall-mobility-service-on-a-windows-server-computer] da VM do Azure.
- Para aumentar a resiliência:
    - Proteja os dados fazendo backup das VMs do Azure por meio do serviço Backup do Azure. [Saiba mais]( https://docs.microsoft.com/azure/backup/quick-backup-vm-portal).
    - Mantenha as cargas de trabalho em execução e continuamente disponíveis ao replicar as VMs do Azure em uma região secundária com o Site Recovery. [Saiba mais](azure-to-azure-quickstart.md).
- Para aumentar a segurança:
    - Bloqueie e limite o acesso ao tráfego de entrada com a Central de Segurança do Azure [Administração just-in-time]( https://docs.microsoft.com/azure/security-center/security-center-just-in-time)
    - Restrinja o tráfego de rede a pontos de extremidade com os [Grupos de Segurança de Rede](https://docs.microsoft.com/azure/virtual-network/security-overview).
    - Implante o [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-overview) para manter os discos em segurança e proteger os dados contra roubo e acesso não autorizado.
    - Leia mais sobre [como proteger recursos IaaS]( https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/ ) e acesse a [Central de Segurança do Azure](https://azure.microsoft.com/services/security-center/ ).
- Para monitoramento e gerenciamento:
    - Considere implantar o [Gerenciamento de Custos do Azure](https://docs.microsoft.com/azure/cost-management/overview) para monitorar o uso de recursos e os gastos.

### <a name="post-migration-steps-on-premises"></a>Etapas locais após a migração

- Mova o tráfego do aplicativo para o aplicativo em execução na instância migrada da VM do Azure.
- Remova as VMs locais do inventário local de VMs.
- Remova as VMs locais dos backups locais.
- Atualize todas as documentações internas para mostrar o novo local e o endereço IP das VMs do Azure.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você migrou máquinas virtuais locais para máquinas virtuais do Azure. Agora, é possível [configurar a recuperação de desastre](azure-to-azure-replicate-after-migration.md) para as VMs do Azure em uma região secundária do Azure.

  
