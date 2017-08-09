---
title: Replicar VMs do Hyper-V em nuvens do VMM para o Azure com o Azure Site Recovery | Microsoft Docs
description: "Fornece uma visão geral para replicar VMs do Hyper-V em nuvens do VMM para o Azure usando o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 74b75232b4b1c14dbb81151cdab5856a1e4da28c
ms.openlocfilehash: af68d21184c137b2b0f1bb4c1afb9bf507e8332d
ms.contentlocale: pt-br
ms.lasthandoff: 07/26/2017

---
# <a name="replicate-hyper-v-virtual-machines-in-vmm-clouds-to-azure-using-site-recovery-in-the-azure-portal"></a>Replicar máquinas virtuais Hyper-V em nuvens VMM no Azure usando o Site Recovery no Portal do Azure
> [!div class="op_single_selector"]
> * [Portal do Azure](site-recovery-vmm-to-azure.md)
> * [Azure clássico](site-recovery-vmm-to-azure-classic.md)
> * [Implantação do Resource Manager do PowerShell](site-recovery-vmm-to-azure-powershell-resource-manager.md)
> * [Implantação clássica do PowerShell](site-recovery-deploy-with-powershell.md)


Este artigo fornece uma visão geral das etapas necessárias para a replicação de máquinas virtuais (VMs) do Hyper-V locais gerenciadas em nuvens do System Center VMM (Virtual Machine Manager) o azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.

Depois de ler este artigo, poste comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="step-1-review-the-scenario-architecture"></a>Etapa 1: Analisar a arquitetura do cenário

Antes de iniciar a implantação, examine a arquitetura do cenário e entenda todos os componentes que você precisa implantar.

Ir para a [Etapa 1: Examinar a arquitetura](vmm-to-azure-walkthrough-architecture.md)

## <a name="step-2-review-prerequisites-and-limitations"></a>Etapa 2: Analisar os pré-requisitos e as limitações

Certifique-se de que você compreende os pré-requisitos e as limitações da implantação.

**Pré-requisitos do Azure**: você precisa de uma conta do Microsoft Azure, redes do Azure e contas de armazenamento.
**Servidores do VMM no local e os hosts do Hyper-V**: verifique se os servidores VMM e os hosts do Hyper-V são compatíveis e se estão preparados para a implantação do Site Recovery.
**VMs replicadas**: verifique se as VMs que você deseja replicar atendem aos requisitos do Azure.

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](vmm-to-azure-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Etapa 3: Planejar a capacidade

Se você estiver fazendo uma implantação completa, precisará descobrir quais recursos de replicação serão necessários. Há algumas ferramentas disponíveis para ajudá-lo a fazer isso. Se estiver fazendo uma configuração rápida para testar o ambiente, ignore esta etapa.

Ir para a [Etapa 3: Planejar a capacidade](vmm-to-azure-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Etapa 4: Planejar a rede

Você precisa planejar para assegurar a configuração do mapeamento de rede ao implantar o cenário, a conexão das VMs do Azure a redes virtuais do Azure após failover e a atribuição dos endereços IP apropriados.

Ir para a [Etapa 4: Planejar a rede](vmm-to-azure-walkthrough-network.md)


## <a name="step-5-prepare-azure-resources"></a>Etapa 5: Preparar os recursos do Azure

Configure uma conta, redes e armazenamento do Azure. Você pode fazer isso durante a implantação, mas recomendamos fazer isso antes de começar.

Ir para a [Etapa 5: Preparar o Azure](vmm-to-azure-walkthrough-prepare-azure.md)

## <a name="step-6-prepare-vmm-and-hyper-v"></a>Etapa 6: Preparar o VMM e o Hyper-V

Prepare os servidores do VMM locais e os hosts do Hyper-V para implantação do Site Recovery.

Vá para a [Etapa 6: Preparar servidores locais](vmm-to-azure-walkthrough-vmm-hyper-v.md)

## <a name="step-7-set-up-a-vault"></a>Etapa 7: Configurar um cofre

Configurar um cofre dos Serviços de Recuperação. O cofre contém definições de configuração e orquestra a replicação.

[Etapa 7: Configurar um cofre](vmm-to-azure-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Etapa 8: Definir as configurações de origem e destino

Configure os locais de replicação de origem e de destino. Adicione o servidor do VMM ao cofre e baixe os arquivos de instalação de componentes do Site Recovery. Execute a instalação do Provedor do Azure Site Recovery no servidor do VMM. O Provedor é instalado no servidor do VMM e registra o servidor no cofre. Você pode instalar o agente de Serviços de Recuperação da Microsoft em cada host do Hyper-V.

Vá para a [Etapa 8: Definir as configurações de origem e destino](vmm-to-azure-walkthrough-source-target.md)

## <a name="step-9-configure-network-mapping"></a>Etapa 9: Configurar o mapeamento de rede

Mapear redes de VM do VMM locais para redes virtuais do Azure. Após o failover, as VMs do Azure são criadas na rede do Azure que é mapeada para a rede de VM local na qual a VM do Hyper-V de origem está localizada.

Vá para a [Etapa 9: Configurar o mapeamento de rede](vmm-to-azure-walkthrough-network-mapping.md)


## <a name="step-10-set-up-a-replication-policy"></a>Etapa 10: Configurar uma política de replicação

Especifique como as VMs locais serão replicadas no Azure.

Vá para a [Etapa 10: Configurar uma política de replicação](vmm-to-azure-walkthrough-replication.md)


## <a name="step-11-enable-replication-for-vms"></a>Etapa 11: Habilitar a replicação para VMs

Selecione as VMs que você deseja replicar. Habilitar uma VM para replicação dispara a replicação inicial no Azure, seguida pela replicação delta em andamento.

Vá para a [Etapa 11: Habilitar a replicação](vmm-to-azure-walkthrough-enable-replication.md)


## <a name="step-12-run-a-test-failover"></a>Etapa 12: Executar um failover de teste

Execute um failover de teste para verificar se tudo está funcionando como esperado.

Vá para a [Etapa 12: Executar um failover de teste](vmm-to-azure-walkthrough-test-failover.md)



