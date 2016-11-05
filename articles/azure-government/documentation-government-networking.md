---
title: Documentação do Azure Governamental | Microsoft Docs
description: Ela fornece uma comparação dos recursos e orientações para conectividade privada governamental
services: Azure-Government
cloud: gov
documentationcenter: ''
author: ryansoc
manager: zakramer
editor: ''

ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 08/25/2016
ms.author: ryansoc

---
# Rede do Azure Governamental
## ExpressRoute (Conectividade privada)
Há dois serviços básicos que fornecem conectividade de rede privada ao Azure Governamental: VPN (site a site para uma organização comum) e ExpressRoute.

O Azure ExpressRoute é usado para criar conexões privadas entre os datacenters do Azure Governamental e sua infraestrutura local ou em um ambiente de colocação. As conexões do ExpressRoute não passam pela Internet pública, e oferecem mais confiabilidade, velocidades maiores, latências menores e mais segurança do que conexões típicas pela Internet. Em alguns casos, o uso de conexões de ExpressRoute para transferir dados entre sistemas locais e o Azure proporciona benefícios de custo significativos.

Com o ExpressRoute, você estabelece conexões com o Azure em um local de ExpressRoute (como na instalação do fornecedor do Exchange) ou conecta-se diretamente ao Azure pela rede WAN existente (como VPN MLPS [comutação de rótulo multiprotocolo], fornecida por um provedor de serviços de rede).

![texto alternativo](./media/azure-government-capability-private-connectivity-options.PNG) ![texto alternativo](./media/government-capability-expressroute.PNG)

Para que os serviços de rede ofereçam suporte a soluções e aplicativos de cliente do Azure Governamental, é altamente recomendável que o ExpressRoute (conectividade privada) seja implementado para se conectar ao Azure Governamental. Se as conexões VPN forem usadas, considere o seguinte:

• Os clientes devem contatar o oficial/agência de autorização para determinar se a conectividade privada, ou outro mecanismo de conexão segura, é necessário e para identificar quaisquer restrições adicionais a serem consideradas.

• Os clientes devem decidir se exigem que a VPN site a site seja roteada por meio de uma zona de conectividade privada.

• Os clientes devem obter um circuito MPLS ou VPN com um provedor de acesso de conectividade privada licenciado.

Todos os clientes que utilizam uma arquitetura de conectividade privada devem validar se uma implementação apropriada foi estabelecida e mantida para a conexão de cliente com o ponto de demarcação de roteador de borda GN/C (Rede/cliente de gateway) para o Azure Governamental. Da mesma forma, sua organização deve estabelecer a conectividade de rede entre seu ambiente local e o ponto de demarcação de roteador de borda GN/C (Rede/cliente de gateway) para o Azure Governamental.

ExpressRoute está disponível no Azure Governamental. Para saber mais (incluindo parceiros e locais de emparelhamento), confira a <a href="https://azure.microsoft.com/documentation/services/expressroute/"> Documentação pública do ExpressRoute </a>.

Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>

<!---HONumber=AcomDC_0831_2016-->