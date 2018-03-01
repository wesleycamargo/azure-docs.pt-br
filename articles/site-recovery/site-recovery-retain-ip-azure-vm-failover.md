---
title: "Manter endereços IP durante o failover de máquinas virtuais do Azure para outra região do Azure | Microsoft Docs"
description: "Descreve como manter os endereços IP para cenários de failover de Azure para Azure com o Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2018
ms.author: manayar
ms.openlocfilehash: 28d772df384e620c7e82812adfa2bfa148401132
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="ip-address-retention-for-azure-virtual-machine-failover"></a>Retenção de endereço IP para failover de máquina virtual do Azure

O Azure Site Recovery oferece recuperação de desastre para VMs do Azure. Durante o failover de uma região do Azure para outra, os clientes geralmente requerem a retenção de suas configurações de IP. O Site Recovery, por padrão, imita a rede virtual de origem e a estrutura de sub-rede ao criar esses recursos na região de destino. Para VMs do Azure configuradas com endereços IP privados estáticos, o Site Recovery também procura provisionar o mesmo IP privado na VM de destino se esse IP já não estiver bloqueado por um recurso do Azure ou uma VM replicada.

Para aplicativos simples, a configuração padrão acima é tudo o que é necessário. Para aplicativos empresariais mais complexos, os clientes podem precisar provisionar recursos de rede adicionais para garantir a conectividade após o failover com outros componentes de sua infraestrutura. Este artigo explica os requisitos de rede para fazer fail over em VMs do Azure de uma região para outra enquanto retém os endereços IP da VM.

## <a name="azure-to-azure-connectivity"></a>Conectividade de Azure para Azure

Para o primeiro cenário, vamos considerar a **Empresa A** que tem sua infraestrutura de aplicativos em execução no Azure. Por motivos de conformidade e continuidade dos negócios, a **Empresa A** decide usar o Azure Site Recovery para proteger seus aplicativos.

Devido ao requisito de retenção IP (como para associações de aplicativos), a Empresa A tem a mesma estrutura de sub-rede e rede virtual na região de destino. Para reduzir ainda mais o objetivo de tempo de recuperação (RTO), a **Empresa A** utiliza nós de réplica para SQL Always ON, controladores de domínio, etc. e esses nós de réplica são colocados em uma rede virtual diferente na região de destino. O uso de um espaço de endereço diferente para os nós de réplica permite que a **Empresa A** estabeleça a conectividade VPN site a site entre regiões de origem e destino, o que não seria possível se o mesmo espaço de endereço fosse usado em ambas as extremidades.

A arquitetura de rede tem a seguinte aparência antes do failover:
- Máquinas virtuais de aplicativo são hospedadas no Azure da Ásia Oriental, usando uma rede virtual do Azure com espaço de endereço 10.1.0.0/16. Essa rede virtual é chamada de **VNet de origem**.
- As cargas de trabalho de aplicativo são divididas em três sub-redes – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, respectivamente denominadas **Sub-rede 1**, **Sub-rede 2**, **Sub-rede 3**.
- O Azure do Sudeste Asiático é a região de destino e tem uma rede virtual de recuperação que imita a configuração de espaço de endereço e a sub-rede na origem. Essa rede virtual é chamada de **VNet de recuperação**.
- Nós de réplica, como aqueles necessários para Always On, controlador de domínio, etc. são colocados em uma rede virtual com espaço de endereço 20.1.0.0/16 dentro da Sub-rede 4 com o endereço 20.1.0.0/24. A rede virtual é chamada de **VNet do Azure** e fica no Azure do Sudeste Asiático.
- A **VNet de origem** e a **VNet do Azure** estão conectadas através de conectividade VPN site a site.
- A **VNet de recuperação** não é conectada a nenhuma outra rede virtual.
- A **Empresa A** atribui/verifica o endereço IP de destino para itens replicados. Neste exemplo, o IP de destino é o mesmo que o IP de origem para cada VM.

