---
title: "Rever a arquitetura para replicação de VMware no Azure | Microsoft Docs"
description: "Este artigo fornece uma visão geral dos componentes e da arquitetura usada ao replicar VMs VMware locais para o Azure com o serviço Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27.017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 2bbab5f1ac0efe9632ad6c818504584e2503cf15
ms.contentlocale: pt-br
ms.lasthandoff: 09/27/2017

---

# <a name="step-1-review-the-architecture-for-vmware-replication-to-azure"></a>Etapa 1: rever a arquitetura para replicação do VMware no Azure

Este artigo descreve os componentes e processos usados para replicar máquinas virtuais VMware locais para o Azure usando o serviço [Azure Site Recovery](site-recovery-overview.md).

Publique eventuais comentários no final deste artigo ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="architectural-components"></a>Componentes de arquitetura

A tabela resume os componentes necessários.

**Componente** | **Requisito** | **Detalhes**
--- | --- | ---
**As tabelas** | Você precisa de uma assinatura do Azure, uma conta de armazenamento do Azure e uma rede do Azure. | Os dados replicados são colocados na conta de armazenamento. As VMs do Azure são criadas com os dados replicados quando ocorre failover do site local. As VMs do Azure se conectam à rede virtual do Azure quando são criadas.
**Servidor de configuração** | Um único servidor de gerenciamento local (VM VMware) que executa todos os componentes locais do Site Recovery para a implantação. Isso inclui um servidor de configuração, um servidor em processo e um servidor de destino mestre. | O componente de servidor de configuração coordena a comunicação entre o ambiente local e o Azure e gerencia a replicação de dados.
 **Servidor de processo**:  | Instalado por padrão no servidor de configuração. | Atua como um gateway de replicação. Recebe dados de replicação, otimiza-os com caching, compactação e criptografia e os envia para o Armazenamento do Azure.<br/><br/> O servidor de processo também manipula a instalação por push do Serviço de mobilidade em computadores protegidos e executa a descoberta automática de máquinas virtuais VMware.<br/><br/> À medida que a implantação cresce, você pode adicionar outros servidores de processo dedicados separados para lidar com o aumento do volume de tráfego de replicação.
 **Servidor de destino mestre** | Instalado por padrão no servidor de configuração local. | Lida com os dados de replicação durante o failback do Azure.<br/><br/> Se os volumes de tráfego de failback forem altos, você poderá implantar um servidor de destino mestre separado para failback.
**Servidores VMware** | VMs VMware são hospedadas em servidores ESXi vSphere, e recomendamos que um servidor vCenter gerencie os hosts. | Adicione servidores VMware ao seu cofre dos Serviços de Recuperação.
**Computadores replicados** | O serviço de Mobilidade será instalado em cada VM VMware que você replicar. Ele pode ser instalado manualmente em cada computador ou com uma instalação por push do servidor de processo.

**Figura 1: VMware para componentes do Azure**

![Componentes](./media/vmware-walkthrough-architecture/arch-enhanced.png)

## <a name="replication-process"></a>Processo de replicação

1. Configure a implantação, incluindo os componentes locais e os componentes do Azure. No cofre dos Serviços de Recuperação, você especifica a origem e o destino da replicação, configura o servidor de configuração, cria uma política de replicação, implanta o Serviço de mobilidade, habilita a replicação e executa um failover de teste.
2. Os computadores são replicados de acordo com a política de replicação e uma cópia inicial dos dados é replicada para o armazenamento do Azure.
3. Após a conclusão da replicação inicial, a replicação de alterações delta para o Azure é iniciada. As alterações acompanhadas para uma máquina são mantidas em um arquivo .hrl.
    - As máquinas que estão sendo replicadas se comunicam com o servidor de configuração na porta HTTPS 443 de entrada para o gerenciamento de replicação.
    - Os computadores que estão sendo replicados enviam dados de replicação para o servidor em processo na porta HTTPS 9443 de entrada (pode ser modificada).
    - O servidor de configuração coordena o gerenciamento de replicação com o Azure pela porta HTTPS 443 de saída.
    - O servidor de processo recebe dados de máquinas de origem, otimiza-os e criptografa-os e os envia para o armazenamento do Azure pela porta 443 de saída.
    - Se você habilitar a consistência de várias VMS, as máquinas no grupo de replicação se comunicarão entre si pela porta 20004. Várias VMS serão usadas se você agrupar vários computadores em grupos de replicação que compartilham pontos de recuperação consistentes com o aplicativo e com falhas ao realizar o failover. Isso é útil se os computadores estão executando a mesma carga de trabalho e precisam ser consistentes.
4. O tráfego é replicado para pontos de extremidade públicos do armazenamento do Azure, pela Internet. Como alternativa, você pode usar o [emparelhamento público](../expressroute/expressroute-circuit-peerings.md#azure-public-peering) do Azure ExpressRoute. Não há suporte para a replicação do tráfego através de uma VPN de site a site de um site local para o Azure.


**Figura 2: replicação do VMware para o Azure**

![Avançado](./media/vmware-walkthrough-architecture/v2a-architecture-henry.png)

## <a name="failover-and-failback-process"></a>Processo de failover e failback

1. Depois de verificar que o teste de failover está funcionando conforme o esperado, execute failovers não planejados para o Azure conforme o necessário. Não há suporte para failover planejado.
2. Faça o failover de um único computador, ou crie [planos de recuperação](site-recovery-create-recovery-plans.md) para realizar o failover de várias VMs.
3. Ao executar um failover, VMs de réplica são criadas no Azure. Você confirma um failover para começar a acessar a carga de trabalho por meio da VM do Azure de réplica.
4. Quando o site primário local estiver disponível novamente, você poderá executar o failback. Você configura uma infraestrutura de failback, inicia a replicação da máquina do site secundário para o primário e executa um failover não planejado do site secundário. Depois que você confirmar esse failover, os dados estarão novamente no local e será necessário habilitar a replicação no Azure de novo. [Saiba mais](site-recovery-failback-azure-to-vmware.md)

Há alguns requisitos de failback:


- **Servidor de processo temporário no Azure**: se você deseja fazer failback do Azure após o failover, precisa criar uma VM do Azure configurada como um servidor de processo para lidar com a replicação do Azure. Você pode excluir a VM após a conclusão do failback.
- **Conexão de VPN**: em caso de failback, você precisará de uma conexão de VPN (ou Azure ExpressRoute) configurada da rede do Azure para o site local.
- **Servidor de destino mestre separado local**: o servidor de destino mestre local lida com o failback. O servidor de destino mestre está instalado por padrão no servidor de gerenciamento, mas se você estiver fazendo failback de volumes de tráfego maiores, deverá configurar um servidor de destino mestre separado local com essa finalidade.
- **Política de failback**: para replicar volta para seu site local, você precisa de uma política de failback. Isso é criado automaticamente quando você cria sua política de replicação.
- **Infraestrutura VMware**: você deve fazer failback para uma VM VMware local. Isso significa que você precisa de uma infraestrutura VMware no local, mesmo se estiver replicando servidores físicos do local para o Azure.

**Figura 3: failback físico/de VMware**

![Failback](./media/vmware-walkthrough-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Próximas etapas

Ir para a [Etapa 2: Verificar os pré-requisitos e as limitações](vmware-walkthrough-prerequisites.md)

