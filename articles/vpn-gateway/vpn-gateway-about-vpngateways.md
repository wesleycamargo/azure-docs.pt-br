---
title: "Visão geral do Gateway de VPN: criar conexões de VPN entre locais para redes virtuais do Azure | Microsoft Docs"
description: "Esta visão geral do Gateway de VPN explica as maneiras de se conectar a redes virtuais do Azure usando uma conexão VPN na Internet. Diagramas de configurações de conexão básica estão incluídos."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 3be72ac862650d2381a56c4ab682e41a8aaa8a27
ms.lasthandoff: 03/17/2017


---
# <a name="about-vpn-gateway"></a>Sobre o Gateway de VPN
Para enviar o tráfego de rede entre sua rede virtual do Azure e seu site local, deverá criar um gateway de rede virtual para sua rede virtual. Um gateway de VPN é um tipo de gateway de rede virtual que envia o tráfego criptografado em uma conexão pública. Você também pode usar gateways de VPN para enviar o tráfego entre as redes virtuais do Azure pela rede da Microsoft.

Há dois tipos de gateway de rede virtual: 'ExpressRoute' e 'Vpn'. Ao criar um gateway de rede virtual, especifique o tipo de gateway que você deseja criar. Um gateway de VPN é um gateway de rede virtual que usa o tipo de gateway 'Vpn'. 

Cada rede virtual pode ter dois gateways de rede virtual, mas somente um de cada tipo. Dependendo das configurações que você escolher, é possível criar várias conexões a um único gateway de VPN. Um exemplo disso é uma configuração de conexão de vários sites. Quando você cria várias conexões ao mesmo gateway de VPN, todos os túneis VPN, incluindo VPNs Ponto a Site, compartilham a largura de banda disponível para o gateway.

## <a name="configuring-a-vpn-gateway"></a>Configurando um Gateway de VPN
Uma conexão de gateway VPN conta com vários recursos que são configurados com definições específicas. A maioria dos recursos pode ser configurada separadamente, embora eles devam ser configurados em uma determinada ordem em alguns casos.

###<a name="settings"></a>Configurações
As configurações que você escolheu para cada recurso são essenciais para a criação de uma conexão bem-sucedida. Para obter informações sobre os recursos individuais e as configurações do Gateway de VPN, consulte [Sobre as configurações do Gateway de VPN](vpn-gateway-about-vpn-gateway-settings.md). Você encontrará informações para ajudá-lo a entender os tipos de gateway, tipos de VPN, tipos de conexão, as sub-redes de gateway, gateways de rede local e várias outras configurações de recursos que você possa considerar.

###<a name="deployment-tools"></a>Ferramentas de implantação
Você pode começar criando e configurando os recursos usando uma ferramenta de configuração, como o portal do Azure. Você pode, posteriormente, decidir trocar para outra ferramenta, como o PowerShell, para configurar recursos adicionais ou modificar os recursos existentes, quando aplicável. Atualmente, não é possível configurar cada recurso e definição de recursos no portal do Azure. As instruções nos artigos para cada topologia de conexão especificam quando uma ferramenta de configuração específica é necessária. 

