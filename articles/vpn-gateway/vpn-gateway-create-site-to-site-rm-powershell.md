<properties
   pageTitle="Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell e o Gerenciador de Recursos do Azure | Microsoft Azure"
   description="Este artigo lhe orienta através da criação de uma VNet usando o modelo do Gerenciador de Recursos e conectando-o à sua rede local usando uma conexão de gateway de VPN site a site. Inclui etapas adicionais para modificar os prefixos de endereços IP para sites locais existentes."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/13/2015"
   ms.author="cherylmc"/>

# Criar uma rede virtual com uma conexão VPN site a site usando o PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

Este artigo mostrará a você como criar uma rede virtual e uma conexão VPN site a site para sua rede local usando o modelo de implantação do Gerenciador de Recursos do Azure. Você pode selecionar o artigo do modelo de implantação e da ferramenta de implantação usando as guias acima.

>[AZURE.NOTE]É importante saber que atualmente o Azure funciona com dois modelos de implantação: Gerenciador de Recursos e clássico. Antes de começar a configuração, entenda as ferramentas e os modelos de implantação. Para saber mais sobre os modelos de implantação, consulte [Modelos de implantação do Azure](../azure-classic-rm.md).

## Antes de começar

Verifique se você tem os itens a seguir antes de iniciar a configuração.

- Um dispositivo VPN compatível e alguém que possa configurá-lo. Veja [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md).

- Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.

>[AZURE.IMPORTANT]Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
	
- Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [benefícios de assinante do MSDN](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para uma [avaliação gratuita](http://azure.microsoft.com/pricing/free-trial/).

- A versão mais recente dos cmdlets do Azure PowerShell. Você pode baixar e instalar a versão mais recente na seção Windows PowerShell da [página de download](http://azure.microsoft.com/downloads/). Este artigo foi escrito para o Azure PowerShell *0.9.8*.

>[AZURE.NOTE]Se você estiver executando aplicativos críticos, continue a usar o Azure PowerShell 0.9.8. Na maioria dos casos, a única diferença entre as duas versões é que o cmdlet de Visualização 1.0 segue o padrão {verbo}-AzureRm{substantivo}, enquanto o 0.9.8 nome não inclui Rm. Por exemplo, New-AzureRmResourceGroup em vez de New-AzureResourceGroup. Para obter informações sobre a Visualização do Azure PowerShell 1.0, consulte esta [postagem de blog](https://azure.microsoft.com/blog/azps-1-0-pre/). Para obter mais informações sobre os cmdlets da Visualização do Azure PowerShell 1.0, consulte [Cmdlets do Gerenciador de Recursos do Azure](https://msdn.microsoft.com/library/mt125356.aspx).


## 1\. Conecte-se as suas assinaturas 


Abra o console do PowerShell e conecte-se à sua conta.

**Observação:** as instruções a seguir são baseadas na versão 0.9.8 dos cmdlets do Azure PowerShell.

Use o exemplo a seguir para ajudar com a conexão:

		Add-AzureAccount

Se você tem mais de uma assinatura, use *Select-AzureSubscription* para conectar-se à assinatura que deseja usar.

		Select-AzureSubscription "yoursubscription"

Em seguida, alterne para o modo Gerenciador de Recursos do Azure.
		
		Switch-AzureMode -Name AzureResourceManager

## 2\. Criar uma rede virtual e uma sub-rede de gateway

- Se você já tiver uma rede virtual com uma sub-rede de gateway, pule para **Etapa 3 - adicionar seu site local**. 
- Se você já tem uma rede virtual e deseja adicionar uma sub-rede de gateway à sua VNet, consulte [Adicionar uma sub-rede de gateway a uma VNet](#gatewaysubnet).

### Para criar uma rede virtual e uma sub-rede de gateway

Use o exemplo abaixo para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios.

Em primeiro lugar, crie um grupo de recursos:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.

O exemplo abaixo cria uma rede virtual denominada *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede de gateway a uma VNet (opcional)

Essa etapa será necessária somente se você precisar adicionar um gateway de sub-rede a uma VNet.

Se já tiver uma rede virtual e desejar adicionar uma sub-rede de gateway a ela, você poderá criar sua sub-rede de gateway usando o exemplo abaixo. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se você nomear algo, você criará uma sub-rede, mas não será considerado pelo Azure como uma sub-rede de gateway.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Agora, defina a configuração.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 3\. Adicionar o site local

Em uma rede virtual, o *site local* geralmente se refere ao seu local. Você dará a esse site um nome pelo qual o Azure pode fazer referência a ele.

Você também especificará o prefixo de espaço de endereço para o site local. O Azure usará o prefixo do endereço IP que você especifica para identificar qual tráfego enviar para o site local. Isso significa que você terá que especificar cada prefixo de endereço que deseja que seja associado ao site local. Você pode atualizar facilmente esses prefixos se sua rede local mudar.

Ao usar os exemplos do PowerShell, observe o seguinte:
	
- O *GatewayIPAddress* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
- O *AddressPrefix* é o espaço de endereço local.

Para adicionar um site local com um prefixo de endereço único:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um site local com vários prefixos de endereço:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### Para modificar os prefixos de endereço IP para seu site local

Às vezes, os prefixos de site local mudam. As etapas usadas para modificar os prefixos de endereço IP dependem de você ter criado ou não uma conexão de gateway de VPN. Consulte [Modificar os prefixos de endereço IP para um site local](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Solicitar um endereço IP público para o gateway

Em seguida, você solicitará um endereço IP público a ser alocado para seu gateway de VPN VNet do Azure. Esse não é o mesmo endereço IP que é atribuído ao seu dispositivo VPN, em vez disso, ele é atribuído ao próprio gateway de VPN do Azure. Não é possível especificar o endereço IP que deseja usar; ele é alocado dinamicamente para seu gateway. Você usará esse endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

Use o exemplo do PowerShell abaixo. O Método de Alocação para esse endereço deve ser Dinâmico.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Criar a configuração de endereçamento IP do gateway

A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Criar o gateway

Nesta etapa, você criará o gateway de rede virtual. Observe que criar um gateway é uma operação que leva algum tempo para ser concluída.

Use os seguintes valores:

- O Tipo de Gateway é *Vpn*.
- O VpnType pode ser RouteBased* (referenciado como um Gateway Dinâmico em algumas documentações) ou *Policy Based* (referenciado como um Gateway Estático em algumas documentações). Para obter mais informações sobre tipos de gateway de VPN, consulte [Sobre Gateways de VPN](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Configurar o dispositivo de VPN

Neste ponto, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Fale com o fabricante do dispositivo para obter informações específicas de configuração. Além disso, consulte [Dispositivos VPN](http://go.microsoft.com/fwlink/p/?linkid=615099) para obter mais informações.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Criar a conexão VPN

Em seguida, você criará a conexão VPN site a site entre o gateway de rede virtual e o dispositivo VPN. Certifique-se de substituir os valores pelos seus próprios. A chave compartilhada deve corresponder ao valor usado para a configuração do dispositivo VPN.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após um instante, a conexão será estabelecida.

## 9\. Verificar uma conexão VPN

Neste momento, as conexões VPN site a site criadas com o Gerenciador de Recursos não podem ser vistas no Portal de Visualização. No entanto, é possível verificar se a conexão foi bem-sucedida usando *Get-AzureVirtualNetworkGatewayConnection –Debug*. No futuro, teremos um cmdlet para isso, bem como a capacidade de exibir sua conexão no Portal de Visualização.

Você pode usar o seguinte exemplo de cmdlet, configurando os valores para que correspondam aos seus próprios. Quando solicitado, selecione *A* para executar Todos.

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Após o cmdlet ter sido concluído, role pelos valores para exibi-los. No exemplo abaixo, o status da conexão é exibido como *Conectado* e você pode ver os bytes em ingresso e egresso.

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


## Para modificar os prefixos de endereço IP para um site local

Se você precisar alterar os prefixos para seu site local, use as instruções a seguir. Dois conjuntos de instruções são fornecidos e dependem de você já ter criado ou não a conexão de gateway de VPN.

### Adicionar ou remover prefixos sem uma conexão de gateway de VPN


- **Para adicionar** prefixos de endereço adicionais a um site local que você criou, mas que ainda não tem uma conexão de gateway de VPN, use o exemplo a seguir.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- **Para remover** um prefixo de endereço de um site local que não tem uma conexão VPN, use o exemplo abaixo. Exclua os prefixos de que você não precisa mais. Neste exemplo, não é mais necessário prefixar 20.0.0.0/24 (do exemplo anterior), portanto, iremos atualizar o site local e excluir o prefixo.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Adicionar ou remover prefixos com uma conexão de gateway de VPN

Se você criou sua conexão VPN e deseja adicionar ou remover os prefixos de endereço IP contidos no site local, você precisará fazer as seguintes etapas, nessa ordem. Isso resultará em algum tempo de inatividade para a conexão VPN, pois você precisará remover e recompilar o gateway. No entanto, já que você solicitou um endereço IP para a conexão, você não precisará reconfigurar seu roteador VPN local novamente, a menos que você decida alterar os valores usados anteriormente.

 
1. Remova a conexão de gateway. 
2. Modifique os prefixos para seu site local. 
3. Crie uma nova conexão de gateway. 

Você pode usar o exemplo a seguir como uma diretriz.


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Próximas etapas

Adicione uma máquina virtual à rede virtual. [Criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=Oct15_HO3-->