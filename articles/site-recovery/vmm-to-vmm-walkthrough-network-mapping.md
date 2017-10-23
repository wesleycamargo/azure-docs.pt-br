---
title: "Mapear redes para a replicação de VMs do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como mapear redes ao replicar máquinas virtuais do Hyper-V para um site secundário do VMM com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 461b7c1c-ef61-4005-aeec-2324e727a3d0
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 606e2d3d0e31b9d80200105e812f9d7bbbcf939c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-map-networks-for-hyper-v-vm-replication-to-a-secondary-site"></a>Etapa 7: Mapear redes para replicação de VM do Hyper-V para um site secundário


Depois de definir as [configurações de origem e destino](vmm-to-vmm-walkthrough-source-target.md) para a replicação de máquinas de virtuais (VMs) do Hyper-V para um site secundário do System Center Virtual Machine Manager (VMM), use este artigo para configurar o mapeamento de rede para a replicação de máquinas virtuais (VMs) do Hyper-V para um site secundário, usando o [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Antes de começar

- Saiba mais sobre [mapeamento de rede](vmm-to-vmm-walkthrough-network.md#network-mapping-overview) antes de iniciar.
- Verifique se as máquinas virtuais nos servidores VMM estão conectadas a uma rede de VM.

## <a name="configure-network-mapping"></a>Configurar o mapeamento de rede

1. Em **Mapeamento de Rede** > **Mapeamentos de rede**, clique em **+Mapeamento de Rede**.
2. Na guia **Adicionar mapeamento de rede**, selecione os servidores VMM de origem e destino. As redes da VM associadas aos servidores VMM são recuperadas.
3. Em **Rede de origem**, selecione a rede que você deseja usar na lista de redes VM associadas ao servidor primário do VMM.
4. Em **Rede de destino**, selecione a rede que você deseja usar no servidor VMM secundário. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/vmm-to-vmm-walkthrough-network-mapping/network-mapping2.png)

Veja o que acontece quando começa o mapeamento de rede:

* As máquinas virtuais de réplica existentes que corresponderem à rede VM de origem serão conectadas às redes da VM de destino.
* As máquinas virtuais que estiverem conectadas à rede VM de origem serão conectadas à rede mapeada de destino após a replicação.
* Se você modificar um mapeamento existente com uma nova rede, as máquinas virtuais de réplica serão conectadas usando as novas configurações.
* Se a rede de destino tiver várias sub-redes, e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover. Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 8: Configurar uma política de replicação](vmm-to-vmm-walkthrough-replication.md).