---
title: Arquivo de inclusão
description: Arquivo de inclusão
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/18/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d059cab5668eef8d4dafc1442ca9749a7dcf8c9d
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162507"
---
### <a name="what-is-the-difference-between-an-azure-virtual-network-gateway-vpn-gateway-and-an-azure-virtual-wan-vpngateway"></a>Qual é a diferença entre um gateway de rede virtual do Azure (Gateway de VPN) e um vpngateway de WAN Virtual do Azure?

O WAN Virtual fornece conectividade site a site em larga escala e é construído para proporcionar facilidade de uso, escalabilidade e taxa de transferência. Dispositivos de branch CPE provisionam e se conectam automaticamente à WAN Virtual do Azure. Esses dispositivos estão disponíveis em um ecossistema crescente de SD-WAN e parceiros de VPN.

### <a name="which-device-providers-virtual-wan-partners-are-supported-at-launch-time"></a>Quais provedores de dispositivo (parceiros de WAN Virtual) têm suporte no momento da inicialização? 

Neste momento, Citrix e Riverbed dão suporte à experiência de WAN Virtual totalmente automatizada. Para saber mais, confira [Parceiros de WAN Virtual](https://aka.ms/virtualwan).

### <a name="am-i-required-to-use-a-preferred-partner-device"></a>Eu sou obrigado a usar um dispositivo de parceiro preferido?

Não. Você pode usar qualquer dispositivo com capacidade para VPN que cumpre aos requisitos da Versão prévia para suporte a IKEv2 IPsec.

### <a name="how-do-virtual-wan-partners-automate-connectivity-with-azure-virtual-wan"></a>Como os parceiros de WAN Virtual automatizam a conectividade com a WAN Virtual do Azure?

Soluções de conectividade definidas pelo software normalmente gerenciam seus dispositivos de branch usando um controlador ou um centro de provisionamento de dispositivos. O controlador pode usar as APIs do Azure para automatizar a conectividade com a WAN Virtual do Azure. Para saber mais, confira [Automação de parceiros de WAN Virtual](../articles/virtual-wan/virtual-wan-configure-automation-providers.md).

### <a name="does-virtual-wan-change-any-existing-connectivity-features"></a>A WAN Virtual altera quaisquer recursos de conectividade existentes?   

Não há alterações nos recursos de conectividade do Azure existentes.

### <a name="are-there-new-resource-manager-resources-available-for-virtual-wan"></a>Há novos recursos do Gerenciador de recursos disponíveis para WAN Virtual?
  
Sim, a WAN Virtual apresenta novos recursos do Gerenciador de Recursos. Para saber mais, confira a [Visão geral](https://go.microsoft.com/fwlink/p/?LinkId=2004389).

### <a name="are-there-any-special-requirements-to-join-the-preview"></a>Há algum requisito especial para ingressar na Versão prévia? 

Antes de configurar a WAN Virtual do Azure, você precisa inscrever sua assinatura na versão prévia. Você se inscrever, envie um email para <azurevirtualwan@microsoft.com> com a ID da sua assinatura. Após a inscrição da sua assinatura, você receberá um email. Até que você recebe a confirmação de que sua assinatura foi registrada, não será possível trabalhar com o WAN Virtual no portal.

Considerações:

* A versão prévia está limitada a apenas regiões públicas do Azure.
* Até 100 conexões têm suporte por hub virtual. Cada conexão é composta por dois túneis que estão em uma configuração ativa-ativa. Os túneis terminam em um vpngateway do Hub Virtual do Azure.
* Considere o uso desta Versão prévia se:
  * Você quiser implantar a largura de banda agregada com menos de 1 Gbps por hub virtual.
  * Você tem um dispositivo VPN que dá suporte à configuração com base em rotas e conectividade IKEv2 IPsec.
  * Você quiser explorar o uso de dispositivos de ramificação operacionais e SD-WAN dos parceiros de lançamento (Riverbed e Citrix).<br>ou o<br>Você quiser configurar conectividade entre branches e do branch para o Azure, o que inclui o gerenciamento de configuração de seu dispositivo local. (Autoconfiguração)

### <a name="is-global-vnet-peering-supported-with-azure-virtual-wan"></a>O emparelhamento VNet Global é compatível com a WAN Virtual do Azure? 

 Não.

### <a name="can-spoke-vnets-connected-to-a-virtual-hub-communicate-with-each-other"></a>VNets spoke conectadas a um hub virtual podem se comunicar umas com as outras?

Sim. Você pode fazer diretamente o emparelhamento de VNet entre spokes que estão conectados a um hub virtual. Para saber mais, confira [Emparelhamento de rede virtual](../articles/virtual-network/virtual-network-peering-overview.md).

### <a name="can-i-deploy-and-use-my-favorite-network-virtual-appliance-in-an-nva-vnet-with-azure-virtual-wan"></a>Posso implantar e usar minha solução de virtualização de rede favorita (em uma VNet NVA) com a WAN Virtual do Azure?

Sim, você pode conectar sua solução de virtualização de rede (NVA) favorita com a WAN Virtual do Azure, mas isso exigirá recursos de roteamento no hub que estão em nosso roteiro. Todos os spokes conectados à VNet NVA devem estar conectados ao hub virtual. 

### <a name="can-an-nva-vnet-have-a-virtual-network-gateway"></a>Uma VNet NVA pode ter um gateway de rede virtual?

Não. A VNet NVA não pode ter um gateway de rede virtual se estiver conectada ao hub virtual. 

### <a name="is-there-support-for-bgp"></a>Há suporte para BGP?

Sim, há suporte para BGP. Para garantir que as rotas de uma VNet NVA sejam anunciadas adequadamente, os spokes devem desabilitar o BGP, se eles estiverem conectados a uma VNet NVA que, por sua vez, está conectado a um hub virtual. Além disso, conecte as VNets spoke ao hub virtual.

### <a name="can-i-direct-traffic-using-udr-in-the-virtual-hub"></a>Posso direcionar o tráfego usando UDR no hub virtual?

Isso está em nosso roteiro. Fique atento!

### <a name="is-there-any-licensing-or-pricing-information-for-virtual-wan"></a>Há quaisquer informações de licenciamento ou preços para a WAN Virtual?
 
Não há custos adicionais durante a Versão prévia. Os [preços atuais de VPN do Azure e egresso](https://azure.microsoft.com/pricing/details/vpn-gateway/) permanecem em vigor durante a Versão prévia.

### <a name="how-do-new-partners-that-are-not-listed-in-your-launch-partner-list-get-onboarded"></a>Como integrar novos parceiros que não estejam na sua lista de parceiros de lançamento?

Envie um email para azurevirtualwan@microsoft.com. Um parceiro ideal é aquele que tem um dispositivo que pode ser provisionado para conectividade IPSec IKEv2.

### <a name="is-it-possible-to-construct-azure-virtual-wan-with-a-resource-manager-template"></a>É possível construir a WAN Virtual do Azure com um modelo do Resource Manager?

Estamos trabalhando nisso. No momento, o serviço é orientado a REST e ao Portal.

### <a name="is-branch-to-branch-connectivity-allowed-in-virtual-wan"></a>A conectividade de branch para branch é permitida na WAN Virtual?

Sim, a conectividade de branch para branch está disponível na WAN Virtual.

### <a name="does-branch-to-branch-traffic-traverse-through-the-azure-virtual-wan"></a>O tráfego de branch para branch atravessa a WAN Virtual do Azure?

Sim.

### <a name="how-is-virtual-wan-different-from-the-existing-azure-virtual-network-gateway"></a>Como a WAN Virtual é diferente do Gateway de Rede Virtual do Azure existente?

VPN de Gateway de Rede Virtual é limitada a 30 túneis. Para conexões, você deve usar a WAN Virtual para VPN em larga escala. Na visualização pública, isso é limitado a 100 conexões de branch com 1 Gbps no hub.

### <a name="does-this-virtual-wan-require-expressroute-from-each-site"></a>Essa WAN Virtual exige o ExpressRoute de cada site?

Não, a WAN Virtual não exige o ExpressRoute de cada site. Ele usa a conectividade de site a site IPsec padrão por meio de links da Internet do dispositivo para um hub de WAN Virtual do Azure.

### <a name="is-there-a-network-throughput-limit-when-using-azure-virtual-wan"></a>Há um limite de taxa de transferência de rede ao usar a WAN Virtual do Azure?

Na visualização pública, o número de branches é limitado a 100 conexões por hub/região e a um total de 1G no hub.

### <a name="does-virtual-wan-allow-the-on-premises-device-to-utilize-multiple-isps-in-parallel-or-is-it-always-a-single-vpn-tunnel"></a>A WAN Virtual permite que o dispositivo local utilize vários ISPs em paralelo ou sempre será um único túnel VPN?

Sim, você pode ter túneis ativo-ativo (2 túneis = 1 conexão WAN Virtual do Azure) de um único branch, dependendo do dispositivo branch.

### <a name="how-is-traffic-is-routed-on-the-azure-backbone"></a>Como o tráfego é roteado no backbone do Azure?

O tráfego permite que o padrão: dispositivo branch ->ISP->Microsoft Edge->Microsoft DC->Microsoft edge->ISP->dispositivo branch.

### <a name="in-this-model-what-do-you-need-at-each-site-just-an-internet-connection"></a>Nesse modelo, o que você precisa em cada site? Apenas uma conexão com a Internet?

Sim. Uma conexão com a Internet e o dispositivo físico, preferencialmente de nossos parceiros integrados. A menos que você queira gerenciar manualmente a configuração e a conectividade para o Azure do seu dispositivo preferido.