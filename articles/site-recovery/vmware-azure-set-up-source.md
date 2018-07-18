---
title: Configurar o ambiente de origem para replicação de VMware no Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como configurar seu ambiente local para replicar máquinas virtuais VMware no Azure com o Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: 1380c1bc820a815fae317a86fcd0ee4f46dd9aa5
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/10/2018
ms.locfileid: "37952647"
---
# <a name="set-up-the-source-environment-for-vmware-to-azure-replication"></a>Configurar o ambiente de origem para replicação de VMware no Azure

Este artigo descreve como configurar seu ambiente local de origem para replicar máquinas virtuais VMware no Azure. Ele inclui etapas para selecionar o cenário de replicação, configuração de um computador local como o servidor de configuração de Recuperação de Site, e descobrir automaticamente as VMs locais. 

## <a name="prerequisites"></a>pré-requisitos

O artigo supõe que você já tenha:
- [Configurar recursos](tutorial-prepare-azure.md) no [portal do Azure](http://portal.azure.com).
- [Configurar o VMware local](vmware-azure-tutorial-prepare-on-premises.md), incluindo uma conta dedicada para a descoberta automática.



## <a name="choose-your-protection-goals"></a>Escolher as metas de proteção

1. No Portal do Azure, vá até a folha do cofre de **Serviços de Recuperação** e selecione seu cofre.
2. No menu de recursos do cofre, clique em **Introdução** > **Site Recovery** > **Etapa 1: Preparar Infraestrutura** > **Meta de proteção**.

    ![Escolher metas](./media/vmware-azure-set-up-source/choose-goals.png)
3. Em **Meta de proteção**, selecione **Para o Azure** e escolha **Sim, com o Hipervisor VMware vSphere**. Em seguida, clique em **OK**.

    ![Escolher metas](./media/vmware-azure-set-up-source/choose-goals2.png)

## <a name="set-up-the-configuration-server"></a>Configurar o servidor de configuração

Você pode configurar o servidor de configuração como uma VM do VMware local por meio de um modelo de OVA (Open Virtualization Application). [Saiba mais](concepts-vmware-to-azure-architecture.md) sobre os componentes que serão instalados na VM do VMware.

1. Saiba mais sobre os [pré-requisitos](vmware-azure-deploy-configuration-server.md#prerequisites) para implantação de servidor de configuração.
2. [Verificar números de capacidade](vmware-azure-deploy-configuration-server.md#capacity-planning) para implantação.
3. [Baixar](vmware-azure-deploy-configuration-server.md#download-the-template) e [importar](vmware-azure-deploy-configuration-server.md#import-the-template-in-vmware) o modelo OVA para configurar um VM do VMware local que executa o servidor de configuração. A licença fornecida com o modelo OVA é uma licença de avaliação e tem validade de 180 dias. Após esse período, o cliente precisa ativar o Windows com uma licença adquirida.
4. Ativar a VM do VMware e [registre-o](vmware-azure-deploy-configuration-server.md#register-the-configuration-server-with-azure-site-recovery-services) no cofre dos Serviços de Recuperação.


## <a name="add-the-vmware-account-for-automatic-discovery"></a>Adicionar a conta VMware para descoberta automática

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

## <a name="connect-to-the-vmware-server"></a>Conectar ao servidor do VMware

Para permitir que o Azure Site Recovery descubra máquinas virtuais em execução no ambiente local, você precisa conectar seu VMware vCenter Server ou hosts vSphere ESXi ao Site Recovery.

Selecione **+vCenter** para iniciar a conexão de um VMware vCenter Server ou um host VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]


## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Próximas etapas
[Configurar o ambiente de destino](./vmware-azure-set-up-target.md) no Azure.
