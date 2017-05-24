---
title: "O que é a Recuperação de Site? | Microsoft Docs"
description: "Fornece uma visão geral do serviço Azure Site Recovery e resume os cenários de implantação."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: e9b97b00-0c92-4970-ae92-5166a4d43b68
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/14/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 89921997f68bd72ee6086d2b635361eeeaba1c28
ms.contentlocale: pt-br
ms.lasthandoff: 05/11/2017


---
# <a name="what-is-site-recovery"></a>O que é a Recuperação de Site?

Bem-vindo ao Azure Site Recovery! Este artigo fornece uma visão geral rápida do serviço.

As interrupções são causadas por falhas operacionais e eventos naturais. Sua organização precisa de uma estratégia BCDR (recuperação de desastres continuidade de negócios) de forma que, durante o tempo de inatividade planejado e não planejado, os dados permaneçam seguros, os aplicativos permaneçam disponíveis e a empresa recupere as condições normais de trabalho assim que possível.

Os Serviços de Recuperação do Azure contribuem para sua estratégia BCDR. O serviço [Backup do Azure](https://docs.microsoft.com/en-us/azure/backup/) mantém seus dados seguros e recuperáveis. O Site Recovery replica, faz failover e recupera cargas de trabalho para que elas permaneçam disponíveis quando ocorre uma falha.

## <a name="what-does-site-recovery-provide"></a>O que o Site Recovery fornece?

- **Recuperação de desastres na nuvem** – Você pode replicar no Azure as cargas de trabalho em execução em VMs e servidores físicos em vez de fazer isso em um site secundário. Isso elimina o custo e a complexidade de manter um data center secundário.
- **Replicação flexível para ambientes híbridos** – Você pode replicar qualquer carga de trabalho em execução em VMs locais do Hyper-V e VMs do VMware com suporte, bem como em servidores físicos Windows/Linux.
- **Migração** – Você pode usar o Site Recovery para migrar instâncias AWS para VMs do Azure ou para migrar VMs do Azure entre regiões do Azure.
- **BCDR simplificado** – Você pode implantar a replicação de um único local no portal do Azure.  Você pode fazer failovers e failbacks simples de um ou de vários computadores.
- **Resiliência** – O Site Recovery orquestra a replicação e o failover sem interceptar dados de aplicativo.
Os dados replicados são armazenados no armazenamento do Azure, com toda a resiliência que ele oferece. Quando ocorrer um failover, as VMs do Azure serão criadas com base nos dados replicados.
- **Desempenho de replicação** – O Site Recovery fornece uma frequência da replicação tão baixa quanto 30 segundos para o Hyper-V, assim como a replicação contínua para o VMware. Você pode definir limites de RPO (objetivo de ponto de recuperação) para controlar a frequência com que os pontos de recuperação de dados são criados e pode reduzir o RTO (objetivo do tempo de recuperação) com o processo de recuperação automatizada do Site Recovery e a integração com o [Gerenciador de Tráfego do Azure](https://azure.microsoft.com/en-us/blog/reduce-rto-by-using-azure-traffic-manager-with-azure-site-recovery/)
- **Consistência de aplicativo** – Os computadores replicam usando instantâneos consistentes de aplicativo. Além de capturar os dados do disco, os instantâneos consistentes com os aplicativos capturam todos os dados na memória e todas as transações em andamento.
- **Testes sem interrupção** – Você pode facilmente executar failovers de teste para dar suporte à recuperação de desastre, sem afetar os ambientes de produção.
- **Failover e recuperação flexíveis** – Você pode executar failovers planejados para interrupções esperadas sem perda de dados ou failovers não planejados com perda mínima de dados (dependendo da frequência de replicação) para desastres inesperados. É possível executar facilmente o failback para o site primário quando ele estiver disponível novamente.
- **Planos de recuperação personalizados** – os planos de recuperação permitem que você modele e personalize o failover e a recuperação de aplicativos de várias camadas distribuídos por várias VMs. Ordene grupos em planos e adicione scripts e ações manuais. Os planos de recuperação podem ser integrados com runbooks de automação do Azure.
- **Aplicativos de várias camadas** – Você pode criar planos de recuperação para failover e recuperação sequenciados de aplicativos de várias camadas. Você pode agrupar computadores em diferentes camadas (por exemplo, banco de dados, Web, aplicativo) em um plano de recuperação e personalizar como cada grupo faz o failover e é iniciado.
* **Integração com as tecnologias BCDR existentes** – O Site Recovery integra-se a outras tecnologias BCDR. Por exemplo, você pode usar o Site Recovery para proteger o back-end do SQL Server das cargas de trabalho corporativas, incluindo o suporte nativo para o SQL Server AlwaysOn, a fim de gerenciar o failover dos grupos de disponibilidade.
* **Integração com a biblioteca de automação** – Uma biblioteca de Automação do Azure avançada fornece scripts prontos para a produção e específicos do aplicativo que podem ser baixados e integrados com o Site Recovery.
* **Gerenciamento de rede simples**— O gerenciamento de rede avançado no Site Recovery e no Azure simplifica os requisitos de rede do aplicativo, incluindo reservar endereços IP, configurar balanceadores de carga e integrar o Gerenciador de Tráfego do Azure para ter uma troca de rede eficiente.


## <a name="whats-supported"></a>Há suporte para quê?

**Com suporte** | **Detalhes**
--- | ---
**Quais são as regiões têm suporte para o Site Recovery?** | [Regiões com suporte](https://azure.microsoft.com/en-us/regions/services/) |
**O que posso replicar?** | VMs locais do VMware, VMs do Hyper-V e servidores físicos Windows e Linux.
**De quais sistemas operacionais os computadores replicados precisam?** | [Sistemas operacionais com suporte](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para VMs do VMware<br/><br/> Para VMs do Hyper-V, há suporte para qualquer [SO convidado](https://technet.microsoft.com/en-us/windows-server-docs/compute/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows) com suporte do Azure e do Hyper-V.<br/><br/> [Sistemas operacionais](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) para servidores físicos
**Para qual local posso replicar?** | Para o armazenamento do Azure ou um datacenter secundário<br/><br/> Para o Hyper-V, apenas as VMs nos hosts Hyper-V gerenciados em nuvens do System Center VMM podem replicar para um datacenter secundário.
**Quais servidores/hosts VMware são necessários?** | As VMs VMware que você deseja replicar podem ser gerenciadas por [hosts vSphere/servidores vCenter com suporte](site-recovery-support-matrix-to-azure.md#support-for-datacenter-management-servers)
**Quais cargas de trabalho posso replicar?** | Você pode replicar qualquer carga de trabalho em execução em um computador de replicação com suporte. Além disso, a equipe do Site Recovery realizou testes específicos do aplicativo para [alguns aplicativos](site-recovery-workload.md#workload-summary).


## <a name="which-azure-portal"></a>Qual portal do Azure?

* O Site Recovery pode ser implantado tanto no mais recente [Portal do Azure](https://portal.azure.com) quanto no [Portal Clássico do Azure](https://manage.windowsazure.com/).
* No portal clássico do Azure, você pode dar suporte ao Site Recovery com o modelo de gerenciamento de serviços clássico.
* No Portal do Azure, você pode oferecer suporte ao modelo clássico ou ao mais recente [modelo de implantação do Resource Manager](../azure-resource-manager/resource-manager-deployment-model.md).
- O portal clássico deve ser usado somente para manter as implantações do Site Recovery existentes. Não é possível criar novos cofres no portal clássico.

## <a name="next-steps"></a>Próximas etapas
* Leia mais sobre [Suporte a carga de trabalho](site-recovery-workload.md)
* Saiba mais sobre a [arquitetura e os componentes do Site Recovery](site-recovery-components.md)

