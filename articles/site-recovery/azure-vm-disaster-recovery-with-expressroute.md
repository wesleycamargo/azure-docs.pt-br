---
title: Usar o ExpressRoute com a recuperação de desastre de máquina virtual do Azure | Microsoft Docs
description: Descreve como usar o Azure ExpressRoute com o Azure Site Recovery para recuperação de desastre de máquina virtual do Azure
services: site-recovery
documentationcenter: ''
author: mayanknayar
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/11/2018
ms.author: manayar
ms.openlocfilehash: 44ecbcc51cb53f4d7b68f5c5e24e7d81c5a4208c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/11/2018
ms.locfileid: "34071580"
---
# <a name="using-expressroute-with-azure-virtual-machine-disaster-recovery"></a>Usar o ExpressRoute com a recuperação de desastre de máquina virtual do Azure

O Microsoft Azure ExpressRoute permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada, facilitada por um provedor de conectividade. Este artigo descreve como usar o ExpressRoute com o Site Recovery para a recuperação de desastre de máquinas virtuais do Azure.

## <a name="prerequisites"></a>pré-requisitos

Antes de começar, verifique se você compreende:
-   Os [circuitos](../expressroute/expressroute-circuit-peerings.md) do ExpressRoute
-   Os [domínios de roteamento](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains) do ExpressRoute
-   A [arquitetura de replicação](azure-to-azure-architecture.md) da máquina virtual do Azure
-   A [configuração da replicação](azure-to-azure-tutorial-enable-replication.md) nas máquinas virtuais do Azure
-   O [failover](azure-to-azure-tutorial-failover-failback.md) das máquinas virtuais do Azure

## <a name="expressroute-and-azure-virtual-machine-replication"></a>Replicação no ExpressRoute e na máquina virtual do Azure

Quando as máquinas virtuais do Azure são protegidas com o Site Recovery, os dados de replicação são enviados para uma conta do Armazenamento do Azure ou para uma réplica do disco gerenciado na região de destino do Azure, se as máquinas virtuais do Azure usarem o [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md). Embora os pontos de extremidade de replicação sejam públicos, por padrão, o tráfego para a replicação da VM do Azure não passa pela Internet, independentemente da região do Azure em que a rede virtual de origem esteja.

