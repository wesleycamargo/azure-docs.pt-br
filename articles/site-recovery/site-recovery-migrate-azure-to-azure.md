<properties
	pageTitle="Migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra com o Site Recovery | Microsoft Azure"
	description="Use o Azure Site Recovery para migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Site Recovery

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md).

## Visão geral

Este artigo descreve como usar a Recuperação de Site para migrar ou fazer failover de instâncias do Windows em execução no AWS no Azure. Ele resume as etapas descritas por completo em [Replicar máquinas virtuais VMware ou servidores físicos para o Azure](site-recovery-vmware-to-azure-classic.md). O artigo no link é a versão aprimorada mais recente do cenário que replica VMs VMware ou servidores físicos Windows/Linux no Azure. Sugerimos seguir o artigo no link para obter instruções detalhadas sobre cada etapa na implantação.

>[AZURE.NOTE] Para a migração entre regiões, você **não deve mais usar** as instruções deste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

## Introdução

Veja o que você precisa antes de iniciar:

- **Servidor de gerenciamento**: uma VM local executando o Windows Server 2012 R2 que atua como o servidor de gerenciamento. Você instala os componentes de Recuperação de Site (incluindo os servidores de configuração e de processo) neste servidor. Leia mais em [Considerações sobre o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [pré-requisitos locais](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Máquinas virtuais IaaS**: as VMs que você deseja migrar.

## Etapas de implantação.

1. [Crie um cofre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Implantar um servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Depois que você implantou o servidor de gerenciamento, ele valida se pode se comunicar com a VM que você deseja migrar.
4. [Crie um grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Um grupo de proteção contém computadores protegidos que compartilham as mesmas configurações de replicação. Especifique as configurações de replicação para um grupo e elas serão aplicadas a todos os computadores que você adicionar ao grupo.
5. [Instale o Serviço de mobilidade](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Observe que as regras de firewall nas máquinas virtuais IaaS que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço.
6. [Habilitar proteção para máquinas](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Adicione máquinas que você deseja proteger ao grupo de replicação. 
7. Você pode descobrir as máquinas virtuais IaaS que deseja migrar para o Azure usando o endereço IP privado das máquinas virtuais. Localize esse endereço no painel de máquina virtual do Azure.
8. Na guia do grupo de proteção que você criou, clique em **Adicionar Máquinas** > **Máquinas Físicas**.

	![Descoberta de EC2](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Especifique o endereço IP privado da máquina virtual.

	![Descoberta de EC2](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
Depois de adicionar um computador ao grupo, será possível habilitar a proteção, e a replicação inicial será executada de acordo com as configurações do grupo de proteção.

10. [ Execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você poderá executar um failover não planejado de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover não planejado para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
		
## Próximas etapas

Poste comentários ou dúvidas no [fórum do Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0309_2016-->