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
   ms.date="03/08/2016"
   ms.author="cherylmc" />

# Sobre a conectividade segura entre locais para redes virtuais

Este artigo discute as diferentes maneiras que você pode conectar seu site local a uma rede virtual do Azure. Este artigo aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

Há três opções de conexão disponíveis: site a site, ponto a site e a Rota Expressa. A opção escolhida pode depender de várias considerações, como:


- Que tipo de taxa de transferência sua solução exige?
- Você deseja se comunicar através da Internet pública por meio de VPN segura ou através de uma conexão privada?
- Você tem um endereço IP público disponível para uso?
- Você planeja usar um dispositivo VPN? Nesse caso, é compatível?
- Você está conectando apenas alguns computadores ou deseja ter uma conexão persistente para o seu site?
- Qual tipo de gateway de VPN é necessário para a solução que você deseja criar?

A tabela a seguir pode ajudá-lo a decidir a melhor opção de conectividade para sua solução.


| - | **Ponto a Site** | **Site a Site** | **Rota Expressa** |
|------------------------------|----------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------|
| **Serviços com Suporte no Azure** | Serviços de Nuvem e Máquinas Virtuais | Serviços de Nuvem e Máquinas Virtuais | [Lista de serviços](../expressroute/expressroute-faqs.md#supported-services) |
| **Larguras de Banda Típicas** | Normalmente < 100 Mbps agregados | Normalmente < 100 Mbps agregados | 50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps e 10 Gbps |
| **Protocolos com Suporte** | SSTP (Secure Sockets Tunneling Protocol) | IPsec | Conexão direta pelas tecnologias VLANs e VPN do NSP (MPLS, VPLS...) |
| **Roteamento** | Baseado em rota (dinâmico) | Damos suporte a cenários baseados em política (roteamento estático) e em rota (VPN de roteamento dinâmico) | BGP |
| **Resiliência de conexão** | ativo-passivo | ativo-passivo | ativo-ativo |
| **Caso de uso típico** | Criação de protótipos, cenários de desenvolvimento / teste / laboratório para serviços de nuvem e máquinas virtuais | Cenários de desenvolvimento / teste / laboratório e de cargas de trabalho de produção em pequena escala para serviços de nuvem e máquinas virtuais | Acesso a todos os serviços do Azure (lista validada), cargas de trabalho essenciais e em nível Empresarial, Backup, Big Data e Azure como um site de DR |
| **CONTRATO DE NÍVEL DE SERVIÇO** | [CONTRATO DE NÍVEL DE SERVIÇO](https://azure.microsoft.com/support/legal/sla/) | [CONTRATO DE NÍVEL DE SERVIÇO](https://azure.microsoft.com/support/legal/sla/) | [CONTRATO DE NÍVEL DE SERVIÇO](https://azure.microsoft.com/support/legal/sla/) |
| **Preços** | [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preços](https://azure.microsoft.com/pricing/details/vpn-gateway/) | [Preços](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentação Técnica** | [Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentação de Gateway de VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentação da Rota Expressa](https://azure.microsoft.com/documentation/services/expressroute/) |
| **PERGUNTAS FREQUENTES** | [Perguntas frequentes de gateway de VPN](vpn-gateway-vpn-faq.md) | [Perguntas frequentes de gateway de VPN](vpn-gateway-vpn-faq.md) | [Perguntas Frequentes sobre Rota Expressa](../expressroute/expressroute-faqs.md) |


## Conexões site a site

Uma VPN site a site permite que você crie uma conexão segura entre o site local e sua rede virtual. Para criar uma conexão site a site, um dispositivo VPN que está localizado em sua rede local é configurado para criar uma conexão segura com o Gateway de VPN do Azure. Depois que a conexão é criada, os recursos na sua rede local e aqueles localizados na sua rede virtual podem se comunicar diretamente e com segurança. Conexões site a site não exigem que você estabeleça uma conexão separada para cada computador cliente na sua rede local a fim de acessar os recursos na rede virtual.

**Usar uma conexão site a site quando:**

- Você desejar criar uma solução híbrida.
- Convém obter uma conexão entre o local e sua rede virtual sem exigir configurações do lado do cliente.
- Você procura uma conexão persistente. 

**Requisitos**

- O dispositivo VPN local deve ter um endereço IP de IPv4 voltado para a Internet. Isso não pode ficar atrás de um NAT.
- Você deve ter um dispositivo VPN compatível. Consulte [Sobre Dispositivos VPN](vpn-gateway-about-vpn-devices.md). 
- O dispositivo VPN que você usar deve ser compatível com o tipo de gateway exigido pela sua solução. Consulte [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md).
- O SKU do Gateway também terá impacto sobre a taxa de transferência agregada. Consulte [SKUs de gateway](vpn-gateway-about-vpngateways.md#gateway-skus) para obter mais informações. 

Para obter informações sobre como configurar uma conexão de gateway de VPN site a site usando o Portal Clássico do Azure e o modelo de implantação clássica, consulte [Configurar uma rede virtual com uma conexão VPN site a site para o modelo de implantação clássico](vpn-gateway-site-to-site-create.md). Para obter informações sobre como configurar uma VPN site a site usando o modelo de implantação do Gerenciador de Recursos, consulte [Criar uma rede virtual com uma conexão VPN site a site para o modelo de implantação do Gerenciador de Recurso](vpn-gateway-create-site-to-site-rm-powershell.md).


## Conexões Ponto a Site

Uma VPN ponto a site também permite que você crie uma conexão segura para sua rede virtual. Em uma configuração ponto a site, a conexão é configurada individualmente em cada computador cliente que você deseja conectar à rede virtual. Conexões ponto a site não exigem um dispositivo VPN. Esse tipo de conexão usa um cliente VPN que você instala em cada computador cliente. A VPN é estabelecida manualmente iniciando a conexão do computador cliente local.

Configurações ponto a site e site a site podem existir simultaneamente, mas ao contrário de conexões site a site, as conexões ponto a site não podem ser configuradas simultaneamente com uma conexão de Rota Expressa na mesma rede virtual.

**Usar uma conexão ponto a site quando:**

- Você deseja configurar alguns clientes para se conectar a uma rede virtual.

- Você deseja se conectar à sua rede virtual de um local remoto. Por exemplo, conectar de uma cafeteria ou um local de conferência.

- Você tem uma conexão site a site, mas tem alguns clientes que precisam se conectar de um local remoto.

- Você não tem acesso a um dispositivo VPN que atende aos requisitos mínimos para uma conexão site a site.

- Você não tem um endereço IP IPv4 voltado para Internet para seu dispositivo VPN.

Para saber mais sobre como configurar uma conexão ponto a site para o modelo de implantação clássica, confira [Configurar uma conexão VPN ponto a site para uma rede virtual para o modelo de implantação clássico](vpn-gateway-point-to-site-create.md). Para saber mais sobre como configurar uma conexão ponto a site para o modelo de implantação do Gerenciador de Recurso, confira [Configurar uma conexão VPN ponto a site para uma rede virtual para o modelo do Gerenciador de Recurso](vpn-gateway-howto-point-to-site-rm-ps.md).

## Conexões de Rota Expressa

A Rota Expressa do Azure permite criar conexões privadas entre os datacenters do Azure e a infraestrutura no seu local ou em um ambiente de colocalização. As conexões de Rota Expressa não acessam a Internet pública e oferecem mais confiabilidade, velocidades mais rápidas, latências menores e maior segurança que as conexões típicas através da Internet.

Em alguns casos, o uso de conexões da Rota Expressa para transferir dados entre o local e o Azure pode proporcionar um custo/benefício significativo. Com a Rota Expressa, você pode estabelecer conexões com o Azure em um local de Rota Expressa (instalação do provedor do Exchange) ou conectar-se diretamente ao Azure da sua rede WAN existente (como uma VPN MPLS) fornecida por um provedor de serviço de rede.

Para obter mais informações sobre a Rota Expressa, consulte [Visão geral técnica da Rota Expressa](../expressroute/expressroute-introduction.md).


## Próximas etapas

Confira as [Perguntas frequentes sobre a Rota Expressa](vpn-gateway-vpn-faq.md) e as [Perguntas frequentes sobre o Gateway de VPN](../expressroute/expressroute-faqs.md) para saber mais.

<!---HONumber=AcomDC_0316_2016-->