---
title: Migrar VMs do AWS para o Azure | Microsoft Docs
description: "Este artigo descreve como migrar máquinas virtuais em execução no AWS (Amazon Web Services) para o Azure usando o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 02/12/2017
ms.author: bsiva
translationtype: Human Translation
ms.sourcegitcommit: 5031f64ffcd34b6287a3ecd87dd027c2bc7c716f
ms.openlocfilehash: 9d0d0ba4ca5966b39ce62ea8296d48e5930c9782


---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Migrar máquinas virtuais no AWS (Amazon Web Services) para Azure com o Azure Site Recovery

Este artigo descreve como migrar instâncias Windows do AWS para máquinas virtuais do Azure com o serviço do [Azure Site Recovery](site-recovery-overview.md).

A migração é efetivamente um failover do AWS para o Azure. Não é possível fazer failback das máquinas e não há replicação contínua. Este artigo descreve as etapas para migração no Portal do Azure, que se baseiam nas instruções para [replicar um computador físico no Azure](site-recovery-vmware-to-azure.md).

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Sistemas operacionais com suporte

O Site Recovery pode ser usado para migrar instâncias EC2 que estejam executando qualquer um dos seguintes sistemas operacionais:

- Windows (somente&64; bits)
    - Windows Server 2008 R2 SP1+ (drivers Citrix PV ou somente drivers AWS PV. **Não há suporte para instâncias executando drivers RedHat PV**) Windows Server 2012 Windows Server 2012 R2
- Linux (somente&64; bits)
    - Red Hat Enterprise Linux 6.7 (apenas instâncias de HVM virtualizadas)

## <a name="prerequisites"></a>Pré-requisitos

Aqui está o que você precisa para essa implantação

* **Servidor de configuração**: uma VM local que executa o Windows Server 2012 R2 e que esteja implantada como o servidor de configuração. Por padrão, os outros componentes do Site Recovery (servidor de processo e servidor de destino mestre) são instalados quando você implanta o servidor de configuração. [Saiba mais](site-recovery-components.md#replicate-vmware-vmsphysical-servers-to-azure)
* **Instâncias EC2**: as instâncias de máquinas virtuais EC2 que você quer migrar.

## <a name="deployment-steps"></a>Etapas de implantação.

1. Criar um cofre
2. Implante o servidor de configuração
3. Depois que você implantou o servidor de configuração, valide se ele pode se comunicar com a VM que você deseja migrar.
4. Defina as configurações da replicação.
5. Instalar o serviço de Mobilidade. Cada VM que você deseja proteger requer o serviço de Mobilidade instalado. Esse serviço envia dados ao servidor de processo. O serviço de Mobilidade pode ser instalado manualmente ou enviado por push e instalado automaticamente pelo servidor de processo quando a proteção para a VM é habilitada. Regras de firewall nas instâncias de EC2 que você deseja migrar devem ser configuradas para permitir a instalação por push desse serviço. O grupo de segurança para instâncias de EC2 deve ter as seguintes regras:

    ![regras de firewall](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)
6. Habilite a replicação. Habilite a replicação para as VMs que você deseja migrar. Você pode descobrir as instâncias de EC2 usando os endereços IP privados, que podem ser obtidos no console EC2.
7. Execute um failover não planejado. Depois que a replicação inicial for concluída, você poderá executar um failover não planejado do AWS para o Azure para cada VM. Opcionalmente, é possível criar um plano de recuperação e executar um failover não planejado, para migrar várias máquinas virtuais do AWS para o Azure. [Leia mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

Obtenha instruções detalhadas das [etapas de implantação](site-recovery-vmware-to-azure.md) e para executar um [failover não planejado](site-recovery-failover.md#run-an-unplanned-failover).



<!--HONumber=Feb17_HO2-->