![Conectividade de Azure para Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Failover completo de região

No caso de uma interrupção regional, a **Empresa A** pode recuperar toda a sua implantação rápida e facilmente usando os poderosos [planos de recuperação](site-recovery-create-recovery-plans.md) do Azure Site Recovery. Com o endereço IP de destino já definido para cada VM antes do failover, a **Empresa A** pode orquestrar o failover e automatizar o estabelecimento da conexão entre a VNet de recuperação e a VNet do Azure conforme o diagrama abaixo.

![Conectividade de Azure para Azure em failover completo de região](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover.png)

Dependendo dos requisitos do aplicativo, as conexões entre as duas VNets na região de destino podem ser estabelecida antes, durante (como etapa intermediária) ou após o failover. Use [planos de recuperação](site-recovery-create-recovery-plans.md) para adicionar scripts e definir a ordem de failover.

A Empresa A também tem a opção de usar o emparelhamento de VNet ou VPN site a site para estabelecer a conectividade entre a VNet de recuperação e a VNet do Azure. O emparelhamento de VNet não usa um gateway de VPN e tem restrições diferentes. Além disso, os [preços do emparelhamento VNet](https://azure.microsoft.com/pricing/details/virtual-network) são calculados de maneira diferente dos [preços de Gateway de VPN de VNet a VNet](https://azure.microsoft.com/pricing/details/vpn-gateway). Para failovers, é aconselhável imitar a conectividade da origem, incluindo o tipo de conexão, para minimizar incidentes imprevisíveis decorrente de alterações de rede.

### <a name="isolated-application-failover"></a>Failover de aplicativo isolado

Em determinadas condições, os usuários talvez precisem fazer failover de partes de sua infraestrutura de aplicativo. Um exemplo é o failover em um aplicativo específico ou camada que está hospedada em uma sub-rede dedicada. Embora seja possível fazer um failover de sub-rede com retenção de IP, não é recomendável para a maioria das situações, pois aumenta substancialmente as inconsistências de conectividade. Você também perderá a conectividade de sub-rede para outras sub-redes dentro da mesma rede virtual do Azure.

Uma maneira melhor de levar em conta os requisitos de failover de aplicativos de nível de sub-rede é usar endereços IP de destino diferentes para o failover (se a conectividade for necessária para outras sub-redes na rede virtual de origem) ou isolar cada aplicativo em sua própria rede virtual dedicada na origem. Com a segunda abordagem, você pode estabelecer conectividade de inter-rede na fonte e emular o mesmo durante o failover para a região de destino.

Para projetar aplicativos individuais para garantir a resiliência, é aconselhável colocar um aplicativo em sua própria rede virtual dedicada e estabelecer a conectividade entre essas redes virtuais, conforme necessário. Isso permite o failover de aplicativo isolado ao mesmo tempo em que mantém os endereços IP privados originais.

A configuração pré-failover terá a seguinte aparência:
- Máquinas virtuais de aplicativo são hospedadas no Azure da Ásia Oriental, usando uma rede virtual do Azure com espaço de endereço 10.1.0.0/16 para o primeiro aplicativo e 15.1.0.0/16 para o segundo aplicativo. As redes virtuais são nomeadas **VNet1 de origem** e **VNet2 de origem** para o primeiro e o segundo aplicativo, respectivamente.
- Cada VNet é também dividida em duas sub-redes.
- O Azure do Sudeste Asiático é a região de destino e tem as redes virtuais de recuperação VNet1 de recuperação e VNet2 de recuperação.
- Nós de réplica, como aqueles necessários para Always On, controlador de domínio, etc. são colocados em uma rede virtual com espaço de endereço 20.1.0.0/16 dentro da **Sub-rede 4** com o endereço 20.1.0.0/24. A rede virtual é chamada de VNet do Azure e fica no Azure do Sudeste Asiático.
- A **VNet1 de origem** e a **VNet do Azure** estão conectadas através de conectividade VPN site a site. Similarmente, a **VNet2 de origem** e a **VNet do Azure** também estão conectadas através de conectividade VPN site a site.
- A **VNet1 de origem** e a **VNet2 de origem** também são conectadas por meio de VPN site a site neste exemplo. Como as duas VNets estão na mesma região, o emparelhamento de VNets também pode ser usado em vez de VPN site a site.
- A **VNet1 de recuperação** e a **VNet2 de recuperação** não são conectadas a nenhuma outra rede virtual.
- Para reduzir o objetivo de tempo de recuperação (RTO), gateways de VPN são configurados na **VNet1 de recuperação** e na **VNet2 recuperação** antes do failover.

![Aplicativo isolado de conectividade de Azure para Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover.png)

Em caso de uma situação de desastre que afete apenas um aplicativo (neste exemplo, situado no VNet2 de origem), a Empresa A pode recuperar o aplicativo afetado da seguinte maneira:
- As conexões VPN entre **VNet 1 de origem** e **VNet2 de origem** e entre **VNet2 de origem** e **VNet do Azure** são desconectadas.
- Conexões VPN são estabelecidas entre **VNet1 de origem** e **VNet2 de recuperação** e entre **VNet2 de recuperação** e **VNet do Azure**.
- Máquinas virtuais de **VNet2 de origem** passam por failover para **VNet2 recuperação**.

![Aplicativo isolado de conectividade de Azure para Azure depois do failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover.png)

O exemplo de failover isolado acima pode ser expandido para incluir mais aplicativos e conexões de rede. Recomenda-se seguir um modelo de conexão similar, sempre que possível, ao realizar failover da origem para o destino.

### <a name="further-considerations"></a>Outras considerações

Gateways de VPN utilizam endereços IP públicos e saltos de gateway para estabelecer conexões. Se você não quiser usar IP público e/ou se quiser evitar saltos extras, pode usar o emparelhamento de VNet global para emparelhar redes virtuais em regiões do Azure.

Esse recurso está atualmente em versão prévia pública e está sendo expandido para oferecer suporte a mais regiões — permitindo a conectividade direta entre VMs sem qualquer envolvimento da internet pública ou saltos extras.

Para obter mais informações, consulte a [documentação de emparelhamento](../virtual-network/virtual-network-create-peering.md#register) e os [preços](https://azure.microsoft.com/en-us/pricing/details/virtual-network/).

## <a name="on-premises-to-azure-connectivity"></a>Conectividade local para Azure

Para o segundo cenário, vamos considerar a **Empresa B** que tem uma parte de sua infraestrutura de aplicativos em execução no Azure e o restante em execução local. Por motivos de conformidade e continuidade dos negócios, a **Empresa B** decide usar o Azure Site Recovery para proteger seus aplicativos em execução no Azure.

A arquitetura de rede tem a seguinte aparência antes do failover:
- Máquinas virtuais de aplicativo são hospedadas no Azure da Ásia Oriental, usando uma rede virtual do Azure com espaço de endereço 10.1.0.0/16. Essa rede virtual é chamada de **VNet de origem**.
- As cargas de trabalho de aplicativo são divididas em três sub-redes – 10.1.0.0/24, 10.1.1.0/24, 10.1.2.0/24, respectivamente denominadas **Sub-rede 1**, **Sub-rede 2**, **Sub-rede 3**.
- O Azure do Sudeste Asiático é a região de destino e tem uma rede virtual de recuperação que imita a configuração de espaço de endereço e a sub-rede na origem. Essa rede virtual é chamada de **VNet de recuperação**.
- As VMs no Azure da Ásia Oriental são conectadas ao datacenter local por meio de ExpressRoute ou VPN Site a Site.
- Para reduzir o objetivo de tempo de recuperação (RTO), a Empresa B provisiona gateways na VNet de recuperação no Azure do Sudeste Asiático antes do failover.
- A **Empresa B** atribui/verifica o endereço IP de destino para itens replicados. Neste exemplo, o IP de destino é o mesmo que o IP de origem para cada VM

![Conectividade local para Azure antes do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover.png)

### <a name="full-region-failover"></a>Failover completo de região

No caso de uma interrupção regional, a **Empresa B** pode recuperar toda a sua implantação rápida e facilmente usando os poderosos [planos de recuperação](site-recovery-create-recovery-plans.md) do Azure Site Recovery. Com o endereço IP de destino já definido para cada VM antes do failover, a **Empresa B** pode orquestrar o failover e automatizar o estabelecimento da conexão entre a VNet de recuperação e o datacenter local conforme o diagrama abaixo.

A conexão original entre o Azure da Ásia Oriental e o datacenter local deve ser desconectada antes de estabelecer a conexão entre o Azure do Sudeste Asiático e o datacenter local. O roteamento local também é reconfigurado para apontar para a região de destino e gateways após o failover.

![Conectividade local para Azure depois do failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover.png)

### <a name="subnet-failover"></a>Failover da sub-rede

Ao contrário do cenário de Azure para Azure descrito para a **Empresa A**, um failover de nível de sub-rede não é possível neste caso para **Empresa B**. Isso ocorre porque o espaço de endereço nas redes virtuais de origem e de recuperação é o mesmo e a origem original para a conexão local está ativa.

Para obter resiliência de aplicativo, é recomendável que cada aplicativo esteja hospedado em sua própria rede virtual dedicada do Azure. Os aplicativos podem, então, passar por failover isoladamente e as conexões necessárias de local para origem podem ser direcionadas para a região de destino conforme descrito acima.

## <a name="next-steps"></a>Próximas etapas
- Saiba mais sobre [planos de recuperação](site-recovery-create-recovery-plans.md).
