---
title: "Revisar os pré-requisitos da replicação do Hyper-V para um site secundário do VMM com o Azure Site Recovery | Microsoft Docs"
description: "Descreve os pré-requisitos para a replicação de VMs do Hyper-V para um site secundário do VMM com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 21ff0545-8be5-4495-9804-78ab6e24add6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 7897a30bf1774609ca8e6037dabcd5fbf4151271
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="step-2-review-the-prerequisites-and-limitations-for-hyper-v-vm-replication-to-a-secondary-vmm-site"></a>Etapa 2: Examinar os pré-requisitos e as limitações para replicação de VM do Hyper-V para um site secundário do VMM


Depois de examinar a [arquitetura do cenário](vmm-to-vmm-walkthrough-architecture.md), leia este artigo para compreender os pré-requisitos de implantação, ao replicar máquinas virtuais (VMs) do Hyper-V gerenciadas em nuvens do System Center Virtual Machine Manager (VMM), para um site secundário usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Requisito** | **Detalhes**
--- | ---
**As tabelas** | Uma assinatura do [Microsoft Azure](http://azure.microsoft.com/).<br/><br/> Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).<br/><br/> [Saiba mais](https://azure.microsoft.com/pricing/details/site-recovery/) sobre os preços da Recuperação de Site.<br/><br/> Verifique as regiões suportadas do Site Recovery, em Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
**Servidores do VMM** | Recomendamos que você tenha dois servidores do VMM, uma no site primário e um secundário.<br/><br/> Há suporte para a replicação entre nuvens em um único servidor VMM.<br/><br/> Os servidores VMM devem estar executando pelo menos o System Center 2012 SP1 com as atualizações mais recentes.<br/><br/> Servidores VMM precisam de acesso à Internet.
**Nuvens do VMM** | Cada servidor VMM deve ter uma ou mais nuvens, e todas as nuvens devem ter o perfil de Capacidade do Hyper-V definido. <br/><br/>As nuvens devem conter um ou mais grupos de hosts do VMM.<br/><br/> Se você tiver apenas um servidor VMM, ele precisa de pelo menos duas nuvens, para atuar como primário e secundário.
**Hyper-V** | Os servidores Hyper-V devem executar, no mínimo, o Windows Server 2012 com a função Hyper-V e ter as últimas atualizações instaladas.<br/><br/> Um servidor Hyper-V deve conter uma ou mais VMs.<br/><br/>  Os servidores host Hyper-V devem estar localizados em grupos de hosts nas nuvens VMM primárias e secundárias.<br/><br/> Se você estiver executando o Hyper-V em um cluster no Windows Server 2012 R2, instale a [atualização 2961977](https://support.microsoft.com/kb/2961977)<br/><br/> Se estiver executando o Hyper-V em um cluster no Windows Server 2012, o agente de cluster não será criado automaticamente se você tiver um cluster baseado em endereço IP estático. Configure o agente de cluster manualmente. [Leia mais](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx).<br/><br/> Os servidores do Hyper-V precisam de acesso à Internet.




## <a name="next-steps"></a>Próximas etapas

Vá para [Etapa 3: Planejar a rede](vmm-to-vmm-walkthrough-network.md).
