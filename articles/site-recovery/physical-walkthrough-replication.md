---
title: "Configurar uma política de replicação para a replicação de servidor físico para o Azure com o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar uma política de replicação durante a replicação de servidores físicos locais para o armazenamento do Azure usando o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d7874bd8-6626-4668-9ec9-dbd2d26f8f81
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 9ce23382001b54e7b9b7a58b8dd9aa61b400826d
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-a-replication-policy-for-physical-server-replication-to-azure"></a>Etapa 8: Configurar uma política de replicação para a replicação de servidor físico para o Azure


Este artigo descreve como configurar uma política de replicação quando você estiver replicando servidores físicos Windows/Linux para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="configure-a-policy"></a>Configurar uma política

1. Clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limite de RPO**, especifique o limite de RPO. Esse valor indica com que frequência os pontos de recuperação de dados são criados. Um alerta será gerado se a replicação contínua exceder esse limite.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) qual será a duração da janela de retenção para cada ponto de recuperação. VMs replicadas podem ser recuperadas para qualquer ponto em uma janela. Há suporte para retenção de até 24 horas para máquinas replicadas para armazenamento premium e 72 horas para o armazenamento padrão.
5. Em **Frequência do instantâneo consistente com o aplicativo**, especifique com que frequência (em minutos) os pontos de recuperação contendo instantâneos consistentes com aplicativos serão criados. Clique em **OK** para criar a política.

    ![Política de replicação](./media/physical-walkthrough-replication/gs-replication2.png)
8. Quando você cria uma nova política, ela é automaticamente associada ao servidor de configuração. Por padrão, uma política de correspondência é criada automaticamente para failback. Por exemplo, se a política de replicação for **rep-policy**, a política de failback será **rep-policy-failback**. Essa política não é usada até você iniciar um failback do Azure.

## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 9: Instalar o Serviço de mobilidade](physical-walkthrough-install-mobility.md)
