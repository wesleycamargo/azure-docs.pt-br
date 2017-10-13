---
title: Replicar VMs do VMware para o Azure com o Azure Site Recovery | Microsoft Docs
description: "Fornece uma visão geral das etapas para replicar as cargas de trabalho em execução em VMs do VMware no Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: db6f5f95929503e82a529dba26b56af1edb0767f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="replicate-vmware-vms-to-azure-with-site-recovery"></a>Replicar VMs do VMware no Azure com o Site Recovery

Este artigo fornece uma visão geral das etapas necessárias para replicar máquinas virtuais locais do VMware para o Azure, usando o serviço do [Azure Site Recovery](site-recovery-overview.md) no portal do Azure.


![Processo de implantação](./media/vmware-walkthrough-overview/vmware-to-azure-process.png)

**Figura 1: Resumo do processo de implantação**

## <a name="step-1-review-architecture-and-prerequisites"></a>Etapa 1: Examinar a arquitetura e os pré-requisitos

Antes de iniciar a implantação, examine a arquitetura do cenário e entenda todos os componentes que você precisa implantar

Ir para a [Etapa 1: Examinar a arquitetura](vmware-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Etapa 2: Examinar os pré-requisitos

Verifique se os pré-requisitos estão em vigor para cada componente de implantação:

- **Pré-requisitos do Azure**: você precisa de uma conta do Microsoft Azure, redes do Azure e contas de armazenamento.
- **Componentes locais do Site Recovery**: Você precisa de uma máquina que execute os componentes locais do Site Recovery.
- **Pré-requisitos do VMware local**: Você precisa configurar as contas para que o Site Recovery possa acessar os servidores e VMs do VMware.
- **VMs replicadas**: As VMs que você deseja replicar precisam atender aos requisitos do Azure e ter o componente de serviço de Mobilidade instalado.

[Etapa 2: Examinar os pré-requisitos e as limitações](vmware-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Etapa 3: Planejar a capacidade

Se você estiver fazendo uma implantação completa, precisará descobrir quais recursos de replicação serão necessários. Há algumas ferramentas disponíveis para ajudá-lo a fazer isso. Vá para a Etapa 2. Se estiver fazendo uma configuração rápida para testar o ambiente, ignore esta etapa.

Ir para a [Etapa 3: Planejar a capacidade](vmware-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Etapa 4: Planejar a rede

Você precisa fazer um planejamento de rede para garantir que as VMs do Azure serão conectadas a redes após o failover e que elas têm os endereços IP corretos.

Ir para a [Etapa 4: Planejar a rede](vmware-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Etapa 5: Preparar os recursos do Azure

Configure as redes e o armazenamento do Azure antes de começar. Você pode fazer isso durante a implantação, mas recomendamos fazer isso antes de começar.

Ir para a [Etapa 5: Preparar o Azure](vmware-walkthrough-prepare-azure.md)


## <a name="step-6-prepare-vmware"></a>Etapa 6: Preparar o VMware

Você precisa configurar contas que o Site Recovery usará para:

- Acessar os servidores de virtualização do VMware para detectar automaticamente as VMs.
- Acessar as VMs para instalar o serviço de Mobilidade. Cada VM que você deseja replicar deve ter o agente de serviço de Mobilidade instalado antes de habilitar a replicação para ele.

Vá para a [Etapa 6: Preparar o VMware](vmware-walkthrough-prepare-vmware.md)

## <a name="step-7-set-up-a-vault"></a>Etapa 7: Configurar um cofre

Você precisa configurar um cofre dos Serviços de Recuperação para orquestrar e gerenciar a replicação. Ao configurar o cofre, você especifica o que deseja replicar e o local para o qual deseja replicá-lo.

Vá para a [Etapa 7: Configurar um cofre](vmware-walkthrough-create-vault.md)

## <a name="step-8-configure-source-and-target-settings"></a>Etapa 8: Definir as configurações de origem e destino

Configure a origem e o destino usados para a replicação. Definir as configurações de origem inclui executar a Instalação Unificada para instalar os componentes locais do Site Recovery.

Ir para a [Etapa 8: Configurar a origem e o destino](vmware-walkthrough-source-target.md)

## <a name="step-9-set-up-a-replication-policy"></a>Etapa 9: Definir uma política de replicação

Configure uma política para especificar as configurações de replicação das VMs do VMware no cofre.

Vá para a [Etapa 9: Configurar uma política de replicação](vmware-walkthrough-replication.md)

## <a name="step-10-install-the-mobility-service"></a>Etapa 10: Instalar o serviço de Mobilidade

O serviço de Mobilidade deve ser instalado em cada VM que você deseja replicar. Há algumas maneiras de configurar o serviço com a instalação por push ou pull.

Vá para a [Etapa 10: Instalar o serviço de Mobilidade](vmware-walkthrough-install-mobility.md)

## <a name="step-11-enable-replication"></a>Etapa 11: Habilitar a replicação

Depois que o serviço de Mobilidade estiver em execução em uma VM, você pode habilitar a replicação para ele. Depois de habilitar, a replicação inicial da VM ocorre.

Vá para a [Etapa 11: Habilitar a replicação](vmware-walkthrough-enable-replication.md)

## <a name="step-12-run-a-test-failover"></a>Etapa 12: Executar um failover de teste

Após a conclusão da replicação inicial e a replicação delta estiver em execução, você poderá executar um failover de teste para verificar se tudo está funcionando como esperado.

Vá para a [Etapa 12: Executar um failover de teste](vmware-walkthrough-test-failover.md)
