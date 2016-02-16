<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell e o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Este artigo orienta você durante a criação de uma Rede Virtual usando o modelo do Gerenciador de Recursos e o conectando à sua rede local usando uma conexão de gateway de VPN site a site. As conexões site a site podem ser usadas para configurações híbridas. Inclui etapas adicionais para modificar os prefixos de endereços IP para sites locais existentes."
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
   ms.date="02/04/2016"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell

> [AZURE.SELECTOR]
- [Azure Classic Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artigo lhe mostrará como criar uma rede virtual e uma conexão VPN site a site para sua rede local usando o modelo de implantação do **Gerenciador de Recursos do Azure**. As conexões site a site podem ser usadas para configurações entre instalações e híbridas. Para criar uma conexão site a site para o modelo de implantação **clássico**, consulte [Configurar uma conexão site a site usando o modelo de implantação clássico](vpn-gateway-site-to-site-create.md). Se quiser conectar VNets, mas não estiver criando uma conexão com uma instalação local, consulte [Configurar uma conexão de VNet a VNet para o modelo de implantação clássico](virtual-networks-configure-vnet-to-vnet-connection.md) ou [Configurar uma conexão de VNet para VNet para o modelo de implantação do Gerenciador de Recursos](vpn-gateway-vnet-vnet-rm-ps.md).

**Sobre modelos de implantação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## Antes de começar

Verifique se você tem os itens a seguir antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.

- Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
	
- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

## Instalar os módulos do PowerShell

Você precisará da versão mais recente dos cmdlets do PowerShell do gerenciador de recursos do Azure para configurar sua conexão.
	
[AZURE.INCLUDE [vpn-gateway-ps-rm-howto](../../includes/vpn-gateway-ps-rm-howto-include.md)]

## 1\. Conecte-se as suas assinaturas 

Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

	Login-AzureRmAccount

Verificar as assinaturas da conta.

	Get-AzureRmSubscription 

Especifique a assinatura que você deseja usar.

	Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

## 2\. Criar uma rede virtual e uma sub-rede de gateway

- Se você já tiver uma rede virtual com uma sub-rede de gateway, pule para **Etapa 3 - Adicionar seu site local**. 
- Se você já tiver uma rede virtual e quiser adicionar uma sub-rede de gateway à sua Rede Virtual, consulte [Adicionar uma sub-rede de gateway a uma Rede Virtual](#gatewaysubnet).

### Para criar uma rede virtual e uma sub-rede de gateway

Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios.

Em primeiro lugar, crie um grupo de recursos:
	
	New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.

O exemplo abaixo cria uma rede virtual denominada *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará.

	$subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
	$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
	New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma Rede Virtual (opcional)

Esta etapa só será necessária se você precisar adicionar um gateway de sub-rede a uma Rede Virtual criada anteriormente.

Se já tiver uma rede virtual e desejar adicionar uma sub-rede de gateway a ela, você poderá criar sua sub-rede de gateway usando o exemplo abaixo. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se você nomear algo, você criará uma sub-rede, mas não será considerado pelo Azure como uma sub-rede de gateway.

	$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet
	Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Agora, defina a configuração.

	Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3\. Adicionar o site local

Em uma rede virtual, o *site local* geralmente se refere ao seu local. Você dará a esse site um nome pelo qual o Azure pode fazer referência a ele.

Você também especificará o prefixo de espaço de endereço para o site local. O Azure usará o prefixo do endereço IP que você especifica para identificar qual tráfego enviar para o site local. Isso significa que você terá que especificar cada prefixo de endereço que deseja que seja associado ao site local. Você pode atualizar facilmente esses prefixos se sua rede local mudar.

Ao usar os exemplos do PowerShell, observe o seguinte:
	
- O *GatewayIPAddress* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
- O *AddressPrefix* é o espaço de endereço local.

Para adicionar um site local com um prefixo de endereço único:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um site local com vários prefixos de endereço:

	New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar os prefixos de endereço IP para seu site local

Às vezes, os prefixos de site local mudam. As etapas usadas para modificar os prefixos de endereço IP dependem de você ter criado ou não uma conexão de gateway de VPN. Veja [Modificar os prefixos de endereço IP para um site local](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Solicitar um endereço IP público para o gateway

Em seguida, você solicitará um endereço IP público a ser alocado para seu gateway de VPN VNet do Azure. Esse não é o mesmo endereço IP que é atribuído ao seu dispositivo VPN, em vez disso, ele é atribuído ao próprio gateway de VPN do Azure. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

Use o exemplo do PowerShell abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.

	$gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

>[AZURE.NOTE] O gateway de VPN do Azure do modelo de implantação do Gerenciador de Recursos atualmente dá suporte apenas a endereços IP públicos usando o método de Alocação Dinâmica. No entanto, isso não significa que o endereço IP será alterado. A única vez em que o endereço IP de gateway de VPN do Azure é alterado é quando o gateway é excluído e recriado. O endereço IP público do gateway não será alterado durante o redimensionamento, a redefinição ou outros tipos de manutenção interna/atualização do gateway de VPN do Azure.

## 5\. Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.

	$vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
	$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
	$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Criar o gateway

Nesta etapa, você criará o gateway de rede virtual. Observe que a criação de um gateway pode demorar muito para ser concluída. Geralmente, 20 minutos ou mais.

Use os seguintes valores:

- O Tipo de Gateway é *Vpn*.
- O VpnType pode ser RouteBased* (mencionado como um Gateway Dinâmico em algumas documentações) ou *Baseado em Políticas* (mencionado como um Gateway Estático em algumas documentações). Para saber mais sobre os tipos de gateway de VPN, veja [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Configurar o dispositivo de VPN

Neste ponto, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Fale com o fabricante do dispositivo para obter informações específicas de configuração. Além disso, veja [Dispositivos VPN](http://go.microsoft.com/fwlink/p/?linkid=615099) para saber mais.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

	Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Criar a conexão VPN

Em seguida, você criará a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN. Certifique-se de substituir os valores pelos seus próprios. A chave compartilhada deve corresponder ao valor usado para a configuração do dispositivo VPN.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após um instante, a conexão será estabelecida.

## 9\. Verificar uma conexão VPN

Neste momento, as conexões VPN site a site criadas com o Gerenciador de Recursos não podem ser vistas no Portal de Visualização. No entanto, é possível verificar se a conexão foi bem-sucedida usando *Get-AzureRmVirtualNetworkGatewayConnection –Debug*. No futuro, teremos um cmdlet para isso, bem como a capacidade de exibir sua conexão no Portal de Visualização.

Você pode usar o seguinte exemplo de cmdlet, configurando os valores para que correspondam aos seus próprios. Quando solicitado, selecione *A* para executar Todos.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Após o cmdlet ter sido concluído, role pelos valores para exibi-los. No exemplo abaixo, o status da conexão é exibido como *Conectado* e é possível ver os bytes de entrada e saída.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## Modificar os prefixos de endereço IP para um site local

Se você precisar alterar os prefixos para seu site local, use as instruções a seguir. Dois conjuntos de instruções são fornecidos e dependem de você já ter criado ou não a conexão de gateway de VPN.

### Adicionar ou remover prefixos sem uma conexão de gateway de VPN

- **Para adicionar** outros prefixos de endereço a um site local que você criou, mas que ainda não tenha uma conexão de gateway de VPN, use o exemplo a seguir.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um site local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, iremos atualizar o site local e excluir o prefixo.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Adicionar ou remover prefixos com uma conexão de gateway de VPN

Se você criou sua conexão VPN e deseja adicionar ou remover os prefixos de endereço IP contidos no site local, você precisará fazer as seguintes etapas, nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN, pois você precisará remover e recompilar o gateway. No entanto, já que você solicitou um endereço IP para a conexão, você não precisará reconfigurar seu roteador VPN local novamente, a menos que você decida alterar os valores usados anteriormente.
 
1. Remova a conexão de gateway. 
2. Modifique os prefixos para seu site local. 
3. Crie uma nova conexão de gateway. 

Você pode usar o exemplo a seguir como uma diretriz.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

## Próximas etapas

Quando sua conexão for concluída, você poderá adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-tutorial.md) para obter as etapas.

<!---HONumber=AcomDC_0211_2016-->