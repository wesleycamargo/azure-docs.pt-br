<properties 
   pageTitle="Sobre o Gateway de VPN| Microsoft Azure"
   description="Saiba mais sobre o Gateway de VPN para a Rede Virtual do Azure."
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
   ms.date="07/20/2016"
   ms.author="cherylmc" />

# Sobre o Gateway de VPN

O Gateway de VPN é uma coleção de configurações que são usadas para enviar o tráfego de rede entre as redes virtuais e os locais. As seções neste artigo analisam as configurações relacionadas ao Gateway de VPN. O Gateway de VPN é usado para as conexões de Site para Site, Ponto para Site e da Rota Expressa. O Gateway de VPN também é usado para enviar o tráfego entre várias redes virtuais no Azure (VNet para VNet).

O Gateway de VPN pode ser adicionado a uma rede virtual para criar uma conexão. Cada rede virtual pode ter apenas um Gateway de VPN e há etapas de configuração específicas para cada conexão. Para os diagramas de conexão, consulte [Topologias de conexão do Gateway de VPN](vpn-gateway-topology.md).

## <a name="gwsku"></a>SKUs do Gateway

Ao criar um gateway de VPN, você precisará especificar a SKU de gateway que deseja usar. Os SKUs do Gateway se aplicam aos tipos de gateway de VPN e da Rota Expressa. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

Há 3 SKUs de gateway de VPN:

- Básico
- Padrão
- HighPerformance

O exemplo a seguir especifica o `-GatewaySku` como *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard -GatewayType Vpn -VpnType RouteBased

###  <a name="aggthroughput"></a>Taxa de transferência agregada estimada por SKU e tipo de gateway


A tabela a seguir mostra os tipos de gateway e a taxa de transferência agregada estimada. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>Tipos de gateway

O tipo de gateway especifica como o próprio gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Gerenciador de Recursos. Não confunda o tipo de gateway com o tipo VPN, que especifica o tipo de roteamento para a VPN. Os valores disponíveis para o `-GatewayType` são:

- Vpn
- Rota Expressa


Este exemplo para o modelo de implantação do Gerenciador de Recursos especifica -GatewayType como *Vpn*. Ao criar um gateway, você deve garantir que o tipo de gateway seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## <a name="connectiontype"></a>Tipos de conexão

Cada configuração exige um tipo específico de conexão. Os valores disponíveis do PowerShell do Gerenciador de Recursos para o `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- Rota Expressa
- VPNClient

No exemplo abaixo, estamos criando uma conexão site a site, que exige o tipo de conexão "IPsec".

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## <a name="vpntype"></a>Tipos de VPN

Cada configuração requer um tipo específico de VPN para funcionar. Se estiver combinando duas configurações, como a criação de uma conexão Site a Site e uma conexão Ponto a Site com a mesma rede virtual, você deverá usar um tipo VPN que atenda a ambos os requisitos de conexão.

No caso de conexões coexistentes Ponto a Site e Site a Site, você deve usar um tipo de VPN baseada em rota ao trabalhar com o modelo de implantação do Azure Resource Manager ou um gateway dinâmico se estiver trabalhando com o modo de implantação clássico.

Ao criar a configuração, você selecionará o tipo de VPN necessário para a conexão.

Há dois tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]

Este exemplo para o modelo de implantação do Gerenciador de Recursos especifica o `-VpnType` como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos do gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Sub-rede do gateway

Para configurar um gateway de VPN, primeiro você precisa criar uma sub-rede de gateway para a rede virtual. As sub-redes do gateway devem ser nomeadas como *GatewaySubnet* para funcionarem adequadamente. Esse nome permite que o Azure saiba que essa sub-rede deve ser usada para o gateway.<BR>Se você estiver usando o portal clássico, a sub-rede do gateway será nomeada automaticamente como *Gateway* na interface do portal. Isso é específico da exibição da sub-rede de gateway no portal clássico. Nesse caso, a sub-rede é efetivamente criada no Azure como *GatewaySubnet* e pode ser exibida dessa maneira no portal do Azure e no PowerShell.

O tamanho mínimo de sub-rede de gateway depende totalmente da configuração que você deseja criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29 em algumas configurações, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.).

A criação de um gateway maior impede que você atinja as limitações de tamanho de gateway. Por exemplo, se tiver criado um gateway com um tamanho de sub-rede de gateway /29 e desejar configurar uma configuração com coexistência de Site a Site/Rota Expressa, você precisará excluir o gateway, excluir a sub-rede de gateway, criar a sub-rede de gateway como /28 ou maior e recriar o gateway.

Criando uma sub-rede de gateway com um tamanho maior desde o início, você pode poupar tempo posteriormente, ao adicionar novos recursos de configuração ao ambiente de rede.

O exemplo a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, o que oferece endereços IP suficientes para a maioria das configurações existentes no momento.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verifique se o GatewaySubnet não tem um NSG (Grupo de Segurança da Rede) aplicado, pois isso pode causar falha nas conexões.



## <a name="lng"></a>Gateways da rede local

O gateway de rede local geralmente se refere ao seu local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local. Você dará ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especificará os prefixos de endereço que estão localizados no caminho local. O Azure examinará os prefixos de endereço de destino para ol tráfego de rede, conferirá a configuração que você especificou para o gateway de rede local e roteará os pacotes adequadamente. Você pode modificar os prefixos de endereço conforme necessário.


### Modificar prefixos de endereço - Gerenciador de Recursos

Ao modificar os prefixos de endereço, o procedimento varia dependendo de você já ter criado ou não o gateway de VPN. Consulte a seção do artigo [Modificar prefixos de endereço para um gateway de rede local](vpn-gateway-create-site-to-site-rm-powershell.md#modify).

No exemplo a seguir, você pode ver que um gateway de rede local denominado MyOnPremiseWest está sendo especificado e conterá dois prefixos de endereço IP.

	New-AzureRmLocalNetworkGateway -Name MyOnPremisesWest -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')	

### Modificar prefixos de endereço - implantação clássica

Se você precisar modificar seus sites locais ao usar o modelo de implantação clássico, poderá usar a página de configuração das Redes Locais no portal clássico ou modificar diretamente o arquivo de Configuração de Rede, NETCFG. XML.



## Próximas etapas

Consulte o artigo [Perguntas Frequentes sobre o Gateway de VPN](vpn-gateway-vpn-faq.md) para obter mais informações antes de prosseguir com o planejamento e o projeto da configuração.





 

<!---HONumber=AcomDC_0720_2016-->