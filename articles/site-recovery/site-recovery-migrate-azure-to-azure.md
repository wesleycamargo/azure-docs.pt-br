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
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Site Recovery


## Visão geral

O Azure Site Recovery colabora com sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em vários cenários de implantação. Para obter uma lista completa de cenários de implantação, consulte a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

Este artigo descreve como usar o Site Recovery para migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra. O artigo usa a maioria das etapas descritas em [Configurar a proteção entre máquinas virtuais VMware ou servidores físicos locais e o Azure](site-recovery-vmware-to-azure-classic-legacy.md). Sugerimos que você leia o artigo para obter instruções detalhadas sobre cada etapa na implantação.

## Introdução

Veja o que você precisa antes de iniciar:

- **Servidor de configuração**: uma máquina virtual do Azure que atua como o servidor de configuração. O servidor de configuração coordena a comunicação entre computadores locais e servidores do Azure.
- **Servidor de destino mestre**: uma máquina virtual do Azure, que atua como o servidor de destino mestre. Esse servidor recebe e retém os dados replicados dos computadores protegidos.
- **Um servidos de processo**: uma máquina virtual que executa o Windows Server 2012 R2. Máquinas virtuais protegidas enviam dados de replicação para esse servidor.
- **Máquinas virtuais IaaS**: as VMs que você deseja migrar.

- Leia mais sobre esses componentes em [Do que eu preciso?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)
- Você também deve ler as diretrizes em [planejamento de capacidade](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning) e se certificar de que todos os [pré-requisitos de implantação](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) estejam em vigor antes de iniciar.

## Etapas de implantação.

1. [Criar um cofre](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. [Implantar um servidor de configuração](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como uma VM do Azure.
3. [Implantar o servidor de destino mestre](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como uma VM do Azure.
4. [Implantar um servidor de processo](site-recovery-vmware-to-azure.md-classic-legacy#step-4-deploy-the-on-premises-process-server). Observe que:

	- Você deve implantar o servidor de processo na mesma rede virtual/sub-rede que as VMs IaaS que deseja migrar. ![VMs IaaS](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- Depois que tiver implantado o servidor de processo, verifique se ele pode se comunicar com as máquinas virtuais que você migrará.
	- Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. As regras de firewall nas máquinas virtuais IaaS que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. 

	- Depois que o servidor de processo é implantado e registrado no servidor de configuração no cofre do Site Recovery, ele deve aparecer na guia **Servidores de Configuração** no console do Site Recovery. Observe que isso pode levar até 15 minutos.
	
		![servidor de processo](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [Instale as últimas atualizações](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates). Verifique se todos os servidores de componente que você instalou estão atualizados.
6. [Crie um grupo de proteção](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group). Para começar a proteger as máquinas virtuais migradas usando o Site Recovery, é necessário configurar um grupo de proteção. Especifique as configurações de replicação para um grupo e elas serão aplicadas a todos os computadores que você adiciona a esse grupo. 
7. [Configure as máquinas virtuais](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect). Você precisará ter o serviço de Mobilidade instalado em cada VM (automática ou manualmente).
8. [Etapa 8: habilite a proteção para máquinas virtuais](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection). Habilite a proteção para VMs adicionando-as a um grupo de proteção. Observe que:

	- Você pode descobrir as máquinas virtuais IaaS que deseja migrar para o Azure usando o endereço IP privado das máquinas virtuais. Localize esse endereço no painel de máquina virtual do Azure.
	-  Na guia do grupo de proteção que você criou, clique em Adicionar Máquinas > Máquinas Físicas ![Descoberta de EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- Especifique o endereço IP privado da máquina virtual.
		- ![Descoberta de EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- A proteção será habilitada e a replicação inicial será executada de acordo com as configurações de replicação inicial para o grupo de proteção.
9. [Etapa 9: execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você poderá executar um failover não planejado de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover não planejado para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
		
## Próximas etapas

Poste comentários ou dúvidas no [fórum do Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0121_2016-->