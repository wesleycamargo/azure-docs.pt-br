---
title: Preparar recursos do Azure para replicar VMs do Hyper-V (com o System Center VMM) para o Azure usando o Azure Site Recovery | Microsoft Docs
description: "Descreve o que você precisa em vigor no Azure antes de iniciar a replicação de VMs do Hyper-V (com o VMM) para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 1568bdc3-e767-477b-b040-f13699ab5644
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: 365dd9477f791432c1a92f1b81eb573dbbc6f874
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---

# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-with-vmm-to-azure"></a>Etapa 5: preparar os recursos do Azure para a replicação do Hyper-V (com o VMM) para o Azure

Depois de verificar os [requisitos de rede](vmm-to-azure-walkthrough-network.md), use as instruções neste artigo para preparar recursos do Azure para que você possa replicar VMs do Hyper-V locais em nuvens do System Center Virtual Machine Manager (VMM) no Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

- Obter uma [conta do Microsoft Azure](http://azure.microsoft.com/).
- Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Verifique as regiões suportadas do Site Recovery, em Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Saiba mais sobre os [Preços do Site Recovery](site-recovery-faq.md#pricing) e obtenha os [Detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).
- Verifique se sua conta do Azure tem as [permissões](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) corretas para criar VMs do Azure. [Saiba mais](../active-directory/role-based-access-built-in-roles.md) sobre o controle de acesso baseado em função do Azure.


## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

- Configurar uma [rede do Azure](../virtual-network/virtual-network-get-started-vnet-subnet.md). As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
- A rede deve estar na mesma região que o cofre dos Serviços de Recuperação
- O Site Recovery no portal do Azure pode usar redes configuradas no [Gerenciador de Recursos](../resource-manager-deployment-model.md), ou no modo clássico.
- É recomendável configurar uma rede antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação da Recuperação de Site.
- Saiba mais sobre os [preços de rede virtual](https://azure.microsoft.com/pricing/details/virtual-network/).


## <a name="set-up-an-azure-storage-account"></a>Configure uma conta de armazenamento do Azure

- O Site Recovery replica máquinas locais para o armazenamento do Azure. As VMs do Azure são criadas a partir do armazenamento após o failover.
- Configure uma [conta de armazenamento do Azure](../storage/storage-create-storage-account.md#create-a-storage-account) padrão ou premium para reter os dados replicados para o Azure.
- O [armazenamento premium](../storage/storage-premium-storage.md) normalmente é usado para máquinas virtuais que precisam de um desempenho de E/S consistentemente alto e baixa latência para hospedar cargas de trabalho com uso intensivo de E/S.
- Se você desejar usar uma conta premium para armazenar dados replicados, também precisará de uma conta de armazenamento standard para armazenar os logs de replicação que capturam as alterações contínuas nos dados locais.
- Dependendo do modelo de recurso que você deseja usar para as VMs do Azure com failover, você configurará uma conta no [modo Resource Manager](../storage/storage-create-storage-account.md) ou no [modo clássico](../storage/storage-create-storage-account-classic-portal.md).
- É recomendável que você configure uma conta de armazenamento antes de começar. Caso você não faça isso, será necessário fazê-lo durante a implantação do Site Recovery. As contas devem estar na mesma região que o cofre dos Serviços de Recuperação.
- Não é possível mover contas de armazenamento usadas pelo Site Recovery entre grupos de recursos na mesma assinatura ou entre assinaturas diferentes.


## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 6: Preparar o VMM](vmm-to-azure-walkthrough-vmm-hyper-v.md)

