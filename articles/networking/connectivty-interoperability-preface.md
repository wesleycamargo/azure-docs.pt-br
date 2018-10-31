---
title: 'Interoperabilidade da ExpressRoute, VPN site a site e emparelhamento VNet - Configuração de teste: interoperabilidade de recursos de conectividade de back-end do Azure | Microsoft Docs'
description: Esta página fornece uma configuração de teste que é usada para analisar a interoperabilidade dos recursos do ExpressRoute, VPN site a site e emparelhamento VNet.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: e859a0a3ac35a9d9f2dab579b7609192e599f90f
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946917"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-setup"></a>Interoperabilidade do ExpressRoute, VPN site a site e emparelhamento VNet - configuração de teste
Neste artigo, vamos identificar uma configuração de teste que podemos usar para analisar como os diferentes recursos interagem entre si no plano de controle e no plano de dados. Antes de discutir a configuração de teste, vamos examinar brevemente o significado desses diferentes recursos de rede do Azure.

ExpressRoute: usando o emparelhamento privado do ExpressRoute, é possível conectar diretamente espaços de IP privados de sua rede local a as implantações de VNet do Azure.  Usando o ExpressRoute é possível obter maior largura de banda e conexão privada. Há muitos parceiros eco do ExpressRoute que oferecem conectividade ExpressRoute com SLA. Para saber mais sobre o ExpressRoute e como configurá-lo, consulte [Introdução ao ExpressRoute][ExpressRoute]

VPN site a site: para conectar com segurança uma rede local ao Azure pela Internet ou pelo ExpressRoute, a opção VPN site a site (S2S) está disponível. Para saber mais sobre como configurar VPN S2S para conectar-se ao Azure, consulte [Configurar Gateway de VPN][VPN]

Emparelhamento VNet: o emparelhamento VNet está disponível para estabelecer conectividade entre VNets (redes virtuais). Para saber mais sobre o emparelhamento VNet, consulte [Tutorial em emparelhamento VNet][VNet].

##<a name="test-setup"></a>Configuração de teste

O diagrama abaixo ilustra a configuração do teste.

[![1]][1]

A parte central da configuração de teste é a VNet do Hub na Região do Azure 1. A VNet do Hub está conectada a diferentes redes da seguinte maneira:

1.  VNet de spoke via emparelhamento VNet. A VNet de spoke tem acesso remoto a ambos os gateways na VNet de spoke.
2.  Para VNet de Branch via VPN site a site. A conectividade usa o eBGP para trocar rotas.
3.  Para a rede Localização-1 local via emparelhamento privado do ExpressRoute, como o caminho principal e a conectividade de VPN site a site, como o caminho de back-up. No restante deste documento, vamos consultar esse circuito do ExpressRoute como ExpressRoute1. Por padrão, os circuitos do ExpressRoute fornecem conectividade redundante para Alta Disponibilidade. No ExpressRoute1, a subinterface do roteador CE secundário voltada para o MSEE secundário é desabilitado. Isso é indicado usando uma linha vermelha sobre a seta de linha dupla no diagrama acima.
4.  Para a rede Localização-2 local via emparelhamento privado do ExpressRoute. No restante deste documento, vamos indicar este segundo circuito do ExpressRoute como ExpressRoute2.
5.  ExpressRoute1 também conecta a VNet do Hub e a Localização-1 local a uma Vnet remota na Região do Azure 2.

## <a name="further-reading"></a>Leitura adicional

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usar conectividade VPN site a site e ExpressRoute em tandem

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN site a site por meio do ExpressRoute 

A VPN site a site pode ser configurada sobre emparelhamento do Microsoft Azure ExpressRoute para trocar dados entre sua rede local e as VNets do Azure com confidencialidade, antirreprodução, autenticidade e integridade. Para obter mais informações sobre como configurar VPN IPSec site a site em modo de túnel sobre emparelhamento do Microsoft Azure ExpressRoute, consulte [VPN site a site sobre emparelhamento do Microsoft Azure ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração da VPN S2S sobre emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência sobre túnel IPSec é limitada pela capacidade de GW de VPN. A taxa de transferência de GW de VPN é menor em comparação com a taxa de transferência do ExpressRoute. Nesses cenários, o uso do túnel IPSec para tráfego altamente seguro e emparelhamento privado para todos os outros tráfego ajudaria a otimizar a utilização da largura de banda do ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN site a site como um caminho de failover seguro para ExpressRoute
O ExpressRoute é oferecido como um par de circuitos redundantes para garantir alta disponibilidade. É possível configurar a conectividade de ExpressRoute com redundância geográfica em diferentes regiões do Azure. Além disso, conforme feito em nossa configuração do teste em uma determinada região do Azure, se você quiser um caminho de failover para a conectividade do ExpressRoute poderá fazê-lo usando a VPN site a site. Quando os mesmos prefixos são anunciados no ExpressRoute e na VPN S2S, o Azure prefere o ExpressRoute sobre VPN S2S. Para evitar o roteamento assimétrico entre o ExpressRoute e a VPN S2S, a configuração de rede local também deve alternar preferindo o ExpressRoute à conectividade de VPN S2S.

Para obter mais informações sobre como configurar conexões coexistentes de VPN site a site e ExpressRoute, consulte [Coexistência de site a site e ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender conectividade de back-end para VNets de spoke e localizações de branch

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Conectividade de VNet de spoke usando emparelhamento de VNet

A arquitetura de Vnet de hub e spoke é amplamente utilizada. O hub é uma VNet (rede virtual) no Azure que atua como um ponto central de conectividade entre as VNets de spoke e a rede local. Os spokes são VNets que emparelham com o hub e podem ser usados para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão de VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, consulte [Arquitetura de hub e spoke][Hub-n-Spoke]

O emparelhamento VNet em uma região permite que as VNets de spoke usem gateway de VNet de hub (ambos os gateways de ExpressRoute e VPN) para comunicarem-se com redes remotas.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Conectividade de VNet de branch usando VPN site a site

Se você quiser VNets de branch (em diferentes regiões) e as redes locais comunicarem-se entre si por meio de uma VNet de hub, a solução nativa do Azure será conectividade de VPN site a site usando VPN. Uma opção alternativa é usar um NVA para roteamento no hub.

Para configurar gateways de VPN, consulte [Configurar Gateway de VPN][VPN]. Para implantar NVA altamente disponível, consulte [Implantar NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Para os detalhes de configuração da topologia de teste, consulte [Detalhes de configuração][Configuration].

Para análise do plano de controle da configuração do teste e reconhecimento das exibições de diferentes VNet/VLAN da topologia, consulte [Análise do plano de controle][Control-Analysis].

Para análise do plano de dados da configuração do teste e exibições de recursos de monitoramento de rede do Azure, consulte [Análise do plano de dados][Data-Analysis].

Para saber quantos circuitos do ExpressRoute você pode conectar a um Gateway do ExpressRoute, saber quantos Gateways do ExpressRoute você pode conectar a um circuito do ExpressRoute ou para obter mais informações sobre outros limites de escala do ExpressRoute, consulte [Perguntas frequentes do ExpressRoute][ExR-FAQ]



<!--Image References-->
[1]: ./media/backend-interoperability/TestSetup.png "A topologia de teste"

<!--Link References-->
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha




