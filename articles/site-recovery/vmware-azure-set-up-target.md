---
title: Preparar o ambiente de destino para replicação do VMware no Azure | Microsoft Docs
description: Este artigo descreve como preparar o ambiente de destino do Azure para replicação de uma VM do VMware no Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: cbf9c1a49481de49fb8d9c6599acec7bc3600df3
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/04/2018
ms.locfileid: "52837504"
---
# <a name="prepare-the-target-environment-for-disaster-recovery-of-vmware-vms-or-physical-servers-to-azure"></a>Preparar o ambiente de destino para recuperação de desastre de VMs VMware ou servidores físicos para o Azure

Este artigo descreve como configurar o ambiente de destino do Azure para iniciar a replicação de máquinas virtuais do VMware ou de servidores físicos no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que:
- Você criou um cofre dos Serviços de Recuperação no [portal do Azure](http://portal.azure.com "portal do Azure") para proteger seus computadores de origem
- Você configurou seu ambiente local para replicar as [máquinas virtuais do VMware](vmware-azure-set-up-source.md) ou [servidores físicos](physical-azure-set-up-source.md) de origem no Azure.

## <a name="prepare-target"></a>Preparar o destino

Após concluir a **Etapa 1: Selecione a meta de proteção** e **Etapa 2: Preparar a Fonte**, você será direcionado para a **Etapa 3: Destino**

![Preparar o destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** No menu suspenso, selecione a Assinatura na qual você quer replicar as máquinas virtuais ou os servidores físicos.
2. **Modelo de Implantação:** Selecione o modelo de implantação (Clássico ou Resource Manager)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma conta de armazenamento compatível e a rede virtual na assinatura de destino na qual replicar e fazer failover da máquina virtual ou servidor físico.

Depois que as validações são concluídas com êxito, clique em OK de modo a passar para a próxima etapa.

Caso não tenha uma rede virtual ou conta de armazenamento do Resource Manager compatível, crie uma clicando nos botões **+ Conta de Armazenamento** ou **+ Rede** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas
[Definir configurações de replicação](vmware-azure-set-up-replication.md).
