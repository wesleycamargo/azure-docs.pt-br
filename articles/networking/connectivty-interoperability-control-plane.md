---
title: 'Interoperabilidade de ExpressRoute, VPN site a site e emparelhamento VNet - Análise de plano de controle: Recursos de conectividade de back-end do Azure Interoperabilidade | Microsoft Docs'
description: Esta página fornece a análise do plano de controle da configuração de teste criada para analisar a interoperabilidade dos recursos de ExpressRoute, VPN Site-to-site e emparelhamento VNet.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: ee887da18b5666e61bc25365791b2e7dffb925e0
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946919"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---control-plane-analysis"></a>Interoperabilidade do ExpressRoute, VPN site a site e emparelhamento VNet - Análise do plano de controle

Neste artigo, vamos percorre a análise do plano de controle da configuração do teste. Se você quiser revisar o Test Setup, veja o [Test Setup][Setup]. Para revisar os detalhes da configuração do Test Setup, consulte [Test Setup Configuration][Configuration].

A análise do plano de controle examina essencialmente as rotas trocadas entre redes dentro de uma topologia. A análise do plano de controle ajuda a ver como a rede diferente visualiza a topologia.

##<a name="hub-and-spoke-vnet-perspective"></a>Perspectiva de Hub e Spoke VNet

O diagrama a seguir ilustra a rede a partir da perspectiva Hub VNet e Spoke VNet (destacada em azul). O diagrama também mostra o Número do Sistema Autônomo (ASN) de diferentes redes e rotas trocadas entre diferentes redes. 

[![1]][1]

Observe que o ASN do gateway ExpressRoute da VNet é diferente do ASN dos Microsoft Enterprise Edge Routers (MSEEs). O gateway ExpressRoute usa um ASN privado (65515) e os MSEEs usam o ASN público (12076) globalmente. Quando você configura o emparelhamento de ExpressRoute, porque MSEE é o par, você usa 12076 como o ASN do par. No lado do Azure, o MSEE estabelece o emparelhamento do eBGP com o ExpressRoute GW. O emparelhamento de eBGP dupla MSEE estabelece para cada emparelhamento de ExpressRoute é transparente no nível do plano de controle. Portanto, quando uma tabela de rotas da Rota Expressa é visualizada, você vê a ExpressRoute GW ASN da VNet para os prefixos da VNet. Uma captura de tela de tabela de rota do ExpressRoute de exemplo é mostrada abaixo: 

[![5]][5]

No Azure, o ASN é significativo apenas a partir de uma perspectiva de emparelhamento. Por padrão, o ASN do ExpressRoute GW e do VPN GW é 65515.

##<a name="on-premises-location-1-and-remote-vnet-perspective-via-expressroute-1"></a>Perspectiva na localização local 1 e remota via VNet via ExpressRoute-1

O Local-1 local e a Rede virtual remota estão conectados ao Hub VNet via ExpressRoute 1 e, portanto, compartilham a mesma perspectiva da topologia, conforme mostrado no diagrama abaixo.

[![2]][2]

##<a name="on-premises-location-1-and-branch-vnet-perspective-via-site-to-site-vpn"></a>Visão Local-1 e Filial VNet no Local via VPN Site-to-Site

Local-1 local e VNet de filial estão conectados ao VPN GW da Hub VNet via conexões VPN site a site e, portanto, compartilham a mesma perspectiva da topologia, como mostrado no diagrama abaixo.

[![3]][3]

##<a name="on-premises-location-2-perspective"></a>Perspectiva local-2 no local

Local-2 no local está conectado à VNet de Hub por meio de emparelhamento privado do ExpressRoute 2. 

[![4]][4]

## <a name="further-reading"></a>Leitura adicional

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usando a conectividade VPN ExpressRoute e Site-to-Site em conjunto

####  <a name="site-to-site-vpn-over-expressroute"></a>VPN site a Site por meio do ExpressRoute

