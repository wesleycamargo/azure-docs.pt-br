---
title: "Examinar os pré-requisitos da replicação do Hyper-V para o Azure (sem o System Center VMM) usando o Azure Site Recovery | Microsoft Docs"
description: "Descreve os pré-requisitos para a configuração de replicação, failover e recuperação de VMs locais do Hyper-V para o Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 7ef3fb46-52f5-4c8a-b1a1-658c2305762a
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: cbb5d3598ef91512991d7d1e9f854eb12980752b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-2-review-the-prerequisites-for-hyper-v-without-vmm-to-azure-replication"></a>Etapa 2: Examinar os pré-requisitos do Hyper-V (sem o VMM) para a replicação do Azure

Os pré-requisitos são resumidos na tabela.


**Pré-requisito** | **Detalhes** 
--- | --- 
**As tabelas** | Saiba mais sobre os [requisitos do Azure](site-recovery-prereq.md#azure-requirements).
**Servidores locais** | [Saiba mais](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm) sobre os requisitos dos hosts Hyper-V locais.
**VMs do Hyper-V locais** | As VMs que você deseja replicar devem estar executando um [sistema operacional com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) e estar de acordo com os [pré-requisitos do Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
**URLs do Azure** | Os hosts Hyper-V precisam de acesso a essas URLs:<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.<br/></br> Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).<br/></br> Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).



## <a name="next-steps"></a>Próximas etapas

- Se estiver fazendo uma implantação completa, vá para a [Etapa 3: planejar a capacidade](hyper-v-site-walkthrough-capacity.md)
- Se estiver fazendo uma implantação de teste simples, vá para a [Etapa 4: Planejar a rede](hyper-v-site-walkthrough-network.md).
