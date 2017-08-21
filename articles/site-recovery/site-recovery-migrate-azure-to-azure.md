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
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 49bc337dac9d3372da188afc3fa7dff8e907c905
ms.openlocfilehash: ef2972c077a2b1dd2b2fd6ce53cc6560520ea870
ms.contentlocale: pt-br
ms.lasthandoff: 07/14/2017

---
# <a name="migrate-azure-iaas-virtual-machines-between-azure-regions-with-azure-site-recovery"></a>Migrar máquinas virtuais IaaS do Azure entre regiões do Azure com o Azure Site Recovery
## <a name="overview"></a>Visão geral
Bem-vindo ao Azure Site Recovery! Use este artigo se você deseja migrar VMs do Azure entre regiões do Azure. Antes de começar, observe que:

* O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: Azure Resource Manager e Clássico. O Azure também tem dois portais – o portal clássico do Azure, que dá suporte ao modelo de implantação clássica, e o portal do Azure, com suporte para ambos os modelos de implantação. As etapas básicas para migração são as mesmas se você estiver configurando o Site Recovery no Resource Manager ou no clássico. No entanto, as instruções de interface do usuário e capturas de tela neste artigo são relevantes para o Portal do Azure.
* **No momento, você pode apenas migrar de uma região para outra. Você pode executar o failover de VMs de uma região do Azure para outra, mas não pode executar o failback delas novamente.**

Publique eventuais comentários ou perguntas no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos
Aqui está o que você precisa para essa implantação:

* **Máquinas virtuais IaaS**: as VMs que você deseja migrar. Você pode migrar essas VMs tratando-as como computadores físicos.

## <a name="deployment-steps"></a>Etapas de implantação.
Esta seção descreve as etapas de implantação no novo Portal do Azure.

1. [Crie um cofre](site-recovery-vmware-to-azure.md).
2. [Habilite a replicação](site-recovery-vmware-to-azure.md). Habilite a replicação para as VMs que você deseja migrar e escolha o Azure como fonte. 
3. [ Execute um failover não planejado](site-recovery-failover.md). Depois que a replicação inicial for concluída, você poderá executar um failover não planejado de uma região do Azure para outra. Se preferir, você pode criar um plano de recuperação e executar um failover não planejado para migrar várias máquinas virtuais entre regiões. [Saiba mais](site-recovery-create-recovery-plans.md) sobre planos de recuperação.

## <a name="next-steps"></a>Próximas etapas
Saiba mais sobre outros cenários de replicação em [O que é o Azure Site Recovery?](site-recovery-overview.md)

