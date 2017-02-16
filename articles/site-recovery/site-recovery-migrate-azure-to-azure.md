---
title: "Migrar VMs IaaS do Azure entre regiões do Azure | Microsoft Docs"
description: "Use o Azure Site Recovery para migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 3b606aa6dc3b84ed80cd3cc5452bbe1da6c79a8b
ms.openlocfilehash: 3db1ec50b357c5bf6da51aaaf0f04ccb65d1bb05


---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery
## <a name="overview"></a>Visão geral
Bem-vindo ao Azure Site Recovery! Use este artigo se você deseja migrar VMs do Azure entre regiões do Azure. Antes de começar, observe que:

* O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. As etapas básicas para migração são as mesmas se você estiver configurando o Site Recovery no Resource Manager ou no clássico. No entanto, as instruções de interface do usuário e capturas de tela neste artigo são relevantes para o Portal do Azure.
* **No momento, você pode apenas migrar de uma região para outra. Você pode executar o failover de VMs de uma região do Azure para outra, mas não pode executar o failback delas novamente.**
* As instruções de migração neste artigo baseiam-se nas instruções para replicar um computador físico para o Azure. Elas incluem links para as etapas em [Replicar VMs VMware ou computadores físicos para o Azure](site-recovery-vmware-to-azure.md), que descreve como replicar um servidor físico no Portal do Azure.
* Se você estiver configurando o Site Recovery no portal clássico, siga as instruções detalhadas [neste artigo](site-recovery-vmware-to-azure-classic.md). **Não se deve mais usar** as instruções contidas neste [artigo herdado](site-recovery-vmware-to-azure-classic-legacy.md).

Publique quaisquer comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
Aqui está o que você precisa para essa implantação:

* **Servidor de configuração**: uma VM local que executa o Windows Server 2012 R2 e que atua como o servidor de configuração. Você instala os outros componentes do Site Recovery (incluindo o servidor de processo e o servidor de destino mestre) nessa VM também. Leia mais em [arquitetura de cenário](site-recovery-vmware-to-azure.md#scenario-architecture) e [pré-requisitos do servidor de configuração](site-recovery-vmware-to-azure.md#configuration-server-or-additional-process-server-prerequisites).
* **Máquinas virtuais IaaS**: as VMs que você deseja migrar. Você pode migrar essas VMs tratando-as como computadores físicos.

## <a name="deployment-steps"></a>Etapas de implantação.
Esta seção descreve as etapas de implantação no novo Portal do Azure. Se você precisar dessas etapas de implantação para o Site Recovery no portal clássico, consulte [este artigo](site-recovery-vmware-to-azure-classic.md).

1. [Crie um cofre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Implante um servidor de configuração](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Depois que você implantou o servidor de configuração, verifique se ele pode se comunicar com a VM que você deseja migrar.
4. [Defina as configurações de replicação](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Crie uma política de replicação e atribua ao servidor de configuração.
5. [Instalar o serviço de Mobilidade](site-recovery-vmware-to-azure.md#step-6-replicate-applications). Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. As regras de firewall nas VMs que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço.
6. [Habilite a replicação](site-recovery-vmware-to-azure.md#enable-replication). Habilite a replicação para as VMs que você deseja migrar. Você pode descobrir as máquinas virtuais IaaS que deseja migrar para o Azure usando o endereço IP privado das máquinas virtuais. Localize esse endereço no painel de máquina virtual do Azure. Ao habilitar a replicação, você define o tipo de computador para as VMs como computadores físicos.
7. [ Execute um failover não planejado](site-recovery-failover.md#run-an-unplanned-failover). Depois que a replicação inicial for concluída, você poderá executar um failover não planejado de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover não planejado para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outros cenários de replicação em [O que é o Azure Site Recovery?](site-recovery-overview.md)



<!--HONumber=Jan17_HO5-->


