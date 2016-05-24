<properties 
   pageTitle="Sobre a conectividade segura entre locais para redes virtuais | Microsoft Azure"
   description="Saiba mais sobre os tipos de conexão segura entre locais para redes virtuais, incluindo site a site, ponto a site e conexões de Rota Expressa."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Sobre a conectividade segura entre locais para redes virtuais

Este artigo discute as diferentes maneiras que você pode conectar seu site local a uma rede virtual do Azure. Este artigo aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico. Se você estiver procurando diagramas de conexão do Gateway de VPN, consulte as [topologias de conexão do Gateway de VPN do Azure](vpn-gateway-topology.md).

Há três opções de conexão disponíveis: site a site, ponto a site e a Rota Expressa. A opção escolhida pode depender de várias considerações, como:


- Que tipo de taxa de transferência sua solução exige?
- Você deseja se comunicar através da Internet pública por meio de VPN segura ou através de uma conexão privada?
- Você tem um endereço IP público disponível para uso?
- Você planeja usar um dispositivo VPN? Nesse caso, é compatível?
- Você está conectando apenas alguns computadores ou deseja ter uma conexão persistente para o seu site?
- Qual tipo de gateway de VPN é necessário para a solução que você deseja criar?

A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## Conexões site a site

Uma VPN site a site permite que você crie uma conexão segura entre o site local e sua rede virtual. Para criar uma conexão site a site, um dispositivo VPN que está localizado em sua rede local é configurado para criar uma conexão segura com o Gateway de VPN do Azure. Depois que a conexão é criada, os recursos na sua rede local e aqueles localizados na sua rede virtual podem se comunicar diretamente e com segurança. Conexões site a site não exigem que você estabeleça uma conexão separada para cada computador cliente na sua rede local a fim de acessar os recursos na rede virtual.

**Usar uma conexão site a site quando:**

- Você desejar criar uma solução híbrida.
- Convém obter uma conexão entre o local e sua rede virtual sem exigir configurações do lado do cliente.
- Você procura uma conexão persistente. 

**Requisitos**

- O dispositivo VPN local deve ter um endereço IP de IPv4 voltado para a Internet. Isso não pode ficar atrás de um NAT.
- Você deve ter um dispositivo VPN compatível. Consulte [Sobre Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- O dispositivo VPN que você usar deve ser compatível com o tipo de gateway exigido pela sua solução. Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).
- O SKU do Gateway também terá impacto sobre a taxa de transferência agregada. Consulte [SKUs de gateway](vpn-gateway-about-vpngateways.md#gwsku) para obter mais informações. 

**Modelos e métodos de implantação disponíveis para S2S**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## Conexões Ponto a Site

Uma VPN ponto a site também permite que você crie uma conexão segura para sua rede virtual. Em uma configuração ponto a site, a conexão é configurada individualmente em cada computador cliente que você deseja conectar à rede virtual. Conexões ponto a site não exigem um dispositivo VPN. Esse tipo de conexão usa um cliente VPN que você instala em cada computador cliente. A VPN é estabelecida manualmente iniciando a conexão do computador cliente local.

Configurações ponto a site e site a site podem existir simultaneamente, mas ao contrário de conexões site a site, as conexões ponto a site não podem ser configuradas simultaneamente com uma conexão de Rota Expressa na mesma rede virtual.

**Usar uma conexão ponto a site quando:**

- Você deseja configurar alguns clientes para se conectar a uma rede virtual.

- Você deseja se conectar à sua rede virtual de um local remoto. Por exemplo, conectar de uma cafeteria ou um local de conferência.

- Você tem uma conexão site a site, mas tem alguns clientes que precisam se conectar de um local remoto.

- Você não tem acesso a um dispositivo VPN que atende aos requisitos mínimos para uma conexão site a site.

- Você não tem um endereço IP IPv4 voltado para Internet para seu dispositivo VPN.

**Modelos e métodos de implantação disponíveis para P2S**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Conexões de Rota Expressa

A Rota Expressa do Azure permite criar conexões privadas entre os datacenters do Azure e a infraestrutura no seu local ou em um ambiente de colocalização. As conexões de Rota Expressa não acessam a Internet pública e oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança que as conexões típicas através da Internet.

Em alguns casos, o uso de conexões da Rota Expressa para transferir dados entre o local e o Azure pode proporcionar um custo/benefício significativo. Com a Rota Expressa, você pode estabelecer conexões com o Azure em um local de Rota Expressa (instalação do provedor do Exchange) ou conectar-se diretamente ao Azure da sua rede WAN existente (como uma VPN MPLS) fornecida por um provedor de serviço de rede.

Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica](../expressroute/expressroute-introduction.md) da Rota Expressa.


## Próximas etapas

- Para obter mais informações sobre o Gateway de VPN, consulte os artigos [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md), Perguntas Frequentes do [Gateway de VPN](vpn-gateway-vpn-faq.md) e [Planejamento e Design](vpn-gateway-plan-design.md).

- Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica](../expressroute/expressroute-introduction.md) da Rota Expressa, [Perguntas Frequentes](../expressroute/expressroute-faqs.md) e [Fluxos de Trabalho](../expressroute/expressroute-workflows.md).

<!---HONumber=AcomDC_0518_2016-->