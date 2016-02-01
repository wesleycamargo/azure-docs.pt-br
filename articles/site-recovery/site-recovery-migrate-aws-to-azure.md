<properties
	pageTitle="Migrar máquinas virtuais do Windows do Amazon Web Services para o Azure com o Site Recovery | Microsoft Azure"
	description="Use o Azure Site Recovery para migrar máquinas virtuais do Windows em execução nos serviços de Web da Amazon (AWA) para o Azure."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Migrar máquinas virtuais do Windows no AWS (Amazon Web Services) para o Azure Site Recovery


## Visão geral

O Azure Site Recovery colabora para a estratégia de BCDR (continuidade de negócios e recuperação de desastre) gerenciando replicação, failover e recuperação de máquinas virtuais em várias implantações. Para obter uma lista completa de cenários de implantação, confira a [Visão geral do Azure Site Recovery](site-recovery-overview.md).

Este artigo descreve como usar a Recuperação de Site para migrar ou fazer failover de instâncias do Windows em execução no AWS no Azure. O artigo usa a maioria das etapas descritas em [Configurar a proteção entre máquinas virtuais VMware ou servidores físicos locais e o Azure](site-recovery-vmware-to-azure-classic-legacy.md). Sugerimos que você leia o artigo para obter instruções detalhadas sobre cada etapa na implantação.

## Introdução

Veja o que você precisa antes de iniciar:

- **Servidor de configuração**: uma máquina virtual do Azure que atua como o servidor de configuração. O servidor de configuração coordena a comunicação entre computadores locais e servidores do Azure.
- **Servidor de destino mestre**: uma máquina virtual do Azure, que atua como o servidor de destino mestre. Esse servidor recebe e retém os dados replicados dos computadores protegidos.
- **Um servidos de processo**: uma máquina virtual que executa o Windows Server 2012 R2. Máquinas virtuais protegidas enviam dados de replicação para esse servidor.
- **Instâncias de VM EC2**: As instâncias que você deseja migrar e, em seguida, proteger.

- Leia mais sobre esses componentes em [o que eu preciso?](site-recovery-vmware-to-azure-classic-legacy.md#what-do-i-need)
- Você também deve ler as diretrizes em [planejamento de capacidade](site-recovery-vmware-to-azure-classic-legacy.md#capacity-planning) e certificar-se de que todos os [pré-requisitos de implantação](site-recovery-vmware-to-azure-classic-legacy.md#before-you-start) estão em vigor antes de iniciar.

## Etapas de implantação.

1. [Criar um cofre](site-recovery-vmware-to-azure-classic-legacy.md#step-1-create-a-vault)
2. [Implantar um servidor de configuração](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como uma VM do Azure.
3. [Implantar o servidor de destino mestre](site-recovery-vmware-to-azure-classic-legacy.md#step-2-deploy-a-configuration-server) como uma VM do Azure.
4. [Implantar um servidor de processo](site-recovery-vmware-to-azure-classic-legacy.md#step-4-deploy-the-on-premises-process-server). Observe que:

	- Você deve implantar o servidor de processo na mesma sub-rede/Nuvem Privada Virtual Amazon que suas instâncias de EC2. ![Instâncias EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- Depois que você implantou o servidor de processo, ele valida se pode se comunicar com as instâncias de EC2 que você deseja migrar.
	- Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Regras de firewall nas instâncias de EC2 que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. O grupo de segurança para instâncias de EC2 deve ter as seguintes regras:

		![regras de firewall](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- Depois que o servidor do processo é implantado e registrado com o servidor de configuração no cofre de Recuperação de Site, ele deve aparecer sob a guia **Configuration Servers** no console de Recuperação de Site. Isso pode levar até 15 minutos.
	
		![servidor de processo](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [Instale as últimas atualizações](site-recovery-vmware-to-azure-classic-legacy.md#step-5-install-latest-updates). Verifique se todos os servidores de componente que você instalou estão atualizados.
6. [Crie um grupo de proteção](site-recovery-vmware-to-azure-classic-legacy.md#step-7-create-a-protection-group). Para começar a proteger as instâncias migradas usando a Recuperação de Site, é necessário configurar um grupo de proteção. Especifique configurações de replicação para um grupo e elas serão aplicadas a todas as instâncias que você adicionar ao grupo. 
7. [Configure máquinas virtuais](site-recovery-vmware-to-azure-classic-legacy.md#step-8-set-up-machines-you-want-to-protect). Você precisará instalar o serviço de Mobilidade em cada instância (automática ou manualmente).
8. [Habilite a proteção para máquinas virtuais](site-recovery-vmware-to-azure-classic-legacy.md#step-9-enable-protection). Habilite a proteção para instâncias adicionando-as a um grupo de proteção. Observe que:

	- Você pode descobrir as instâncias de EC2 que você deseja migrar para o Azure usando o endereço IP privado da instância, que pode ser obtido a partir do console EC2.
	-  Na guia para o grupo de proteção que você criou, clique em Adicionar Máquinas > Máquinas Físicas ![Descoberta de EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- Especifique o endereço IP privado da instância.
		- ![Descoberta de EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- Será possível habilitar a proteção, e a replicação inicial será executada de acordo com as configurações de replicação inicial para o grupo de proteção
9. [Execute um failover não planejado](site-recovery-failover-classic-legacy.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você pode executar um failover não planejado do AWS para o Azure. Opcionalmente, é possível criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
		
## Próximas etapas

Publicar comentários ou perguntas no [Fórum de Recuperação de Site](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0121_2016-->