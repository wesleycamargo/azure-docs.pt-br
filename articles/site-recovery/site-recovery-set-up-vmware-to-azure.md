---
title: Configurar o ambiente de origem (VMware para Azure) | Microsoft Docs
description: "Este artigo descreve como configurar seu ambiente local para iniciar a replicação de máquinas virtuais VMware no Azure."
services: site-recovery
author: AnoopVasudavan
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/22/2018
ms.author: anoopkv
ms.openlocfilehash: 2b6b0e5cc95f28b5596e7e898f5f035e3647d9c5
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/07/2018
---
# <a name="set-up-the-source-environment-vmware-to-azure"></a>Configurar o ambiente de origem (VMware para Azure)
> [!div class="op_single_selector"]
> * [VMware no Azure](./site-recovery-set-up-vmware-to-azure.md)
> * [Físico para Azure](./site-recovery-set-up-physical-to-azure.md)

Este artigo descreve como configurar sua origem, ambiente local, para replicar máquinas virtuais que são executadas no VMware para o Azure. Ele inclui etapas para selecionar o cenário de replicação, configuração de um computador local como o servidor de configuração de Recuperação de Site, e descobrir automaticamente as VMs locais. 

## <a name="prerequisites"></a>pré-requisitos

O artigo supõe que você já tenha:
- [Configurar recursos](tutorial-prepare-azure.md) no [portal do Azure](http://portal.azure.com).
- [Configurar o VMware local](tutorial-prepare-on-premises-vmware.md), incluindo uma conta dedicada para a descoberta automática.



## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. No Portal do Azure, vá até a folha do cofre de **Serviços de Recuperação** e selecione seu cofre.
2. No menu de recursos do cofre, clique em **Introdução** > **Site Recovery** > **Etapa 1: Preparar Infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/site-recovery-set-up-vmware-to-azure/choose-goals.png)
3. Em **Meta de proteção**, selecione **Para o Azure** e escolha **Sim, com o Hipervisor VMware vSphere**. Em seguida, clique em **OK**.

    ![Escolher metas](./media/site-recovery-set-up-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Você configura o servidor de configuração como uma VM do VMware local, use um modelo de OVF (Open Virtualization Format). [Saiba mais](concepts-vmware-to-azure-architecture.md) sobre os componentes que serão instalados na VM do VMware. 

1. Saiba mais sobre os [pré-requisitos](how-to-deploy-configuration-server.md#prerequisites) para implantação de servidor de configuração. [Verificar números de capacidade](how-to-deploy-configuration-server.md#capacity-planning) para implantação.
2. [Baixar](how-to-deploy-configuration-server.md#download-the-template) e [importar](how-to-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVF (how-to-deploy-configuration-server.md) para configurar um VM do VMware local que executa o servidor de configuração.
3. Ativar a VM do VMware e [registre-o](how-to-deploy-configuration-server.md#register-the-configuration-server) no cofre dos Serviços de Recuperação.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Adicionar a conta VMware para descoberta automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Conectar ao servidor do VMware

Para permitir que o Azure Site Recovery descubra máquinas virtuais em execução no ambiente local, você precisa conectar seu VMware vCenter Server ou hosts vSphere ESXi ao Site Recovery.

Selecione **+vCenter** para iniciar a conexão de um VMware vCenter Server ou um host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas
[Configurar o ambiente de destino](./site-recovery-prepare-target-vmware-to-azure.md) no Azure.
