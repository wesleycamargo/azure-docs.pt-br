---
title: "Este tutorial mostra como migrar VMs locais e servidores físicos para o Azure com o Site Recovery | Microsoft Docs"
description: "Este artigo descreve como migrar VMs locais e servidores físicos para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Migrar para o Azure com o Site Recovery

Leia este artigo para saber mais sobre como usar o serviço [Azure Site Recovery](site-recovery-overview.md) para migrar máquinas virtuais locais (VMs) e servidores físicos, para máquinas virtuais do Azure.

## <a name="before-you-start"></a>Antes de começar

Assista a este vídeo para uma visão geral rápida das etapas necessárias para migrar para o Azure.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>O que queremos dizer por migração?

Você pode implantar o Site Recovery para a replicação de VMs locais e servidores físicos para o Azure, e para migrá-los.

- Quando você replica, você configura as máquinas locais para serem replicadas regularmente no Azure. Em seguida, quando ocorrer uma interrupção, você faz o failover das máquinas do site local para o Azure e acessa-as a partir dele. Quando o site local estiver disponível novamente, você executar o failback a partir do Azure.
- Quando você usa o Site Recovery para migração, você replica máquinas locais para o Azure. Em seguida, você faz o fail over delas do seu site local para o Azure e conclui o processo de migração. Não há nenhum failback envolvido.  

## <a name="what-can-site-recovery-migrate"></a>O que o Site Recovery pode migrar?

Você pode:

- **Migrar do local**: Migrar cargas de trabalho em VMs do Hyper-V locais, VMs VMware e servidores físicos para o Azure. Após a migração, as cargas de trabalho em execução nas máquinas locais serão executadas em VMs do Azure. 
- **Migrar dentro do Azure**: Migrar VMs do Azure entre regiões do Azure. 
- **Migrar AWS**: Migrar instâncias do Windows AWS para VMs do IaaS do Azure. 

## <a name="migrate-from-on-premises-to-azure"></a>Migrar do local para o Azure

Para migrar VMs Hyper-V locais, VMs VMware e servidores físicos, você segue quase as mesmas etapas que você seguiria para a replicação normal. 


## <a name="migrate-between-azure-regions"></a>Migrar entre regiões do Azure

Para migrar VMs do Azure entre regiões, siga quase as mesmas etapas que você seguiria para a migração completa.

1. Você [habilita a replicação](azure-to-azure-tutorial-enable-replication.md)) para as máquinas que deseja migrar.
2. Você [executa um failover de teste rápido](azure-to-azure-tutorial-dr-drill.md) para verificar se tudo está funcionando.
3. Em seguida, você [executa um failover não planejado](azure-to-azure-tutorial-failover-failback.md) com a opção **Migração completa**.
4. Depois de concluir a migração, você pode [configurar a replicação para recuperação de desastres](site-recovery-azure-to-azure-after-migration.md), a partir da região do Azure para a qual você migrou, para uma região secundária.



## <a name="migrate-aws-to-azure"></a>Migrar AWS para o Azure

Você pode migrar instâncias do AWS para VMs do Azure.
- Nesse cenário, apenas a migração tem suporte. Em outras palavras, você pode replicar as instâncias do AWS e realizar failover no Azure, mas você não pode realizar failback.
- Instâncias do AWS são tratadas da mesma forma como os servidores físicos para fins de migração. Você configura um cofre de serviços de recuperação, implanta um servidor de configuração no local para gerenciar a replicação, adiciona-o ao cofre e especifica as configurações de replicação.
- Você pode habilitar a replicação para os computadores que deseja migrar e executar um failover de teste rápido. Em seguida, execute um failover não planejado com a opção **Migração Completa**.






## <a name="next-steps"></a>Próximas etapas

- [Migrar máquinas locais para o Azure](tutorial-migrate-on-premises-to-azure.md)
- [Migrar máquinas virtuais de uma região do Azure para outra](site-recovery-migrate-azure-to-azure.md)
- [Migrar AWS para o Azure](tutorial-migrate-aws-to-azure.md)
