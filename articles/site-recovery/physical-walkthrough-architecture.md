---
title: "Examinar a arquitetura para a replicação de servidor físico para o Azure usando o Azure Site Recovery | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usados ao replicar servidores físicos locais para o Azure com o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 09c9b136-35f5-465e-8d0f-f4c5d5d9f880
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: Human Translation
ms.sourcegitcommit: 138f04f8e9f0a9a4f71e43e73593b03386e7e5a9
ms.openlocfilehash: 843c3f1b54f50fe50b162ed242deab717a080830
ms.contentlocale: pt-br
ms.lasthandoff: 06/29/2017

---

# <a name="step-1-review-the-architecture-for-physical-server-replication-to-azure"></a>Etapa 1: Examinar a arquitetura para a replicação de servidor físico para o Azure

Este artigo descreve os componentes e os processos usados ao replicar servidores físicos Windows/Linux locais para o Azure, usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela resume os componentes necessários.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Você precisa de uma conta do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados são armazenados na conta de armazenamento e as VMs do Azure são criadas com os dados replicados quando ocorre o failover. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Um único servidor de gerenciamento local (servidor físico ou VM do VMware) que executa todos os componentes locais do Site Recovery. Isso inclui um servidor de configuração, um servidor em processo e um servidor de destino mestre. | O componente de servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
 **Servidor de processo**  | Instalado por padrão no servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor em processo também manipula a instalação por push do Serviço de mobilidade nos computadores protegidos.<br/><br/> Você pode adicionar outros servidores em processo dedicados separados para lidar com o aumento do volume do tráfego de replicação.
 **Servidor de destino mestre** | Instalado por padrão no servidor de configuração local. | Lida com os dados de replicação durante o failback do Azure.<br/><br/> Se os volumes de tráfego de failback forem altos, você poderá implantar um servidor de destino mestre separado para failback.
**Servidores replicados** | O componente do Serviço de mobilidade é instalado em cada servidor Windows/Linux que você deseja replicar. Ele pode ser instalado manualmente em cada computador ou com uma instalação por push do servidor de processo.
**Failback** | Para o failback, uma infraestrutura do VMware é necessária. Você não pode realizar o failback para um servidor físico.


**Figura 1: Componentes do Físico para o Azure**

![Componentes](./media/physical-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação, implanta o Serviço de mobilidade, habilita a replicação e executa um failover de teste.
2.  Os computadores são replicados de acordo com a política de replicação e uma cópia inicial dos dados é replicada para o armazenamento do Azure.
4. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
    - As máquinas que estão sendo replicadas se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - Os computadores que estão sendo replicados enviam dados de replicação para o servidor em processo na porta HTTPS 9443 de entrada (pode ser modificada).
    - O servidor de configuração coordena o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
    - O servidor de processo recebe dados de máquinas de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure pela porta 443 de saída.
    - Se você habilitar a consistência de várias VMS, as máquinas no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Isso é útil se os computadores estão executando a mesma carga de trabalho e precisam ser consistentes.
5. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#public-peering) do Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.

**Figura 2: Replicação do Físico para o Azure**

![Avançado](./media/physical-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

Depois de verificar que o teste de failover está funcionando conforme o esperado, execute failovers não planejados para o Azure conforme o necessário. Ao executar um failover, as VMs do Azure são criadas com base nos dados replicados. Depois, quando o site primário local estiver disponível novamente, você poderá fazer failback. Observe que:

- Não há suporte para failover planejado.
- Faça failover de um único computador ou crie [planos de recuperação](site-recovery-create-recovery-plans.md) para fazer failover de vários computadores juntos.
- Você confirma um failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
- Quando o site primário estiver disponível novamente, replique o computador do site secundário para o primário. Depois, execute um failover não planejado do site secundário. Depois que você confirmar esse failover, os dados estarão novamente no local e será necessário habilitar a replicação no Azure de novo.

Os componentes do failback incluem:

- **Servidor em processo temporário no Azure**: você precisa configurar uma VM do Azure para atuar como um servidor em processo, a fim de manipular a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão VPN**: você precisa de uma conexão VPN (ou do Azure ExpressRoute) da rede do Azure com o site local.
- **Servidor de destino mestre local separado**: o servidor de destino mestre local (instalado por padrão no servidor de configuração) manipula o failback. Se você fizer failback de grandes volumes de tráfego, deverá configurar um servidor de destino mestre local separado para essa finalidade.
- **Política de failback**: você precisa de uma política de failback. Isso é criado automaticamente quando você cria sua política de replicação.
- **Infraestrutura VMware**: você deve fazer failback para uma VM VMware local. Isso significa que você precisa de uma infraestrutura VMware no local, mesmo se estiver replicando servidores físicos do local para o Azure.

**Figura 3: Failback de servidor físico**

![Failback](./media/physical-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](physical-walkthrough-prerequisites.md)

