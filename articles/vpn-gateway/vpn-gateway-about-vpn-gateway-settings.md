<properties 
   pageTitle="Sobre configurações do Gateway de VPN para gateways de rede virtual | Microsoft Azure"
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
   ms.date="08/29/2016"
   ms.author="cherylmc" />

# Sobre as configurações do Gateway de VPN

Uma solução de conexão de gateway de VPN depende da configuração de vários recursos para enviar tráfego de rede entre redes virtuais e instalações locais. Cada recurso contém definições configuráveis. A combinação de recursos e configurações determina o resultado da conexão.

As seções neste artigo abordam os recursos e as configurações que se relacionam a um gateway de VPN. Você pode achar útil exibir as configurações disponíveis usando diagramas da topologia de conexão. Você pode encontrar descrições e diagramas da topologia para cada solução de conexão no artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwtype"></a>Tipos de gateway

O tipo de gateway especifica como o gateway se conecta e é uma definição de configuração necessária para o modelo de implantação do Resource Manager. Cada rede virtual pode ter apenas um gateway de rede virtual de cada tipo. Os valores disponíveis para o `-GatewayType` são:

- Vpn
- Rota Expressa


Um gateway de VPN requer o -GatewayType *Vpn*, mostrado no exemplo a seguir do PowerShell para o modelo de implantação do Resource Manager. Quando estiver criando um gateway de rede virtual, você deve garantir que o tipo de gateway seja o correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
	-VpnType RouteBased
 

## <a name="gwsku"></a>SKUs do Gateway

Ao criar um gateway de VPN, você precisa especificar a SKU do gateway de rede virtual que deseja usar. Os SKUs do Gateway se aplicam aos tipos de gateway de VPN e da Rota Expressa. Os preços diferem entre os SKUs de gateway. Para obter informações sobre preços, veja [Preços de gateway de VPN](https://azure.microsoft.com/pricing/details/vpn-gateway/). Para obter mais informações sobre a Rota Expressa, consulte a [Visão Geral Técnica da Rota Expressa](../expressroute/expressroute-introduction.md).

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


## <a name="connectiontype"></a>Tipos de conexão

Cada configuração exige um tipo específico de conexão de gateway de rede virtual. Os valores disponíveis do PowerShell do Gerenciador de Recursos para o `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- Rota Expressa
- VPNClient

No exemplo do PowerShell a seguir, criamos uma conexão S2S que exige o tipo de conexão *IPsec*.

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
	-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
	-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos de VPN

Quando você cria o gateway de rede virtual para uma configuração de gateway de VPN, é preciso especificar um tipo de VPN. O tipo de VPN que você escolhe depende da topologia de conexão que quer criar. Por exemplo, uma conexão P2S requer um tipo de VPN RouteBased. Um tipo de VPN também pode depender do hardware que você usará. As configurações S2S requerem um dispositivo VPN. Alguns dispositivos VPN são compatíveis apenas com um determinado tipo de VPN.

O tipo de VPN que você escolher deve atender a todos os requisitos de conexão da solução que você quer criar. Por exemplo, se você quiser criar uma conexão de gateway de VPN S2S e uma conexão de gateway de VPN P2S para a mesma rede virtual, use o tipo de VPN *RouteBased*, pois P2S requer um tipo de VPN RouteBased. Você também precisa verificar se o seu dispositivo VPN é compatível com uma conexão VPN RouteBased.

Depois que um gateway de rede virtual é criado, não é possível alterar o tipo de VPN. Você precisa excluir o gateway de rede virtual e criar outro. Há dois tipos de VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


O exemplo do PowerShell a seguir para o modelo de implantação do Resource Manager especifica `-VpnType` como *RouteBased*. Ao criar um gateway, você deve garantir que o -VpnType seja correto para sua configuração.

	New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
	-Location 'West US' -IpConfigurations $gwipconfig `
	-GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos do gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)]


## <a name="gwsub"></a>Sub-rede do gateway

Para configurar um gateway de rede virtual, primeiramente você precisa criar uma sub-rede de gateway para a VNet. A sub-rede do gateway deve ser nomeada como *GatewaySubnet* para funcionar corretamente. Esse nome permite que o Azure saiba que essa sub-rede deve ser usada para o gateway. Se estiver usando o Portal Clássico, a sub-rede do gateway será automaticamente denominada *Gateway* na interface do portal. Isso é específico da exibição da sub-rede de gateway no portal clássico. Nesse caso, a sub-rede é efetivamente criada com o valor *GatewaySubnet* e pode ser exibida dessa maneira no Portal do Azure e no PowerShell.

O tamanho mínimo da sub-rede de gateway depende totalmente da configuração que você quer criar. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, é recomendável criar uma sub-rede de gateway de /28 ou maior (/28, /27, /26 etc.).

A criação de um gateway maior impede que você atinja as limitações de tamanho de gateway. Por exemplo, você pode ter criado um gateway de rede virtual com um tamanho de sub-rede de gateway /29 para uma conexão S2S. Você agora quer configurar uma configuração coexistente S2S/Rota Expressa. Essa configuração exige um tamanho mínimo de sub-rede de gateway de /28. Para criar sua configuração, você teria que modificar a sub-rede de gateway para acomodar o requisito mínimo para a conexão, que é /28.

O exemplo de PowerShell do Resource Manager a seguir mostra uma sub-rede de gateway chamada GatewaySubnet. Você pode ver que a notação CIDR especifica /27, que permite endereços IP suficientes para a maioria das configurações existentes no momento.

	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

>[AZURE.IMPORTANT] Verifique se a sub-rede de gateway não tem um NSG (Grupo de Segurança da Rede) aplicado a ela, pois isso pode causar falha nas conexões.


## <a name="lng"></a>Gateways de rede locais

Ao criar uma solução de gateway de VPN, o gateway de rede local geralmente representa sua localização no local. No modelo de implantação clássico, o gateway de rede local era conhecido como um Site Local.

Você dá ao gateway de rede local um nome e o endereço IP público do dispositivo VPN local e especifica os prefixos de endereço que estão localizados no caminho local. O Azure examina os prefixos de endereço de destino para o tráfego de rede, consulta a configuração que você especificou para o gateway de rede local e roteia os pacotes adequadamente. Você também especifica os gateways de rede para configurações de rede virtual para rede virtual que usam uma conexão de gateway de VPN.

O seguinte exemplo de PowerShell do Resource Manager cria um novo gateway de rede local:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
	-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Às vezes, você precisa modificar as configurações do gateway de rede local. Por exemplo, quando você adicionar ou modificar o intervalo de endereços, ou se o endereço IP do dispositivo VPN mudar. Para uma rede virtual clássica, você pode alterar essas configurações no Portal Clássico na página Redes Locais. Para o Resource Manager, confira [Modificar as configurações de gateway de rede local usando o PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets do PowerShell e APIs REST

Para obter recursos técnicos adicionais e requisitos de sintaxe específicos ao usar cmdlets do PowerShell e APIs REST para configurações do Gateway de VPN, veja as seguintes páginas:

|**Clássico** | **Gerenciador de Recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[API REST](https://msdn.microsoft.com/library/jj154113.aspx)|[API REST](https://msdn.microsoft.com/library/mt163859.aspx)|


## Próximas etapas

Consulte [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações sobre as configurações de conexão disponíveis.







 

<!---HONumber=AcomDC_0907_2016-->