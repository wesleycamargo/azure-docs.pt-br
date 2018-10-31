---
title: 'Interoperabilidade de ExpressRoute, VPN Site a site e o emparelhamento VNet - detalhes de configuração: interoperabilidade de recursos de conectividade de back-end do Azure | Microsoft Docs'
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
ms.openlocfilehash: d94900b764331c6fff0e0384e6edbebc88ac938b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946914"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---test-configuration-details"></a>Interoperabilidade de rede virtual emparelhamento - detalhes de configuração de teste, VPN Site a site e ExpressRoute

Neste artigo, vamos percorrer os detalhes de configuração da configuração do teste. Para revisar a Configuração de teste, veja o [Test Setup][Setup]. 

##<a name="spoke-vnet-connectivity-using-vnet-peering"></a>Conectividade de VNet de spoke usando emparelhamento de VNet

A captura de tela abaixo do portal do Azure mostra os detalhes de emparelhamento de rede virtual de rede virtual spoke. Para obter orientação passo a passo para configurar o emparelhamento VNet entre duas redes virtuais, consulte [Gerenciar o emparelhamento de VNet][VNet-Config]. Se você quiser que a rede virtual Spoke use os gateways conectados à rede virtual Hub, você precisa verificar *Usar gateways remotos*.

[![1]][1]

A captura de tela abaixo do portal do Azure mostra os detalhes de emparelhamento de VNet do hub de rede virtual. Se você quiser que a VNet do Hub para permita que a rede virtual Spoke use seus gateways, você precisa verificar *Usar gateways remotos*.

[![2]][2]

##<a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Conectividade de VNet de branch usando VPN site a site

A conectividade VPN site a Site entre redes virtuais de filial e o Hub é configurada usando gateways de VPN. Por padrão, os gateways VPN e ExpressRoute são configurados com o valor ASN privado de 65515. Gateway de VPN permite que você altere o valor ASN. Na configuração do teste, conforme mostrado na imagem a seguir do portal do Azure, o valor ASN do gateway de VPN de VNet branch é alterado para 65516 para habilitar o roteamento de eBGP entre redes virtuais de filial e o Hub.


[![3]][3]


##<a name="location-1-on-premises-connectivity-using-expressroute-and-site-to-site-vpn"></a>Local 1 na conectividade local usando VPN Site a Site e ExpressRoute

###<a name="expressroute1-configuration-details"></a>Detalhes de configuração ExpressRoute1

A seguinte captura de tela do portal mostra a configuração de circuito de ExpressRoute de 1 de região do Azure na direção de roteadores de CE locais local-1.

[![4]][4]

A captura de tela do portal a seguir mostra a configuração de conexão entre ExpressRoute1 circuito e a VNet do Hub.

[![5]][5]

A configuração a seguir é a listagem do roteador CE primário (roteadores são usados como roteadores CE na configuração de teste do Cisco ASR1000) de configuração relacionados à conectividade de emparelhamento privada do ExpressRoute. Quando o VPN Site a Site e o circuito de ExpressRoute são configurados em paralelo para conectar uma rede local ao Azure; o Azure prefere o circuito do ExpressRoute por padrão. Para evitar roteamento assimétrico, na rede local também deve preferir o ExpressRoute por meio da VPN Site a Site para as rotas recebidas tanto por meio do ExpressRoute e VPN Site a Site. Isso é feito na configuração a seguir usando o atributo de preferência de local do BGP. 

    interface TenGigabitEthernet0/0/0.300
     description Customer 30 private peering to Azure
     encapsulation dot1Q 30 second-dot1q 300
     ip vrf forwarding 30
     ip address 192.168.30.17 255.255.255.252
    !
    interface TenGigabitEthernet1/0/0.30
     description Customer 30 to south bound LAN switch
     encapsulation dot1Q 30
     ip vrf forwarding 30
     ip address 192.168.30.0 255.255.255.254
     ip ospf network point-to-point
    !
    router ospf 30 vrf 30
     router-id 10.2.30.253
     redistribute bgp 65021 subnets route-map BGP2OSPF
     network 192.168.30.0 0.0.0.1 area 0.0.0.0
    default-information originate always
     default-metric 10
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 192.168.30.18 remote-as 12076
      neighbor 192.168.30.18 activate
      neighbor 192.168.30.18 next-hop-self
      neighbor 192.168.30.18 soft-reconfiguration inbound
      neighbor 192.168.30.18 route-map prefer-ER-over-VPN in
      neighbor 192.168.30.18 prefix-list Cust30_to_Private out
     exit-address-family
    !
    route-map prefer-ER-over-VPN permit 10
     set local-preference 200
    !
    ip prefix-list Cust30_to_Private seq 10 permit 10.2.30.0/25
    !

