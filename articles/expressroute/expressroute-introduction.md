---
title: Introdução à Rota Expressa | Microsoft Docs
description: Esta página fornece uma visão geral do serviço Rota Expressa, incluindo o modo de funcionamento de uma conexão da Rota Expressa.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: ''

ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc

---
# <a name="expressroute-technical-overview"></a>Visão Geral Técnica da Rota Expressa
A Rota Expressa do Microsoft Azure permite que você estenda suas redes locais até a nuvem da Microsoft por meio de uma conexão privada dedicada, facilitada por um provedor de conectividade. Com a Rota Expressa, você pode estabelecer conexões com os serviços de nuvem da Microsoft, como o Microsoft Azure, o Office 365 e o CRM Online. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (VPN IP), uma rede Ethernet ponto a ponto ou uma conexão cruzada virtual por meio de um provedor de conectividade em uma colocalização. As conexões da Rota Expressa não passam pela Internet pública. Isso permite que as conexões da Rota Expressa ofereçam mais confiabilidade, mais velocidade, latências menores e muito mais segurança do que as conexões típicas pela Internet.

![](./media/expressroute-introduction/expressroute-basic.png)

**Entre os principais benefícios estão:**

* Conectividade de Camada 3 entre sua rede local e a Microsoft Cloud por meio de um provedor de conectividade. A conectividade pode ocorrer de uma rede “qualquer para qualquer” (IPVPN), de uma conexão Ethernet ponto a ponto ou por meio de uma conexão cruzada virtual via troca Ethernet.
* Conectividade com os serviços de nuvem da Microsoft em todas as regiões da região geopolítica.
* Conectividade global com os serviços da Microsoft em todas as regiões com o complemento premium da Rota Expressa.
* Roteamento dinâmico entre sua rede e a Microsoft por meio de protocolos padrão da indústria (BGP).
* Redundância interna em cada local de emparelhamento para proporcionar maior confiabilidade.
* [SLA](https://azure.microsoft.com/support/legal/sla/)do tempo de atividade da conexão.
* QoS e suporte para várias classes de serviço para aplicativos especiais, como o Skype for Business.

Consulte as [Perguntas Frequentes sobre Rota Expressa](expressroute-faqs.md) para obter mais detalhes.

## <a name="<a-name="howtoconnect"></a>how-can-i-connect-my-network-to-microsoft-using-expressroute?"></a><a name="howtoconnect"></a>Como posso conectar minha rede à Microsoft usando a Rota Expressa?
Há três maneiras diferentes para você criar uma conexão entre sua rede local e a nuvem da Microsoft:

### <a name="co-located-at-a-cloud-exchange"></a>Colocalizada em uma troca de nuvem
Se você estiver colocalizado em uma instalação que possua uma troca de nuvem, poderá solicitar conexões cruzadas virtuais para a nuvem da Microsoft por meio da troca Ethernet do provedor da colocalização. Os provedores da colocalização podem oferecer conexões cruzadas de Camada 2 ou conexões cruzadas gerenciadas de Camada 3 entre sua infraestrutura na instalação de colocalização e a nuvem da Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Conexões Ethernet ponto a ponto
Você pode conectar seus data centers/escritórios locais à nuvem da Microsoft por meio de links de Ethernet ponto a ponto. Os provedores de Ethernet ponto a ponto podem oferecer conexões de Camada 2 ou conexões gerenciadas de Camada 3 entre seu site e a nuvem da Microsoft.

### <a name="any-to-any-(ipvpn)-networks"></a>Redes qualquer para qualquer (IPVPN)
É possível integrar sua WAN à nuvem da Microsoft. Os Provedores de IPVPN (normalmente, VPN MPLS) oferecem conectividade “qualquer para qualquer” entre suas filiais e datacenters. A nuvem da Microsoft pode ser interconectada à sua WAN para que ela fique exatamente igual a qualquer outra filial. Normalmente, os provedores de rede WAN oferecem conectividade gerenciada de Camada 3. Os recursos da Rota Expressa são idênticos em todos os modelos de conectividade mencionados acima. 

Os provedores de conectividade podem oferecer um ou mais modelos de conectividade. Converse com seu provedor de conectividade a fim de escolher o modelo mais adequado a você.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

## <a name="expressroute-features"></a>Recursos da Rota Expressa
A Rota Expressa dá suporte aos recursos a seguir: 

### <a name="layer-3-connectivity"></a>Conectividade de Camada 3
A Microsoft usa o protocolo padrão do setor para roteamento dinâmico (BGP) a fim de trocar rotas entre sua rede local, suas instâncias no Azure e os endereços públicos da Microsoft.  Estabelecemos várias sessões BGP com sua rede para perfis de tráfego diferentes. Encontre mais detalhes no artigo [Circuito e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Redundância
Cada circuito da Rota Expressa é formado por duas conexões com dois roteadores de borda do Microsoft Enterprise (MSEEs) do provedor de conectividade/borda de sua rede. A Microsoft exigirá uma conexão BGP dupla do provedor de conectividade/seu lado – uma para cada MSEE. Você pode optar por não implantar dispositivos redundantes/circuitos de Ethernet em seu lado. No entanto, os provedores de conectividade usam dispositivos redundantes para garantir que as conexões sejam entregues à Microsoft de forma redundante. É obrigatório ter uma configuração de conectividade de Camada 3 redundante para que nosso [SLA](https://azure.microsoft.com/support/legal/sla/) seja válido. 

### <a name="connectivity-to-microsoft-cloud-services"></a>Conectividade com serviços de nuvem da Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

As conexões da Rota Expressa permitem o acesso aos seguintes serviços:

* Serviços do Microsoft Azure
* Serviços do Microsoft Office 365
* Serviços do Microsoft CRM Online 

Visite a página de [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter uma lista detalhada dos serviços com suporte por meio da Rota Expressa.

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Conectividade com todas as regiões em uma região geopolítica
Você pode se conectar à Microsoft em um dos nossos [locais de emparelhamento](expressroute-locations.md) e ter acesso a todas as regiões dentro da região geopolítica. 

Por exemplo, se você estiver conectado à Microsoft em Amsterdã por meio da Rota Expressa, terá acesso a todos os serviços de nuvem da Microsoft hospedados no Norte da Europa e na Europa Ocidental. Consulte o artigo [Parceiros e locais de emparelhamento da Rota Expressa](expressroute-locations.md) para obter uma visão geral das regiões geopolíticas, regiões associadas da nuvem da Microsoft e locais de emparelhamento correspondentes da Rota Expressa.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Conectividade global com o complemento premium da Rota Expressa
Você pode habilitar o complemento da Rota Expressa premium a fim de estender a conectividade pelas fronteiras geopolíticas. Por exemplo, se você estiver conectado à Microsoft em Amsterdã por meio da Rota Expressa, terá acesso a todos os serviços de nuvem da Microsoft hospedados em todas as regiões do mundo (nuvens nacionais são excluídas). Você pode acessar serviços implantados na América do Sul ou na Austrália da mesma maneira que acessa as regiões do Norte da Europa e da Europa Ocidental.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecossistema abundante de parceiros de conectividade
A Rota Expressa tem um ecossistema de provedores de conectividade e parceiros de SI em constante crescimento. Consulte o artigo [Provedores e locais da Rota Expressa](expressroute-locations.md) para obter as informações mais recentes.

### <a name="connectivity-to-national-clouds"></a>Conectividade com nuvens nacionais
A Microsoft opera ambientes de nuvem isolados para regiões geopolíticas e segmentos de clientes especiais. Consulte a página [Provedores e locais de Rota Expressa](expressroute-locations.md) para obter uma lista de nuvens e provedores nacionais.

### <a name="supported-bandwidth-options"></a>Opções de largura de banda com suporte
É possível comprar circuitos da Rota Expressa para várias larguras de banda. Veja abaixo a lista de larguras de banda com suporte. Não deixe de entrar em contato com seu provedor de conectividade para verificar a lista de larguras de banda com suporte oferecidas.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Dimensionamento dinâmico de largura de banda
Você pode aumentar a largura de banda do circuito da Rota Expressa (em uma base de melhor esforço) sem precisar subdividir suas conexões. 

### <a name="flexible-billing-models"></a>Modelos flexíveis de cobrança
Escolha o modelo de cobrança que funcione melhor para você. Escolha um dos modelos de cobrança listados abaixo. Consulte as [Perguntas frequentes sobre a Rota Expressa](expressroute-faqs.md) para obter mais detalhes. 

* **Dados ilimitados**. O circuito da Rota Expressa é cobrado com base em uma taxa mensal, e todas as transferências de dados de entrada e de saída são incluídas gratuitamente. 
* **Dados limitados**. O circuito da Rota Expressa será cobrado com base em uma taxa mensal. Todas as transferências de dados de entrada são gratuitas. A cobrança pelas transferências de dados de saída ocorrem de acordo com a quantidade de GB da transferência de dados. As taxas de transferência de dados variam de acordo com a região.
* **Complemento Rota Expressa premium**. O Rota Expressa premium é um complemento para o circuito da Rota Expressa. O complemento premium da Rota Expressa fornece os seguintes recursos: 
  * Aumento dos limites de rota para emparelhamento público e privado do Azure de 4.000 para 10.000 rotas.
  * Conectividade global para serviços. Um circuito da Rota Expressa criado em qualquer região (excluindo nuvens nacionais) terá acesso a recursos em qualquer região do mundo. Por exemplo, uma rede virtual criada na Europa Ocidental pode ser acessada por meio de um circuito da Rota Expressa provisionado no Vale do Silício.
  * Aumento do número de links de Rede Virtual por circuito da Rota Expressa de 10 para um limite maior, dependendo da largura de banda do circuito.

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre conexões e domínios de roteamento da Rota Expressa. Consulte [Circuitos e domínios de roteamento da Rota Expressa](expressroute-circuit-peerings.md).
* Encontrar um provedor de serviços. Consulte [Parceiros e locais de emparelhamento da Rota Expressa](expressroute-locations.md).
* Verifique se todos os pré-requisitos foram atendidos. Confira [Pré-requisitos da Rota Expressa](expressroute-prerequisites.md).
* Consulte os requisitos para o [Roteamento](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurar sua conexão da Rota Expressa.
  * [Criar um circuito da Rota Expressa](expressroute-howto-circuit-classic.md)
  * [Configurar o roteamento](expressroute-howto-routing-classic.md)
  * [Vincular uma Rede Virtual a um circuito de Rota Expressa](expressroute-howto-linkvnet-classic.md)

<!--HONumber=Oct16_HO2-->


