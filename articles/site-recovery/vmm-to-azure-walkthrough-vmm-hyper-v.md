---
title: "Preparar o System Center VMM para replicação do Hyper-V para o Azure | Microsoft Docs"
description: "Descreve como preparar o servidor do System Center VMM para replicação do Hyper-V no Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: afcd81ae-d192-4013-a0af-3dac45b3c7e9
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: ec118ed837dbf140083b3ae1e4ecd41c81562018
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---

# <a name="step-6-prepare-vmm-servers-and-hyper-v-hosts-for-hyper-v-replication-to-azure"></a>Etapa 6: Preparar os servidores do VMM e os hosts Hyper-V para replicação do Hyper-V no Azure

Depois de configurar os [componentes do Azure](vmm-to-azure-walkthrough-prepare-azure.md) para a implantação, use as instruções neste artigo para preparar servidores VMM locais e os hosts do Hyper-V para interagir com o Azure Site Recovery.

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-vmm-servers"></a>Preparar os servidores VMM

- Você precisa de pelo menos um servidor do VMM que atenda aos requisitos de suporte para replicação do Site Recovery (site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers).
- Prepare o servidor do VMM para [mapeamento de rede](vmm-to-azure-walkthrough-network.md#network-mapping-for-replication-to-azure).
- Certificar-se de que o servidor do VMM possa acessar essas URLs

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.
- Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
- Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).

Durante a implantação do Site Recovery, baixe o Provedor do Site Recovery e instale-o em cada servidor do VMM. O servidor do VMM é registrado no cofre dos Serviços de Recuperação.




## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 7: Criar um cofre](vmm-to-azure-walkthrough-create-vault.md)


