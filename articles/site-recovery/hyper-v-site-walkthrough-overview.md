---
title: Replicar VMs do Hyper-V para o Azure com o Azure Site Recovery | Microsoft Docs
description: "Descreve como implantar orquestrar a replicação, o failover e a recuperação de VMs do Hyper-V locais para o Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: efddd986-bc13-4a1d-932d-5484cdc7ad8d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/21/2017
ms.author: raynew
ms.openlocfilehash: da10b213bc2543942b5ac77cf5c5d8547c00220c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-without-vmm-to-azure"></a>Replicar máquinas virtuais do Hyper-V (sem o VMM) para o Azure 

> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-hyper-v-site-to-azure.md)
> * [Azure clássico](site-recovery-hyper-v-site-to-azure-classic.md)
> * [PowerShell – Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)
>
>

Este artigo fornece uma visão geral das etapas necessárias para replicar máquinas virtuais locais do Hyper-V para o Azure, usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure. Nessa implantação, VMs de Hyper-V não são gerenciadas pelo System Center Virtual Machine Manager (VMM).


Depois de ler este artigo, poste comentários no final ou faça perguntas técnicas no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-architecture-and-prerequisites"></a>Etapa 1: Examinar a arquitetura e os pré-requisitos

Antes de iniciar a implantação, examine a arquitetura do cenário e entenda todos os componentes que você precisa implantar

Ir para a [Etapa 1: Examinar a arquitetura](hyper-v-site-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Etapa 2: Examinar os pré-requisitos

Verifique se os pré-requisitos estão em vigor para cada componente de implantação:

- **Pré-requisitos do Azure**: você precisa de uma conta do Microsoft Azure, redes do Azure e contas de armazenamento.
- **Pré-requisitos do Hyper-V local**: verifique se os hosts Hyper-V estão preparados para a implantação do Site Recovery.
- **VMs replicadas**: as VMs que você deseja replicar precisam atender aos requisitos do Azure.

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](hyper-v-site-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Etapa 3: Planejar a capacidade

Se você estiver fazendo uma implantação completa, precisará descobrir quais recursos de replicação serão necessários. Há algumas ferramentas disponíveis para ajudá-lo a fazer isso. Vá para a Etapa 2. Se estiver fazendo uma configuração rápida para testar o ambiente, ignore esta etapa.

Ir para a [Etapa 3: Planejar a capacidade](hyper-v-site-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Etapa 4: Planejar a rede

Você precisa fazer um planejamento de rede para garantir que as VMs do Azure serão conectadas a redes após o failover e que elas têm os endereços IP corretos.

Ir para a [Etapa 4: Planejar a rede](hyper-v-site-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Etapa 5: Preparar os recursos do Azure

Configure as redes e o armazenamento do Azure antes de começar. Você pode fazer isso durante a implantação, mas recomendamos fazer isso antes de começar.

Ir para a [Etapa 5: Preparar o Azure](hyper-v-site-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-hyper-v"></a>Etapa 6: Preparar o Hyper-V

Verifique se os Hyper-V Servers atendem aos requisitos de implantação do Site Recovery.

Ir para a [Etapa 6: Preparar o Hyper-V](hyper-v-site-walkthrough-prepare-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Etapa 7: Configurar um cofre

Você precisa configurar um cofre dos Serviços de Recuperação para orquestrar e gerenciar a replicação. Ao configurar o cofre, você especifica o que deseja replicar e o local para o qual deseja replicá-lo.

Ir para a [Etapa 7: Criar um cofre](hyper-v-site-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Etapa 8: Definir as configurações de origem e destino

Configure a origem e o destino usados para a replicação. A definição das configurações de origem inclui adicionar hosts Hyper-V a um site do Hyper-V, instalar o Provedor do Site Recovery e o agente dos Serviços de Recuperação em cada host Hyper-V e registrar o site no cofre dos Serviços de Recuperação.

Ir para a [Etapa 8: Configurar a origem e o destino](hyper-v-site-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Etapa 9: Configurar uma política de replicação

Configure uma política para especificar as configurações de replicação das VMs do Hyper-V no cofre.

Ir para a [Etapa 9: Configurar uma política de replicação](hyper-v-site-walkthrough-replication.md)


## <a name="step-10-enable-replication"></a>Etapa 10: Habilitar a replicação

Depois que você tiver uma política de replicação em vigor, após a habilitação, ocorrerá a replicação inicial da VM.

Ir para a [Etapa 10: Habilitar a replicação](hyper-v-site-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Etapa 11: Executar um failover de teste

Após a conclusão da replicação inicial e a replicação delta estiver em execução, você poderá executar um failover de teste para verificar se tudo está funcionando como esperado.

Ir para a [Etapa 11: Executar um failover de teste](hyper-v-site-walkthrough-test-failover.md)
