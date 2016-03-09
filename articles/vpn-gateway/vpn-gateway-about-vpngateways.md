<properties 
   pageTitle="Sobre gateways de VPN para conectividade entre locais de rede virtual | Microsoft Azure"
   description="Saiba mais sobre gateways de VPN, que podem ser usados para conexões entre locais em configurações híbridas. Este artigo cobre SKUs de gateway (Básica, Standard e Alto desempenho) configurações de coexistência de gateway de VPN e Rota Expressa, tipos de roteamento de gateway (Estático, Dinâmico e Baseado em rota), e requisitos de gateway para conectividade de rede virtual para ambos os modelos de implantação Clássica e do Gerenciador de Recursos."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/19/2016"
   ms.author="cherylmc" />

# Sobre gateways de VPN

Gateways de VPN são usados para enviar tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure (VNet-to-VNet). Ao criar um gateway, há alguns fatores a serem levados em consideração.

- O SKU de gateway que você deseja usar
- O tipo VPN para a conexão
- O dispositivo VPN, se necessário para a conexão

**Sobre os modelos de implantação**

[AZURE.INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]
 

## SKUs de gateway

[AZURE.INCLUDE [vpn-gateway-table-sku](../../includes/vpn-gateway-table-sku-include.md)]

## Tipos de gateway de VPN

Há dois tipos de VPN:

- **Baseado em política:** gateways baseados em política são chamados de *Gateways Estáticos* no modelo de implantação clássica. A funcionalidade de um gateway estático não foi alterada, mesmo que o nome tenha sido alterado. Esse tipo de gateway dá suporte a VPNs baseadas em política. As VPNs baseadas em política direcionam pacotes por meio de túneis IPsec com seletores de tráfego, com base nas combinações de prefixos de endereço entre a rede local e a rede virtual do Azure. Os seletores ou as políticas de tráfego normalmente são definidos como uma lista de acesso em suas configurações de VPN.
 
- **Baseadas em rota:** gateways baseadas em rota são chamados de *Gateways Dinâmicos* no modelo de implantação clássica. A funcionalidade de um gateway dinâmico não foi alterada, mesmo que o nome tenha sido alterado. Gateways baseados em rota implementam VPNs baseadas em rota. As VPNs baseadas em rota usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel VPN correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. O seletor de política ou de tráfego para as VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

Algumas conexões, como ponto a site e VNet a VNet, só funcionam com um tipo de VPN específico. Você verá os requisitos do gateway listados no artigo que corresponde ao cenário de conexão que você deseja criar.

Dispositivos VPN também têm limitações de configuração. Ao criar um gateway de VPN, você seleciona o tipo de VPN necessário para sua conexão, verificando se o dispositivo VPN selecionado também dá suporte a esse tipo de roteamento. Consulte [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações.

Por exemplo, para usar uma conexão site a site simultaneamente com uma conexão ponto a site, precisará configurar um gateway de VPN baseado em rota. Embora seja verdade que conexões site a site funcionam com gateways baseados em política, conexões ponto a site requerem um tipo de gateway baseado em rota. Como ambas as conexões passam pelo mesmo gateway, você terá de selecionar o tipo de gateway que dê suporte a ambas. Além disso, o dispositivo VPN que você deve usar também deve oferecer suporte a configurações baseadas em rota.


## Requisitos do gateway


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]

## Próximas etapas

Selecione o dispositivo VPN para sua configuração. Veja [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_0302_2016-->