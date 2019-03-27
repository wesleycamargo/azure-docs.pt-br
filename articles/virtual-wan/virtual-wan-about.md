---
title: Visão geral da WAN Virtual do Azure | Microsoft Docs
description: Saiba mais sobre conectividade de ramificação por ramificação escalonável automatizada de WAN Virtual, regiões disponíveis e parceiros.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 03/20/2019
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 6f2f002c5ff08e21741927d07a0facfd09ec0914
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2019
ms.locfileid: "58295684"
---
# <a name="what-is-azure-virtual-wan"></a>O que é a WAN Virtual do Azure?

WAN Virtual do Azure é um serviço de rede que fornece conectividade de ramificação otimizada e automatizada para e por meio do Azure. As regiões do Azure servem como hubs para os quais é possível optar por conectar suas ramificações. Depois que as ramificações estiverem conectadas, você poderá aproveitar o backbone do Azure para estabelecer conectividade de ramificação por ramificação e ramificação a VNet. Para obter uma lista de parceiros e localizações que dão suporte à VPN de WAN Virtual, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

A WAN Virtual do Azure reúne muitos serviços de conectividade de nuvem do Azure, como VPN site a site (geralmente disponível), ExpressRoute (versão prévia), VPN de usuário ponto a site (versão prévia) em uma única interface operacional. A conectividade com VNets do Azure é estabelecida usando conexões de rede virtual.

![Diagrama de WAN virtual](./media/virtual-wan-about/vwangraphic.png)

Este artigo fornece uma visão rápida da conectividade de rede na WAN Virtual do Azure. A WAN Virtual oferece as seguintes vantagens:

* **Soluções de conectividade integradas no hub e spoke:** Automatize a configuração site a site e a conectividade entre sites locais e um hub do Azure.
* **Definição e configuração do spoke automatizado:** Conecte as redes virtuais e cargas de trabalho ao hub do Azure diretamente.
* **Solução de problemas intuitiva:** É possível ver o fluxo de ponta a ponta no Azure e usar essas informações para executar as ações necessárias.

## <a name="resources"></a>Recursos da WAN Virtual

Para configurar uma WAN Virtual de ponta a ponta, você cria os seguintes recursos:

* **virtualWAN:** O recurso virtualWAN representa uma sobreposição virtual da rede do Azure e é uma coleção de vários recursos. Ele contém links para todos os seus hubs virtuais que você gostaria de ter dentro da WAN Virtual. Os recursos da WAN Virtual ficam isolados uns dos outros e não podem conter um hub comum. Os hubs virtuais na WAN Virtual não se comunicam entre si. A propriedade "Permitir tráfego de ramificação a ramificação" habilita tráfego entre sites de VPN, bem como VPN para Sites habilitados para ExpressRoute (atualmente em versão prévia).

* **Hub:** Um hub virtual é uma rede virtual gerenciada pela Microsoft. O hub contém vários pontos de extremidade de serviço para habilitar a conectividade de sua rede local (vpnsite). O hub é o núcleo da sua rede em uma região. Pode haver apenas um hub por região do Azure. Quando você cria um hub usando o portal do Azure, ele cria uma VNet de hub virtual e um hub virtual vpngateway.

  Um gateway de hub não é o mesmo que um gateway de rede virtual que você pode usar para ExpressRoute e Gateway de VPN. Por exemplo, ao usar WAN Virtual, você não cria uma conexão de site a site do site local diretamente para a rede virtual. Em vez disso, você cria uma conexão site a site com o hub. O tráfego sempre passa pelo gateway do hub. Isso significa que suas VNets não precisam de um gateway de rede virtual próprio. A WAN Virtual permite que suas VNets tirem proveito do dimensionamento fácil por meio do hub virtual e do gateway do hub virtual.

* **Conexão de rede virtual do hub:** O recurso de conexão de rede virtual do Hub é usado para conectar o hub à rede virtual. Neste momento, você só pode se conectar a redes virtuais que estejam na mesma região do hub.

* **Tabela de rotas de hub:**  Você pode criar uma rota de hub virtual e aplicar a rota à tabela de rotas do hub virtual. Você pode aplicar várias rotas à tabela de rotas do hub virtual.

**Recursos adicionais de WAN Virtual**

  * **Site:** Esse recurso é usado apenas para conexões site a site. O recurso do site é **vpnsite**. Ele representa o dispositivo VPN local e as respectivas configurações. Ao trabalhar com um parceiro de WAN Virtual, você terá uma solução interna para exportar automaticamente essas informações para o Azure.

## <a name="connectivity"></a>Conectividade

A WAN Virtual permite três tipos de conectividade: site a site, ponto a site (versão prévia) e ExpressRoute (versão prévia).

### <a name="s2s"></a>Conexões VPN site a site

![Diagrama de WAN virtual](./media/virtual-wan-about/virtualwan.png)

Ao criar uma conexão site a site de WAN Virtual, você poderá trabalhar com um parceiro disponível. Se você não quiser usar um parceiro, poderá configurar a conexão manualmente. Para obter mais informações, consulte [Criar uma conexão site a site usando WAN Virtual](virtual-wan-site-to-site-portal.md).

#### <a name="s2spartner"></a>Fluxo de trabalho do parceiro da WAN Virtual

Ao trabalhar com um parceiro da WAN Virtual, o fluxo de trabalho será:

1. O controlador do dispositivo de branch (VPN/SDWAN) é autenticado para exportar informações do site no Azure usando uma [Entidade de Serviço do Azure](../active-directory/develop/howto-create-service-principal-portal.md).
2. O controlador do dispositivo de branch (VPN/SDWAN) obtém a configuração de conectividade do Azure e atualiza o dispositivo local. Isso automatiza o download da configuração, a edição e a atualização do dispositivo VPN local.
3. Quando o dispositivo tiver a configuração do Azure correta, uma conexão site a site (dois túneis ativos) será estabelecida com a WAN do Azure. O Azure é compatível com IKEv1 e IKEv2. BGP é opcional.

#### <a name="partners"></a>Parceiros para conexões da WAN Virtual site a site

Para obter uma lista dos parceiros e localizações disponíveis, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

### <a name="p2s"></a>Conexões VPN ponto a site (versão prévia)

Uma conexão P2S (ponto a site) permite que você crie uma conexão segura para seu hub virtual de um computador cliente individual. Uma conexão P2S é estabelecida iniciando-a do computador cliente. Essa solução é útil para pessoas que trabalham remotamente que querem se conectar de um local remoto, como de casa ou de uma conferência. A VPN P2S também é uma solução útil para usar em vez de uma VPN S2S, quando você tiver apenas alguns clientes que precisam se conectar.

Para criar a conexão, consulte [Criar uma conexão ponto a site usando WAN Virtual](virtual-wan-point-to-site-portal.md).

### <a name="er"></a>Conexões do ExpressRoute (versão prévia)

O ExpressRoute permite conectar a rede local ao Azure em uma conexão privada. Para criar a conexão, consulte [Criar uma conexão ExpressRoute usando WAN Virtual](virtual-wan-expressroute-portal.md).

## <a name="locations"></a>Localizações

Para obter informações de localização, consulte o artigo [Parceiros e localizações de WAN Virtual](virtual-wan-locations-partners.md).

## <a name="faq"></a>Perguntas frequentes

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="next-steps"></a>Próximas etapas

[Criar uma conexão site a site usando WAN Virtual](virtual-wan-site-to-site-portal.md)
