<properties
	pageTitle="Migrar máquinas virtuais do Windows do Amazon Web Services para o Azure com o Site Recovery | Microsoft Azure"
	description="Este artigo descreve como migrar máquinas virtuais do Windows em execução nos serviços Web da Amazon (AWA) para o Azure usando o Azure Site Recovery."
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
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuais do Windows no AWS (Amazon Web Services) para o Azure Site Recovery

O Azure Site Recovery contribui para sua estratégia de BCDR (continuidade de negócios e recuperação de desastre) administrando a replicação, o failover e a recuperação de máquinas virtuais e servidores físicos. As máquinas podem ser replicadas no Azure ou em um datacenter local secundário. Para uma breve visão geral, leia [O que é o Azure Site Recovery?](site-recovery-overview.md)


## Visão geral

Este artigo descreve como usar a Recuperação de Site para migrar ou fazer failover de instâncias do Windows em execução no AWS no Azure. Ele resume as etapas que estão descritas por completo em [Replicar máquinas virtuais VMware ou servidores físicos no Azure](site-recovery-vmware-to-azure-classic.md). O artigo no link é a versão aprimorada mais recente do cenário que replica VMs VMware ou servidores físicos Windows/Linux no Azure. Sugerimos que você siga o artigo no link para obter instruções detalhadas sobre cada etapa na implantação.

>[AZURE.NOTE] Para a migração de máquinas virtuais do Windows em AWS, você deve **não usar mais** as instruções deste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

## Introdução

Veja o que você precisa antes de iniciar:

- **Servidor de gerenciamento**: uma VM local executando o Windows Server 2012 R2 que atua como o servidor de gerenciamento. Você instala os componentes de Recuperação de Site (incluindo os servidores de configuração e de processo) neste servidor. Leia mais em [Considerações sobre o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [pré-requisitos locais](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Instâncias de VM EC2**: As instâncias que você deseja migrar e, em seguida, proteger.

## Etapas de implantação.

1. [Criar um cofre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Implantar um servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Depois que você implantou o servidor de gerenciamento, ele valida se pode se comunicar com as instâncias de EC2 que você deseja migrar.
4. [Crie um grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Um grupo de proteção contém computadores protegidos que compartilham as mesmas configurações de replicação. Especifique as configurações de replicação para um grupo e elas serão aplicadas a todos os computadores que você adiciona ao grupo. 
5. [Instalar o serviço Mobilidade](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Regras de firewall nas instâncias de EC2 que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. O grupo de segurança para instâncias de EC2 deve ter as seguintes regras:

	![regras de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilitar proteção para máquinas](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Adicione máquinas que você deseja proteger ao grupo de replicação.

	![habilitar proteção](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. Você pode descobrir as instâncias de EC2 que você deseja migrar para o Azure usando o endereço IP privado da instância, que pode ser obtido a partir do console EC2. No grupo de proteção, você pode adicionar o endereço IP privado de cada instância.

	![habilitar proteção](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Depois de adicionar um computador ao grupo, será possível habilitar a proteção e a replicação inicial será executada de acordo com as configurações do grupo de proteção.

8. [Execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você pode executar um failover não planejado do AWS para o Azure para cada VM. Opcionalmente, é possível criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
		
## Próximas etapas

Publicar comentários ou perguntas no [Fórum de Recuperação de Site](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0309_2016-->