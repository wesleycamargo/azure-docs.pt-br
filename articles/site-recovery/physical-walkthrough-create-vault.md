---
title: "Configurar um cofre para a replicação de servidor físico para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Resume as etapas necessárias para configurar um cofre para replicar servidores físicos para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99f2605-f417-4995-be77-5323136b814f
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: deb5ad0495edc969b374795eeb2698326dd4ff4d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-set-up-a-vault-for-physical-server-replication-to-azure"></a>Etapa 6: Configurar um cofre para a replicação de servidor físico para o Azure


Este artigo descreve como configurar um cofre. Você cria o cofre e especifica o que deseja replicar de sua localização local para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


Poste perguntas e comentários na parte inferior deste artigo ou no [Fórum de Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).




## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Selecionar uma meta de proteção

Selecione o que você deseja replicar e para onde deseja replicar.

1. Clique em **Cofres dos Serviços de Recuperação** > cofre.
2. No Menu Recursos, clique em **Site Recovery** > **Preparar Infraestrutura** > **Meta de proteção**.
3. Em **Meta de proteção**, selecione **Para o Azure** > **Não virtualizado/Outro**.


## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 7: Definir a origem e o destino](physical-walkthrough-source-target.md)