A VPN site a site pode ser configurada por meio do ExpressRoute Microsoft emparelhamento para trocar dados entre sua rede local e suas VNets do Azure com confidencialidade, ante reprodução, autenticidade e integridade. Para obter mais informações sobre como configurar a VPN IPSec Site-to-Site no modo de encapsulamento sobre o emparelhamento da Microsoft ExpressRoute, consulte [VPN de site a site sobre o Microsoft-emparelhamento do ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração do S2S VPN sobre o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPSec é limitada pela capacidade de VPN GW. A taxa de transferência VPN GW é menor em comparação com a taxa de transferência do ExpressRoute. Nesses cenários, o uso do túnel IPSec para tráfego altamente seguro e emparelhamento privado para todos os outros tráfego ajudaria a otimizar a utilização da largura de banda do ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN Site-to-Site como um caminho de failover seguro para o ExpressRoute
O ExpressRoute é oferecido como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme feito em nossa configuração de teste, dentro de uma determinada região do Azure, se você quiser um caminho de failover para a conectividade da Rota Expressa, poderá fazê-lo usando a VPN Site a Site. Quando os mesmos prefixos são anunciados no ExpressRoute e no S2S VPN, o Azure prefere o ExpressRoute sobre o S2S VPN. Para evitar o roteamento assimétrico entre o ExpressRoute e o S2S VPN, a configuração de rede local também deve alternar preferindo o ExpressRoute à conectividade VPN S2S.

Para obter mais informações sobre como configurar conexões coexistentes de VPN de rota expressa e site a site, consulte [ExpressRoute e Coexistência de Site a Site][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender a conectividade de back-end para redes virtuais spoke e locais de filiais

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Falou de conectividade VNet usando emparelhamento VNet

A arquitetura Vnet de hub e spoke é amplamente usada. O hub é uma rede virtual (VNet) no Azure que atua como um ponto central de conectividade entre seus VNets spoke e a rede local. Os spokes são VNets que espiam com o hub e podem ser usados para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão ExpressRoute ou VPN. Para mais detalhes sobre a arquitetura, veja [Arquitetura de Hub e Spoke][Hub-n-Spoke]

O emparelhamento VNet em uma região permite que os VNets spoke usem gateways VNet de hub (gateways VPN e ExpressRoute) para se comunicarem com redes remotas.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Ramificar a conectividade de rede virtual usando VPN Site a Site

Se você quiser VNets de filial (em diferentes regiões) e as redes locais se comunicarem entre si por meio de uma vnet de hub, a solução nativa do Azure é conectividade de VPN de site para site usando VPN. Uma opção alternativa é usar um NVA para roteamento no hub.

Para configurar gateways VPN, consulte [Configurando o gateway VPN][VPN]. Para implantar um NVA altamente disponível, consulte [implantar um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Para análise do plano de dados da configuração de teste e das exibições de recursos de monitoramento de rede do Azure, consulte [Análise de plano de dados][Data-Analysis].

Para saber quantos circuitos da Rota Expressa você pode conectar a um Gateway da Rota Expressa ou quantos Rádios da Rota Expressa você pode conectar a um circuito da Rota Expressa ou conhecer outros limites de escala da Rota Expressa, consulte [Perguntas frequentes da Rota Expressa][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubView.png "hub e Spoke perspectiva de rede virtual da topologia"
[2]: ./media/backend-interoperability/Loc1ExRView.png " Perspectiva VNet local-1 e remota via ExpressRoute 1 da topologia"
[3]: ./media/backend-interoperability/Loc1VPNView.png "Localização-1 e perspectiva da filial VNet via VPN S2S da topologia"
[4]: ./media/backend-interoperability/Loc2View.png "local 2 perspectiva da topologia"
[5]: ./media/backend-interoperability/ExR1-RouteTable.png "RouteTable ExpressRoute 1"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering




