---
title: 'Interoperabilidade de ExpressRoute, VPN site a site e emparelhamento VNet - Análise de plano de dados: Recursos de conectividade de back-end do Azure Interoperabilidade | Microsoft Docs'
description: Esta página fornece a análise do plano de dados da configuração de teste que é criada para analisar a interoperabilidade dos recursos de ExpressRoute, VPN Site-to-site e VNet Peering.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2018
ms.locfileid: "49946915"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Interoperabilidade de ExpressRoute, VPN site a site e emparelhamento VNet - Análise de plano de dados

Neste artigo, vamos percorre a análise do plano de dados da configuração do teste. Para revisar a Configuração de teste, veja o [Test Setup][Setup]. Para revisar os detalhes da configuração do Test Setup, consulte [Test Setup Configuration][Configuration]. Para revisar a análise do plano de controle da configuração do teste, consulte [Control Plane Analysis][Control-Analysis].

A análise do plano de dados examina o caminho percorrido pelos pacotes que atravessam de uma rede local (LAN / VNet) para outra dentro de uma topologia. O caminho de dados entre duas redes locais pode não ser necessariamente simétrico. Portanto, neste artigo, vamos analisar o caminho de encaminhamento de uma rede local para outro separadamente do caminho inverso.

##<a name="data-path-from-hub-vnet"></a>Caminho de dados de VNet do Hub

###<a name="path-to-spoke-vnet"></a>Caminho para a rede virtual de Spoke

Emparelhamento VNet emula a funcionalidade de ponte de rede entre as duas redes virtuais emparelhadas. A saída de rastreamento de rotas de uma VNet do Hub para uma VM na rede virtual spoke é listada abaixo:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

O clipe de tela a seguir é a visualização da conexão gráfica da VNet do hub e da VNet do spoke apresentadas pelo Observador da Rede do Azure:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Caminho para a rede virtual de ramificação

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

No traceroute acima, o primeiro salto é o VPN GW do Hub VNet. O segundo salto é o VPN GW da Branch VNet, cujo endereço IP não é anunciado na VNet do hub. O terceiro salto é a VM na filial VNet.

O clipe de tela a seguir é a visualização de conexão gráfica da VNet do hub e da VNet de filial apresentada pelo Observador da Rede do Azure:

[![2]][2]

Para a mesma conexão, o recorte de tela seguir é o modo de exibição de grade apresentado pelo observador de rede do Azure:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Caminho para o local no local-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Em que o rastreamento de rotas acima, o primeiro salto é o ponto de extremidade do túnel de GW do ExpressRoute para o MSEE. O segundo e terceiro salto são respectivamente roteador CE e IPs de LAN local 1, esses endereços IP não são divulgados na VNet de Hub. O quarto salto é a VM no local-1.


###<a name="path-to-on-premises-location-2"></a>Caminho para o local-2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Em que o rastreamento de rotas acima, o primeiro salto é o ponto de extremidade do túnel de GW do ExpressRoute para o MSEE. O segundo e o terceiro salto, respectivamente, são roteador CE e Local 2 IPs de LAN da localidade, esses endereços IP não são anunciados na VNet do hub. O quarto salto é a VM no Local-2.

###<a name="path-to-remote-vnet"></a>Caminho para a rede virtual remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Em que o rastreamento de rotas acima, o primeiro salto é o ponto de extremidade do túnel de GW do ExpressRoute para o MSEE. O segundo salto é o IP GW da VNet Remota. O intervalo de IP do segundo salto não é anunciado na VNet do hub. O terceiro salto é a VM na rede virtual remota.

##<a name="data-path-from-spoke-vnet"></a>Caminho de dados de rede virtual de Spoke

Lembre-se de que o Spired VNet compartilha a visão de rede do Hub VNet. Através do encaminhamento VNet, o spoke VNet usa a conectividade de gateway remoto do hub VNet como se eles estivessem conectados diretamente ao spoke VNet.

###<a name="path-to-hub-vnet"></a>Caminho para a VNet do Hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Caminho para a rede virtual de ramificação

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

No traceroute acima, o primeiro salto é o VPN GW do Hub VNet. O segundo salto é o VPN GW do Branch VNet, cujo endereço IP não é anunciado dentro do Hub / Spoke VNet. O terceiro salto é a VM na filial VNet.

###<a name="path-to-on-premises-location-1"></a>Caminho para o local no local-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

No traceroute acima, o primeiro salto é o endpoint de túnel GR ExpressRoute do Hub VNet para MSEE. O segundo e o terceiro salto, respectivamente, são o roteador CE e os IPs da LAN do Local 1 no local, esses endereços IP não são anunciados na VNet do hub / spoke. O quarto salto é a VM no local-1.

###<a name="path-to-on-premises-location-2"></a>Caminho para o local-2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

