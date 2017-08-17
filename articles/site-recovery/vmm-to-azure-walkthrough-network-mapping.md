---
title: Configurar mapeamento de rede para replicar VMs do Hyper-V em nuvens do VMM para o Azure com o Azure Site Recovery | Microsoft Docs
description: Descreve como configurar o mapeamento de rede ao replicar VMs do Hyper-V em nuvens do VMM para o Azure com o Azure Site Recovery
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ed6f73d8baea5af0d2aa5f0ae885f305911ccc82
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="step-9-configure-network-mapping-for-hyper-v-replication-with-vmm-to-azure"></a>Etapa 9: Configurar o mapeamento de rede para replicação do Hyper-V (com VMM) para o Azure

Depois de definir as [configurações de replicação de origem e destino](vmm-to-azure-walkthrough-source-target.md), use este artigo para configurar o mapeamento de rede para mapear entre redes de VMs do VMM local e redes do Azure.

Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar

- Saiba mais sobre [mapeamento de rede](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- [Preparar VMM para mapeamento de rede](vmm-to-azure-walkthrough-network.md#prepare-vmm-for-network-mapping). 
- Verifique se as máquinas virtuais no servidor do VMM estão conectadas a uma rede VM e se você criou pelo menos uma rede virtual do Azure. Várias redes VM podem ser mapeadas para uma única rede do Azure.

## <a name="configure-mapping"></a>Configurar o mapeamento

Configure o mapeamento da seguinte maneira:

1. Em **Infraestrutura do Site Recovery** > **Mapeamentos de rede** > **Mapeamento de Rede**, clique no ícone **+Mapeamento de Rede**.

    ![Mapeamento de rede](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping1.png)
2. Em **Adicionar mapeamento de rede**, selecione o servidor do VMM de origem e **Azure** como o destino.
3. Verifique a assinatura e o modelo de implantação após o failover.
4. Em **Rede de origem**, selecione a rede de VMs locais de origem que você deseja mapear na lista associada ao servidor do VMM.
5. Em **Rede de destino**, selecione a rede do Azure na qual as VMs do Azure de réplica estarão localizadas quando criadas. Em seguida, clique em **OK**.

    ![Mapeamento de rede](./media/vmm-to-azure-walkthrough-network-mapping/network-mapping2.png)

Veja o que acontece quando começa o mapeamento de rede:

* As máquinas virtuais existentes na rede VM de origem estão conectadas à rede de destino quando o mapeamento começa. As VMs que estiverem conectadas à rede VM de origem serão conectadas à rede do Azure mapeada quando a replicação ocorrer.
* Se você modificar um mapeamento de rede existente, as máquinas virtuais de réplica serão conectadas usando as novas configurações.
* Se a rede de destino tiver várias sub-redes e uma dessas sub-redes tiver o mesmo nome que a sub-rede em que a máquina virtual de origem está localizada, a máquina virtual de réplica será conectada à sub-rede de destino após o failover.
* Se não houver uma sub-rede de destino com um nome correspondente, a máquina virtual será conectada à primeira sub-rede na rede.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 10: Criar uma política de replicação](vmm-to-azure-walkthrough-replication.md)

