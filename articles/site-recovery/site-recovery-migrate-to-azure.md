---
title: Migrar para o Azure com o Site Recovery | Microsoft Docs
description: "Este artigo fornece uma visão geral da migração de VMs e servidores físicos para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f82634af931a1e9a9646c5631ebd0e5923a0adcc
ms.openlocfilehash: cbb6de4587871c40c9d4e97c9fb2a88eab4945a6


---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrar para o Azure com a Recuperação de Site?

Leia este artigo para obter uma visão geral do uso do serviço Azure Site Recovery para a migração de máquinas virtuais e servidores físicos.

As organizações precisam de uma estratégia de BCDR que determine como os aplicativos, as cargas de trabalho e os dados permanecerão em execução e disponíveis durante o tempo de inatividade planejado e não planejado, e como recuperarão as condições normais de trabalho assim que possível. Sua estratégia de BCDR devem manter os dados comerciais seguros e passíveis de recuperação e garantir que as cargas de trabalho permaneçam continuamente disponíveis mediante um desastre.

A Recuperação de Site é um serviço do Azure que colabora com sua estratégia de BCDR por meio da coordenação da replicação de servidores físicos e máquinas virtuais locais na nuvem (Azure) ou em um datacenter secundário. Quando ocorrem paralisações em seu local primário, você realiza o failover em um local secundário a fim de manter os aplicativos e cargas de trabalho disponíveis. Quando o local primário retoma as operações normais, você realiza o failback. Saiba mais em [O que é Recuperação de Site?](site-recovery-overview.md)

Este artigo descreve a implantação no [portal do Azure](https://portal.azure.com). O [portal clássico do Azure](https://manage.windowsazure.com/) pode ser usado para manter os cofres de Recuperação de Site existentes, mas não é possível criar novos cofres.

Poste comentários na parte inferior deste artigo. Faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer por migração?

Você pode implantar a Recuperação de Site para replicação completa de VMs locais e servidores físicos, para o Azure ou para um site secundário. Você replica máquinas, realiza o failover delas do site primário quando ocorrem paralisações e realiza o failback delas para o site primário ao ocorrer a recuperação. Além da replicação completa, você pode usar o Site Recovery para migrar VMs e servidores físicos para o Azure, para que os usuários possam acessar a carga de trabalho de máquina de VMs do Azure. A migração envolve a replicação e o failover do site primário para o Azure. No entanto, diferentemente da replicação completa, isso não inclui um mecanismo de failback.

## <a name="what-can-site-recovery-migrate"></a>O que o Site Recovery pode migrar?

Você pode:

- Migre cargas de trabalho em execução em VMs do Hyper-V locais, VMs VMware e servidores físicos para execução em VMs do Azure. Você também pode fazer a replicação completa e o failback nesse cenário.
- Migre [VMs IaaS do Azure](site-recovery-migrate-azure-to-azure.md) entre regiões do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.
- Migre [instâncias do Windows AWS](site-recovery-migrate-aws-to-azure.md) para VMs do IaaS do Azure. Atualmente, apenas a migração tem suporte nesse cenário, o que significa que não há suporte para failback.

## <a name="migrate-on-premises-vms-and-physical-servers"></a>Migrar servidores físicos e VMs locais

Para migrar VMs Hyper-V locais, VMs VMware e servidores físicos, você segue quase as mesmas etapas usadas para a replicação normal. Você configura um cofre de serviços de recuperação, configura os servidores de gerenciamento (dependendo do que deseja migrar), adiciona-os ao cofre e especifica as configurações de replicação. Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido para garantir que tudo funcione adequadamente.

Após verificar se o ambiente de replicação está funcionando, você usa um failover planejado ou não, dependendo do [que tem suporte](site-recovery-failover.md#failover-and-failback) para seu cenário. Para a migração, você não precisa confirmar um failover nem excluir nada. Em vez disso, você seleciona a opção **Migração Completa** para cada máquina que deseja migrar. A ação **Concluir Migração** conclui o processo de migração, remove a replicação da máquina e interrompe a cobrança de Recuperação de Site para a máquina.

## <a name="migrate-between-azure-regions"></a>Migrar entre regiões do Azure

Você pode migrar VMs do Azure entre regiões usando o Site Recovery. Nesse cenário, apenas a migração tem suporte. Em outras palavras, você pode replicar as VMs do Azure e realizar failover para outra região, mas não pode realizar failback. Nesse cenário, você configura um cofre de serviços de recuperação, implanta um servidor de configuração no local para gerenciar a replicação, adiciona-o ao cofre e especifica as configurações de replicação. Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido. Em seguida, execute um failover não planejado com a opção **Migração Completa**.

## <a name="migrate-aws-to-azure"></a>Migrar AWS para o Azure

Você pode migrar instâncias do AWS para VMs do Azure. Nesse cenário, apenas a migração tem suporte. Em outras palavras, você pode replicar as instâncias do AWS e realizar failover no Azure, mas você não pode realizar failback. Instâncias do AWS são tratadas da mesma forma como os servidores físicos para fins de migração. Você configura um cofre de serviços de recuperação, implanta um servidor de configuração no local para gerenciar a replicação, adiciona-o ao cofre e especifica as configurações de replicação. Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido. Em seguida, execute um failover não planejado com a opção **Migração Completa**.




## <a name="next-steps"></a>Próximas etapas

- [Migrar VMs do VMware para o Azure](site-recovery-vmware-to-azure.md)
- [Migrar servidores físicos para o Azure](site-recovery-vmware-to-azure.md)
- [Migrar VMs Hyper-V em nuvens VMM para o Azure](site-recovery-vmm-to-azure.md)
- [Migrar VMs do Hyper-V sem VMM para o Azure](site-recovery-hyper-v-site-to-azure.md)
- [Migrar VMs do Azure entre regiões do Azure](site-recovery-migrate-azure-to-azure.md)
- [Migrar instâncias do AWS para o Azure](site-recovery-migrate-aws-to-azure.md)



<!--HONumber=Jan17_HO4-->


