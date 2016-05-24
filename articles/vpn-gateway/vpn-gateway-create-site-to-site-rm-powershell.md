<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN Site a Site usando o PowerShell e o Azure Resource Manager | Microsoft Azure"
   description="Este artigo orienta você durante a criação de uma Rede Virtual usando o modelo do Gerenciador de Recursos e o conectando à sua rede local usando uma conexão de gateway de VPN site a site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/13/2016"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN Site a Site usando o PowerShell e o Azure Resource Manager

> [AZURE.SELECTOR]
- [Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Portal Clássico do Azure](vpn-gateway-site-to-site-create.md)
- [PowerShell – Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artigo mostrará como criar uma rede virtual e uma conexão VPN Site a Site para sua rede local usando o modelo de implantação do Azure Resource Manager. As conexões Site a Site podem ser usadas para configurações híbridas e entre instalações.


**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Diagrama de conexão 

![Diagrama Site a Site](./media/vpn-gateway-create-site-to-site-rm-powershell/site2site.png "site-to-site")

**Modelos de implantação e ferramentas para conexões Site a Site**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Se você quiser conectar Redes Virtuais, mas não estiver criando uma conexão com uma instalação local, confira [Configurar uma conexão de Rede Virtual para Rede Virtual](vpn-gateway-vnet-vnet-rm-ps.md). Se você estiver procurando um tipo diferente de configuração da conexão, consulte o artigo [Topologias de conexão do Gateway de VPN](vpn-gateway-topology.md).


## Antes de começar

Verifique se você tem os itens a seguir antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.

- Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
	
- Uma assinatura do Azure. Se você ainda não tiver uma assinatura do Azure, poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
	
- Você precisará instalar a versão mais recente dos cmdlets do PowerShell do Gerenciador de Recursos do Azure. Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.


## 1\. Conecte-se as suas assinaturas 

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount

Verificar as assinaturas da conta.

	Get-AzureRmSubscription 

Especifique a assinatura que você deseja usar.

	Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## 2\. Criar uma rede virtual e uma sub-rede de gateway

Os exemplos a seguir mostram uma sub-rede de gateway de /28. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, não recomendamos isso. É recomendável criar uma sub-rede de gateway de /27 ou maiores (/26, /25 etc.) para acomodar os requisitos de recursos adicionais.

Se você já tiver uma rede virtual com uma sub-rede do gateway que seja /29 ou maior, poderá pular para [Adicionar seu gateway da rede local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### Para criar uma rede virtual e uma sub-rede de gateway

Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios.

Em primeiro lugar, crie um grupo de recursos:
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.

O exemplo abaixo cria uma rede virtual denominada *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede do gateway a uma rede virtual já criada

Esta etapa só será necessária se você precisar adicionar um gateway de sub-rede a uma Rede Virtual criada anteriormente.

Você pode criar sua sub-rede de gateway usando o exemplo a seguir. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se usar outro nome, você criará uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Agora, defina a configuração.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. <a name="localnet"></a>Adicionar seu gateway da rede local

Em uma rede virtual, o gateway de rede local geralmente se refere ao seu local. Você dará ao site um nome pelo qual Azure pode fazer referência a ele e também especificará o prefixo de espaço de endereço para o gateway de rede local.

O Azure usará o prefixo de endereço IP que você especificar para identificar qual tráfego enviar a seu local. Isso significa que você terá que especificar cada prefixo de endereço que deseja associar ao gateway de rede local. Você pode atualizar facilmente esses prefixos se sua rede local mudar.

Ao usar os exemplos do PowerShell, observe o seguinte:
	
- O *GatewayIPAddress* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
- O *AddressPrefix* é o espaço de endereço local.

Para adicionar um gateway de rede local com um único prefixo de endereço:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereço:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar os prefixos de endereço IP do gateway de rede local

Às vezes, os prefixos de gateway de rede local são alterados. As etapas usadas para modificar os prefixos de endereço IP dependem de você ter criado ou não uma conexão de gateway de VPN. Consulte a seção [Modificar os prefixos do endereço IP para um gateway da rede local](#modify) deste artigo.


## 4\. Solicitar um endereço IP público para o gateway de VPN

Em seguida, você solicitará um endereço IP público a ser alocado para seu gateway de VPN VNet do Azure. Esse não é o mesmo endereço IP que é atribuído ao dispositivo VPN; em vez disso, ele é atribuído ao próprio gateway de VPN do Azure. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

Use o exemplo do PowerShell abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] O gateway de VPN do Azure do modelo de implantação do Gerenciador de Recursos atualmente dá suporte apenas a endereços IP públicos usando o método de Alocação Dinâmica. No entanto, isso não significa que o endereço IP será alterado. A única vez em que o endereço IP de gateway de VPN do Azure é alterado é quando o gateway é excluído e recriado. O endereço IP público do gateway não será alterado durante o redimensionamento, a redefinição ou outros tipos de manutenção interna/atualização do gateway de VPN do Azure.

## 5\. Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Criar o gateway de rede virtual

Nesta etapa, você criará o gateway de rede virtual. Observe que a criação de um gateway pode demorar muito para ser concluída. Geralmente, 20 minutos ou mais.

Use os seguintes valores:

- O *-GatewayType* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico para a configuração que você está implementando. Por exemplo, outras configurações de gateway podem exigir -GatewayType ExpressRoute. 

- O *-VpnType* pode ser *RouteBased* (referido como Gateway Dinâmico em alguns documentos) ou *PolicyBased* (referido como Gateway Estático em alguns documentos). Para saber mais sobre os tipos de gateway de VPN, veja [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md#vpntype).
- O *-GatewaySku* pode ser *Básico*, *Standard* ou *HighPerformance*. 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

## 7\. Configurar o dispositivo de VPN

Neste ponto, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Fale com o fabricante do dispositivo para obter informações específicas de configuração. Além disso, veja [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para saber mais.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Criar a conexão VPN

Em seguida, você criará a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN. Substitua os valores pelos seus próprios. A chave compartilhada deve corresponder ao valor usado para a configuração do dispositivo VPN. Observe que o `-ConnectionType` para Site a Site é *IPsec*.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após um instante, a conexão será estabelecida.

## 9\. Verificar uma conexão VPN

Existem algumas maneiras diferentes de verificar a conexão VPN. A seguir, falaremos sobre como fazer uma verificação básica usando o portal do Azure e o PowerShell.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar os prefixos do endereço IP para um gateway da rede local

Se precisar alterar os prefixos do gateway de rede local, use as instruções a seguir. São fornecidos dois conjuntos de instruções. As instruções a serem escolhidas dependerão de você já ter criado ou não a conexão de gateway VPN.

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]


## Próximas etapas

- Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.

- Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

<!---HONumber=AcomDC_0518_2016-->