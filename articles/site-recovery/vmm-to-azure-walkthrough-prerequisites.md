---
title: "Examinar os pré-requisitos da replicação do Hyper-V para o Azure (com o System Center VMM) usando o Azure Site Recovery | Microsoft Docs"
description: "Descreve os pré-requisitos para a configuração de replicação, failover e recuperação de VMs locais do Hyper-V em nuvens VMM para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a1c30fd5-c979-473c-af44-4f725ad3e3ba
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/24/2017
ms.author: raynew
ms.openlocfilehash: 47c178c66ec98fe5d333edd725b64465026e73ed
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-with-vmm-to-azure-replication"></a>Etapa 2: Examinar os pré-requisitos do Hyper-V (com o VMM) para a replicação do Azure

Depois de revisar a [arquitetura do cenário](vmm-to-azure-walkthrough-architecture.md), leia este artigo para ter certeza de que compreende os pré-requisitos de implantação. 

## <a name="prerequisites-and-limitations"></a>Pré-requisitos e limitações

**Requisito** | **Detalhes**
--- | ---
**Conta do Azure** | Você precisa de uma conta do [Microsoft Azure](http://azure.microsoft.com/) .
**Armazenamento do Azure** | Você precisa de uma conta de armazenamento do Azure para armazenar os dados replicados.<br/><br/> A conta de armazenamento deve estar na mesma região que o cofre dos Serviços de Recuperação do Azure.<br/><br/>Você pode usar [armazenamento com redundância geográfica](../storage/common/storage-redundancy.md#geo-redundant-storage) ou armazenamento com redundância local. Recomendamos usar armazenamento com redundância geográfica. Com o armazenamento com redundância geográfica, os dados são resilientes em caso de interrupção, ou se a região primária não puder ser recuperada.<br/><br/> Você pode usar uma conta de armazenamento padrão do Azure, ou pode usar o [Armazenamento Premium](../storage/common/storage-premium-storage.md) do Azure. Armazenamento Premium pode hospedar cargas de trabalho intensivas de E/S e é normalmente usado em VMs que precisam de um desempenho de E/S consistentemente alto e baixa latência. Se você usar o Armazenamento Premium para os dados replicados, também precisará de uma conta de Armazenamento Standard. Uma conta de Armazenamento Standard armazena os logs de replicação que capturam as alterações contínuas nos dados locais.
**Rede do Azure** | Você precisa de uma [rede do Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md), à qual as VMs do Azure criadas após o failover se conectarão. A rede do Azure deve estar na mesma região do que o cofre dos Serviços de Recuperação.
**Servidores VMM locais** | Você precisa de um ou mais servidores VMM em execução no System Center 2012 R2 ou versão posterior.<br/><br/> Cada servidor VMM deve estar em uma ou mais nuvens privadas. Cada nuvem precisa de um ou mais grupos de hosts.<br/><br/> O servidor VMM precisa de acesso à Internet.
**Hyper-V local** | Os servidores de host Hyper-V devem executar pelo menos o Windows Server 2012 R2 com a função Hyper-V habilitada, ou o Microsoft Hyper-V Server 2012 R2. Instale as atualizações mais recentes.<br/><br/> O host Hyper-V deve estar localizado em um grupo de hosts do VMM (localizado em uma nuvem do VMM).<br/><br/> Um host deve ter uma ou mais VMs que você deseja replicar.<br/><br/> Os hosts do Hyper-V devem estar conectados à Internet para replicação no Azure, seja diretamente ou com um proxy. Os servidores Hyper-V devem ter as correções descritas no artigo [2961977](https://support.microsoft.com/kb/2961977).
**VMs do Hyper-V locais** | As VMs que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar de acordo com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). O nome da VM pode ser modificado depois que a replicação é habilitada. 




## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 3: Planejar a capacidade](vmm-to-azure-walkthrough-capacity.md)
