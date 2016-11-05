---
title: Criar uma rede virtual com uma conexão VPN Site a Site usando o PowerShell e o Azure Resource Manager | Microsoft Docs
description: Este artigo orienta você durante a criação de uma Rede Virtual usando o modelo de implantação do Resource Manager e o conectando à sua rede local usando uma conexão de gateway de VPN Site a Site.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: ''
tags: azure-resource-manager

ms.service: vpn-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/31/2016
ms.author: cherylmc

---
# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Criar uma rede virtual com uma conexão site a site usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portal do Azure](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Resource Manager - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Clássico - Portal Clássico](vpn-gateway-site-to-site-create.md)
> 
> 

Este artigo mostra como criar uma rede virtual e uma conexão VPN Site a Site para sua rede local usando o **modelo de implantação do Azure Resource Manager**. As conexões Site a Site podem ser usadas para configurações híbridas e entre instalações.

![Diagrama Site a Site](./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site-to-site") 

### <a name="deployment-models-and-tools-for-site-to-site-connections"></a>Modelos de implantação e ferramentas para conexões Site a Site
[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

Se você quiser conectar Redes Virtuais, mas não estiver criando uma conexão com uma instalação local, confira [Configurar uma conexão de Rede Virtual para Rede Virtual](vpn-gateway-vnet-vnet-rm-ps.md).

## <a name="before-you-begin"></a>Antes de começar
Verifique se você tem os itens a seguir antes de iniciar a configuração.

* Um dispositivo VPN compatível e alguém que possa configurá-lo. Confira [Sobre dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se você não estiver familiarizado com a configuração de seu dispositivo VPN ou se não estiver familiarizado com os intervalos de endereços IP localizados em sua configuração de rede local, será necessário coordenar com alguém que possa fornecer os detalhes para você.
* Um endereço IP público voltado para o exterior para seu dispositivo VPN. Esse endereço IP não pode estar localizado atrás de um NAT.
* Uma assinatura do Azure. Se ainda não tiver uma assinatura do Azure, você poderá ativar os [Benefícios do assinante do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inscrever-se para obter uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
* A versão mais recente dos cmdlets do PowerShell do Azure Resource Manager. Confira [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar os cmdlets do PowerShell.

## <a name="1.-connect-to-your-subscription"></a>1. Conecte-se as suas assinaturas
Alterne para o modo do PowerShell para usar os cmdlets do Gerenciador de Recursos. Para obter mais informações, consulte [Usando o Windows PowerShell com o Gerenciador de Recursos](../powershell-azure-resource-manager.md).

Abra o console do PowerShell e conecte-se à sua conta. Use o exemplo a seguir para ajudar com a conexão:

    Login-AzureRmAccount

Verificar as assinaturas da conta.

    Get-AzureRmSubscription 

Especifique a assinatura que você quer usar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="2.-create-a-virtual-network-and-a-gateway-subnet"></a>2. Criar uma rede virtual e uma sub-rede de gateway
Os exemplos usam uma sub-rede de gateway de /28. Embora seja possível criar uma sub-rede de gateway tão pequena quanto /29, recomendamos que você crie uma sub-rede maior que inclua mais endereços selecionando pelo menos /28 ou /27. Isso permitirá endereços suficientes a fim de acomodar as possíveis configurações adicionais que você possa querer no futuro.

Se você já tiver uma rede virtual com uma sub-rede do gateway que seja /29 ou maior, poderá pular para [Adicionar seu gateway da rede local](#localnet).

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para criar uma rede virtual e uma sub-rede de gateway
Use o exemplo a seguir para criar uma rede virtual e uma sub-rede de gateway. Substitua os valores pelos seus próprios. 

Em primeiro lugar, crie um grupo de recursos:

    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, crie sua rede virtual. Verifique se os espaços de endereço especificados não se sobrepõem a qualquer espaço de endereço em sua rede local.

O exemplo a seguir cria uma rede virtual denominada *testvnet* e duas sub-redes, uma chamada *GatewaySubnet* e outra chamada *Subnet1*. É importante criar uma sub-rede especificamente chamada de *GatewaySubnet*. Se você usar outro nome, a configuração de conexão falhará. 

Defina as variáveis.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Crie a Rede Virtual.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="<a-name="gatewaysubnet"></a>to-add-a-gateway-subnet-to-a-virtual-network-you-have-already-created"></a><a name="gatewaysubnet"></a>Para adicionar uma sub-rede do gateway a uma rede virtual já criada
Esta etapa só será necessária se você precisar adicionar um gateway de sub-rede a uma Rede Virtual criada anteriormente.

Você pode criar sua sub-rede de gateway usando o exemplo a seguir. Certifique-se de nomear a sub-rede de gateway como 'GatewaySubnet'. Se usar outro nome, você criará uma sub-rede, mas o Azure não a tratará como uma sub-rede de gateway.

Defina as variáveis.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Crie a sub-rede de gateway.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Defina a configuração. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## <a name="3.-<a-name="localnet"></a>add-your-local-network-gateway"></a>3. <a name="localnet"></a>Adicionar seu gateway da rede local
Em uma rede virtual, o gateway de rede local geralmente se refere ao seu local. Você dá ao site um nome pelo qual Azure pode fazer referência a ele e também especifica o prefixo de espaço de endereço para o gateway de rede local. 

O Azure usa o prefixo de endereço IP que você especifica para identificar qual tráfego enviar a seu local. Isso significa que você tem que especificar cada prefixo de endereço que deseja associar ao gateway de rede local. Você pode atualizar facilmente esses prefixos se sua rede local mudar. 

Ao usar os exemplos do PowerShell, observe o seguinte:

* O *GatewayIPAddress* é o endereço IP do dispositivo VPN local. O dispositivo VPN não pode estar localizado atrás de um NAT. 
* O *AddressPrefix* é o espaço de endereço local.

Para adicionar um gateway de rede local com um único prefixo de endereço:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereço:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Para modificar os prefixos de endereço IP do gateway de rede local
Às vezes, os prefixos de gateway de rede local são alterados. As etapas usadas para modificar os prefixos de endereço IP dependem de você ter criado uma conexão de gateway de VPN. Consulte a seção [Modificar os prefixos do endereço IP para um gateway da rede local](#modify) deste artigo.

## <a name="4.-request-a-public-ip-address-for-the-vpn-gateway"></a>4. Solicitar um endereço IP público para o gateway de VPN
Em seguida, você solicitará um endereço IP público a ser alocado para seu gateway de VPN VNet do Azure. Esse não é o mesmo endereço IP que é atribuído ao dispositivo VPN; em vez disso, ele é atribuído ao próprio gateway de VPN do Azure. Você não pode especificar o endereço IP que deseja usar. Ele é alocado ao gateway dinamicamente. Você usa esse endereço IP ao configurar seu dispositivo VPN local para se conectar ao gateway.

O gateway de VPN do Azure do modelo de implantação do Gerenciador de Recursos atualmente dá suporte apenas a endereços IP públicos usando o método de Alocação Dinâmica. No entanto, isso não significa que o endereço IP será alterado. A única vez em que o endereço IP de gateway de VPN do Azure é alterado é quando o gateway é excluído e recriado. O endereço IP público do gateway não será alterado durante o redimensionamento, a redefinição ou outros tipos de manutenção interna/atualização do gateway de VPN do Azure.

Use o seguinte comando do PowerShell:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="5.-create-the-gateway-ip-addressing-configuration"></a>5. Criar a configuração de endereçamento IP do gateway
A configuração do gateway define a sub-rede e o endereço IP público a serem usados. Use o exemplo a seguir para criar a configuração do gateway.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="6.-create-the-virtual-network-gateway"></a>6. Criar o gateway de rede virtual
Nesta etapa, você cria o gateway de rede virtual. Criação de um gateway pode demorar muito para ser concluída. Geralmente, 45 minutos ou mais. 

Use os seguintes valores:

* O *-GatewayType* para uma configuração Site a Site é *Vpn*. O tipo de gateway é sempre específico para a configuração que você está implementando. Por exemplo, outras configurações de gateway podem exigir -GatewayType ExpressRoute. 
* O *-VpnType* pode ser *RouteBased* (referido como Gateway Dinâmico em alguns documentos) ou *PolicyBased* (referido como Gateway Estático em alguns documentos). Para saber mais sobre os tipos de gateway de VPN, veja [Sobre gateways de VPN](vpn-gateway-about-vpngateways.md#vpntype).
* O *-GatewaySku* pode ser *Básico*, *Standard* ou *HighPerformance*.   
  
        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="7.-configure-your-vpn-device"></a>7. Configurar o dispositivo de VPN
Neste ponto, você precisará do endereço IP público do gateway de rede virtual para configurar seu dispositivo VPN local. Fale com o fabricante do dispositivo para obter informações específicas de configuração. Confira [Dispositivos VPN](vpn-gateway-about-vpn-devices.md) para saber mais.

Para localizar o endereço IP público do seu gateway de rede virtual, use o exemplo a seguir:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="8.-create-the-vpn-connection"></a>8. Criar a conexão VPN
Em seguida, crie a conexão VPN Site a Site entre o gateway de rede virtual e o dispositivo VPN. Substitua os valores pelos seus próprios. A chave compartilhada deve corresponder ao valor usado para a configuração do dispositivo VPN. Observe que o `-ConnectionType` para Site a Site é *IPsec*. 

Defina as variáveis.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Crie a conexão.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Após um instante, a conexão será estabelecida. 

## <a name="<a-name="toverify"></a>to-verify-a-vpn-connection"></a><a name="toverify"></a>Para verificar uma conexão VPN
Existem algumas maneiras diferentes de verificar a conexão VPN.

[!INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="<a-name="modify"></a>to-modify-ip-address-prefixes-for-a-local-network-gateway"></a><a name="modify"></a>Para modificar os prefixos do endereço IP para um gateway de rede local
Se precisar alterar os prefixos do gateway de rede local, use as instruções a seguir. São fornecidos dois conjuntos de instruções. As instruções escolhidas dependem de você já ter criado sua conexão de gateway. 

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="<a-name="modifygwipaddress"></a>to-modify-the-gateway-ip-address-for-a-local-network-gateway"></a><a name="modifygwipaddress"></a>Para modificar o endereço IP do gateway para um gateway de rede local
[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximas etapas
* Você pode adicionar máquinas virtuais às suas redes virtuais. Veja [Criar uma máquina virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter as etapas.
* Para obter informações sobre o BGP, consulte a [Visão Geral do BGP](vpn-gateway-bgp-overview.md) e [Como configurar o BGP](vpn-gateway-bgp-resource-manager-ps.md).

<!--HONumber=Oct16_HO2-->


