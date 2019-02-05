---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/19/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 98ea4d78a473123708be6e371587252acad6ffcd
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/29/2019
ms.locfileid: "55205114"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um vpngateway de WAN Virtual do Azure?

A WAN Virtual fornece conectividade site a site em larga escala e é criada visando produtividade, escalabilidade e facilidade de uso. A funcionalidade de conectividade ponto a site e ExpressRoute está atualmente em versão prévia. Dispositivos de branch CPE provisionam e se conectam automaticamente à WAN Virtual do Azure. Esses dispositivos estão disponíveis em um ecossistema crescente de SD-WAN e parceiros de VPN. Confira a [Lista de Parceiros Preferenciais](https://go.microsoft.com/fwlink/p/?linkid=2019615).

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quais provedores de dispositivo (parceiros de WAN Virtual) têm suporte no momento da inicialização? 

Neste momento, muitos parceiros dão suporte à experiência de WAN Virtual totalmente automatizada. Para saber mais, confira [Parceiros de WAN Virtual](https://go.microsoft.com/fwlink/p/?linkid=2019615). 

### <a name="what-are-the-virtual-wan-partner-automation-steps"></a>Quais são as etapas de automação de parceiro de WAN Virtual?

Para conhecer as etapas de automação de parceiro, confira [Automação de parceiro de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Eu sou obrigado a usar um dispositivo de parceiro preferido?

 Não. Você pode usar qualquer dispositivo com capacidade para VPN que siga os requisitos do Azure para suporte a IPsec IKEv2/IKEv1.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

Soluções de conectividade definidas pelo software normalmente gerenciam seus dispositivos de branch usando um controlador ou um centro de provisionamento de dispositivos. O controlador pode usar as APIs do Azure para automatizar a conectividade com a WAN Virtual do Azure. Para obter mais informações, confira a automação de parceiro da WAN Virtual.

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera quaisquer recursos de conectividade existentes?   

Não há alterações nos recursos de conectividade do Azure existentes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Há novos recursos do Gerenciador de recursos disponíveis para WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Gerenciador de Recursos. Para saber mais, confira a [Visão geral](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="how-many-vpn-devices-can-connect-to-a-single-hub"></a>Quantos dispositivos VPN podem se conectar a um único hub?

O hub virtual é compatível com até 1000 conexões. Cada conexão é composta por dois túneis que estão em uma configuração ativa-ativa. Os túneis terminam em um vpngateway do Hub Virtual do Azure.

### <a name="can-the-on-premises-vpn-device-connect-to-multiple-hubs"></a>O dispositivo VPN local pode se conectar a vários hubs?

Sim. O fluxo de tráfego ao começar seria do dispositivo local para a borda da Microsoft mais próxima e, em seguida, para o hub virtual.

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O emparelhamento VNet Global é compatível com a WAN Virtual do Azure? 

  Não.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>VNets spoke conectadas a um hub virtual podem se comunicar umas com as outras?

Sim. VNets de spoke podem se comunicar diretamente por meio de Emparelhamento de Rede Virtual. No entanto, não damos suporte a VNets que se comunicam de modo transitório por meio do hub. Para saber mais, confira [Emparelhamento de rede virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implantar e usar minha solução de virtualização de rede favorita (em uma VNet NVA) com a WAN Virtual do Azure?

Sim, você pode conectar a VNET da sua NVA (solução de virtualização de rede) favorita à WAN Virtual do Azure. Primeiro, conecte a VNET da sua solução de virtualização de rede virtual ao hub com uma conexão de rede virtual do hub. Em seguida, crie uma rota do hub virtual com um próximo salto apontando para a solução de virtualização. Você pode aplicar várias rotas à tabela de rotas do hub virtual. Além disso, todos os spokes conectados à VNET da NVA também precisam ser conectados ao hub virtual para garantir que as rotas da VNET do spoke sejam propagadas para os sistemas locais.

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Uma VNet NVA pode ter um gateway de rede virtual?

 Não. A VNet NVA não pode ter um gateway de rede virtual se estiver conectada ao hub virtual. 

### <a name="is-there-support-for-bgp"></a>Há suporte para BGP?

Sim, há suporte para BGP. Para garantir que as rotas de uma VNET da NVA sejam anunciadas adequadamente, os spokes precisam desabilitar o BGP, caso estejam conectados a uma VNET da NVA que, por sua vez, esteja conectada a um hub virtual. Além disso, conecte as VNETS do spoke ao hub virtual para garantir que as rotas da VNET do spoke sejam propagadas do spoke para os sistemas locais.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego usando UDR no hub virtual?

Sim, você pode direcionar o tráfego para uma VNET usando a tabela de rotas do hub virtual.

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Há quaisquer informações de licenciamento ou preços para a WAN Virtual?
 
Sim. Confira a página [Preços](https://azure.microsoft.com/pricing/details/virtual-wan/).

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como integrar novos parceiros que não estejam na sua lista de parceiros de lançamento?

Envie um email para azurevirtualwan@microsoft.com. Um parceiro ideal é aquele que tem um dispositivo que pode ser provisionado para conectividade IPsec IKEv1 ou IKEv2.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir a WAN Virtual do Azure com um modelo do Resource Manager?

Uma configuração simples de uma WAN Virtual com um hub e um vpnsite pode ser criada usando um [Modelo de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Network). A WAN Virtual é basicamente um serviço controlado por REST ou pelo portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade de branch para branch é permitida na WAN Virtual?

Sim, a conectividade de branch a branch está disponível na WAN Virtual para VPN e da VPN para o ExpressRoute. Embora a VPN site a site já esteja disponível, o ExpressRoute ponto a site está atualmente em versão prévia.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de branch para branch atravessa a WAN Virtual do Azure?

Sim.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Como a WAN Virtual é diferente do Gateway de Rede Virtual do Azure existente?

VPN de Gateway de Rede Virtual é limitada a 30 túneis. Para conexões, você deve usar a WAN Virtual para VPN em larga escala. Você pode conectar até 1000 conexões de branches com 2 Gbps no hub para todas as regiões, exceto a região Centro-oeste dos EUA. Para a região Centro-oeste dos EUA, há 20 Gbps disponíveis. No futuro será disponibilizado 20 Gbps para mais regiões. Uma conexão é um túnel de ativo-ativo do dispositivo VPN local para o hub virtual. Pode haver um hub por região, o que significa que você pode conectar a mais de 1000 branches entre os hubs.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Essa WAN Virtual exige o ExpressRoute de cada site?

Não, a WAN Virtual não exige o ExpressRoute de cada site. Ele usa a conectividade de site a site IPsec padrão por meio de links da Internet do dispositivo para um hub de WAN Virtual do Azure. Seus sites podem estar conectados a uma rede do provedor usando um circuito do ExpressRoute. Para os sites conectados usando o ExpressRoute no hub virtual (em versão prévia), os sites podem ter um fluxo de tráfego branch a branch entre a VPN e o ExpressRoute. 

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há um limite de taxa de transferência de rede ao usar a WAN Virtual do Azure?

O número de branches é limitado a 1000 conexões por hub/região e um total de 2 G no hub. A exceção é o Centro-oeste dos EUA, que tem um total de 20 Gbps. Lançaremos 20 Gbps para outras regiões no futuro.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo local utilize vários ISPs em paralelo ou sempre será um único túnel VPN?

Sim, você pode ter túneis ativo-ativo (2 túneis = 1 conexão WAN Virtual do Azure) de um único branch, dependendo do dispositivo branch.

### <a name="how-is-traffic-routed-on-the-azure-backbone"></a>Como o tráfego é roteado no backbone do Azure?

O tráfego segue o padrão: dispositivo branch ->ISP->Microsoft Edge->Microsoft DC->Microsoft Edge->ISP->dispositivo branch

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Nesse modelo, o que você precisa em cada site? Apenas uma conexão com a Internet?

Sim. Uma conexão de Internet e um dispositivo físico, preferencialmente de nossos [parceiros](https://go.microsoft.com/fwlink/p/?linkid=2019615) integrados. Opcionalmente, você pode gerenciar manualmente a configuração e a conectividade com o Azure usando seu dispositivo preferido.
