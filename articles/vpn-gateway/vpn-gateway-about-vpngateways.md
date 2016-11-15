---
title: Sobre o Gateway de VPN| Microsoft Docs
description: "Saiba mais sobre conexões de Gateway de VPN para Redes Virtuais do Azure."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 909320f7f898a10ff58c50d276bbe9b2b2a92b48


---
# <a name="about-vpn-gateway"></a>Sobre o Gateway de VPN
Um gateway de rede virtual é usado para enviar o tráfego de rede entre as redes virtuais do Azure e locais, bem como entre as redes virtuais no Azure (VNet a VNet). Quando você configura um gateway de VPN, é necessário criar e configurar um gateway de rede virtual e uma conexão de gateway de rede virtual.

No modelo de implantação do Resource Manager, quando você cria um recurso de gateway de rede virtual, também especifica várias configurações. Uma das configurações requeridas é '-GatewayType'. Há dois tipos de gateway de rede virtual: Vpn e ExpressRoute. 

Quando o tráfego de rede é enviado em uma conexão privada dedicada, você pode usar o tipo de gateway 'ExpressRoute'. Isso também é referido como um gateway ExpressRoute. Quando o tráfego de rede é enviado criptografado em uma conexão pública, você pode usar o tipo de gateway 'Vpn'. Isso é referido como um gateway VPN. As conexões Site a Site, Ponto a Site e VNet a VNet usam um gateway VPN.

Cada rede virtual pode ter apenas um gateway de rede virtual por tipo de gateway. Por exemplo, você pode ter um gateway de rede virtual que usa -GatewayType ExpressRoute, e outro que usa -GatewayType Vpn. Este artigo se concentra principalmente no Gateway de VPN. Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Preços
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKUs de gateway
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para saber mais sobre as SKUs de gateway para Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Taxa de transferência agregada estimada por SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>Configurando um Gateway de VPN
Quando você configura um gateway VPN, as instruções usadas dependem do modelo de implantação utilizado para criar sua rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Para obter mais informações sobre os modelos de implantação, consulte [Noções básicas sobre o Resource Manager e os modelos de implantação clássicos](../resource-manager-deployment-model.md).

Uma conexão de gateway VPN conta com vários recursos que são configurados com definições específicas. A maioria dos recursos pode ser configurada separadamente, embora eles devam ser configurados em uma determinada ordem em alguns casos. Você pode começar criando e configurando os recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode, posteriormente, decidir trocar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária. Para obter informações sobre os recursos individuais e as configurações do Gateway de VPN, consulte [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md).

As seções abaixo contêm tabelas que listam:

* modelo de implantação disponível
* ferramentas de configuração disponíveis
* links que levam você diretamente a um artigo, se disponível

Use os diagramas e as descrições para ajudar a selecionar a topologia de conexão adequada a seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar topologias mais complexas usando os diagramas como uma diretriz.

## <a name="sitetosite-and-multisite"></a>Site a Site e Vários Sites
### <a name="sitetosite"></a>Site a site
Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.   

![Conexão S2S](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")

### <a name="multisite"></a>Multissite
Você pode criar e configurar uma conexão de gateway VPN entre sua VNet e várias redes locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (gateway dinâmico para VNets clássicas). Como uma VNet pode ter apenas um gateway VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Isso é geralmente chamado de conexão com "vários sites".

![Conexão de vários sites](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### <a name="deployment-models-and-methods-for-sitetosite-and-multisite"></a>Modelos de implantação e métodos para Site a Site e Vários Sites
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnettovnet"></a>VNet a VNet
Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode até combinar a comunicação VNet a VNet com as configurações de conexão de vários sites. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

As redes virtuais às quais você se conecta podem estar:

* na mesma região ou em regiões diferentes
* na mesma assinatura ou em assinaturas diferentes 
* no mesmo modelo de implantação ou em modelos de implantação diferentes

![Conectando VNet a VNet](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")

#### <a name="connections-between-deployment-models"></a>Conexões entre os modelos de implantação
Atualmente, o Azure tem dois modelos de implantação: o clássico e o Resource Manager. Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Você pode criar uma conexão entre as Redes Virtuais para permitir que os recursos em uma rede virtual se comuniquem diretamente com os recursos em outra.

#### <a name="vnet-peering"></a>Emparelhamento VNet
Talvez você possa usar o emparelhamento VNet para criar sua conexão, desde que a rede virtual atenda a certos requisitos. O emparelhamento de Rede Virtual não usa um gateway de rede virtual. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnettovnet"></a>Modelos de implantação e métodos para VNet a VNet
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="pointtosite"></a>Ponto a Site
Uma conexão de gateway VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). As conexões P2S não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Você estabelece a conexão VPN ao iniciá-la do computador cliente. Essa solução é útil quando você deseja conectar sua VNet a partir de um local remoto, como de casa ou em uma conferência, ou quando tem apenas alguns clientes que precisam conectar-se a uma VNet. As conexões P2S podem ser usadas junto com as conexões S2S pelo mesmo gateway VPN, desde que todos os requisitos de configuração de ambas as conexões sejam compatíveis.

![Conexão ponto a site](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### <a name="deployment-models-and-methods-for-pointtosite"></a>Modelos de implantação e métodos para Ponto a Site
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>Rota Expressa
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

Em uma conexão ExpressRoute, um gateway de rede virtual é configurado com o tipo de gateway 'ExpressRoute', em vez de 'Vpn'. Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

## <a name="sitetosite-and-expressroute-coexisting-connections"></a>Conexões coexistentes Site a Site e de Rota Expressa
A Rota Expressa é uma conexão direta e dedicada da sua WAN (não pela Internet pública) para Serviços Microsoft, incluindo o Azure. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e Rota Expressa para a mesma rede virtual oferece várias vantagens.

Você pode configurar uma VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio da Rota Expressa. Observe que isso requer dois gateways de rede virtual para a mesma rede virtual, um usando um -GatewayType Vpn e outro usando um -GatewayType ExpressRoute.

![Coexistência de conexão](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelos de implantação e métodos para S2S e ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Próximas etapas
Planeje sua configuração de gateway VPN. Consulte [Design e Planejamento do Gateway de VPN](vpn-gateway-plan-design.md) e [Conectar sua rede local ao Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md).




<!--HONumber=Nov16_HO2-->