###<a name="site-to-site-vpn-configuration-details"></a>Detalhes de configuração de VPN site a Site

Veja a seguir a listagem da configuração de roteador de CE primário relacionada à conectividade VPN Site a Site:

    crypto ikev2 proposal Cust30-azure-proposal
     encryption aes-cbc-256 aes-cbc-128 3des
     integrity sha1
     group 2
    !
    crypto ikev2 policy Cust30-azure-policy
     match address local 66.198.12.106
     proposal Cust30-azure-proposal
    !
    crypto ikev2 keyring Cust30-azure-keyring
     peer azure
      address 52.168.162.84
      pre-shared-key local IamSecure123
      pre-shared-key remote IamSecure123
    !
    crypto ikev2 profile Cust30-azure-profile
     match identity remote address 52.168.162.84 255.255.255.255
     identity local address 66.198.12.106
     authentication local pre-share
     authentication remote pre-share
     keyring local Cust30-azure-keyring
    !
    crypto ipsec transform-set Cust30-azure-ipsec-proposal-set esp-aes 256 esp-sha-hmac
     mode tunnel
    !
    crypto ipsec profile Cust30-azure-ipsec-profile
     set transform-set Cust30-azure-ipsec-proposal-set
     set ikev2-profile Cust30-azure-profile
    !
    interface Loopback30
     ip address 66.198.12.106 255.255.255.255
    !
    interface Tunnel30
     ip vrf forwarding 30
     ip address 10.2.30.125 255.255.255.255
     tunnel source Loopback30
     tunnel mode ipsec ipv4
     tunnel destination 52.168.162.84
     tunnel protection ipsec profile Cust30-azure-ipsec-profile
    !
    router bgp 65021
     !
     address-family ipv4 vrf 30
      network 10.2.30.0 mask 255.255.255.128
      neighbor 10.10.30.254 remote-as 65515
      neighbor 10.10.30.254 ebgp-multihop 5
      neighbor 10.10.30.254 update-source Tunnel30
      neighbor 10.10.30.254 activate
      neighbor 10.10.30.254 soft-reconfiguration inbound
     exit-address-family
    !
    ip route vrf 30 10.10.30.254 255.255.255.255 Tunnel30

##<a name="location-2-on-premises-connectivity-using-expressroute"></a>Local 2 na conectividade local usando VPN Site a Site e ExpressRoute

Um segundo circuito do ExpressRoute, mais próximo ao Local 2 no local, conecta-se o Local 2 no local para a VNet do hub. A captura de tela do portal a seguir mostra a segunda configuração de ExpressRoute.

[![6]][6]

A captura de tela do portal a seguir mostra a configuração de conexão entre o circuito do ExpressRoute e a VNet do Hub.

[![7]][7]

O ExpressRoute1 conecta-se a Vnet do Hub e o Local 1 no local a uma rede virtual remota em uma região do Azure diferente.

[![8]][8]

## <a name="further-reading"></a>Leitura adicional

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usando a conectividade VPN ExpressRoute e Site-to-Site em conjunto

#### <a name="site-to-site-vpn-over-expressroute"></a>VPN site a Site por meio do ExpressRoute