No traceroute acima, o primeiro salto é o endpoint de túnel GR ExpressRoute do Hub VNet para MSEE. O segundo e o terceiro salto, respectivamente, são o roteador CE e os IPs locais da localidade 2, esses endereços IP não são anunciados nas VNets Hub / Spoke. O quarto salto é a VM no Local-2.

###<a name="path-to-remote-vnet"></a>Caminho para a rede virtual remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

No traceroute acima, o primeiro salto é o endpoint de túnel GR ExpressRoute do Hub VNet para MSEE. O segundo salto é o IP GW da VNet Remota. O intervalo de IP do segundo salto não é anunciado dentro da VNet de hub / spoke. O terceiro salto é a VM na rede virtual remota.

##<a name="data-path-from-branch-vnet"></a>Caminho de dados de rede virtual do Branch

###<a name="path-to-hub-vnet"></a>Caminho para a VNet do Hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

No traceroute acima, o primeiro salto é o VPN GW da Branch VNet. O segundo salto é o VPN GW do Hub VNet, cujo endereço IP não é anunciado dentro do Remote VNet. O terceiro salto é a VM na VNet do Hub.

###<a name="path-to-spoke-vnet"></a>Caminho para a rede virtual de Spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

No traceroute acima, o primeiro salto é o VPN GW da Branch VNet. O segundo salto é o VPN GW do Hub VNet, cujo endereço IP não é anunciado dentro do VNet remoto, e o terceiro salto é o VM no Spoke VNet.

###<a name="path-to-on-premises-location-1"></a>Caminho para o local no local-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

No traceroute acima, o primeiro salto é o VPN GW da Branch VNet. O segundo salto é o VPN GW do Hub VNet, cujo endereço IP não é anunciado dentro do Remote VNet. O terceiro salto é o ponto de terminação do túnel VPN no roteador CE principal. O quarto salto é um endereço IP interno do endereço IP da LAN Local-1 local que não é anunciado fora do roteador CE. O quinto salto é a VM de destino no Local-1 local.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Caminho para Local-2 e VNet Remoto no Local

Como discutimos antes na análise do plano de controle, a ramificação de rede virtual não tem nenhuma visibilidade para o local no local-2 ou a rede virtual remota acordo com a configuração de rede. Os seguintes resultados ping confirmam o fato. 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

##<a name="data-path-from-on-premises-location-1"></a>Caminho de dados do local-1

###<a name="path-to-hub-vnet"></a>Caminho para a VNet do Hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

No traceroute acima, os dois primeiros saltos fazem parte da rede On-Premises. O terceiro salto é a interface principal do MSEE voltada para o roteador CE. O quarto salto é ExpressRoute G / W do hub VNet, cujo intervalo de IP não é anunciado para a rede no local. O quinto salto é o VM de destino.

O Observador de Rede do Azure fornece apenas a exibição centrada no Azure. Portanto, para a exibição centralizada no local, usamos o NPM (Monitor de Desempenho de Rede do Azure). O NPM fornece agentes que podem ser servidores instalados na rede fora do Azure e fazem análise de caminho de dados.

O clipe de tela a seguir é a exibição de topologia da conectividade da VM de localização local 1 para a VM na VNet do hub via ExpressRoute.

[![4]][4]

Lembre-se de que a configuração de teste usa a VPN de site para site como conectividade de backup para o ExpressRoute entre o Location-1 local e o Hub VNet. Para testar o caminho de dados, induziremos uma falha do link da Rota Expressa entre o roteador principal local Location-1 CE e o MSEE correspondente, desligando a interface CE voltada para o MSEE.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

O clipe de tela a seguir é a exibição de topologia da conectividade da VM local de 1 localidade para a VM na VNet do hub por meio da conectividade VPN de site a site quando a conectividade da Rota Expressa está inativa.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Caminho para a rede virtual de Spoke

Vamos trazer de volta a conectividade primária do ExpressRoute para fazer a análise do caminho de dados em direção ao Spoke VNet.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

Vamos trazer a conectividade primária ExpressRoute-1 para o restante da análise do caminho de dados.

###<a name="path-to-branch-vnet"></a>Caminho para a rede virtual de ramificação

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Caminho para o local-2

Conforme discutimos anteriormente na análise do plano de controle, a Local-1 local não tem visibilidade da localização local 2 por configuração de rede. Os seguintes resultados ping confirmam o fato. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Caminho para a rede virtual remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Caminho de dados do local-2

###<a name="path-to-hub-vnet"></a>Caminho para a VNet do Hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Caminho para a rede virtual de Spoke

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Caminho para a rede virtual, rede virtual de 1 de local e remoto no local de ramificação

Como discutimos anteriormente na análise do plano de controle, o Local-1 não tem visibilidade para ramificar a VNet, a localização 1 e a VNet Remota de acordo com a configuração da rede. 

