---
title: "Habilitar a replicação do Hyper-V para um site secundário com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como habilitar a replicação para a replicação de máquinas virtuais do Hyper-V para um site secundário do System Center VMM com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d8782d14-9fef-4396-8912-ff945efc851b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 6673d192dbc18bfc955d9e7e3c55893512511ffb
ms.contentlocale: pt-br
ms.lasthandoff: 08/01/2017

---
# <a name="step-9-enable-replication-to-a-secondary-site-for-hyper-v-vms"></a>Etapa 9: Habilitar replicação para um site secundário para máquinas virtuais do Hyper-V


Depois de configurar uma política de replicação, use este artigo para habilitar a replicação para um site secundário do System Center Virtual Machine Manager (VMM) para máquinas virtuais (VMs) do Hyper-V usando o [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="select-vms-to-replicate"></a>Selecionar VMs a replicar

1. Clique em **Etapa 2: replicar aplicativo** > **Origem**. 

    ![Habilitar a replicação](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication1.png)

2. Em **Origem**, selecione o servidor VMM e a nuvem na qual os hosts Hyper-V que você deseja replicar estão localizados. Em seguida, clique em **OK**.

    ![Habilitar a replicação](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication2.png)
3. Em **Destino**, verifique o servidor VMM secundário e a nuvem.
4. Em **Máquinas virtuais**, selecione as VMs que você deseja proteger na lista.

    ![Habilitar Proteção da Máquina Virtual](./media/vmm-to-vmm-walkthrough-enable-replication/enable-replication5.png)

É possível acompanhar o progresso da ação **Habilitar Proteção** em **Trabalhos** > **Trabalhos do Site Recovery**. Após a conclusão do trabalho **Finalizar Proteção**, a replicação inicial é concluída, e a máquina virtual estará pronta para failover.

Observe que:

- Também é possível habilitar a proteção para máquinas virtuais no console do VMM. Clique em **Habilitar Proteção** na barra de ferramentas na guia propriedades > **Azure Site Recovery** da máquina virtual.
- Depois de habilitar a replicação, é possível exibir as propriedades da VM em **Itens Replicados**. No painel **Informações Gerais**, você pode ver informações sobre a política de replicação da VM e seu status. Clique em **Propriedades** para obter mais detalhes.

## <a name="onboard-existing-vms"></a>Integrar VMs existentes

Se você tiver máquinas virtuais existentes no VMM replicadas com a Réplica do Hyper-V, poderá carregá-las na replicação do Azure Site Recovery da seguinte maneira:

1. Verifique se o servidor Hyper-V que hospeda a VM existente está localizado na nuvem primária e se o servidor Hyper-V que hospeda a máquina virtual de réplica está localizado na nuvem secundária.
2. Certifique-se de ter uma política de replicação configurada para a nuvem primária do VMM.
3. Habilite a replicação para a máquina virtual primária. O Azure Site Recovery e o VMM garantem que o mesmo host de réplica e a mesma máquina virtual são detectados, e o Azure Site Recovery reutilizará e restabelecerá a replicação usando as configurações especificadas.


## <a name="next-steps"></a>Próximas etapas

Vá para [Etapa 10: Executar um failover de teste](vmm-to-vmm-walkthrough-test-failover.md).

