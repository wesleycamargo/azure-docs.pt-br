---
title: "Configurar uma política de replicação para a replicação da VM do Hyper-V (com o VMM) para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Descreve como configurar uma política de replicação para a replicação de VMs do Hyper-V (com o VMM) para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: ee808b20-324b-4198-b831-edb65b95e8b7
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 592e1c3f647e5b1f1d9aa776657e8f89b60349e1
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---
# <a name="step-10-set-up-a-replication-policy-for-hyper-v-vm-replication-with-vmm-to-azure"></a>Etapa 10: Configurar uma política de replicação para a replicação da VM do Hyper-V (com o VMM) para o Azure


Depois de configurar o [mapeamento de rede](vmm-to-azure-walkthrough-network-mapping.md), use este artigo para configurar uma política de replicação T\para replicar máquinas virtuais do Hyper-V gerenciadas nas nuvens do System Center Virtual Machine Manager (VMM) para o Azure local usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).



## <a name="create-a-policy"></a>Criar uma política

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

    ![Rede](./media/vmm-to-azure-walkthrough-replication/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    >  Não há suporte para uma frequência de 30 segundos ao replicar para armazenamento premium. A limitação é determinada pelo número de instantâneos por blob (100) com suporte pelo armazenamento premium. [Saiba mais](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob)

4. Em **Retenção do ponto de recuperação**, especifique, em horas, qual será a duração da janela de retenção para cada ponto de recuperação. Os computadores protegidos podem ser recuperados para qualquer ponto nessa janela.
5. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que incluam instantâneos consistentes com aplicativos. O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual. Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido. Observe que, se você habilitar instantâneos consistentes com aplicativos, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.
6. Em **Hora de início para replicação inicial**, indique quando a replicação inicial deve começar. A replicação ocorre pela largura de banda da Internet, pois talvez você queira agendá-la fora dos horários de pico.
7. Em **Criptografar os dados armazenados no Azure**, especifique se os dados em repouso serão criptografados no armazenamento do Azure. Em seguida, clique em **OK**.

    ![Política de replicação](./media/vmm-to-azure-walkthrough-replication/gs-replication2.png)
8. Quando você cria uma nova política, ela é automaticamente associada à nuvem do VMM. Clique em **OK**. É possível associar nuvens do VMM adicionais (e as VMs nelas) a essa política de replicação em **Replicação** > nome da política > **Associar Nuvem do VMM**.

    ![Política de replicação](./media/vmm-to-azure-walkthrough-replication/policy-associate.png)



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 11: Habilitar a replicação](vmm-to-azure-walkthrough-enable-replication.md)

