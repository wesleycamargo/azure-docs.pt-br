---
title: "Habilite uma política de replicação para replicação de VM Hyper-V (sem o System Center VMM) para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar uma política de replicação durante a replicação de VMs Hyper-V no armazenamento do Azure"
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1777e0eb-accb-42b5-a747-11272e131a52
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: ca5bec5cf1152e6259b9fe7a869edd2d62b88e1a
ms.contentlocale: pt-br
ms.lasthandoff: 08/21/2017

---

# <a name="step-9-set-up-a-replication-policy-for-hyper-v-vm-replication-to-azure"></a>Etapa 9: configurar uma política de replicação para a replicação da VM Hyper-V para o Azure

Este artigo descreve como configurar uma política de replicação quando você estiver replicando VMs Hyper-V para o Azure (sem o System Center VMM) usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="about-snapshots"></a>Sobre instantâneos

O Hyper-V usa dois tipos de instantâneos: um instantâneo padrão, que fornece um instantâneo incremental de toda a máquina virtual, e um instantâneo consistente com aplicativos, que cria um instantâneo pontual dos dados do aplicativo na máquina virtual.
    - Os instantâneos consistentes com aplicativos usam o Serviço de VSS (Cópias de Sombra de Volume) para garantir que os aplicativos estejam em um estado consistente quando o instantâneo for obtido.
    - Se você habilitar instantâneos consistentes com o aplicativo, isso afetará o desempenho de aplicativos executados em máquinas virtuais de origem. Verifique se o valor definido é menor do que o número de pontos de recuperação adicionais que você configurar.

## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Preparar a Infraestrutura** > **Configurações de Replicação** > **+Criar e associar**.

    ![Rede](./media/hyper-v-site-walkthrough-replication/gs-replication.png)
2. Em **Criar e associar política**, especifique um nome de política.
3. Em **Frequência de cópia**, especifique com que frequência você deseja replicar os dados delta após a replicação inicial (a cada 30 segundos, 5 ou 15 minutos).

    > [!NOTE]
    > Não há suporte para uma frequência de 30 segundos ao replicar para armazenamento premium. A limitação é determinada pelo número de instantâneos por blob (100) com suporte pelo armazenamento premium. [Saiba mais](../storage/common/storage-premium-storage.md#snapshots-and-copy-blob).

4. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. VMs podem ser recuperadas para qualquer ponto em uma janela.
5. Em **Frequência do instantâneo consistente com aplicativo**, especifique com que frequência (1 a 12 horas) são criados os pontos de recuperação que contêm instantâneos consistentes com o aplicativo.
6. Em **Hora de início para replicação inicial**, especifique quando a replicação inicial deve começar. A replicação ocorre pela largura de banda da Internet, pois talvez você queira agendá-la fora dos horários de pico. Em seguida, clique em **OK**.

    ![Política de replicação](./media/hyper-v-site-walkthrough-replication/gs-replication2.png)

Quando você cria uma nova política, ela é automaticamente associada ao site do Hyper-V. É possível associar um site do Hyper-V (e as VMs nele) a várias políticas de replicação em **Replicação** > nome da política > **Associar Site do Hyper-V**.



## <a name="next-steps"></a>Próximas etapas

Acesse a [Etapa 10: Habilitar a replicação](hyper-v-site-walkthrough-enable-replication.md)

