---
title: "Replicar VMs do Azure entre regiões do Azure | Microsoft Docs'"
description: "Resume as etapas necessárias para replicar VMs do Azure entre regiões do Azure com o serviço do Azure Site Recovery no portal do Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: 6dd36239-4363-4538-bf80-a18e71b8ec67
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2017
ms.author: raynew
ms.openlocfilehash: 9258613161a61e36b1d0c5796d5763c916d66859
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/03/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Você pode replicar VMs do Azure entre regiões usando o Site Recovery

>Este artigo fornece uma visão geral das etapas necessárias para replicar VMs (máquinas virtuais) em uma região do Azure para VMs do Azure em uma região diferente. 

>[!NOTE]
>
> Atualmente, a replicação de VM do Azure está em versão prévia.

Publique perguntas e comentários na parte inferior deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="step-1-review-architecture"></a>Etapa 1: revisar a arquitetura

Antes de iniciar a implantação, revise a arquitetura do cenário e os componentes que você precisa implantar.

Ir para a [Etapa 1: Examinar a arquitetura](azure-to-azure-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Etapa 2: Examinar os pré-requisitos

Verifique se possui os pré-requisitos do Azure implantados, incluindo uma assinatura, redes virtuais, contas de armazenamento e requisitos de VM.

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](azure-to-azure-walkthrough-prerequisites.md)


## <a name="step-3-plan-networking"></a>Etapa 3: Planejar rede

Verifique se a conectividade de saída está configurada nas VMs do Azure que você deseja replicar e que as conexões locais estão configuradas.

Ir para a [Etapa 4: Planejar a rede](azure-to-azure-walkthrough-network.md)



## <a name="step-4-create-a-vault"></a>Etapa 4: criar um cofre 

É necessário configurar um cofre dos Serviços de Recuperação para orquestrar e gerenciar a replicação e especificar a região de origem.

Acesse a [Etapa 4: criar um cofre](azure-to-azure-walkthrough-vault.md)


## <a name="step-5-enable-replication"></a>Etapa 5: habilitar a replicação


Para habilitar a replicação, você define as configurações de localização de destino, configura uma política de replicação e seleciona as VMs do Azure que deseja replicar. Depois de habilitar, a replicação inicial da VM ocorre.

Acesse a [Etapa 5: habilitar a replicação](azure-to-azure-walkthrough-enable-replication.md)


## <a name="step-6-run-a-test-failover"></a>Etapa 6: executar um failover de teste

Após a conclusão da replicação inicial e a replicação delta estiver em execução, você poderá executar um failover de teste para verificar se tudo está funcionando como esperado.

Acesse a [Etapa 6: executar um failover de teste](azure-to-azure-walkthrough-test-failover.md)


