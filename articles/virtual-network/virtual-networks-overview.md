---
title: Rede Virtual do Azure | Microsoft Docs
description: Saiba mais sobre os conceitos e recursos da Rede Virtual do Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/03/2017
ms.author: jdial
ms.openlocfilehash: dc6916bd25c5a020fdcef0707fe28a1e34fb0f88
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/04/2017
---
# <a name="azure-virtual-network"></a>Rede Virtual do Azure

O serviço da Rede Virtual do Microsoft Azure permite que os recursos do Azure se comuniquem com segurança com outros em uma rede virtual. Uma rede virtual é uma representação da sua própria rede na nuvem. Uma rede virtual é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. É possível conectar redes virtuais umas às outras ou à sua rede local. A figura a seguir mostra algumas funcionalidades do serviço de Rede Virtual do Azure:

![Diagrama de rede](./media/virtual-networks-overview/virtual-network-overview.png)

Para saber mais sobre as seguintes funcionalidades da Rede Virtual do Azure, clique na funcionalidade:
- **[Isolamento:](#isolation)** as redes virtuais são isoladas umas das outras. Crie redes virtuais separadas para desenvolvimento, teste e produção que usem os mesmos blocos de endereços CIDR (10.0.0.0/0, por exemplo). Como alternativa, crie várias redes virtuais que usam blocos de endereço CIDR diferentes e conectam as redes. É possível segmentar uma rede virtual em várias sub-redes. O Azure fornece resolução de nomes interna para máquinas virtuais e instâncias de função dos Serviços de Nuvem do Azure implantadas em uma rede virtual. Outra opção é configurar uma rede virtual para usar seus próprios servidores DNS, em vez de usar a resolução de nome interna do Azure.
- **[Comunicação com a Internet:](#internet)** por padrão, todas as instâncias de função de Máquinas Virtuais e Serviços de Nuvem do Azure em uma rede virtual têm acesso à Internet. Você também pode habilitar o acesso de entrada a recursos específicos, conforme o necessário.
- **[Comunicação de recursos do Azure:](#within-vnet)** recursos do Azure como Serviços de Nuvem e máquinas virtuais podem ser implantados na mesma rede virtual. Os recursos podem se comunicar usando endereços IP privados, mesmo se estiverem em sub-redes diferentes. O Azure fornece roteamento padrão entre sub-redes, VNets e redes locais, portanto você não precisa configurar e gerenciar rotas. É possível personalizar o roteamento do Azure, se desejar.
- **[Conectividade de rede virtual:](#connect-vnets)** redes virtuais podem ser conectadas umas às outras, permitindo que recursos em uma rede virtual se comunique com os recursos de qualquer outra rede virtual.
- **[Conectividade local:](#connect-on-premises)** uma rede virtual pode ser conectada de forma particular a uma rede local ou usando uma conexão de VPN site a site pela Internet.
- **[Filtragem de tráfego:](#filtering)** o tráfego de rede de instâncias de função de máquinas virtuais e Serviços de Nuvem pode ser filtrado na entrada e na saída pelo endereço IP e porta de origem, endereço IP e porta de destino e protocolo.
- **[Roteamento:](#routing)** opcionalmente, é possível substituir o roteamento padrão do Azure configurando suas próprias rotas ou propagando rotas BGP por meio de um gateway de rede.

## <a name = "isolation"></a>Isolamento e segmentação de rede

É possível implementar várias redes virtuais em cada [assinatura](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [região](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) do Azure. Cada rede virtual é isolada de outras redes virtuais. Para cada rede virtual, é possível:
- Especificar um espaço de endereço IP privado personalizado usando endereços públicos e privados (RFC 1918). O Azure atribui um endereço IP particular aos recursos em uma rede virtual do espaço de endereço que você atribuiu.
- Segmentar a rede virtual em uma ou mais sub-redes e alocar uma parte do espaço de endereço da rede virtual a cada sub-rede.
- Usar a resolução de nomes fornecida pelo Azure ou especificar seu próprio servidor DNS que será usado pelos recursos de uma rede virtual. Para saber mais sobre a resolução de nome em redes virtuais, consulte o artigo [Resolução de nomes para VMs e Serviços de Nuvem](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Comunicação pela Internet
Por padrão, todos os recursos em uma rede virtual podem se comunicar por saída pela Internet. O endereço IP privado do recurso passa por SNAT (conversão do endereço de rede de origem) para um endereço IP público selecionado pela infraestrutura do Azure. Para saber mais sobre conectividade de saída na Internet, leia o artigo [Noções básicas das conexões de saída no Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Para evitar a conectividade de Internet de saída, implemente rotas personalizadas ou filtragem de tráfego.

Para comunicar-se internamente com os recursos do Azure da Internet, ou para comunicar-se externamente com a Internet sem SNAT, é necessário atribuir um endereço IP público ao recurso. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Comunicação segura entre os recursos do Azure

É possível implantar máquinas virtuais em uma rede virtual. Máquinas virtuais se comunicam com outros recursos em uma rede virtual por meio de um adaptador de rede. Para saber mais sobre adaptadores de rede, leia o artigo [Adaptadores de rede](virtual-network-network-interface.md).

Você também pode implantar vários outros tipos de recursos do Azure para uma rede virtual, tais como Máquinas Virtuais do Azure, Serviços de Nuvem do Azure, Ambientes de Serviço de Aplicativo do Azure e Conjuntos de Dimensionamento de Máquinas Virtuais do Azure. Para obter uma lista completa de recursos do Azure que podem ser implantados em uma rede virtual, [Integração de serviços de rede virtual aos serviços do Azure](virtual-network-for-azure-services.md).

Alguns recursos não podem ser implantados em uma rede virtual, mas permitem restringir a comunicação dos recursos apenas a uma rede virtual. Para saber mais sobre como restringir o acesso aos recursos, consulte [Pontos de extremidade de serviço de rede virtual](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Conectar redes virtuais

É possível conectar redes virtuais umas às outras, permitindo que recursos em uma rede virtual se comunique com os de qualquer outra por meio de emparelhamento de rede virtual. A largura de banda e a latência de comunicação entre os recursos em redes virtuais diferentes é como se eles estivessem conectados à mesma rede virtual. Para saber mais sobre emparelhamento, leia o artigo [Emparelhamento de rede virtual](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Conectar-se a uma rede local

Você pode conectar sua rede local a uma rede virtual usando qualquer combinação das seguintes opções:
- **VPN (rede virtual privada) de ponto a site:** estabelecida entre uma rede virtual e um único computador na sua rede. Cada computador que deseja estabelecer conectividade com uma rede virtual precisa configurar suas conexões de forma independente. Esse tipo de conexão é ótimo se você estiver começando a usar o Azure, ou para os desenvolvedores, pois exige pouca ou nenhuma alteração em sua rede existente. A conexão usa o protocolo SSTP para fornecer comunicação criptografada pela Internet entre o computador e a rede virtual. A latência de uma VPN ponto a site é imprevisível, pois o tráfego atravessa a Internet.
- **VPN site a site:** estabelecida entre o dispositivo VPN e um Gateway de VPN do Azure implantado em uma rede virtual. Esse tipo de conexão permite que qualquer recurso local autorizado acesse uma rede virtual. A conexão é uma VPN IPSec/IKE que fornece comunicação criptografada pela Internet entre o dispositivo local e o Gateway de VPN do Azure. A latência de uma conexão site a site é imprevisível, pois o tráfego atravessa a Internet.
- **Azure ExpressRoute:** estabelecida entre sua rede e o Azure, por meio de um parceiro de ExpressRoute. Essa conexão é privada. O tráfego não atravessa a Internet. A latência de uma conexão de ExpressRoute é previsível, pois o tráfego não atravessa a Internet.

Para saber mais sobre todas as opções de conexão anteriores, leia o artigo [Diagramas de topologia de conexão](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrar o tráfego de rede
Filtre o tráfego de rede entre sub-redes usando uma ou as duas opções a seguir:
- **Grupos de segurança de rede:** cada grupo de segurança de rede pode conter várias regras de segurança de entrada e saída que permitem filtrar o tráfego por endereço IP de origem e de destino, porta e protocolo. Você pode aplicar um grupo de segurança de rede a um adaptador de rede em uma máquina virtual. Também é possível aplicar um grupo de segurança de rede à sub-rede na qual um adaptador de rede ou outro recurso do Azure, se encontra. Para saber mais sobre grupos de segurança de rede, confira [Grupos de segurança de rede](security-overview.md#network-security-groups).
- **Solução de virtualização de rede:** uma solução de virtualização de rede é uma máquina virtual que executa um software responsável por uma função de rede, como um firewall. Você pode exibir uma lista das soluções de virtualização de rede disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Também há soluções de virtualização de rede disponíveis para fornecer otimização WAN e demais funções de tráfego de rede. As soluções de virtualização de rede são usadas normalmente com rotas BGP ou definidas pelo usuário. Você também pode usar uma solução de virtualização de rede para filtrar o tráfego entre redes virtuais.

## <a name="routing"></a>Rotear o tráfego de rede

Por padrão, o Azure cria tabelas de rotas que permitem aos recursos conectados a qualquer sub-rede e em qualquer rede virtual comunicar-se mutuamente. Você pode implementar uma ou as duas opções a seguir para substituir as rotas padrão criadas pelo Azure:
- **Rotas definidas pelo usuário:** você pode criar tabelas de rotas personalizadas com rotas que controlam para onde o tráfego será roteado em cada sub-rede. Para saber mais sobre as rotas definidas pelo usuário, consulte [Rotas definidas pelo usuário](virtual-networks-udr-overview.md#user-defined).
- **Rotas BGP:** se você conectar sua rede virtual à rede local usando um Gateway de VPN do Azure ou conexão do ExpressRoute, será possível propagar as rotas BGP para suas redes virtuais.

## <a name="pricing"></a>Preços

Não há cobrança pelas redes virtuais, sub-redes, tabelas de rotas ou grupos de segurança de rede. Uso de largura de banda de Internet de saída, endereços IP públicos, emparelhamento de rede virtual, Gateways de VPN e ExpressRoute têm suas próprias estruturas de preços. Veja as páginas de preço de [Rede virtual](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) e [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute) para saber mais.

## <a name="faq"></a>Perguntas frequentes

Para examinar as perguntas frequentes sobre Rede Virtual do Azure, consulte o artigo [Perguntas frequentes sobre a rede virtual](virtual-networks-faq.md).


## <a name="next-steps"></a>Próximas etapas

- Crie sua primeira rede virtual e implante algumas máquinas virtuais a ela executando as etapas no artigo [Criar sua primeira rede virtual](virtual-network-get-started-vnet-subnet.md).
- Crie uma conexão ponto a site para uma rede virtual executando as etapas em [Configurar uma conexão ponto a site](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Saiba mais sobre alguns dos outros principais [recursos de rede](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do Azure.
