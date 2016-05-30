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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuais do Windows no AWS (Amazon Web Services) para o Azure Site Recovery

## Visão geral

Este artigo descreve como usar a Recuperação de Site para migrar instâncias do Windows em execução no AWS para o Azure. Antes de começar, observe que:

- No momento, é possível apenas fazer migrações. Isso significa que é possível executar failover do AWS para o Azure, mas não é possível executar failback delas novamente.
- Este artigo resume e usa muitas das etapas descritas por completo em [Replicar máquinas virtuais ou servidores físicos VMware no Azure](site-recovery-vmware-to-azure-classic.md), que fornece as instruções avançadas mais recentes para configurar a replicação. Sugerimos que você siga este artigo para obter instruções detalhadas, conforme é feita a migração.
- **Não se deve mais usar** as instruções contidas neste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

Poste quaisquer comentários ou perguntas ao final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Pré-requisitos

Veja o que você precisa antes de iniciar:

- **Servidor de gerenciamento**: uma VM que executa o Windows Server 2012 R2 e que atua como o servidor de gerenciamento. Você instala os componentes de Recuperação de Site (incluindo os servidores de configuração e de processo) neste servidor. Leia mais em [considerações sobre o servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#management-server-considerations) e [pré-requisitos de origem](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Instâncias de VM EC2**: As instâncias que você deseja migrar e, em seguida, proteger.

## Etapas de implantação.

1. [Criar um cofre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Implante um servidor de gerenciamento](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Depois que você implantou o servidor de gerenciamento, ele valida se pode se comunicar com as instâncias de EC2 que você deseja migrar.
4. [Crie um grupo de proteção](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Um grupo de proteção contém computadores protegidos que compartilham as mesmas configurações de replicação. Especifique as configurações de replicação para um grupo e elas serão aplicadas a todos os computadores que você adicionar ao grupo. 
5. [Instale o serviço de Mobilidade](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Regras de firewall nas instâncias de EC2 que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. O grupo de segurança para instâncias de EC2 deve ter as seguintes regras:

	![regras de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilite proteção para os computadores](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Adicione máquinas que você deseja proteger ao grupo de replicação.

	![habilitar proteção](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. Você pode descobrir as instâncias de EC2 que você deseja migrar para o Azure usando o endereço IP privado da instância, que pode ser obtido a partir do console EC2. No grupo de proteção, você pode adicionar o endereço IP privado de cada instância.

	![habilitar proteção](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Depois de adicionar um computador ao grupo, será possível habilitar a proteção e a replicação inicial será executada de acordo com as configurações do grupo de proteção.

8. [Execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você pode executar um failover não planejado do AWS para o Azure para cada VM. Opcionalmente, é possível criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.
		
## Próximas etapas

Saiba mais sobre outros cenários de replicação em [O que é o Azure Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0518_2016-->