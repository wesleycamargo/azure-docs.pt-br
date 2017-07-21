---
title: Preparar recursos do Azure para replicar VMs do Hyper-V (sem o System Center VMM) para o Azure usando o Azure Site Recovery | Microsoft Docs
description: "Descreve o que você precisa em vigor no Azure antes de iniciar a replicação de VMs do Hyper-V (Sem VMM) para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 28fa722c-675e-4637-98eb-7ccbf3806d69
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 31ecec607c78da2253fcf16b3638cc716ba3ab89
ms.openlocfilehash: fe67a94a2b56fbc72035582f7ee1625b12b21ead
ms.contentlocale: pt-br
ms.lasthandoff: 06/23/2017

---

# <a name="step-5-prepare-azure-resources-for-hyper-v-replication-to-azure"></a>Etapa 5: preparar os recursos do Azure para a replicação do Hyper-V para o Azure

Use as instruções neste artigo para preparar recursos do Azure para que você possa replicar VMs Hyper-V locais no Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Antes de começar

Não se esqueça de ler os [pré-requisitos](hyper-v-site-walkthrough-prerequisites.md)

## <a name="set-up-an-azure-account"></a>Configurar uma conta do Azure

- Obter uma [conta do Microsoft Azure](http://azure.microsoft.com/).
- Você pode começar com uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).
- Verifique as regiões suportadas do Site Recovery, em Disponibilidade Geográfica nos [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
- Saiba mais sobre os [Preços do Site Recovery](site-recovery-faq.md#pricing) e obtenha os [Detalhes de preços](https://azure.microsoft.com/pricing/details/site-recovery/).


## <a name="set-up-an-azure-network"></a>Configurar uma rede do Azure

- Configure uma rede do Azure. As VMs do Azure são colocadas nessa rede quando são criadas após o failover.
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

Vá para a [Etapa 6: preparar recursos do Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

