---
title: Rede do Azure Governmenmt | Microsoft Docs
description: "Ela fornece uma comparação dos recursos e orientações para conectividade privada governamental"
services: azure-government
cloud: gov
documentationcenter: 
author: jawalte
manager: zakramer
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 01/24/2017
ms.author: jawalte
translationtype: Human Translation
ms.sourcegitcommit: 8288ca2e85ec0cd1a1b53862b2d60fcb88e7be4b
ms.openlocfilehash: c6b8c27299c1bfceb045ee7d82981654f3a446d8


---
# <a name="azure-government-networking"></a>Rede do Azure Governamental
## <a name="expressroute-private-connectivity"></a>ExpressRoute (Conectividade privada)
ExpressRoute está disponível no Azure Governamental. Para obter mais informações (incluindo parceiros e locais de emparelhamento), consulte a [ documentação pública do ExpressRoute ](../expressroute/index.md).

### <a name="variations"></a>Variações
O ExpressRoute está disponível geralmente (GA) no Azure Government. 

* Os clientes do Governo conectam uma capacidade fisicamente isolada em uma conexão ExpressRoute (ER)  do Azure Government (Gov)
* O Azure Gov fornece a maior disponibilidade e durabilidade utilizando vários pares de região localizados a, no mínimo, 800 km de distância 
* Por padrão, toda a conectividade ER do Azure Gov é configurada com redundância entre os ativos e tem suporte para expansão e oferece a capacidade de circuito até 10 G (a menor é de 50 MB)
* Os locais ER do Azure Gov fornecem caminhos otimizados (saltos mais curtos, baixa latência, alto desempenho etc.) para clientes e regiões com redundância geográfica do Azure Gov
* A conexão privada ER do Azure Gov não utiliza, percorre nem depende da Internet
* A infraestrutura física e lógica do Azure Gov é fisicamente dedicada e separada, e o acesso é restrito aos cidadãos americanos
* A Microsoft possui e opera toda a infraestrutura de fibra entre as Regiões do Azure Gov e os locais de Encontro ER do Azure Gov
* O ER do Azure Gov fornece conectividade com os serviços de nuvem do Microsoft Azure, O365 e CRM

### <a name="considerations"></a>Considerações
Há dois serviços básicos que fornecem conectividade de rede privada ao Azure Governamental: VPN (site a site para uma organização comum) e ExpressRoute.

O Azure ExpressRoute é usado para criar conexões privadas entre os datacenters do Azure Governamental e sua infraestrutura local ou em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública, e oferecem mais confiabilidade, velocidades maiores, latências menores e mais segurança do que conexões típicas pela Internet. Em alguns casos, o uso de conexões de ExpressRoute para transferir dados entre sistemas locais e o Azure proporciona benefícios de custo significativos.   

Com o ExpressRoute, você estabelece conexões com o Azure em um local de ExpressRoute (como na instalação do fornecedor do Exchange) ou conecta-se diretamente ao Azure pela rede WAN existente (como VPN MLPS [comutação de rótulo multiprotocolo], fornecida por um provedor de serviços de rede).

![texto alternativo](./media/azure-government-capability-private-connectivity-options.PNG)  ![texto alternativo](./media/government-capability-expressroute.PNG)  

Para que os serviços de rede ofereçam suporte a soluções e aplicativos de cliente do Azure Governamental, é altamente recomendável que o ExpressRoute (conectividade privada) seja implementado para se conectar ao Azure Governamental. Se as conexões VPN forem usadas, considere o seguinte:

* Os clientes devem contatar o oficial/agência de autorização para determinar se a conectividade privada ou outro mecanismo de conexão segura é necessário e identificar quaisquer restrições adicionais a considerar.
* Os clientes devem decidir se exigem que a VPN site a site seja roteada por meio de uma zona de conectividade privada.
* Os clientes devem obter um circuito MPLS ou VPN com um provedor de acesso de conectividade privada licenciado.

Todos os clientes que utilizam uma arquitetura de conectividade privada devem validar se uma implementação apropriada foi estabelecida e mantida para a conexão de cliente com o ponto de demarcação de roteador de borda GN/C (Rede/cliente de gateway) para o Azure Governamental. Da mesma forma, sua organização deve estabelecer a conectividade de rede entre seu ambiente local e o ponto de demarcação de roteador de borda GN/C (Rede/cliente de gateway) para o Azure Governamental.

