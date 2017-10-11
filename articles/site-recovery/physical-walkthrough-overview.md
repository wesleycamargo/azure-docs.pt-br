---
title: "Replicar servidores físicos locais no Azure com o Azure Site Recovery | Microsoft Docs"
description: "Fornece uma visão geral das etapas para replicação de cargas de trabalho em execução em servidores físicos locais com Windows/Linux para o Azure com o serviço Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 20122f01-929a-4675-b85b-a9b99d2618bc
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 0a09b35e98dc0b2f5283c2a707a3a2b8ac9a39f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-physical-servers-to-azure-with-site-recovery"></a>Replicar servidores físicos no Azure com o Site Recovery

Este artigo fornece uma visão geral das etapas necessárias para replicar servidores físicos locais com Windows/Linux no Azure usando o serviço do [Azure Site Recovery](site-recovery-overview.md) no Portal do Azure.


## <a name="step-1-review-architecture-and-prerequisites"></a>Etapa 1: Examinar a arquitetura e os pré-requisitos

Antes de iniciar a implantação, examine a arquitetura do cenário e entenda todos os componentes que você precisa configurar na implantação.

Ir para a [Etapa 1: Examinar a arquitetura](physical-walkthrough-architecture.md)


## <a name="step-2-review-prerequisites"></a>Etapa 2: Examinar os pré-requisitos

Verifique se os pré-requisitos estão em vigor para cada componente de implantação:

- **Pré-requisitos do Azure**: você precisa de uma conta do Microsoft Azure, redes do Azure e contas de armazenamento.
- **Componentes locais do Site Recovery**: Você precisa de uma máquina que execute os componentes locais do Site Recovery.
- **Máquinas replicadas**: os servidores que você deseja replicar precisam atender aos requisitos locais e do Azure.

Acesse [Etapa 2: Examinar os pré-requisitos e as limitações](physical-walkthrough-prerequisites.md)

## <a name="step-3-plan-capacity"></a>Etapa 3: Planejar a capacidade

Se você estiver fazendo uma implantação completa, precisará descobrir quais recursos de replicação serão necessários. Se estiver fazendo uma configuração rápida para testar o ambiente, ignore esta etapa.

Ir para a [Etapa 3: Planejar a capacidade](physical-walkthrough-capacity.md)

## <a name="step-4-plan-networking"></a>Etapa 4: Planejar a rede

Você precisa fazer um planejamento de rede para garantir que as VMs do Azure serão conectadas a redes após o failover e que elas têm os endereços IP corretos.

Ir para a [Etapa 4: Planejar a rede](physical-walkthrough-network.md)

##  <a name="step-5-prepare-azure-resources"></a>Etapa 5: Preparar os recursos do Azure

Configure as redes e o armazenamento do Azure antes de começar. 

Ir para a [Etapa 5: Preparar o Azure](physical-walkthrough-prepare-azure.md)


## <a name="step-6-set-up-a-vault"></a>Etapa 6: Configurar um cofre

Configure um cofre dos Serviços de Recuperação para orquestrar e gerenciar a replicação. Ao configurar o cofre, você especifica o que deseja replicar e o local para o qual deseja replicá-lo.

Vá para a [Etapa 6: configurar um cofre](physical-walkthrough-create-vault.md)

## <a name="step-7-configure-source-and-target-settings"></a>Etapa 7: Definir as configurações de origem e destino

Definir configurações para o site de origem e de destino (Azure). As configurações de origem incluem executar a Instalação Unificada para instalar os componentes locais do Site Recovery.

Acesse a [Etapa 7: Configurar a origem e o destino](physical-walkthrough-source-target.md)

## <a name="step-8-set-up-a-replication-policy"></a>Etapa 8: Configurar uma política de replicação

Configure uma política para especificar como os servidores físicos devem replicar.

Acesse a [Etapa 8: Configurar uma política de replicação](physical-walkthrough-replication.md)

## <a name="step-9-install-the-mobility-service"></a>Etapa 9: instalar o Serviço de mobilidade manualmente

O serviço de mobilidade deve ser instalado em cada servidor que você deseja replicar. Há algumas maneiras de configurar o serviço com a instalação por push ou pull.

Ir para a [Etapa 9: Instalar o Serviço de mobilidade](physical-walkthrough-install-mobility.md)

## <a name="step-10-enable-replication"></a>Etapa 10: Habilitar a replicação

Depois que o serviço de Mobilidade estiver em execução em um servidor, você pode habilitar a replicação para ele. Depois de habilitar, a replicação inicial da VM ocorre.

Acesse a [Etapa 10: Habilitar a replicação](physical-walkthrough-enable-replication.md)

## <a name="step-11-run-a-test-failover"></a>Etapa 11: Executar um failover de teste

Após a conclusão da replicação inicial e a replicação delta estiver em execução, você poderá executar um failover de teste para verificar se tudo está funcionando como esperado.

Acesse a [Etapa 11: Executar um failover de teste](physical-walkthrough-test-failover.md)