###<a name="deployment-model"></a>Modelo de implantação
Quando você configura um gateway de VPN, as etapas efetuadas dependem do modelo de implantação utilizado para criar sua rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Para obter mais informações sobre os modelos de implantação, consulte [Noções básicas sobre o Resource Manager e os modelos de implantação clássicos](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="diagrams"></a>Diagramas de topologia de conexão
É importante saber que há diferentes configurações disponíveis para conexões de gateway de VPN. Você precisa determinar qual configuração melhor atende às suas necessidades. Nas seções a seguir, você pode exibir diagramas de topologia e informações sobre as conexões de gateway de VPN a seguir: as seções a seguir contêm tabelas que listam:

* Modelo de implantação disponível
* Ferramentas de configuração disponíveis
* Links que levam você diretamente a um artigo, se disponível

Use os diagramas e as descrições para ajudar a selecionar a topologia de conexão adequada a seus requisitos. Os diagramas mostram as principais topologias de linha de base, mas é possível criar topologias mais complexas usando os diagramas como uma diretriz.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site a Site e Multissite (túnel VPN IPsec/IKE)
### <a name="S2S"></a>Site a site
Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões S2S podem ser usadas para configurações entre instalações e híbridas.   

![Exemplo de conexão Site a Site do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="Multi"></a>Vários Sites
Esse tipo de conexão é uma variação da conexão Site a Site. Você pode criar mais de uma conexão de VPN em seu gateway de rede virtual, normalmente se conectando a vários sites locais. Ao trabalhar com várias conexões, você deve usar um tipo de VPN baseado em rota (conhecido como gateway dinâmico ao trabalhar com redes virtuais clássicas). Como cada rede virtual pode ter apenas um gateway de VPN, todas as conexões por meio do gateway compartilham a largura de banda disponível. Isso é geralmente chamado de conexão com "vários sites".

![Exemplo de conexão Multissite do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Modelos de implantação e métodos para Site a Site e Vários Sites
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="P2S"></a>Ponto a Site (VPN sobre SSTP)
Uma conexão de gateway VPN Ponto a Site (P2S) permite que você crie uma conexão segura para sua rede virtual a partir de um computador cliente individual. O P2S é uma conexão VPN sobre SSTP (Secure Socket Tunneling Protocol). As conexões P2S não exigem um dispositivo VPN ou um endereço IP voltado para o público para funcionar. Você estabelece a conexão VPN ao iniciá-la do computador cliente. Essa solução é útil quando você deseja conectar sua VNet a partir de um local remoto, como de casa ou em uma conferência, ou quando tem apenas alguns clientes que precisam conectar-se a uma VNet. As conexões P2S podem ser usadas com as conexões S2S pelo mesmo gateway de VPN, desde que todos os requisitos de configuração de ambas as conexões sejam compatíveis.

![Exemplo de conexão Ponto de Site do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-point-to-site-connection-diagram.png)

### <a name="deployment-models-and-methods-for-point-to-site"></a>Modelos de implantação e métodos para Ponto a Site
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="V2V"></a>Conexões de VNet para VNet (túnel VPN IPsec/IKE)
Conectar uma rede virtual a outra rede virtual é semelhante a conectar uma rede virtual (Rede Virtual para Rede Virtual) a um site local. Os dois tipos de conectividade usam um gateway de VPN para fornecer um túnel seguro usando IPsec/IKE. Você pode até combinar a comunicação VNet a VNet com as configurações de conexão de vários sites. Isso permite estabelecer topologias de rede que combinam conectividade entre instalações a conectividade de rede intervirtual.

As redes virtuais às quais você se conecta podem estar:

* na mesma região ou em regiões diferentes
* na mesma assinatura ou em assinaturas diferentes 
* no mesmo modelo de implantação ou em modelos de implantação diferentes

![Exemplo de conexão VNet a VNet do Gateway de VPN do Azure](./media/vpn-gateway-about-vpngateways/vpngateway-vnet-to-vnet-connection-diagram.png)

###<a name="connections-between-deployment-models"></a>Conexões entre os modelos de implantação
Atualmente, o Azure tem dois modelos de implantação: o clássico e o Resource Manager. Se você já usa o Azure há algum tempo, provavelmente terá as VMs do Azure e as funções de instância em execução em uma Rede Virtual clássica. Suas VMs e instâncias de função mais recentes podem estar em execução em uma Rede Virtual criada no Resource Manager. Você pode criar uma conexão entre as Redes Virtuais para permitir que os recursos em uma rede virtual se comuniquem diretamente com os recursos em outra.

###<a name="vnet-peering"></a>Emparelhamento VNet
Talvez você possa usar o emparelhamento VNet para criar sua conexão, desde que a rede virtual atenda a certos requisitos. O emparelhamento de Rede Virtual não usa um gateway de rede virtual. Para obter mais informações, consulte [Emparelhamento da VNet](../virtual-network/virtual-network-peering-overview.md).

###<a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Modelos de implantação e métodos para VNet a VNet
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="ExpressRoute"></a>ExpressRoute (conexão privada dedicada)
A Rota Expressa do Microsoft Azure permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade. Com a Rota Expressa, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização.

As conexões da Rota Expressa não passam pela Internet pública. Isso permite que as conexões da Rota Expressa ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

Uma conexão ExpressRoute não usa um gateway de VPN, embora ela use um gateway de rede virtual como parte de sua configuração necessária. Em uma conexão ExpressRoute, um gateway de rede virtual é configurado com o tipo de gateway 'ExpressRoute', em vez de 'Vpn'. Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

## <a name="coexisting"></a>Conexões coexistentes Site a Site e de ExpressRoute
A Rota Expressa é uma conexão direta e dedicada da sua WAN (não pela Internet pública) para Serviços Microsoft, incluindo o Azure. O tráfego da VPN Site a Site é criptografado ao percorer a Internet pública. Poder configurar conexões VPN Site a Site e Rota Expressa para a mesma rede virtual oferece várias vantagens.

Você pode configurar uma VPN Site a Site como um caminho de failover seguro para a Rota Expressa ou usar VPNs Site a Site para se conectar a sites que não fazem parte de sua rede, mas estão conectados por meio da Rota Expressa. Observe que essa configuração exige dois gateways de rede virtual para a mesma rede virtual, um usando o tipo de gateway 'Vpn' e o outro usando o tipo de gateway 'ExpressRoute'.

![Exemplos de conexões coexistentes do ExpressRoute e do Gateway de VPN](./media/vpn-gateway-about-vpngateways/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Modelos de implantação e métodos para S2S e ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="pricing"></a>Preços
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKUs de gateway
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Para saber mais sobre as SKUs de gateway para Gateway de VPN, veja [SKUs de Gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Taxa de transferência agregada estimada por SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="next-steps"></a>Próximas etapas
- Planeje sua configuração de gateway VPN. Confira [Planejamento e design do Gateway de VPN](vpn-gateway-plan-design.md).
- Exiba as [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para saber mais.
- Exiba os [Limites de serviço e assinatura](../azure-subscription-service-limits.md#networking-limits).


