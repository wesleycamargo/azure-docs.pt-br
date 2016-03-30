<properties 
   pageTitle="Sobre gateways de VPN para conectividade entre locais de rede virtual | Microsoft Azure"
   description="Saiba mais sobre gateways de VPN, que podem ser usados para conexões Site a Site entre locais para configurações híbridas, conexões VNet a VNet e conexões Ponto a Site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Sobre gateways de VPN

Os Gateways de VPN, também chamados de Gateways de Rede Virtual do Azure, são usados para enviar o tráfego de rede entre redes virtuais e locais. Eles também são usados para enviar tráfego entre várias redes virtuais no Azure (VNet-to-VNet). As seções a seguir discutem os itens relacionados a um gateway de VPN.

As instruções que você usará para criar o gateway de VPN dependem do modelo de implantação que usou para criar a rede virtual. Por exemplo, se tiver criado a rede virtual usando o modelo de implantação clássico, você usará as diretrizes e instruções do modelo de implantação clássico para criar e configurar o gateway de VPN. Você não pode criar um gateway de VPN do Gerenciador de Recursos para uma rede virtual do modelo de implantação clássico.

Confira [Noções básicas sobre o Gerenciador de Recursos e modelos de implantação clássicos](../resource-manager-deployment-model.md) para obter mais informações sobre modelos de implantação.


## <a name="gwsub"></a>Sub-rede de gateway

Para configurar um gateway de VPN, primeiro você precisa criar uma sub-rede de gateway para a rede virtual. Todas as sub-redes de gateway devem ser nomeadas como GatewaySubnet para funcionar adequadamente.

O tamanho mínimo de sub-rede de gateway depende totalmente da configuração que você deseja criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29 em algumas configurações, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.).

A criação de um gateway maior impede que você atinja as limitações de tamanho de gateway. Por exemplo, se tiver criado um gateway com um tamanho de sub-rede de gateway /29 e desejar configurar uma configuração com coexistência de Site a Site/Rota Expressa, você precisará excluir o gateway, excluir a sub-rede de gateway, criar a sub-rede de gateway como /28 ou maior e recriar o gateway.

Criando uma sub-rede de gateway com um tamanho maior desde o início, você pode poupar tempo posteriormente, ao adicionar novos recursos de configuração ao ambiente de rede.

O exemplo a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, o que oferece endereços IP suficientes para a maioria das configurações existentes no momento.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27


## <a name="gwtype"></a>Tipos de gateway

O tipo de gateway especifica como o próprio gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Gerenciador de Recursos. Não confunda o tipo de gateway com o tipo VPN, que especifica o tipo de roteamento para a VPN. Os valores disponíveis para GatewayType são: *Vpn* e *Rota Expressa*.


Este exemplo para o modelo de implantação do Gerenciador de Recursos especifica -GatewayType como *Vpn*. Ao criar um gateway, você deve garantir que o tipo de gateway seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="gwsku"></a>SKUs de gateway

Ao criar um gateway de VPN, você precisará especificar a SKU de gateway que deseja usar. Há 3 SKUs de gateway de VPN:

- Básico
- Padrão
- HighPerformance

O exemplo a seguir especifica GatewaySku como *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

### Taxa de transferência agregada estimada por SKU e tipo de gateway


A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="vpntype"></a>Tipos de VPN

Cada configuração requer um tipo específico de VPN para funcionar. Se estiver combinando duas configurações, como a criação de uma conexão Site a Site e uma conexão Ponto a Site com a mesma rede virtual, você deverá usar um tipo VPN que atenda a ambos os requisitos de conexão. No caso de conexões coexistentes Ponto a Site e Site a Site, você deve usar um tipo de VPN baseada em rota ao trabalhar com o modelo de implantação do Azure Resource Manager ou um gateway dinâmico se estiver trabalhando com o modo de implantação clássico.

Ao criar a configuração, você selecionará o tipo de VPN necessário para a conexão.

Há dois tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Este exemplo para o modelo de implantação do Gerenciador de Recursos especifica -VpnType como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## <a name="lng"></a>Gateways de rede local

O gateway de rede local geralmente se refere ao seu local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local. Você dará ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especificará os prefixos de endereço que estão localizados no caminho local. O Azure examinará os prefixos de endereço de destino para ol tráfego de rede, conferirá a configuração que você especificou para o gateway de rede local e roteará os pacotes adequadamente. Você pode modificar os prefixos de endereço conforme necessário.



### Modificar prefixos de endereço - Gerenciador de Recursos

Ao modificar os prefixos de endereço, o procedimento varia dependendo de você já ter criado ou não o gateway de VPN. Confira a seção do artigo [Modificar prefixos de endereço para um gateway de rede local](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

No exemplo a seguir, você pode ver que um gateway de rede local denominado MyOnPremiseWest está sendo especificado e conterá dois prefixos de endereço IP.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Modificar prefixos de endereço - implantação clássica

Se precisa modificar seus sites locais ao usar o modelo de implantação clássico, no momento, você pode usar a página de configuração de Redes Locais no portal clássico ou modificar diretamente o arquivo de Configuração de Rede, NETCFG. XML.


## Dispositivos VPN

Verifique se o dispositivo VPN que você planeja usar dá suporte ao tipo de VPN necessário para a configuração. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações sobre dispositivos VPN compatíveis.

## Requisitos do gateway


[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## Próximas etapas

Confira o artigo [Perguntas frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações antes de prosseguir com o planejamento e o projeto da configuração.





 

<!---HONumber=AcomDC_0323_2016-->