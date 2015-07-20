<properties 
   pageTitle="Sobre gateways de VPN para uma rede virtual | Microsoft Azure"
   description="Saiba mais sobre as SKUs de Gateway de VPN Básica, Padrão e de Alto Desempenho, coexistência de Gateway de VPN e Rota Expressa, tipos de roteamento de gateway Estático e Dinâmico e requisitos de gateway para conectividade de rede virtual."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="06/10/2015"
   ms.author="cherylmc" />

# Sobre gateways de VPN

Os Gateways de VPN são usados para enviar tráfego de rede entre redes virtuais e locais, ou entre várias redes virtuais (rede virtual a rede virtual). Ao criar um gateway, há alguns fatores a serem levados em consideração. Você precisará saber qual SKU de Gateway que deseja usar, o tipo de roteamento necessário para a sua configuração (dinâmico ou estático) e o dispositivo VPN que planeja usar se um dispositivo VPN for necessário para a sua configuração.

## SKUs de gateway
Há 3 SKUs de Gateway de VPN; Básico, Padrão e Alto Desempenho. A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](http://azure.microsoft.com/pricing/details/vpn-gateway/).

| SKU | Coexistência de Gateway de VPN e a Rota Expressa | Taxa de transferência de Gateway de Rota Expressa | Taxa de transferência de Gateway de VPN | Túneis IPsec máximo de Gateway de VPN |
|-------------|-----------------------------------|---------------------------------|------------------------|-------------------------------|
| Basic | Não | 500 Mbps | 100 Mbps | 10 |
| Padrão | Sim | 1000 Mbps | 100 Mbps | 10 |
| Alto Desempenho | Sim | 2000 Mbps | 200 Mbps | 30 |

**Observação:** a taxa de transferência da VPN é uma estimativa aproximada baseada nas medidas entre redes virtuais na mesma região do Azure. Não é uma garantia do que você poderá obter para conexões entre locais na Internet, mas deverá ser usada como a maior medida possível.

## Tipos de gateway

Há dois tipos de gateway, *roteamento estático* (também conhecido como uma VPN baseada em política) e *roteamento dinâmico* (também conhecido como uma VPN baseada em rotas). Algumas configurações só funcionam com um tipo de roteamento específico, enquanto alguns dispositivos VPN só funcionam com outro tipo de roteamento específico. Quando você criar um gateway de VPN, selecionará o tipo de gateway necessário para sua configuração, verificando se o dispositivo VPN selecionado também dá suporte a esse tipo de roteamento.

Por exemplo, se você planeja usar uma configuração site a site simultaneamente com uma configuração ponto a site, precisará configurar um gateway de VPN de roteamento dinâmico. Embora seja verdade que as configurações site a site funcionarão com gateways de roteamento estático, as configurações ponto a site exigirão um gateway de roteamento dinâmico. Como ambas as conexões passem pelo mesmo gateway, você terá de selecionar o tipo de gateway que dê suporte a ambas as configurações.

Além disso, você desejará verificar se seu dispositivo VPN dá suporte ao tipo de gateway e os parâmetros de IPsec/IKE e a configuração necessária. Por exemplo, se você deseja criar um gateway dinâmico e seu dispositivo VPN não oferece suporte a VPNs baseadas em rota, precisará reconsiderar seus planos. Você pode decidir adquirir um dispositivo VPN diferente que oferece suporte a gateways dinâmicos ou crie uma conexão de gateway de VPN que dê suporte a um gateway de roteamento estático. Se, posteriormente, você adquirir um dispositivo VPN capaz de dar suporte a um gateway de roteamento dinâmico, sempre poderá recriar o gateway como dinâmico para usar o dispositivo. Nesse caso, você precisará recriar apenas o gateway. Não será necessário recriar a rede virtual.

A seguir, os dois tipos de gateways:

- **Roteamento estático –** gateways de roteamento estático dão suporte a **VPNs baseadas em política**. As VPNs baseadas em política direcionam pacotes por meio de túneis IPsec com seletores de tráfego com base em combinações de prefixos de endereço entre sua rede local e sua rede virtual do Azure. Os seletores ou as políticas de tráfego normalmente são definidos como uma lista de acesso em suas configurações de VPN.

	>[AZURE.NOTE]Nem todas as configurações são compatíveis com gateways de VPN de roteamento estático. Por exemplo, configurações de vários locais, configurações de rede virtual para rede virtual e conexões ponto a site exigem gateways de roteamento dinâmico. Você verá os requisitos de gateway nos artigos sobre cada configuração.

- **Roteamento dinâmico –** os gateways de roteamento dinâmico implementam **VPNs baseadas em rota**. As VPNs baseadas em rota usam "rotas" da tabela de roteamento ou de encaminhamento de IP para direcionar pacotes para as interfaces de túnel VPN correspondentes. As interfaces de túnel criptografam ou descriptografam então os pacotes para dentro e para fora dos túneis. O seletor de política ou de tráfego para as VPNs baseadas em rota são configurados como qualquer para qualquer (ou curingas).

## Requisitos do gateway

A tabela a seguir lista os requisitos para os gateways de VPN estáticos e dinâmicos.


| **Propriedade** | **Gateway de VPN de roteamento estático** | **Gateway de VPN de roteamento dinâmico** | **Gateway de VPN padrão** | **Gateway VPN de alto desempenho** |
|-----------------------------------------|--------------------------------|-----------------------------------------------------------------------|-----------------------------------|----------------------------------|
| Conectividade site a site (S2S) | Configuração de VPN baseada em política | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota | Configuração de VPN baseada em rota |
| Conectividade ponto a site (P2S) | Sem suporte | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) | Com suporte (pode coexistir com S2S) |
| Método de autenticação | Chave pré-compartilhada | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S | -Chave pré-compartilhada para conectividade S2S -Certificados para conectividade de P2S |
| Número máximo de conexões S2S | 1 | 10 | 10 | 30 |
| Número máximo de conexões P2S | Sem suporte | 128 | 128 | 128 |
| Suporte ao roteamento ativo (BGP) | Sem suporte | Sem suporte | Sem suporte | Sem suporte |


## Próximas etapas

Selecione o dispositivo VPN para sua configuração. Veja [Sobre dispositivos VPN](http://go.microsoft.com/fwlink/p/?LinkID=615934).

Configure sua rede virtual. Para conexões entre locais, consulte os seguintes artigos:

- [Configurar uma conexão site a site entre locais para uma Rede Virtual do Azure](vpn-gateway-site-to-site-create.md)
- [Configurar uma conexão VPN ponto a site para a Rede Virtual do Azure](vpn-gateway-point-to-site-create.md)
- [Configurar uma VPN site a site usando o Serviço de Roteamento e Acesso Remoto (RRAS) do Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)

Se você desejar configurar um gateway de VPN, veja [Configure um gateway de VPN](http://go.microsoft.com/fwlink/p/?LinkId=615106).

Se você quiser alterar o tipo de gateway de VPN, veja [Alterar um tipo de gateway de VPN de rede virtual](http://go.microsoft.com/fwlink/p/?LinkId=615109).

Se você quiser conectar vários sites a uma rede virtual, veja [Conectar vários sites locais a uma rede virtual](http://go.microsoft.com/fwlink/p/?LinkID=615106).

 

<!---HONumber=July15_HO2-->