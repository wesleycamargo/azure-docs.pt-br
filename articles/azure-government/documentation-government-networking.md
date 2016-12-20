---
title: Rede do Azure Governmenmt | Microsoft Docs
description: "Ela fornece uma comparação dos recursos e orientações para conectividade privada governamental"
services: Azure-Government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
editor: 
ms.assetid: 3da70579-ecda-421a-8ebf-d52906334e9b
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 09/28/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: 14aa9126d3426c560c1f9497ed0d7a92448137b4
ms.openlocfilehash: 39e67d449c0fa37b76354dc522216f9473df7b97


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

## <a name="next-steps"></a>Próximas etapas
Para obter informações complementares e atualizações, assine o <a href="https://blogs.msdn.microsoft.com/azuregov/">Blog do Microsoft Azure Governamental. </a>




<!--HONumber=Nov16_HO3-->