A VPN site a site pode ser configurada sobre emparelhamento do Microsoft Azure ExpressRoute para trocar dados entre sua rede local e as VNets do Azure com confidencialidade, antirreprodução, autenticidade e integridade. Para obter mais informações sobre como configurar VPN IPSec site a site em modo de túnel sobre emparelhamento do Microsoft Azure ExpressRoute, consulte [VPN site a site sobre emparelhamento do Microsoft Azure ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração da VPN S2S sobre emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPSec é limitada pela capacidade de VPN GW. A taxa de transferência VPN GW é menor em comparação com a taxa de transferência do ExpressRoute. Nesses cenários, o uso do túnel IPSec para tráfego altamente seguro e emparelhamento privado para todos os outros tráfego ajudaria a otimizar a utilização da largura de banda do ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN Site-to-Site como um caminho de failover seguro para o ExpressRoute
O ExpressRoute é oferecido como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme feito em nossa configuração de teste, dentro de uma determinada região do Azure, se você quiser um caminho de failover para a conectividade da Rota Expressa, poderá fazê-lo usando a VPN Site a Site. Quando os mesmos prefixos são anunciados no ExpressRoute e no S2S VPN, o Azure prefere o ExpressRoute sobre o S2S VPN. Para evitar o roteamento assimétrico entre o ExpressRoute e a VPN S2S, a configuração de rede local também deve alternar preferindo o ExpressRoute à conectividade de VPN S2S.

Para obter mais informações sobre como configurar conexões coexistentes de VPN site a site e ExpressRoute, consulte [Coexistência de site a site e ExpressRoute][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender conectividade de back-end para VNets de spoke e localizações de branch

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Conectividade de VNet de spoke usando emparelhamento de VNet

A arquitetura Vnet de hub e spoke é amplamente usada. O hub é uma rede virtual (VNet) no Azure que atua como um ponto central de conectividade entre seus VNets spoke e a rede local. Os spokes são VNets que espiam com o hub e podem ser usados para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão de VPN ou ExpressRoute. Para obter mais informações sobre a arquitetura, consulte [Arquitetura de hub e spoke][Hub-n-Spoke]

O emparelhamento VNet em uma região permite que as VNets de spoke usem gateway de VNet de hub (ambos os gateways de ExpressRoute e VPN) para comunicarem-se com redes remotas.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Conectividade de VNet de branch usando VPN site a site

Se você quiser VNets de filial (em diferentes regiões) e as redes locais se comunicarem entre si por meio de uma vnet de hub, a solução nativa do Azure é conectividade de VPN de site para site usando VPN. Uma opção alternativa é usar um NVA para roteamento no hub.

Para configurar Gateways de VPN, consulte [Configurando o gateway de VPN][VPN]. Para implantar um NVA altamente disponível, consulte [implantar um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Para análise do plano de controle da configuração do teste e reconhecimento das exibições de diferentes VNet/VLAN da topologia, consulte [Análise do plano de controle][Control-Analysis].

Para análise do plano de dados da configuração do teste e exibições de recursos de monitoramento de rede do Azure, consulte [Análise do plano de dados][Data-Analysis].

Para saber quantos circuitos do Azure ExpressRoute você pode conectar a um Gateway do Azure ExpressRoute ou quantos Gateways do Azure ExpressRoute você pode conectar a um circuito da Rota Expressa ou conhecer outros limites de escala do Azure ExpressRoute, consulte [Perguntas frequentes da Rota Expressa][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/SpokeVNet_peering.png "Emparelhamento da rede virtual Spoke da rede virtual"
[2]: ./media/backend-interoperability/HubVNet-peering.png "Emparelhamento da rede virtual Hub da rede virtual"
[3]: ./media/backend-interoperability/BranchVNet-VPNGW.png "Configuração VPN GW da rede virtual de ramificação"
[4]: ./media/backend-interoperability/ExR1.png "Configuração do ExpressRoute1"
[5]: ./media/backend-interoperability/ExR1-Hub-Connection.png "Configuração de Conexão do ExpressRoute1 ao Hub de rede virtual ExR GW"
[6]: ./media/backend-interoperability/ExR2.png "Configuração do ExpressRoute2"
[7]: ./media/backend-interoperability/ExR2-Hub-Connection.png "Configuração de Conexão do ExpressRoute2 ao Hub de rede virtual ExR GW"
[8]: ./media/backend-interoperability/ExR2-Remote-Connection.png "Configuração de Conexão do ExpressRoute2 aa rede virtual ExR GW remota"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
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