##<a name="data-path-from-remote-vnet"></a>Caminho de dados de rede virtual remota

###<a name="path-to-hub-vnet"></a>Caminho para a VNet do Hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Caminho para a rede virtual de Spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Caminho para a VNet de ramificação e local-2

Como discutimos anteriormente na análise do plano de controle, a VNet remota não tem visibilidade para ramificar a VNet e para a localização local 2 pela configuração de rede. 


### <a name="path-to-on-premises-location-1"></a>Caminho para o local no local-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Leitura adicional

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Usando a conectividade VPN ExpressRoute e Site-to-Site em conjunto

####<a name="site-to-site-vpn-over-expressroute"></a>VPN site a Site por meio do ExpressRoute

A VPN site a site pode ser configurada por meio do ExpressRoute Microsoft emparelhamento para trocar dados entre sua rede local e suas VNets do Azure com confidencialidade, ante reprodução, autenticidade e integridade. Para obter mais informações sobre como configurar a VPN IPSec Site-to-Site no modo de encapsulamento sobre o emparelhamento da Microsoft ExpressRoute, consulte [VPN site a site sobre o Microsoft-emparelhamento ExpressRoute][S2S-Over-ExR]. 

A principal limitação da configuração do S2S VPN sobre o emparelhamento da Microsoft é a taxa de transferência. A taxa de transferência no túnel IPSec é limitada pela capacidade de VPN GW. A taxa de transferência VPN GW é menor em comparação com a taxa de transferência do ExpressRoute. Nesses cenários, o uso do túnel IPSec para tráfego altamente seguro e emparelhamento privado para todos os outros tráfego ajudaria a otimizar a utilização da largura de banda do ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN Site-to-Site como um caminho de failover seguro para o ExpressRoute
O ExpressRoute é oferecido como um par de circuitos redundantes para garantir alta disponibilidade. Você pode configurar a conectividade de ExpressRoute geo-redundante em diferentes regiões do Azure. Além disso, conforme feito em nossa configuração de teste, dentro de uma determinada região do Azure, se você quiser um caminho de failover para a conectividade da Rota Expressa, poderá fazê-lo usando a VPN Site a Site. Quando os mesmos prefixos são anunciados no ExpressRoute e no S2S VPN, o Azure prefere o ExpressRoute sobre o S2S VPN. Para evitar o roteamento assimétrico entre o ExpressRoute e o S2S VPN, a configuração de rede local também deve alternar preferindo o ExpressRoute à conectividade VPN S2S.

Para obter mais informações sobre como configurar as conexões coexistentes de VPN do ExpressRoute e do Site a Site, consulte [ExpressRoute e Coexistência de Site a Site][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estender a conectividade de back-end para redes virtuais e locais de filiais de spoke

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Falou de conectividade VNet usando emparelhamento VNet

A arquitetura Vnet de hub e spoke é amplamente usada. O hub é uma rede virtual (VNet) no Azure que atua como um ponto central de conectividade entre seus VNets spoke e a rede local. Os spokes são VNets que espiam com o hub e podem ser usados para isolar cargas de trabalho. O tráfego flui entre o datacenter local e o hub por meio de uma conexão ExpressRoute ou VPN. Para mais detalhes sobre a arquitetura, veja [Arquitetura de Hub e Spoke][Hub-n-Spoke]

O emparelhamento VNet em uma região permite que os VNets spoke usem gateways VNet de hub (gateways VPN e ExpressRoute) para se comunicarem com redes remotas.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Ramificar a conectividade de rede virtual usando VPN Site a Site

Se você quiser VNets de filial (em diferentes regiões) e as redes locais se comunicarem entre si por meio de uma vnet de hub, a solução nativa do Azure é conectividade de VPN de site para site usando VPN. Uma opção alternativa é usar um NVA para roteamento no hub.

Para configurar gateways VPN, consulte [Configurando o gateway VPN][VPN]. Para implantar um NVA altamente disponível, consulte [implantar um NVA altamente disponível][Deploy-NVA].

## <a name="next-steps"></a>Próximas etapas

Para saber quantos circuitos da Rota Expressa você pode conectar a um Gateway da Rota Expressa ou quantos Rádios da Rota Expressa você pode conectar a um circuito da Rota Expressa ou conhecer outros limites de escala da Rota Expressa, consulte [Perguntas frequentes da Rota Expressa][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg " Exibição do Network Watcher da conectividade do Hub VNet para o Speake VNet"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "exibição do observador de rede de conectividade de rede virtual do Hub a rede virtual de ramificação"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "exibição de grade do observador de rede de conectividade de VNet do Hub para rede virtual de ramificação"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "modo de exibição de Monitor de desempenho de rede de conectividade de VM do local de 1 a VNet de Hub por meio do ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Exibição do Network Performance Monitor de conectividade da VM Location-1 ao Hub VNet via S2S VPN"

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




