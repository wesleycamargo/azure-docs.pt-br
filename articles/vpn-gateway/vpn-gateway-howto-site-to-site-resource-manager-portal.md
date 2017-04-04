---
title: 'Conectar a sua rede local para uma rede virtual do Azure: VPN Site a Site: portal | Microsoft Docs'
description: "Etapas para criar uma conexão IPsec de sua rede local para uma rede virtual do Azure pela Internet pública. Essas etapas o ajudarão a criar uma conexão de Gateway de VPN Site a Site entre locais usando o portal."
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 827a4db7-7fa5-4eaf-b7e1-e1518c51c815
ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 80939bb48c29ba39e2d347cb80d6169d79329cfc
ms.lasthandoff: 03/25/2017


---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Criar uma conexão Site a Site no portal do Azure
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Portal do Azure - Clássico](vpn-gateway-howto-site-to-site-classic-portal.md)
> * [Clássico - Portal Clássico](vpn-gateway-site-to-site-create.md)
>
>


Uma conexão de gateway VPN Site a Site (S2S) é uma conexão por túnel VPN IPsec/IKE (IKEv1 ou IKEv2). Esse tipo de conexão exige um dispositivo VPN local com um endereço IP público atribuído a ele e não por uma NAT. As conexões Site a Site podem ser usadas para configurações híbridas e entre instalações.

Este artigo mostra como criar uma rede virtual e uma conexão de gateway de VPN Site a Site com sua rede local usando o modelo de implantação do Azure Resource Manager e o Portal do Azure. 

![Diagrama de conexão Site a Site de Gateway de VPN entre locais](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos e métodos de implantação para conexões Site a Site
[!INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela a seguir mostra os modelos de implantação e os métodos de configurações de site a site disponíveis no momento. Quando houver um artigo com etapas de configuração disponível, o vincularemos diretamente desta tabela.

[!INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais
Se você quiser conectar Redes Virtuais, mas não estiver criando uma conexão com uma instalação local, confira [Configurar uma conexão de Rede Virtual para Rede Virtual](vpn-gateway-vnet-vnet-rm-ps.md). Se você deseja adicionar uma conexão Site a Site a uma rede virtual que já tem uma conexão, consulte [Adicionar uma conexão de S2S a uma rede virtual com uma conexão de gateway de VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar
Antes de começar a configurar, verifique se você tem os seguintes itens:

* Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).
* Se você não estiver familiarizado com os espaços de endereço IP localizados na sua rede local, precisará combinar com alguém que possa fornecer os detalhes. As conexões Site a Site não podem ter espaços de endereço sobrepostos.
* Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) ou inscrever-se para obter uma [conta gratuita](http://azure.microsoft.com/pricing/free-trial).

### <a name="values"></a>Valores de exemplo
Ao usar essas etapas como um exercício, use os seguintes valores de exemplo:

* **Nome da rede virtual:** TestVNet1
* **Espaço de endereço:** 
    * 10.11.0.0/16
    * 10.12.0.0/16 (opcional para este exercício)
* **Sub-redes:**
  * FrontEnd: 10.11.0.0/24
  * BackEnd: 10.12.0.0/24 (opcional para este exercício)
  * GatewaySubnet: 10.11.255.0/27
* **Grupo de recursos:** TestRG1
* **Local:** Leste dos EUA
* **Servidor DNS:** o endereço IP do seu Servidor DNS
* **Nome do gateway de rede virtual:** VNet1GW
* **IP público:** VNet1GWIP
* **Tipo de VPN:** baseada em rota
* **Tipo de Conexão:** site a site (IPsec)
* **Tipo de gateway:** VPN
* **Nome do Gateway de Rede Local:** Site2
* **Nome da conexão:** VNet1toSite2

## <a name="CreatVNet"></a>1. Criar uma rede virtual

[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-s2s-rm-portal-include.md)]

## <a name="dns"></a>2. Especificar um servidor DNS
O DNS não é necessário para conexões Site a Site. No entanto, se você quiser ter a resolução de nomes dos recursos que são implantados em sua rede virtual, deverá especificar um servidor DNS. Essa configuração permite que você especifique o servidor DNS que deseja usar para a resolução de nomes dessa rede virtual. Ela não cria um servidor DNS.

[!INCLUDE [vpn-gateway-add-dns-rm-portal](../../includes/vpn-gateway-add-dns-rm-portal-include.md)]

## <a name="gatewaysubnet"></a>3. Criar uma sub-rede de gateway
Você deve criar uma sub-rede de gateway para seu gateway de VPN. A sub-rede de gateway contém os endereços IP que os serviços de gateway de VPN usarão. Se possível, crie uma sub-rede de gateway usando um bloco CIDR /28 ou /27. Isso garantirá uma quantidade suficiente de endereços IP para acomodar outros requisitos futuros de configuração.

[!INCLUDE [vpn-gateway-add-gwsubnet-rm-portal](../../includes/vpn-gateway-add-gwsubnet-s2s-rm-portal-include.md)]

## <a name="VNetGateway"></a>4. Criar um gateway de rede virtual

[!INCLUDE [vpn-gateway-add-gw-s2s-rm-portal](../../includes/vpn-gateway-add-gw-s2s-rm-portal-include.md)]

## <a name="LocalNetworkGateway"></a>5. Criar um gateway de rede local
O gateway de rede local se refere ao seu local. As configurações que você especificar para o gateway de rede local determinam os espaços de endereço que são roteados para o dispositivo VPN local.

[!INCLUDE [vpn-gateway-add-lng-s2s-rm-portal](../../includes/vpn-gateway-add-lng-s2s-rm-portal-include.md)]

## <a name="VPNDevice"></a>6. Configurar o dispositivo de VPN
[!INCLUDE [vpn-gateway-configure-vpn-device-rm](../../includes/vpn-gateway-configure-vpn-device-rm-include.md)]

## <a name="CreateConnection"></a>7. Criar uma conexão VPN site a site

Nesta etapa, você criará a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN local. Antes de iniciar esta seção, verifique se a criação do gateway de rede virtual e dos gateways de rede local foram concluídas.

[!INCLUDE [vpn-gateway-add-site-to-site-connection-rm-portal](../../includes/vpn-gateway-add-site-to-site-connection-s2s-rm-portal-include.md)]

## <a name="VerifyConnection"></a>8. Verificar a conexão VPN

[!INCLUDE [Azure portal](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas
*  Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Para saber mais, veja [Máquinas virtuais](https://docs.microsoft.com/azure/#pivot=services&panel=Compute).
*  Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).


