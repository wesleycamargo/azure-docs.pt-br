<properties 
   pageTitle="Sobre gateways de VPN para conectividade entre locais de rede virtual | Microsoft Azure"
   description="Saiba mais sobre gateways de VPN, que podem ser usados para conexões entre locais em configurações híbridas. Este artigo cobre SKUs de gateway (Basica, Padrão e Alto desempenho) configurações de coexistência de gateway de VPN e Rota Expressa, tipos de roteamento de gateway (Estático, Dinâmico e Baseado em rota), e requisitos de gateway para conectividade de rede virtual."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/15/2015"
   ms.author="cherylmc" />

# Sobre gateways de VPN

Gateways de VPN são usados para enviar tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure. Ao criar um gateway, há alguns fatores a serem levados em consideração.
 
Ao planejar, considere os seguintes itens:

- O SKU de gateway que você deseja usar
- O tipo de roteamento de gateway para sua conexão
- O dispositivo VPN, se necessário para a conexão

## SKUs de gateway

Há 3 SKUs de gateway de VPN:

- Básico
- Standard
- Alto Desempenho

A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Coexistência de Gateway de VPN e a Rota Expressa | Taxa de transferência de Gateway de Rota Expressa | Taxa de transferência de Gateway de VPN | Túneis IPsec máximo de Gateway de VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | Não | 500 Mbps | 100 Mbps | 10 |
| Padrão | Sim | 1000 Mbps | 100 Mbps | 10 |
| Alto Desempenho | Sim | 2000 Mbps | 200 Mbps | 30 |

**Observação:** a taxa de transferência da VPN é uma estimativa aproximada baseada nas medidas entre redes virtuais na mesma região do Azure. Não é uma garantia do que você poderá obter para conexões entre locais na Internet, mas deverá ser usada como a maior medida possível.

## Tipos de roteamento de gateway

Há dois tipos de roteamento de gateway:

- **Baseado em políticas:** gateways baseados em políticas eram anteriormente chamados de *Gateways estáticos*. A funcionalidade de um gateway estático não foi alterada, mesmo que o nome tenha sido alterado. Esse tipo de gateway dá suporte a VPNs baseadas em política. As VPNs baseadas em política direcionam pacotes por meio de túneis IPsec com seletores de tráfego, com base nas combinações de prefixos de endereço entre a rede local e a rede virtual do Azure. Os seletores ou as políticas de tráfego normalmente são definidos como uma lista de acesso em suas configurações de VPN.
 
- **Baseados em rota:** gateways baseados em rota eram anteriormente chamados de *Gateways dinâmicos*. A funcionalidade de um gateway dinâmico não foi alterada, mesmo que o nome tenha sido alterado. Gateways baseados em rota implementam VPNs baseadas em rota. As VPNs baseadas em rota usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel VPN correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. O seletor de política ou de tráfego para as VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

Algumas conexões (como ponto a site e VNet a VNet) só funcionam com um tipo de roteamento de gateway específico. Você verá os requisitos do gateway listados no artigo que corresponde ao cenário de conexão que você deseja criar.

Dispositivos VPN também têm limitações de configuração. Quando você cria um gateway de VPN, seleciona o tipo de gateway necessário para sua conexão, verificando se o dispositivo VPN selecionado também dá suporte a esse tipo de roteamento. Consulte [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações.

Por exemplo, para usar uma conexão site a site simultaneamente com uma conexão ponto a site, precisará configurar um gateway de VPN baseado em rota. Embora seja verdade que conexões site a site funcionam com gateways baseados em política, conexões ponto a site requerem um tipo de gateway baseado em rota. Como ambas as conexões passam pelo mesmo gateway, você terá de selecionar o tipo de gateway que dê suporte a ambas. Além disso, o dispositivo VPN que você deve usar também deve oferecer suporte a configurações baseadas em rota.


## Requisitos do gateway

A tabela a seguir lista os requisitos para os gateways de VPN estáticos e dinâmicos.


| **Propriedade** | **Gateway de VPN baseado em política** | **Gateway de VPN baseado em rota** | **Gateway de VPN padrão** | **Gateway VPN de alto desempenho** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Conectividade site a site (S2S) | Configuração de VPN baseada em política | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota |
| Conectividade ponto a site (P2S) | Sem suporte | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) |
| Método de autenticação | Chave pré-compartilhada | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S |
| Número máximo de conexões S2S | 1 | 10 | 10 | 30 |
| Número máximo de conexões P2S | Sem suporte | 128 | 128 | 128 |
| Suporte ao roteamento ativo (BGP) | Sem suporte | Sem suporte | Sem suporte | Sem suporte |


## Próximas etapas

Selecione o dispositivo VPN para sua configuração. Veja [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).





 

<!---HONumber=AcomDC_1217_2015-->