---
title: "Migrar VMs IaaS do Azure entre regiões do Azure | Microsoft Docs"
description: "Use o Azure Site Recovery para migrar máquinas virtuais IaaS do Azure de uma região do Azure para outra."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: tysonn
ms.assetid: 8a29e0d9-0010-4739-972f-02b8bdf360f6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2017
ms.author: raynew
ms.openlocfilehash: 805582d89ee906e21fbe588e895ce0fe3a926a66
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/30/2017
---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery

Use este artigo se você quiser migrar máquinas virtuais (VMs) do Azure entre regiões do Azure, usando o serviço de [Recuperação de Site](../site-recovery-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Você precisa das VMs de IaaS que deseja migrar.

## <a name="deployment-steps"></a>Etapas de implantação.

1. [Crie um cofre](azure-to-azure-tutorial-enable-replication.md#create-a-vault).
2. [Habilite a replicação](azure-to-azure-tutorial-enable-replication.md#enable-replication) para as VMs que você deseja migrar e escolha o Azure como a origem. No momento, não há suporte para replicação nativa de VMs do Azure usando discos gerenciados.
3. [Executar um failover](../site-recovery-failover.md). Depois que a replicação inicial for concluída, você poderá executar um failover de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover para migrar várias máquinas virtuais entre regiões. [Saiba mais](../site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## <a name="next-steps"></a>Próximas etapas
Saiba sobre outros cenários de replicação em [O que é o Azure Site Recovery?](../site-recovery-overview.md)
