---
title: Arquitetura de rede de trânsito global WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre a arquitetura de rede de trânsito global para WAN Virtual
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 05/06/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand global transit network architecture as it relates to Virtual WAN.
ms.openlocfilehash: 8cda617ca60a17fceaaa818480ff9bbaef46c3fd
ms.sourcegitcommit: 300cd05584101affac1060c2863200f1ebda76b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2019
ms.locfileid: "65414061"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Arquitetura de rede de trânsito global e WAN Virtual

Arquitetura de rede de trânsito global está sendo adotada pelas empresas para consolidar, conectar e controlar a empresa moderna centrado em nuvem superfície de TI. Em uma empresa centrado em nuvem moderna, o tráfego de rede não precisa ser backhauled para HQ. Arquitetura de rede de trânsito global se baseia em conceitos familiares de rede e novos conceitos que são exclusivos para a nuvem e arquiteturas baseadas em nuvem.

![Arquitetura](./media/virtual-wan-global-transit-network-architecture/architecture2.png)

**Figura 1: Rede de trânsito global com WAN Virtual**

As empresas modernas exigem conectividade onipresente entre usuários, dados e aplicativos distribuídos hyper entre a nuvem e locais. WAN Virtual do Azure permite uma arquitetura de rede de trânsito global, permitindo conectividade onipresente, para qualquer entre conjuntos distribuídos globalmente de redes virtuais, sites, aplicativos e usuários. WAN Virtual do Azure é um serviço gerenciado pela Microsoft. Todos os componentes de rede é composto por esse serviço são hospedados e gerenciados pela Microsoft. Para obter mais informações sobre a WAN Virtual, consulte o [visão geral de WAN Virtual](virtual-wan-about.md) artigo.

Na arquitetura WAN Virtual do Azure, regiões do Azure servem como hubs para o qual você pode optar por conectar suas ramificações. Quando as ramificações estiver conectadas, você pode aproveitar o backbone do Azure para estabelecer a ramificação para rede virtual e, opcionalmente, conectividade de ramificação para ramificação.

Você pode estabelecer uma WAN virtual criando um único hub WAN Virtual na região que tem o maior número de spokes (ramificações, redes virtuais, os usuários) e, em seguida, conectando os spokes que estão em outras regiões para o hub. Como alternativa, se spokes distribuídos geograficamente, você também pode instanciar hubs regionais e interconectar os hubs. Os hubs fazem parte do mesmo WAN virtual, mas eles podem ser associados a políticas regionais diferentes.

## <a name="hub"></a>Trânsito de Hub e spoke

A arquitetura de rede de trânsito global é baseada em um modelo de conectividade clássico de hub e spoke em que a rede de nuvem hospedada 'hub' permite a conectividade transitiva entre pontos de extremidade que podem ser distribuídos em diferentes tipos de 'spokes'.
  
Nesse modelo, um spoke pode ser:

* Rede virtual (redes virtuais)
* Site do branch físico
* Usuário remoto
* Internet

![diagrama de trânsito global de Hub e spoke](./media/virtual-wan-global-transit-network-architecture/architecture.png)

**Figura 2: O hub e spoke**

Figura 2 mostra a exibição lógica da rede global em que os usuários distribuídos geograficamente, locais físicos e redes virtuais são interconectados através de um hub de rede hospedado na nuvem. Essa arquitetura permite a conectividade de trânsito de um salto lógica entre os pontos de extremidade de rede. Os spokes estão conectados ao hub de vários serviços do Azure rede como o ExpressRoute ou site para VPN de site para ramificações físicas, o emparelhamento VNet para redes virtuais e a VPN ponto a site para usuários remotos.

## <a name="crossregion"></a>Conectividade entre regiões

Para uma empresa, um volume na nuvem normalmente segue o volume. A maioria das empresas acessar a nuvem de uma região mais próxima de seus usuários e o local físico. Uma das principais entidades de segurança da arquitetura de rede global é habilitar a conectividade entre regiões entre as entidades de rede e pontos de extremidade. Um volume na nuvem pode abranger várias regiões. Isso significa que o tráfego de uma ramificação que está conectado à nuvem em uma região pode acessar outra ramificação ou uma rede virtual em uma região diferente.

## <a name="any"></a>Para qualquer conectividade

Arquitetura de rede de trânsito global permite *para qualquer conectividade* através de um hub de rede central. Essa arquitetura elimina ou reduz a necessidade de malha completa ou modelos de conectividade de malha parcial que são mais complexos para criar e manter. Além disso, o controle de roteamento no hub e spoke versus redes de malha é mais fácil de configurar e manter.

Para qualquer conectividade, no contexto de uma arquitetura global, permite que uma empresa com usuários distribuídos globalmente, ramificações, data centers, redes virtuais e aplicativos se conectem uns aos outros por meio do hub de trânsito. O hub de trânsito atua como o sistema global de trânsito.