## <a name="support-for-bgp-communities"></a>Suporte a comunidades BGP
Esta seção fornece uma visão geral de como as comunidades BGP serão usadas com a ExpressRoute no AzureGov. A Microsoft anunciará rotas nos caminhos de emparelhamento público e da Microsoft com rotas marcadas com valores de comunidade apropriados. A lógica para fazer isso e os detalhes de valores de comunidade são descritos abaixo. No entanto, a Microsoft não adotará valores de comunidade marcados para rotas anunciadas à Microsoft.

Se estiver se conectando à Microsoft por meio do ExpressRoute em qualquer local de emparelhamento dentro de uma região do AzureGov, você terá acesso a todos os serviços de nuvem da Microsoft em todas as regiões dentro dos limites governamentais. 

Por exemplo, se você conectou à Microsoft em Washington D.C. por meio da ExpressRoute, você terá acesso a todos os serviços de nuvem da Microsoft hospedados em AzureGov.

Confira a guia "Visão geral" na [Documentação pública do ExpressRoute](../expressroute/index.md) para saber mais sobre locais e parceiros e obter uma lista detalhada do ExpressRoute para locais de emparelhamento AzureGov.

Você pode adquirir mais de um circuito de ExpressRoute. Ter várias conexões oferece vantagens significativas para a alta disponibilidade devido à redundância geográfica. Em casos em que há vários circuitos da Rota Expressa, você recebe o mesmo conjunto de prefixos anunciados da Microsoft nos caminhos de emparelhamento público e da Microsoft. Isso significa que você terá vários caminhos de sua rede até a Microsoft. Potencialmente, isso pode fazer com que decisões de roteamento não ideais sejam tomadas em sua rede. Como resultado, você pode ter experiências de conectividade não ideal para diferentes serviços. 

A Microsoft marcará prefixos anunciados por meio do emparelhamento público e do emparelhamento da Microsoft com valores de comunidade BGP apropriados indicando a região em que os prefixos estão hospedados. Você pode contar com os valores de comunidade para tomar decisões de roteamento apropriadas e oferecer o roteamento ideal aos clientes.  Para saber mais, confira a guia de "Introdução" na [Documentação pública do ExpressRoute](../expressroute/index.md) e clique em "Otimizar roteamento".

| **Região do Azure de Nuvens Nacionais**| **Valor de comunidade BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| US Gov Iowa | 12076:51109 |
| US Gov Virginia | 12076:51105 |

Todas as rotas anunciadas pela Microsoft serão marcadas com o valor de comunidade apropriado. 

Além disso, a Microsoft também marcará prefixos com base no serviço ao qual eles pertencem. Isso se aplica somente ao emparelhamento da Microsoft. A tabela a seguir fornece um mapeamento de serviço para o valor de comunidade BGP.

| **Serviço nas Nuvens Nacionais** | **Valor de comunidade BGP** |
| --- | --- |
| **Governo dos EUA** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| CRM Online |12076:5140 |
| Outros serviços Online do Office 365 |12076:5200 |

> [!NOTE]
> A Microsoft não atende a valores de comunidade BGP definidos por você nas rotas anunciadas para a Microsoft.

## <a name="support-for-load-balancer"></a>Suporte para Balanceador de Carga
O Balanceador de Carga está totalmente disponível no Azure Governamental. Para saber mais, confira [Documentação pública do Balanceador de Carga](../load-balancer/load-balancer-overview.md). 

## <a name="support-for-traffic-manger"></a>Suporte para o Gerenciador de Tráfego
O Gerenciador de Tráfego está totalmente disponível no Azure Governamental. Para saber mais, confira [Documentação pública do Gerenciador de Tráfego](../traffic-manager/traffic-manager-overview.md). 

## <a name="support-for-vnet-peering"></a>Suporte para o emparelhamento VNet 
O emparelhamento VNet está totalmente disponível no Azure Governamental. Para saber mais, confira [Documentação pública do emparelhamento VNet](../virtual-network/virtual-network-peering-overview.md). 

## <a name="support-for-vpn-gateway"></a>Suporte para Gateway de VPN 
O Gateway de VPN está totalmente disponível no Azure Governamental. Para saber mais, confira [Documentação pública do Gateway de VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). 

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>




<!--HONumber=Jan17_HO5-->


