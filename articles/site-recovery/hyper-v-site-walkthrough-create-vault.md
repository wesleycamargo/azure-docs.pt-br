---
title: "Definir um cofre para replicação do Hyper-V (sem o System Center VMM) para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar um cofre para replicação do Hyper-V para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a936b3e2-0dbe-47ac-a98e-5285d96dc786
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: 8212ff011633c3a89d3310e828b6d5f1cda6ce3f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-hyper-v-replication"></a>Etapa 7: configurar um cofre para replicação do Hyper-V

Este artigo descreve como configurar um cofre e especifica o que você deseja replicar de sua localização local para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]



## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Objetivo de proteção**, selecione **Para o Azure** > **Sim, com o Hyper-V**. Selecione **Não** para confirmar que você não está usando o VMM. 

    ![Escolher metas](./media/hyper-v-site-walkthrough-create-vault/choose-goals2.png)



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 8: definir a origem e o destino](hyper-v-site-walkthrough-source-target.md)
