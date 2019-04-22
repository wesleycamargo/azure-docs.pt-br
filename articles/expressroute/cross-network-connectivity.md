---
title: Conectividade de rede cruzada do Azure | Microsoft Docs
description: Esta página descreve um cenário de aplicativo para troca de conectividade de rede e a solução com base em recursos de rede do Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/03/2019
ms.author: rambala
ms.openlocfilehash: 4923c7f2048b7368af6314d5e2288216115bc3bc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784732"
---
# <a name="cross-network-connectivity"></a>Conectividade entre redes

A Fabrikam Inc. tem uma grande presença física e implantação do Azure no leste dos EUA. A Fabrikam tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute. Da mesma forma, a Contoso Ltd. tem uma presença e a implantação do Azure no Oeste dos EUA. A Contoso tem conectividade de back-end entre seus locais e implantações do Azure por meio do ExpressRoute.  

A Fabrikam Inc. adquire a Contoso Ltd. Seguindo a fusão, a Fabrikam deseja interconectar as redes. A figura a seguir ilustra o cenário:

 [![1]][1]

As setas tracejadas no meio da figura acima indicam as interconexões de rede desejada. Especificamente, há três tipos de conexões cruzadas desejadas: A Fabrikam e Contoso VNets entre conectar-se 1), 2) cruzado regionais locais e redes virtuais cruzada se conecta (isto é, a conexão de rede de local da Fabrikam a VNet da Contoso e conectar-se Contoso na rede local à Fabrikam VNet) e 3) Fabrikam e Contoso Conecte-se à rede local cruzado. 

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Contoso Ltd., antes da fusão.

[![2]][2]

A tabela a seguir mostra as rotas em vigor de uma VM na assinatura do Contoso, antes da fusão. Por tabela, a VM na rede virtual está ciente do espaço de endereço de rede virtual e rede local da Contoso, além dos valores padrão. 

[![4]][4]

A tabela a seguir mostra a tabela de rotas de emparelhamento privado da ExpressRoute da Fabrikam Inc., antes da fusão.

[![3]][3]

A tabela a seguir mostra as rotas em vigor de uma VM na assinatura do Fabrikam, antes da fusão. Por tabela, a VM na rede virtual está ciente do espaço de endereço de rede virtual e a Fabrikam na rede local, além dos valores padrão.

[![5]][5]

Neste artigo, vamos percorrer passo a passo e abordam como alcançar as conexões cruzadas desejadas, usando os seguintes recursos de rede do Azure:

* [Emparelhamento de rede virtual][Virtual network peering] 
* [Conexão de ExpressRoute de rede virtual][connection]
* [Alcance global][Global Reach] 

## <a name="cross-connecting-vnets"></a>Cross conectando redes virtuais

Emparelhamento de rede virtual (rede virtual emparelhamento) fornece mais ideal e o melhor desempenho de rede ao se conectar duas redes virtuais. Emparelhamento de rede virtual dá suporte a emparelhamento duas redes virtuais na mesma região do Azure (normalmente chamado de emparelhamento de rede virtual) e em duas regiões diferentes do Azure (normalmente chamado de emparelhamento VNet Global). 

Vamos configurar Global emparelhamento VNet entre as redes virtuais em assinaturas do Fabrikam Azure e Contoso. Para saber como criar a rede virtual emparelhamento entre duas redes virtuais, consulte [criar um emparelhamento de rede virtual] [ Configure VNet peering] artigo.

A imagem a seguir mostra a arquitetura de rede depois de configurar o emparelhamento VNet Global.

[![6]][6]

A tabela a seguir mostra as rotas conhecidos para a VM de assinatura do Contoso. Preste atenção para a última entrada da tabela. Essa entrada é o resultado de cruzada conectar as redes virtuais.

[![7]][7]

A tabela a seguir mostra as rotas conhecidos para a assinatura da Fabrikam VM. Preste atenção para a última entrada da tabela. Essa entrada é o resultado de cruzada conectar as redes virtuais.

[![8]][8]

Diretamente o emparelhamento VNet vincula duas redes virtuais (consulte não há nenhum próximo salto para *VNetGlobalPeering* entrada nas duas tabelas acima)

## <a name="cross-connecting-vnets-to-the-on-premises-networks"></a>Cross conectando redes virtuais a redes locais

Podemos nos conectar a um circuito de ExpressRoute para várias redes virtuais. Ver [limites de serviço e assinatura] [ Subscription limits] para o número máximo de redes virtuais que podem ser conectados a um circuito de ExpressRoute. 

Vamos nos conectar Fabrikam circuito do ExpressRoute à rede virtual de assinatura de Contoso e da mesma forma circuito do ExpressRoute da Contoso a assinatura da Fabrikam VNet para habilitar a conectividade entre redes virtuais e redes locais. Para se conectar a uma rede virtual a um circuito de ExpressRoute em uma assinatura diferente, é necessário criar e usar uma autorização.  Consulte o artigo: [Conectar uma rede virtual a um circuito de ExpressRoute][Connect-ER-VNet].

A imagem a seguir mostra a arquitetura de rede depois de configurar o ExpressRoute cruzar a conectividade para as redes virtuais.

[![9]][9]

A tabela a seguir mostra a tabela de rotas pelo emparelhamento privado do ExpressRoute da Contoso Ltd., depois entre conectar redes virtuais a redes locais por meio do ExpressRoute. Consulte a tabela de rota tem as rotas que pertencem a ambas as redes virtuais.

