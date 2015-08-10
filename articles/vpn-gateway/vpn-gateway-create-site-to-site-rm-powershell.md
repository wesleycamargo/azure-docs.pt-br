<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell e o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Criar uma conexão VPN site a site da rede virtual até seu local usando o PowerShell e o Gerenciador de Recursos do Azure"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell e o Gerenciador de Recursos do Azure

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Este tópico mostrará a você como criar uma rede virtual e uma conexão VPN site a site do Gerenciador de Recursos do Azure para sua rede local.

Atualmente, o Azure tem dois modelos de implantação: o modelo de implantação clássico e o modelo de implantação do Gerenciador de Recursos do Azure. A configuração site a site é diferente; depende do modelo que foi usado para implantar a rede virtual. Estas instruções aplicam-se ao Gerenciador de Recursos. Se quiser criar uma conexão de gateway de VPN site a site usando o modelo de implantação clássico, consulte [Criar uma conexão VPN site a site no Portal de Gerenciamento](vpn-gateway-site-to-site-create.md).


## Antes de começar

Antes de começar, verifique se possui os seguintes itens:

- Um dispositivo VPN compatível (e alguém apto a configurá-lo). Consulte [Sobre dispositivos VPN](vpn-gateway-vpn-devices.md).
- Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
- A versão mais recente dos cmdlets do Azure PowerShell. Você pode baixar e instalar a versão mais recente na seção Windows PowerShell da [página de download](http://azure.microsoft.com/downloads/). 
- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).
	

## Conecte-se as suas assinaturas 


Abra o console do PowerShell e alterne para o modo Gerenciador de Recursos do Azure. Use o exemplo a seguir para ajudar com a conexão:

		Add-AzureAccount

Use Select-AzureSubscription para se conectar à assinatura que deseja usar.

		Select-AzureSubscription "yoursubscription"

Em seguida, alterne para o modo ARM. Isso alternará o modo para permitir que você use os cmdlets do ARM.

		Switch-AzureMode -Name AzureResourceManager


## Criar uma rede virtual e uma sub-rede de gateway

- Se você já tiver uma rede virtual com uma sub-rede de gateway, pule para [Adicionar seu site local](#add-your-local-site). 
- Se você tiver uma rede virtual e deseja adicionar uma sub-rede de gateway à sua VNet, consulte [Adicionar uma sub-rede de gateway a uma VNet](#gatewaysubnet).

### Para criar uma rede virtual e uma sub-rede de gateway

Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios.

Em primeiro lugar, crie um grupo de recursos:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. O exemplo abaixo cria uma rede virtual denominada *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma VNet

Se já tiver uma rede virtual e desejar adicionar uma sub-rede de gateway a ela, você poderá criar sua sub-rede de gateway usando o exemplo abaixo. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se você usar outro nome, a configuração do VPN não funcionará conforme esperado.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Adicionar o site local

Em uma rede virtual, o *site local* geralmente se refere ao seu local. Você dará a esse site um nome pelo qual o Azure pode fazer referência a ele.

Você também especificará o prefixo de espaço de endereço para o site local. O Azure usará o prefixo do endereço IP que você especifica para identificar qual tráfego enviar para o site local. Isso significa que você terá que especificar cada prefixo de endereço que deseja que seja associado ao site local. Você pode atualizar facilmente esses prefixos se sua rede local mudar. Use os exemplos do PowerShell abaixo para especificar o site local.

	
- O *GatewayIPAddress* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
- O *AddressPrefix* é o espaço de endereço local.

Use este exemplo para adicionar o site local:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## Solicitar um endereço IP público para o gateway da VNet

Em seguida, você solicitará um endereço IP público a ser alocado para seu gateway de VPN VNet do Azure. Esse não é o mesmo endereço IP que é atribuído ao seu dispositivo VPN, em vez disso, ele é atribuído ao próprio gateway de VPN do Azure. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

Use o exemplo do PowerShell abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Criar o gateway

Nesta etapa, você criará o gateway de rede virtual. Use os seguintes valores:

- O Tipo de Gateway é *Vpn*.
- O VpnType pode ser RouteBased\* (referenciado como um Gateway Dinâmico em algumas documentações) ou *Policy Based* (referenciado como um Gateway Estático em algumas documentações). Para obter mais informações sobre tipos de gateway de VPN, consulte [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Configurar o dispositivo de VPN

Neste ponto, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Fale com o fabricante do dispositivo para obter informações específicas de configuração. Além disso, consulte [Dispositivos VPN](http://go.microsoft.com/fwlink/p/?linkid=615099) para obter mais informações.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Criar a conexão VPN

Em seguida, você criará a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN. Certifique-se de substituir os valores pelos seus próprios. A chave compartilhada deve corresponder ao valor usado para a configuração do dispositivo VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Depois de alguns minutos, a conexão deve ser estabelecida. Neste momento, as conexões VPN site a site criadas com o Gerenciador de Recursos não podem ser vistas no Portal.


## Próximas etapas

Adicione uma máquina virtual à rede virtual. [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=July15_HO5-->