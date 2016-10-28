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
	ms.date="08/22/2016"
	ms.author="raynew"/>

#  Migrar máquinas virtuais do Windows no AWS (Amazon Web Services) para o Azure Site Recovery

## Visão geral

Bem-vindo ao Azure Site Recovery. Use este artigo para migrar instâncias do Windows em execução no AWS para o Azure com o Site Recovery. Antes de começar, observe que:

- O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. As etapas básicas para migração são as mesmas se você estiver configurando o Site Recovery no Gerenciador de Recursos ou no Clássico. No entanto, as capturas de tela e instruções da interface do usuário neste artigo são relevantes para o Portal do Azure.
- **No momento, você pode apenas migrar do AWS para o Azure. Você pode executar o failover de VMs do AWS para o Azure, mas não pode executar o failback delas novamente. Não há nenhuma replicação em andamento.**
- As instruções de migração neste artigo baseiam-se nas instruções para replicar um computador físico para o Azure. Elas incluem links para as etapas em [Replicar VMs VMware ou computadores físicos para o Azure](site-recovery-vmware-to-azure.md), que descreve como replicar um servidor físico no Portal do Azure.
- Se você estiver configurando o Site Recovery no portal clássico, siga as instruções detalhadas [neste artigo](site-recovery-vmware-to-azure-classic.md). **Não se deve mais usar** as instruções contidas neste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Pré-requisitos

Aqui está o que você precisa para essa implantação

- **Servidor de configuração**: uma VM local que executa o Windows Server 2012 R2 e que atua como o servidor de configuração. Você instala os outros componentes do Site Recovery (incluindo o servidor de processo e o servidor de destino mestre) nessa VM também. Leia mais em [arquitetura de cenário](site-recovery-vmware-to-azure.md#scenario-architecture) e [pré-requisitos do servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Instâncias de VM EC2**: As instâncias com Windows que você deseja migrar.

## Etapas de implantação.

Esta seção descreve as etapas de implantação no novo Portal do Azure. Se você precisar dessas etapas de implantação para o Site Recovery no portal clássico, consulte [este artigo](site-recovery-vmware-to-azure-classic.md).

1. [Crie um cofre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implante um servidor de configuração](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Depois que você implantou o servidor de configuração, valide se ele pode se comunicar com a VM que você deseja migrar.
4. [Defina as configurações de replicação](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Crie uma política de replicação e atribua ao servidor de configuração.
5. [Instale o serviço de Mobilidade](site-recovery-vmware-to-azure.md#step-6-replication-application). Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Regras de firewall nas instâncias de EC2 que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. O grupo de segurança para instâncias de EC2 deve ter as seguintes regras:

	![regras de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Habilite a replicação](site-recovery-vmware-to-azure.md#enable-replication). Habilite a replicação para as VMs que você deseja migrar. Você pode descobrir as instâncias de EC2 usando os endereços IP privados, que podem ser obtidos no console EC2.
7. [ Execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você poderá executar um failover não planejado do AWS para o Azure para cada VM. Opcionalmente, é possível criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## Próximas etapas

Saiba mais sobre outros cenários de replicação em [O que é o Azure Site Recovery?](site-recovery-overview.md)

<!---HONumber=AcomDC_0824_2016-->