[![10]][10]

A tabela a seguir mostra a tabela de rotas pelo emparelhamento privado do ExpressRoute da Fabrikam Inc., depois entre conectar redes virtuais a redes locais por meio do ExpressRoute. Consulte a tabela de rota tem as rotas que pertencem a ambas as redes virtuais.

[![11]][11]

A tabela a seguir mostra as rotas conhecidos para a VM de assinatura do Contoso. Preste atenção às *gateway de rede Virtual* entradas da tabela. A VM vê as rotas para ambas as redes locais.

[![12]][12]

A tabela a seguir mostra as rotas conhecidos para a assinatura da Fabrikam VM. Preste atenção às *gateway de rede Virtual* entradas da tabela. A VM vê as rotas para ambas as redes locais.

[![13]][13]

>[!NOTE]
>Em ambos as assinaturas Fabrikam e/ou Contoso, que você também pode ter redes virtuais de spoke para o respectivo hub VNet (não um design de hub e spoke é mostrado nos diagramas de arquitetura neste artigo). As conexões cruzadas entre os gateways de rede virtual do hub para ExpressRoute também permitirá que a comunicação entre o Leste e Oeste hubs e spokes.
>

## <a name="cross-connecting-on-premises-networks"></a>Entre a conexão de redes locais

Alcance Global de ExpressRoute fornece conectividade entre redes locais que estão conectados a diferentes circuitos do ExpressRoute. Vamos configurar o alcance Global entre Contoso e Fabrikam ExpressRoute circuitos. Como os circuitos de ExpressRoute estão em assinaturas diferentes, é necessário criar e usar uma autorização. Ver [configurar o ExpressRoute alcance Global] [ Configure Global Reach] artigo para obter orientação passo a passo.

A imagem a seguir mostra a arquitetura de rede depois de configurar o alcance Global.

[![14]][14]

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Contoso Ltd., depois de configurar o alcance Global. Consulte a tabela de rota tem as rotas que pertencem a ambas as redes locais. 

[![15]][15]

A tabela a seguir mostra a tabela de rotas de emparelhamento privado do ExpressRoute da Fabrikam Inc., depois de configurar o alcance Global. Consulte a tabela de rota tem as rotas que pertencem a ambas as redes locais.

[![16]][16]

## <a name="next-steps"></a>Próximos passos

Ver [perguntas Frequentes de rede virtual][VNet-FAQ], para ainda mais dúvidas sobre rede virtual e o emparelhamento de rede virtual. Ver [perguntas frequentes sobre ExpressRoute] [ ER-FAQ] para conectividade de rede mais dúvidas sobre o ExpressRoute e virtuais.

O Alcance Global é implementado por país. Para ver se o Alcance Global está disponível nos países desejados, confira [Alcance Global do ExpressRoute][Global Reach].

<!--Image References-->
[1]: ./media/cross-network-connectivity/premergerscenario.png "O cenário de aplicação"
[2]: ./media/cross-network-connectivity/contosoexr-rt-premerger.png "tabela de rotas de Contoso ExpressRoute antes de fusão"
[3]: ./media/cross-network-connectivity/fabrikamexr-rt-premerger.png "tabela de rotas do Fabrikam ExpressRoute antes de fusão"
[4]: ./media/cross-network-connectivity/contosovm-routes-premerger.png "VM Contoso roteia antes de fusão"
[5]: ./media/cross-network-connectivity/fabrikamvm-routes-premerger.png "VM Fabrikam roteia antes de fusão"
[6]: ./media/cross-network-connectivity/vnet-peering.png "a arquitetura após o emparelhamento de rede virtual"
[7]: ./media/cross-network-connectivity/contosovm-routes-peering.png "VM Contoso roteia após o emparelhamento de rede virtual"
[8]: ./media/cross-network-connectivity/fabrikamvm-routes-peering.png "VM Fabrikam roteia após o emparelhamento de rede virtual"
[9]: ./media/cross-network-connectivity/exr-x-connect.png "a arquitetura depois ExpressRoutes cross conexão"
[10]: ./media/cross-network-connectivity/contosoexr-rt-xconnect.png "tabela de rotas do Contoso ExpressRoute após cruzada está se conectando ExR e redes virtuais"
[11]: ./media/cross-network-connectivity/fabrikamexr-rt-xconnect.png "Fabrikam ExpressRoute a tabela de rotas após cruzada está se conectando ExR e redes virtuais"
[12]: ./media/cross-network-connectivity/contosovm-routes-xconnect.png "rotas VM Contoso após cruzam está se conectando ExR e redes virtuais"
[13]: ./media/cross-network-connectivity/fabrikamvm-routes-xconnect.png "rotas Fabrikam VM depois de cruzam está se conectando ExR e redes virtuais"
[14]: ./media/cross-network-connectivity/globalreach.png "a arquitetura depois de configurar o alcance Global"
[15]: ./media/cross-network-connectivity/contosoexr-rt-gr.png "tabela de rotas do Contoso ExpressRoute após o alcance Global"
[16]: ./media/cross-network-connectivity/fabrikamexr-rt-gr.png "tabela de rotas da Fabrikam ExpressRoute após o alcance Global"

<!--Link References-->
[Virtual network peering]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[connection]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-global-reach
[Configure VNet peering]: https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions
[Configure Global Reach]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-set-global-reach
[Subscription limits]: https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits
[Connect-ER-VNet]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager
[ER-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[VNet-FAQ]: https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq