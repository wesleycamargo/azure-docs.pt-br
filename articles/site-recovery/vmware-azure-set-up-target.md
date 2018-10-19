---
title: Preparar o ambiente de destino (VMware/Servidor Físico para Azure) | Microsoft Docs
description: Este artigo descreve como preparar o ambiente de destino do Azure para replicação de uma VM do VMware e replicação de Servidores Físicos no Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 09/28/2018
ms.author: bsiva
ms.openlocfilehash: 948812f05697362978ad041566d22977efec92a1
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47434629"
---
# <a name="prepare-the-target-environment-vmwarephysical-to-azure"></a>Preparar o ambiente de destino (VMware/Servidor Físico para Azure)

Este artigo descreve como configurar o ambiente de destino do Azure para iniciar a replicação de máquinas virtuais do VMware ou de servidores físicos no Azure.

## <a name="prerequisites"></a>Pré-requisitos

Este artigo supõe que:
- Você criou um cofre dos Serviços de Recuperação no [portal do Azure](http://portal.azure.com "portal do Azure") para proteger seus computadores de origem
- Você configurou seu ambiente local para replicar as [máquinas virtuais do VMware](vmware-azure-set-up-source.md) ou [servidores físicos](physical-azure-set-up-source.md) de origem no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir a **Etapa 1: Selecionar meta de proteção** e a **Etapa 2: Preparar origem**, você segue para a **Etapa 3: Destino**

![Preparar o destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** no menu suspenso, escolha a assinatura na qual deseja replicar suas máquinas virtuais ou servidores físicos.
2. **Modelo de Implantação:** escolha o modelo de implantação (Clássico ou Resource Manager)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma conta de armazenamento compatível e a rede virtual na assinatura de destino na qual replicar e fazer failover da máquina virtual ou servidor físico.

Depois que as validações são concluídas com êxito, clique em OK de modo a passar para a próxima etapa.

Caso não tenha uma rede virtual ou conta de armazenamento do Resource Manager compatível, crie uma clicando nos botões **+ Conta de Armazenamento** ou **+ Rede** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas
[Definir configurações de replicação](vmware-azure-set-up-replication.md).
