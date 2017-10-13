---
title: "Replicar VMs do Hyper-V para um site secundário do VMM com o Azure Site Recovery | Microsoft Docs"
description: "Fornece uma visão geral para replicar máquinas virtuais do Hyper-V para um site secundário do VMM usando o portal do Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 476ca82a-8f5c-4498-9dcf-e1011d60ed59
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: raynew
ms.openlocfilehash: b422dd2cf23426de2f154a553b38509082536309
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-a-secondary-vmm-site"></a>Replicar máquinas virtuais do Hyper-V em nuvens de VMM para um site de VMM secundário

> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-vmm.md)
> * [Portal clássico](site-recovery-vmm-to-vmm-classic.md)
> * [PowerShell – Resource Manager](site-recovery-vmm-to-vmm-powershell-resource-manager.md)
>
>

Este artigo fornece uma visão geral das etapas necessárias para a replicação de máquinas virtuais (VMs) do Hyper-V locais gerenciadas em nuvens do System Center Virtual Machine Manager (VMM) para um local secundário de VMM usando o [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Etapa 1: Analisar a arquitetura do cenário

Antes de iniciar a implantação, examine a arquitetura do cenário e entenda todos os componentes que você precisa implantar.

Vá para a [Etapa 1: Examinar a arquitetura](vmm-to-vmm-walkthrough-architecture.md).

## <a name="step-2-review-prerequisites-and-limitations"></a>Etapa 2: Examinar os pré-requisitos e as limitações

Certifique-se de que você compreende os pré-requisitos e as limitações da implantação.

**Pré-requisitos do Azure**: você precisa de uma assinatura do Microsoft Azure e de um cofre do Azure Recovery Services para orquestrar e gerenciar a replicação.
**Servidores do VMM no local e os hosts do Hyper-V**: verifique se os servidores VMM e os hosts do Hyper-V são compatíveis e se estão preparados para a implantação do Site Recovery.

Vá para a [Etapa 2: Verificar os pré-requisitos e as limitações](vmm-to-vmm-walkthrough-prerequisites.md).

## <a name="step-3-plan-networking"></a>Etapa 3: Planejar a rede

Você precisa fazer algumas planejamento para garantir que você pode configurar o mapeamento de rede entre redes de VM do VMM quando você implanta o cenário.

Vá para [Etapa 3: Planejar a rede](vmm-to-vmm-walkthrough-network.md).


## <a name="step-4-prepare-vmm-and-hyper-v"></a>Etapa 4: Preparar o VMM e o Hyper-V

Prepare os servidores do VMM e os hosts do Hyper-V para implantação do Site Recovery.

Vá para a [Etapa 4: Preparar servidores locais](vmm-to-vmm-walkthrough-vmm-hyper-v.md).

## <a name="step-5-set-up-a-vault"></a>Etapa 5: Configurar um cofre

Configure um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.

[Etapa 5: Configurar um cofre](vmm-to-vmm-walkthrough-create-vault.md).

## <a name="step-6-set-up-source-and-target-settings"></a>Etapa 6: Definir as configurações de origem e de destino

Configure os locais de VMM de replicação de origem e destino. Adicione os servidores do VMM ao cofre e baixe os arquivos de instalação de componentes do Site Recovery. Execute a instalação do Provedor do Azure Site Recovery no servidor do VMM. O Provedor é instalado no servidor do VMM e registra o servidor no cofre. Você pode instalar o agente de Serviços de Recuperação da Microsoft em cada host do Hyper-V.

Vá para a [Etapa 6: Definir as configurações de origem e de destino](vmm-to-vmm-walkthrough-source-target.md).

## <a name="step-7-configure-network-mapping"></a>Etapa 7: configurar o mapeamento de rede

Mapeie as redes de VM do VMM nos locais de origem e destino. Após o failover, as máquinas virtuais são criadas na rede de destino que é mapeada para a rede de VM de origem em que a VM do Hyper-V de origem está localizada.

Vá para a [Etapa 7: Configurar o mapeamento de rede](vmm-to-vmm-walkthrough-network-mapping.md).


## <a name="step-8-set-up-a-replication-policy"></a>Etapa 8: Configurar uma política de replicação

Especifica como as VMs serão replicadas entre os locais do VMM.

Vá para a [Etapa 8: Configurar uma política de replicação](vmm-to-vmm-walkthrough-replication.md).


## <a name="step-9-enable-replication-for-vms"></a>Etapa 9: Habilitar a replicação para VMs

Selecione as VMs que você deseja replicar. Habilitar uma máquina virtual para replicação dispara a replicação inicial para o site secundário, seguida pela replicação delta em andamento.

Vá para a [Etapa 9: Habilitar a replicação](vmm-to-vmm-walkthrough-enable-replication.md).


## <a name="step-10-run-a-test-failover"></a>Etapa 10: Executar um failover de teste

Execute um failover de teste para verificar se tudo está funcionando como esperado.

Vá para [Etapa 10: Executar um failover de teste](vmm-to-vmm-walkthrough-test-failover.md).