Para a recuperação de desastre de VM do Azure, como os dados de replicação não saem do limite do Azure, o ExpressRoute não é necessário para replicação. Após as máquinas virtuais fazerem o failover para a região do Azure de destino, será possível acessá-las usando o [emparelhamento privado](../expressroute/expressroute-circuit-peerings.md#azure-private-peering).

## <a name="replicating-azure-deployments"></a>Replicar implantações do Azure

Um [artigo](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity) anterior descreveu uma configuração simples com uma Rede Virtual do Azure conectada ao datacenter local do cliente por meio do ExpressRoute. Normalmente, as cargas de trabalho de implantações empresariais são divididas entre várias redes virtuais do Azure, e um hub de conectividade central estabelece a conectividade externa com a Internet e as implantações locais.

Este exemplo descreve a topologia hub-spoke, comum em implantações empresariais:
-   A implantação está na região **Ásia Oriental do Azure** e o datacenter local tem uma conexão de circuito do ExpressRoute por meio de um parceiro de borda em Hong Kong.
-   Os aplicativos são implantados em duas redes virtuais spoke – **Source VNet1**, com espaço de endereço 10.1.0.0/24, e **Source VNet2**, com espaço de endereço 10.2.0.0/24.
-   A rede virtual de hub, **Source Hub VNet**, com espaço de endereço 10.10.10.0/24, atua como gatekeeper. Toda a comunicação entre as sub-redes passa pelo hub.
-   A rede virtual do hub tem duas sub-redes – **NVA Subnet**, com espaço de endereço 10.10.10.0/25, e **Gateway Subnet**, com espaço de endereço 10.10.10.128/25.
-   A **NVA Subnet** tem uma solução de virtualização de rede com endereço IP 10.10.10.10.
-   A **Gateway Subnet** tem um gateway do ExpressRoute conectado à uma conexão do ExpressRoute que roteia para o datacenter local do cliente por meio de um domínio de roteamento com emparelhamento privado.
-   Cada rede virtual spoke está conectada com a rede virtual de hub, e todo o roteamento nessa topologia de rede é controlado pelo UDR (Tabelas de Rotas do Azure). Todo o tráfego de saída de uma VNet para outra ou para o datacenter local é roteado por meio da NVA.

![Local para Azure com o ExpressRoute antes do failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-before-failover.png)

### <a name="hub-and-spoke-peering"></a>Emparelhamento hub-spoke

O emparelhamento spoke-hub tem a seguinte configuração:
-   Permitir endereço de rede virtual: habilitado
-   Permitir tráfego encaminhado: habilitado
-   Permitir trânsito de gateway: desabilitado
-   Usar gateways remotos: habilitado

 ![Configuração do emparelhamento spoke-hub](./media/azure-vm-disaster-recovery-with-expressroute/spoke-to-hub-peering-configuration.png)

O emparelhamento hub-spoke tem a seguinte configuração:
-   Permitir endereço de rede virtual: habilitado
-   Permitir tráfego encaminhado: habilitado
-   Permitir trânsito de gateway: habilitado
-   Usar gateways remotos: desabilitado

 ![Configuração do emparelhamento hub-spoke](./media/azure-vm-disaster-recovery-with-expressroute/hub-to-spoke-peering-configuration.png)

### <a name="enabling-replication-for-the-deployment"></a>Habilitar a replicação para a implantação

Para fazer a configuração acima, primeiro, [configure a recuperação de desastre](azure-to-azure-tutorial-enable-replication.md) para todas as máquinas virtuais que usam o Site Recovery. O Site Recovery pode criar as réplicas das redes virtuais (incluindo sub-redes e sub-redes de gateway) na região de destino, bem como criar os mapeamentos necessários entre as redes virtuais de origem e de destino. Também é possível criar previamente as redes e sub-redes do lado do destino e usá-las ao habilitar a replicação.

O Site Recovery não replica as tabelas de rota, os gateways de rede virtual, as conexões de gateway de rede virtual, o emparelhamento de rede virtual ou quaisquer outros recursos de rede ou conexões. Esses e outros recursos que não fazem parte do [processo de replicação](azure-to-azure-architecture.md#replication-process) precisam ser criados durante ou antes do failover e conectados aos recursos relevantes. É possível usar os [planos de recuperação](site-recovery-create-recovery-plans.md) avançados do Azure Site Recovery para automatizar a criação e a conexão de recursos adicionais usando scripts de automação.

Por padrão, o tráfego de replicação não deve sair do limite do Azure. Normalmente, as implantações de NVA também definem uma rota padrão (0.0.0.0/0) que força o tráfego de saída da Internet a fluir pelo NVA. Nesse caso, o dispositivo poderá apresentar limitações se todo o tráfego de replicação passar pelo NVA. O mesmo também se aplica ao usar rotas padrão para o roteamento de todo o tráfego da VM do Azure para implantações locais. É recomendável [criar um ponto de extremidade de serviço de rede virtual](azure-to-azure-about-networking.md#create-network-service-endpoint-for-storage) para "Armazenamento", para que o tráfego de replicação não saia do limite do Azure.

## <a name="failover-models-with-expressroute"></a>Modelos de failover com o ExpressRoute

Quando as máquinas virtuais do Azure fazem failover para uma região diferente, a conexão existente do ExpressRoute com a rede virtual de origem não é transferida automaticamente para a rede virtual de destino na região de recuperação. Uma nova conexão é necessária para conectar o ExpressRoute com a rede virtual de destino.

É possível replicar as máquinas virtuais do Azure em qualquer região do Azure dentro do mesmo cluster geográfico, conforme detalhado [aqui](azure-to-azure-support-matrix.md#region-support). Se a região de destino do Azure escolhida não estiver dentro da mesma região geopolítica da origem, será necessário habilitar o ExpressRoute Premium caso você esteja usando um único circuito do ExpressRoute para conectividade de região de origem e destino. Para saber mais detalhes, confira [Locais de ExpressRoute](../expressroute/expressroute-locations.md#azure-regions-to-expressroute-locations-within-a-geopolitical-region) e [Preços do ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="two-expressroute-circuits-in-two-different-expressroute-peering-locations"></a>Dois circuitos do ExpressRoute em dois locais de emparelhamento diferentes do ExpressRoute
-   Essa configuração é útil se você quer se proteger contra falhas do circuito primário do ExpressRoute e desastres regionais de grande escala, o que também pode afetar os locais de emparelhamento e interromper o circuito primário do ExpressRoute.
-   Normalmente, o circuito conectado com o ambiente de produção é usado como circuito primário, e o circuito secundário serve para evitar falhas e, geralmente, tem uma largura de banda menor. A largura de banda do circuito secundário pode ser aumentada em um evento de desastre, caso em que o secundário deve assumir o lugar do primário.
-   Com essa configuração, é possível estabelecer conexões do circuito secundário do ExpressRoute com a rede virtual de destino após o failover. Outra opção é estabelecê-las e deixá-las prontas para a declaração de desastre, reduzindo o tempo de recuperação geral. Com conexões simultâneas às redes virtuais do primário e da região de destino, assegure-se de que o roteamento local use o circuito secundário e a conexão somente após o failover.
-   As redes virtuais de origem e destino das VMs protegidas com o Site Recovery podem ter endereços IP iguais ou diferentes no failover, de acordo com sua necessidade. Em ambos os casos, as conexões secundárias podem ser estabelecidas antes do failover.

### <a name="two-expressroute-circuits-in-the-same-expressroute-peering-location"></a>Dois circuitos do ExpressRoute no mesmo local de emparelhamento do ExpressRoute
-   Nessa configuração, é possível se proteger contra falhas do circuito primário do ExpressRoute, mas não contra desastres regionais de grande escala, o que pode afetar os locais de emparelhamento do ExpressRoute. Com esse último, ambos os circuitos primário e secundário podem ser afetados.
-   Outras condições para endereços IP e conexões permanecem as mesmas do caso anterior. Com o circuito primário, é possível ter conexões simultâneas do datacenter local com a rede virtual de origem. Com o circuito secundário, é possível tê-las com a rede virtual de destino. Com conexões simultâneas às redes virtuais do primário e da região de destino, assegure-se de que o roteamento local use o circuito secundário e a conexão somente após o failover.
-   Não é possível conectar ambos os circuitos com a mesma rede virtual quando os circuitos são criados no mesmo local de emparelhamento.

### <a name="single-expressroute-circuit"></a>Circuito único do ExpressRoute
-   Essa configuração não protege contra um desastre regional de grande escala, o que pode afetar o local de emparelhamento do ExpressRoute.
-   Com um único circuito do ExpressRoute, não será possível conectar simultaneamente as redes virtuais de origem e destino com o circuito se o mesmo espaço de endereços IP for usado na região de destino.
-   Quando o mesmo espaço de endereços IP é usado na região de destino, a conexão do lado de origem precisa ser desconectada, e a conexão do lado de destino precisa ser estabelecida logo depois. Essa alteração de conexão pode ser inserida como parte de um plano de recuperação.
-   Em uma falha regional, se a região primária estiver inacessível, a operação de desconexão poderá falhar. Uma interrupção desse tipo pode afetar a criação de conexões na região de destino quando o mesmo espaço de endereços IP é usado na rede virtual de destino.
-   Se a criação de conexões for bem-sucedida nas recuperações das regiões primárias e de destino mais tarde, você poderá enfrentar descartes de pacote se duas conexões simultâneas tentarem se conectar ao mesmo espaço de endereços. Para evitar descartes de pacote, a conexão primária deve ser encerrada imediatamente. Após o failback das máquinas virtuais na região primária, a conexão primária pode ser estabelecida novamente depois da desconexão da conexão secundária.
-   Se um espaço de endereços diferente for usado na rede virtual de destino, então, será possível se conectar simultaneamente com as redes virtuais de origem e destino por meio do mesmo circuito do ExpressRoute.

## <a name="recovering-azure-deployments"></a>Recuperar implantações do Azure
Considere o modelo de failover com dois circuitos do ExpressRoute diferentes em dois locais de emparelhamento distintos, e retenção de endereços IP privados para máquinas virtuais do Azure protegidas. A região de destino da recuperação é o Azure do Sudeste Asiático, e uma conexão do circuito secundário do ExpressRoute é estabelecida por meio de um parceiro de borda em Cingapura.

Para automatizar a recuperação de toda a implantação, além de replicar as máquinas virtuais e redes virtuais, também é necessário criar outros recursos de rede e conexões relevantes. Para a topologia de rede hub-spoke anterior, estas etapas adicionais precisam ser seguidas durante ou depois da operação de [failover](azure-to-azure-tutorial-failover-failback.md):
1.  Criar o gateway do Azure ExpressRoute na rede virtual do hub da região de destino. O gateway do Azure ExpressRoute é necessário para conectar a rede virtual do hub de destino com o circuito do ExpressRoute.
2.  Criar a conexão da rede virtual do hub de destino com o circuito do ExpressRoute de destino.
3.  Configurar os emparelhamentos de VNet entre o hub da região de destino e as redes virtuais spoke. As propriedades de emparelhamento na região de destino serão as mesmas da região de origem.
4.  Configurar os UDRs na VNet do hub, e as duas VNets spoke. As propriedades dos UDRs do lado do destino são as mesmas do lado da origem quando os mesmos endereços IP são usados. Com endereços IP de destino diferentes, os UDRs devem ser modificados de acordo.

As etapas acima podem ser inseridas como parte de um [plano de recuperação](site-recovery-create-recovery-plans.md). De acordo com a conectividade do aplicativo e com os requisitos de tempo de recuperação, as etapas acima também podem ser concluídas antes de iniciar o failover.

Após a recuperação das máquinas virtuais e da conclusão das outras etapas de conectividade, o ambiente de recuperação terá a seguinte aparência: ![Local para Azure com ExpressRoute após o failover](./media/azure-vm-disaster-recovery-with-expressroute/site-recovery-with-expressroute-after-failover.png)

Um exemplo de topologia simples para recuperação de desastre de VM do Azure com um único circuito do ExpressRoute e com o mesmo IP em máquinas virtuais de destino é detalhado [aqui](site-recovery-retain-ip-azure-vm-failover.md#on-premises-to-azure-connectivity).

## <a name="recovery-time-objective-rto-considerations"></a>Considerações de RTO (Objetivo do Tempo de Recuperação)
Para reduzir o tempo de recuperação geral da implantação, é recomendável provisionar e implantar com antecedência os [componentes de rede](azure-vm-disaster-recovery-with-expressroute.md#enabling-replication-for-the-deployment) adicionais da região de destino, como gateways de rede virtual. Um tempo de inatividade reduzido está associado com a implantação de recursos adicionais. Esse tempo de inatividade poderá afetar o tempo de recuperação geral se não for levado em conta no planejamento.

É recomendável executar [simulações de recuperação de desastre](azure-to-azure-tutorial-dr-drill.md) constantemente em implantações protegidas. Uma simulação valida sua estratégia de replicação sem perda de dados ou tempo de inatividade e não afeta seu ambiente de produção. Executar uma simulação também evita problemas de configuração de última hora que podem afetar negativamente o objetivo de tempo de recuperação. É recomendável utilizar uma rede de VM do Azure separada para o failover de teste, e não a rede padrão configurada quando você habilitou a replicação.

Se você estiver usando um único circuito do ExpressRoute, recomendamos usar um espaço de endereços IP diferente para a rede virtual de destino, a fim de evitar problemas no estabelecimento de conexões durante desastres regionais. Se usar endereços IP diferentes não for viável para o ambiente de produção recuperado, o failover de teste da simulação de recuperação de desastre precisará ser feito em uma rede de teste separada com endereços IP diferentes, pois não é possível conectar duas redes virtuais com um espaço de endereços IP sobreposto com o mesmo circuito do ExpressRoute.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre os [circuitos do ExpressRoute](../expressroute/expressroute-circuit-peerings.md).
- Saiba mais sobre os [domínios de roteamento do ExpressRoute](../expressroute/expressroute-circuit-peerings.md#expressroute-routing-domains).
- Saiba mais sobre os [locais do ExpressRoute](../expressroute/expressroute-locations.md).
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md) para automatizar o failover de aplicativo.
