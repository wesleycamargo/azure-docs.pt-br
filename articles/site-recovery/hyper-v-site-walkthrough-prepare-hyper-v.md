---
title: "Preparar os hosts Hyper-V (sem o System Center VMM) para replicação para o Azure | Microsoft Docs"
description: "Descreve como preparar os hosts Hyper-V para replicação para o Azure usando o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0f204e24-8d78-4076-95c5-8137d1be9c01
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.openlocfilehash: f9bcaa8e55be6e8fddaf88ebc3f18f5dbb2811e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="step-6-prepare-hyper-v-hosts-for-replication-to-azure"></a>Etapa 6: Preparar os hosts Hyper-V para replicação para o Azure

Use as instruções deste artigo para preparar os hosts Hyper-V locais para interagir com o Azure Site Recovery.

Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-hosts"></a>Preparar os hosts

- Verifique se os hosts do Hyper-V satisfazem os [pré-requisitos](site-recovery-prereq.md#disaster-recovery-of-hyper-v-vms-to-azure-no-vmm).
- Verifique se os hosts podem acessar as URLs necessárias:

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]
    
- Se tiver regras de firewall baseadas no endereço IP, verifique se elas permitem a comunicação com o Azure.
- Permita os [Intervalos de IP do Datacenter do Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) e a porta HTTPS (443).
- Permita os intervalos de endereços IP para a região do Azure da sua assinatura e para o Oeste dos EUA (usados para Controle de Acesso e Gerenciamento de Identidade).

Durante a implantação do Site Recovery, você adiciona hosts Hyper-V que contêm VMs que você deseja replicar para um site do Hyper-V. O Provedor do Site Recovery e o agente dos Serviços de Recuperação são instalados em cada host. O site do Hyper-V é registrado no cofre dos Serviços de Recuperação.

## <a name="next-steps"></a>Próximas etapas

Vá para a [Etapa 7: Criar um cofre](hyper-v-site-walkthrough-create-vault.md)

