---
title: "Criar um cofre para replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como criar um cofre ao replicar VMs do Hyper-V para um site de VMM secundário do System Center com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ff65dbfb-cb26-410e-ab48-76971625db08
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 28cfcf12b2e369f96664c163c0b6f2aa8a6ddcb9
ms.contentlocale: pt-br
ms.lasthandoff: 08/01/2017

---
# <a name="step-5-create-a-vault-for-hyper-v-replication-to-a-secondary-site"></a>Etapa 5: Criar um cofre para replicação do Hyper-V para um site secundário

Depois de preparar [servidores System Center Virtual Machine Manager (VMM) e hosts/clusters Hyper-V](vmm-to-vmm-walkthrough-vmm-hyper-v.md) para replicação do Hyper-V em um site secundário usando o [Azure Site Recovery](site-recovery-overview.md), você poderá criar um cofre dos Serviços de Recuperação e selecionar o cenário de replicação.

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]


## <a name="choose-a-protection-goal"></a>Escolher um objetivo de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Site Recovery** > **Etapa 1: Preparar a Infraestrutura** > **Meta de proteção**.
2. Selecione **Para site de recuperação** e selecione **Sim, com o Hyper-V**.
3. Selecione **Sim** para indicar que você está usando o VMM para gerenciar os hosts do Hyper-V.
4. Selecione **Sim** se você tiver um servidor VMM secundário. Se você estiver implantando a replicação entre nuvens em um único servidor VMM, clique em **Não**. Em seguida, clique em **OK**.

    ![Escolher metas](./media/vmm-to-vmm-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 6: Configurar a origem e o destino de replicação](vmm-to-vmm-walkthrough-source-target.md).