![caminhos de tráfego](./media/virtual-wan-global-transit-network-architecture/trafficpath.png)

**Figura 3: Caminhos de tráfego da WAN virtual**

WAN Virtual do Azure suporta os seguintes caminhos de conectividade de trânsito global. As letras entre parênteses são mapeados para a Figura 3.

* Ramificação a VNet (a)  
* Ramificação para ramificação (b)
* Usuário-para-VNet remoto (c)
* Usuário-para-branch remoto (d)
* VNet para VNet usando o emparelhamento de rede virtual (e)
* Alcance Global de ExpressRoute 

### <a name="branchvnet"></a>Branch-to-VNet

Branch-to-VNet é o caminho primário compatível com WAN Virtual do Azure. Esse caminho permite que você se conecte ramificações para cargas de trabalho da empresa com o IAAS do Azure que são implantadas em redes virtuais do Azure. Ramificações podem ser conectadas à WAN virtual por meio do ExpressRoute ou VPN site a site. Tráfego de tráfego para redes virtuais que estão conectados para os hubs de WAN virtuais por meio de conexões de rede virtual.

### <a name="branchbranch"></a>Ramificação para ramificação

Ramificações podem ser conectadas a um hub de WAN Virtual do Azure usando os circuitos do ExpressRoute e/ou conexões de VPN site a site. Você pode conectar as ramificações para o hub de WAN Virtual na região mais próxima para o branch.

Essa opção permite que as empresas a aproveitar o backbone do Azure para conectar-se de ramificações. No entanto, mesmo que essa funcionalidade está disponível, você deve avaliar os benefícios da conexão ramificações pela WAN Virtual do Azure versus o uso de uma WAN particular.

### <a name="usertovnet"></a>Usuário-to-VNet remoto

Você pode habilitar o acesso remoto direto e seguro no Azure usando conexões de ponto para site de um cliente de usuário remoto a uma WAN virtual. Os usuários remotos da empresa não precisam hairpin para a nuvem usando uma VPN corporativa.

### <a name="usertobranch"></a>Usuário a ramificação remota

O caminho do usuário para branch remoto permite que os usuários remotos que estão usando uma conexão ponto a site para aplicativos e cargas de trabalho do Azure acesso local ao transitar por meio da nuvem. Esse caminho oferece a flexibilidade de usuários remotos para cargas de trabalho de acesso que são implantados no Azure e locais. As empresas podem habilitar o serviço de acesso de remoto seguro baseado em nuvem central na WAN Virtual do Azure.

### <a name="vnetvnet"></a>Trânsito de rede virtual para rede virtual usando o emparelhamento VNet

Para conectar redes virtuais entre si para dar suporte a aplicativos de várias camadas que são implementados em várias VNets, use o emparelhamento de rede virtual. Um cenário de trânsito de VNet para VNet por meio de WAN Virtual do Azure não tem suporte atualmente, mas está no roteiro do Azure. Conectando redes virtuais por meio do emparelhamento VNet é a solução recomendável para redes virtuais que precisam estar conectados entre si. Para obter mais informações sobre o emparelhamento de rede virtual, consulte [visão geral emparelhamento de VNet](../virtual-network/virtual-network-peering-overview.md).

### <a name="globalreach"></a>Alcance Global de ExpressRoute

O ExpressRoute é uma maneira particular e resiliente para se conectar suas redes locais para o Microsoft Cloud. O alcance Global do ExpressRoute é um recurso de complemento para o ExpressRoute. Com o alcance Global, você pode vincular circuitos do ExpressRoute juntos para tornar uma rede privada entre suas redes locais. Ramificações que estão conectadas ao Azure WAN Virtual usando o ExpressRoute exigem a ExpressRoute alcance Global para se comunicar entre si.

Nesse modelo, cada ramificação que está conectada ao hub de WAN Virtual usando o ExpressRoute pode se conectar a redes virtuais usando o caminho do branch-to-VNet. Tráfego de ramificação para ramificação não transitar hub porque o alcance Global do ExpressRoute permite que um caminho mais ideal ao longo de WAN do Azure.

## <a name="security"></a>Controle de segurança e política

O hub de rede virtual que interconecta e potencialmente vê todo o tráfego de trânsito. É o lugar para funções de rede central do host e serviços, como tal um roteamento de nuvem, diretiva de rede e segurança e controle de acesso à Internet.

## <a name="next-steps"></a>Próximas etapas

Crie uma conexão usando a WAN Virtual.

* [Conexões site a site usando a WAN Virtual](virtual-wan-site-to-site-portal.md)
* [Conexões ponto a site usando a WAN Virtual](virtual-wan-point-to-site-portal.md)
* [Conexões do ExpressRoute usando a WAN Virtual](virtual-wan-expressroute-portal.md)
