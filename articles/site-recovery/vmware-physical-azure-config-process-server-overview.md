---
title: Sobre a configuração, processo e servidores de destino mestre do Azure Site Recovery | Microsoft Docs
description: Este artigo fornece uma visão geral do processo, configuração e servidores de destino mestre usando durante a configuração de recuperação de desastre de VMs do VMware locais no Azure com o Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 78aed4fceab83d942927486cd7ae46a32dd6ca09
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64927546"
---
# <a name="about-site-recovery-components-configuration-process-master-target"></a>Sobre componentes do Site Recovery (configuração, processo, destino mestre)

Este artigo descreve a configuração, processo e os servidores de destino mestre usados ao replicar VMs VMware e servidores físicos para o Azure com o [recuperação de Site](site-recovery-overview.md) service.

## <a name="configuration-server"></a>Servidor de configuração

Recuperação de desastres de VMs do VMware locais e servidores físicos, você precisa de uma recuperação de Site local do servidor de configuração implantado.

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Componentes**  | O computador do servidor de configuração é executado em todos os componentes do Site Recovery no local, que incluem o servidor de configuração, o servidor de processo e o servidor de destino mestre.<br/><br/> Quando você configura o servidor de configuração, todos os componentes são instalados automaticamente. | [Leitura](vmware-azure-common-questions.md#configuration-server) perguntas Frequentes do servidor de configuração.
**Função** | O servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados. | Saiba mais sobre a arquitetura para a [VMware](vmware-azure-architecture.md) e [servidor físico](physical-azure-architecture.md) recuperação de desastres para o Azure.
**Requisitos da VMware** | Para recuperação de desastre de VMs do VMware local, você deve instalar e executar o servidor de configuração como um local, VM VMware altamente disponível. | [Saiba mais sobre](vmware-azure-deploy-configuration-server.md#prerequisites) os pré-requisitos.
**Implantação de VMware** | É recomendável que você implante o servidor de configuração usando um modelo OVA baixado. Esse método fornece uma simplesmente maneira de configurar um servidor de configuração que está em conformidade com todos os requisitos e pré-requisitos.<br/><br/> Se por alguma razão você não puder implantar uma VM do VMware usando um modelo OVA, você pode configurar as máquinas de servidor de configuração manualmente, conforme descrito abaixo para recuperação de desastres de máquina física. | [Implantar](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template) com um modelo OVA.
**Requisitos de servidor físico** | Recuperação de desastres em servidores físicos do local, implante o servidor de configuração manualmente. | [Saiba mais sobre](/physical-azure-set-up-source.md#prerequisites) os pré-requisitos.
**Implantação de servidor físico** | Se ele não pode ser instalado como uma VM VMware, você pode instalá-lo em um servidor físico. | [Implantar](physical-azure-set-up-source.md#set-up-the-source-environment) o servidor de configuração manualmente.


## <a name="process-server"></a>Servidor de processo

**Configuração** | **Detalhes** | **Links**
--- | --- | ---
**Implantação**  | Para recuperação de desastres e replicação de VMs do VMware locais e servidores físicos, você precisa de um servidor de processo local. Por padrão, o servidor de processo é instalado no servidor de configuração quando você implantá-lo. | [Saiba mais](vmware-azure-architecture.md?#architectural-components).
**Função (no local** | -Recebe dados de replicação de máquinas habilitadas para replicação.<br/> -Otimiza os dados de replicação com caching, compactação e criptografia e envia para o armazenamento do Azure.<br/> -Executa uma instalação por push do serviço de mobilidade do Site Recovery em VMs do VMware locais e servidores físicos que você deseja replicar.<br/> -Executa a descoberta automática de máquinas locais. | [Saiba mais](vmware-physical-azure-config-process-server-overview.md#process-server). 
**Função (failback do Azure)** | Após o failover de seu site local, você configurar um servidor de processo no Azure, como uma VM do Azure para lidar com o failback para seu local.<br/><br/> O servidor de processo no Azure é temporário. A VM do Azure pode ser excluída após a conclusão do failback. | [Saiba mais](vmware-azure-set-up-process-server-azure.md).
**Dimensionamento** | Para implantações maiores, local você pode configurar servidores de processo adicional, escalabilidade horizontal. Servidores adicionais expandir a capacidade, manipulando números maiores de replicação de máquinas e maiores volumes de tráfego de replicação.<br/><br/> Você pode mover as máquinas entre dois servidores de processo, a fim de balancear o tráfego de replicação. | [Saiba mais](vmware-azure-set-up-process-server-scale.md),


## <a name="master-target-server"></a>Servidor de destino mestre

O servidor de destino mestre lida com os dados de replicação durante o failback do Azure.

- Ele é instalado por padrão no servidor de configuração.
- Para grandes implantações, você pode adicionar um servidor de destino mestre separado adicional para failback.


## <a name="next-steps"></a>Próximas etapas
- Examine os [arquitetura](/vmware-azure-architecture.md) para recuperação de desastre de VMs VMware e servidores físicos.
- Examine os [requisitos e pré-requisitos](vmware-physical-azure-support-matrix.md) para recuperação de desastre de VMs VMware e servidores físicos no Azure. 
