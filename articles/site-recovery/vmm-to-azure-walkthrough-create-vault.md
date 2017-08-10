---
title: "Definir um cofre para replicação do Hyper-V (com o System Center VMM) para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar um cofre para replicação do Hyper-V (com VMM) para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: b3cd6f03-c33c-406d-91d4-5cba69f79abd
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: af453ec27ba15ad8c59cf9f544584ad18dc0f74a
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

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
3. Em **Objetivo de proteção**, selecione **Para o Azure** > **Sim, com o Hyper-V**. Selecione **Sim** para confirmar que você está usando do VMM. 

     ![Escolher metas](./media/vmm-to-azure-walkthrough-create-vault/choose-goals.png)



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 8: definir a origem e o destino](vmm-to-azure-walkthrough-source-target.md)

