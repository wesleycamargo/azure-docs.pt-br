---
title: Preparar o ambiente de destino para replicação do VMware no Azure | Microsoft Docs
description: Este artigo descreve como preparar o ambiente de destino do Azure para replicação de uma VM do VMware no Azure.
services: site-recovery
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: bsiva
ms.openlocfilehash: 4b428dff1cebf353cc081696649494e6e4ec9b92
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2018
ms.locfileid: "37921103"
---
# <a name="prepare-the-target-environment-for-vmware-replication-to-azure"></a>Preparar o ambiente de destino para replicação do VMware no Azure

Este artigo descreve como configurar o ambiente de destino do Azure para iniciar a replicação de máquinas virtuais do VMware no Azure.

## <a name="prerequisites"></a>pré-requisitos

Este artigo supõe que:
- Você criou um Cofre dos Serviços de Recuperação para proteger suas máquinas virtuais VMware. Você pode criar um Cofre dos Serviços de Recuperação no [Portal do Azure](http://portal.azure.com "Portal do Azure").
- Você [configurou seu ambiente local](vmware-azure-set-up-source.md) para replicar máquinas virtuais VMware no Azure.

## <a name="prepare-target"></a>Preparar o destino

Depois de concluir a **Etapa 1: Selecionar meta de proteção** e a **Etapa 2: Preparar origem**, você segue para a **Etapa 3: Destino**

![Preparar o destino](./media/vmware-azure-set-up-target/prepare-target-vmware-to-azure.png)

1. **Assinatura:** no menu suspenso, escolha a assinatura na qual você quer replicar suas máquinas virtuais.
2. **Modelo de Implantação:** escolha o modelo de implantação (Clássico ou Resource Manager)

Com base no modelo de implantação escolhido, uma validação é executada para garantir que você tenha pelo menos uma conta de armazenamento compatível e a rede virtual na assinatura de destino na qual replicar e fazer failover da máquina virtual.

Depois que as validações são concluídas com êxito, clique em OK de modo a passar para a próxima etapa.

Caso não tenha uma rede virtual ou conta de armazenamento do Resource Manager compatível, crie uma clicando nos botões **+ Conta de Armazenamento** ou **+ Rede** na parte superior da página.

## <a name="next-steps"></a>Próximas etapas
[Definir configurações de replicação](vmware-azure-set-up-replication.md).
