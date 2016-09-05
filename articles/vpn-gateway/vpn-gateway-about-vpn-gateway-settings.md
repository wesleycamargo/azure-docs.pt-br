<properties 
   pageTitle="Sobre as configurações do Gateway de VPN| Microsoft Azure"
   description="Saiba mais sobre as configurações do Gateway de VPN para a Rede Virtual do Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="cherylmc" />

# Sobre as configurações do Gateway de VPN

O Gateway de VPN é uma coleção de configurações que são usadas para enviar o tráfego de rede entre as redes virtuais e os locais. Você também pode usar o Gateway de VPN para enviar tráfego entre redes virtuais no Azure. As seções neste artigo analisam as configurações relacionadas ao Gateway de VPN.

Às vezes, isso ajuda a exibir as configurações disponíveis usando diagramas de conexão. Você pode encontrar diagramas de como implantar cada configuração na seção [Sobre conexões do Gateway de VPN](vpn-gateway-topology.md) do artigo.


## <a name="gwsku"></a>SKUs do Gateway

Ao criar um gateway de VPN, você precisa especificar a SKU de gateway que deseja usar. Os SKUs do Gateway se aplicam aos tipos de gateway de VPN e da Rota Expressa. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

Há 3 SKUs de gateway de VPN:

- Básico
- Standard
- HighPerformance

O exemplo do PowerShell a seguir especifica o `-GatewaySku` como *Standard*.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
	-GatewayType Vpn -VpnType RouteBased


###  <a name="aggthroughput"></a>Taxa de transferência agregada estimada por SKU e tipo de gateway


A tabela a seguir mostra os tipos de gateway e a produtividade agregada estimada. Esta tabela aplica-se a ambos os modelos de implantação do Gerenciador de Recursos e clássico.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="gwtype"></a>Tipos de gateway

O tipo de gateway especifica como o gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Resource Manager. Não confunda o tipo de gateway com o tipo VPN, que especifica o tipo de roteamento para a VPN. Os valores disponíveis para o `-GatewayType` são:

- Vpn
- Rota Expressa


Este exemplo do PowerShell para o modelo de implantação do Resource Manager especifica -GatewayType como *Vpn*. Ao criar um gateway, você deve garantir que o tipo de gateway seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased

## <a name="connectiontype"></a>Tipos de conexão

Cada configuração exige um tipo específico de conexão. Os valores disponíveis do PowerShell do Gerenciador de Recursos para o `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- Rota Expressa
- VPNClient

No exemplo a seguir do PowerShell, criamos uma conexão S2S que exige o tipo de conexão "IPsec".

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos de VPN

Cada configuração requer um tipo específico de VPN para funcionar. Ao criar a configuração, você seleciona o tipo de VPN necessário para a conexão.

Por exemplo, se quiser se conectar a uma rede virtual usando o Site a Site (S2S) e Ponto a Site (P2S), você deve usar um tipo de VPN que atenda aos requisitos de conexão para ambas as configurações. As conexões P2S exigem um tipo de VPN baseada em rota (gateway de roteamento dinâmico), enquanto as conexões S2S, às vezes, podem dar suporte a um tipo de VPN baseada em política. Isso significa que se você já tiver uma conexão S2S que use um tipo de VPN baseada em política (roteamento estático), será preciso recriar o gateway usando o tipo de VPN baseado em rota (roteamento dinâmico) para dar suporte à P2S.

Há dois tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]



Este exemplo do PowerShell para o modelo de implantação do Resource Manager especifica `-VpnType` como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos do gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Sub-rede do gateway

Para configurar um gateway de VPN, primeiro você precisa criar uma sub-rede de gateway para a rede virtual. A sub-rede do gateway deve ser nomeada como *GatewaySubnet* para funcionar corretamente. Esse nome permite que o Azure saiba que essa sub-rede deve ser usada para o gateway.<BR>Se você estiver usando o Portal Clássico, a sub-rede do gateway será nomeada automaticamente como *Gateway* na interface do portal. Isso é específico da exibição da sub-rede de gateway no portal clássico. Nesse caso, a sub-rede é efetivamente criada com o valor *GatewaySubnet* e pode ser exibida dessa maneira no Portal do Azure e no PowerShell.

O tamanho mínimo da sub-rede de gateway depende totalmente da configuração que você quer criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.).

A criação de um gateway maior impede que você atinja as limitações de tamanho de gateway. Por exemplo, você pode ter criado um gateway de rede virtual com um tamanho de sub-rede de gateway /29 para uma conexão S2S. Você agora quer configurar uma configuração coexistente S2S/Rota Expressa. Essa configuração exige um tamanho mínimo de sub-rede de gateway de /28. Para criar sua configuração, você teria que modificar a sub-rede de gateway para acomodar o requisito mínimo para a conexão, que é /28.

O exemplo do PowerShell a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verifique se a sub-rede de gateway não tem um NSG (Grupo de Segurança da Rede) aplicado a ela, pois isso pode causar falha nas conexões.


## <a name="lng"></a>Gateways de rede locais

O gateway de rede local geralmente se refere ao seu local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local. Você dá ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especifica os prefixos de endereço que estão localizados no caminho local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente.

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Para uma rede virtual clássica, você pode alterar essas configurações no Portal Clássico na página Redes Locais. Para o Resource Manager, confira [Modificar as configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>APIs REST e PowerShell

Para obter recursos técnicos e requisitos de sintaxe específicos ao usar as APIs REST e o PowerShell, veja as seguintes páginas:

|**Clássico** | **Gerenciador de Recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Próximas etapas

Consulte [Conexões do Gateway de VPN](vpn-gateway-topology.md) para obter mais informações sobre as configurações de conexão disponíveis.







 

<!---HONumber=AcomDC_0824_2016-->