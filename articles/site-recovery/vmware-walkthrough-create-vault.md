---
title: "Configurar um cofre para a replicação VMware para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar um cofre para replicação do VMware para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8bce940e-f19f-4418-8360-aee7b073519a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: dca95ad46b8de587140c3573ba6ed5702a122032
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-7-set-up-a-vault-for-vmware-replication-to-azure"></a>Etapa 7: configurar um cofre para a replicação do VMware para o Azure


Este artigo descreve como configurar um cofre e especifica o que você deseja replicar de sua localização local para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione **Para o Azure** > **Sim, com o Hipervisor VMware vSphere**.



## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 8: definir a origem e o destino](vmware-walkthrough-source-target.md)
