<properties
   pageTitle="Criar Emparelhamento VNet usando cmdlets do Powershell | Microsoft Azure"
   description="Saiba como criar uma rede virtual usando o Portal do Azure no Resource Manager."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/02/2016"
   ms.author="narayanannamalai"/>

# Criar Emparelhamento VNet usando cmdlets do Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um emparelhamento VNet usando o PowerShell, execute estas etapas:

1. Se você nunca usou o Azure PowerShell, consulte [Como Instalar e Configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções até o fim para entrar no Azure e selecionar sua assinatura.

        Note: PowerShell cmdlet for managing VNet peering is shipped with [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Ler objetos de rede virtual:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2
	
3. Para estabelecer o emparelhamento VNet, você precisa criar dois links, um para cada direção. A etapa a seguir criará um link de emparelhamento VNet da VNet1 para a VNet2 primeiro:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.id 

        Output shows:

        Name			: LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

4. Esta etapa criará um link de emparelhamento VNet da VNet2 para a VNet1:

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.id 

        Output shows:

        Name			: LinkToVNet1
        Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet2
        ProvisioningState		: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic	: False
        AllowGatewayTransit	: False
        UseRemoteGateways	: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

5. Depois de criar o link de emparelhamento VNet, você pode ver o estado do link abaixo:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

        Output shows:

		Name			: LinkToVNet2
		Id				: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
		Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ResourceGroupName	: vnet101
		VirtualNetworkName	: vnet1
		ProvisioningState		: Succeeded
		RemoteVirtualNetwork	: {
		                                     "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
		                                }
		AllowVirtualNetworkAccess	: True
		AllowForwardedTraffic            : False
		AllowGatewayTransit              : False
		UseRemoteGateways                : False
		RemoteGateways                   : null
		RemoteVirtualNetworkAddressSpace : null

	Há algumas propriedades configuráveis para cada emparelhamento VNet:

	|Opção|Descrição|Padrão|
	|:-----|:----------|:------|
	|AllowVirtualNetworkAccess|Se o espaço de endereço da VNet emparelhada deve ser incluído como parte da marca Virtual\_network|Sim|
	|AllowForwardedTraffic|Permite que o tráfego proveniente da VNet emparelhada seja aceito ou descartado|Não|
	|AllowGatewayTransit|Permite que a VNet emparelhada use seu gateway de VNet|Não|
	|UseRemoteGateways|Use o gateway de sua VNet emparelhada. A VNet emparelhada deve ter um gateway configurado e AllowGatewayTransit deve estar selecionado. Você não poderá usar essa opção se tiver um gateway configurado|Não|

	Cada link no emparelhamento VNet tem o conjunto de propriedades acima. Por exemplo, você pode definir AllowVirtualNetworkAccess para True para o link de emparelhamento VNet da VNet1 para a VNet2, e defini-lo para False para o link de emparelhamento VNet na outra direção.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

        You can run Get-AzureRmVirtualNetworkPeering to double check the property value after the change.  From the output, you can see AllowForwardedTraffic changes set to True after running the above cmdlets. 

        Name			: LinkToVNet2
        Id			: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag			: W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName	: vnet101
        VirtualNetworkName	: vnet1
        ProvisioningState	: Succeeded
        RemoteVirtualNetwork	: {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess	: True
        AllowForwardedTraffic		: True
        AllowGatewayTransit		: False
        UseRemoteGateways		: False
        RemoteGateways		: null
        RemoteVirtualNetworkAddressSpace : null

	Após o estabelecimento do emparelhamento neste cenário, você poderá iniciar conexões de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets. Por padrão, o AllowVirtualNetworkAccess é True e o emparelhamento VNet provisionará as devidas ACLs para permitir a comunicação entre as VNets. Mas você ainda poderá aplicar as regras do grupo de segurança (NSG) para bloquear a conectividade entre sub-redes ou máquinas virtuais específicas para ter um controle mais preciso do acesso entre duas redes virtuais. Para obter mais informações sobre como criar as regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar um emparelhamento VNet entre as assinaturas usando o PowerShell, siga as etapas abaixo:

1. Entre no Azure com a conta do Usuário A com privilégios para a Assinatura A e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet5

        This is not a requirement, peering can be established even if users individually raise peering requests for thier respective Vnets as long as the requests match. Adding a privileged user of the other VNet as a user in the local VNet makes it easier to do the setup. 

2. Entre no Azure com a conta do Usuário B com privilégios para a Assinatura B e execute o seguinte cmdlet:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetwork/VNet3

3. Na sessão de logon do Usuário A, execute o cmdlet abaixo:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Na sessão de logon do Usuário B, execute o cmdlet abaixo:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Após o emparelhamento ser estabelecido, qualquer máquina virtual na VNet3 deverá conseguir comunicar-se com qualquer máquina virtual na VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Nesse cenário, você pode executar os cmdlets do PowerShell abaixo para estabelecer o emparelhamento VNet. Você precisa definir a propriedade AllowForwardedTraffic para True e vincular a VNET1 a HubVnet, que permite o tráfego de entrada de fora do espaço do endereço VNet de emparelhamento.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.id

2. Após estabelecer o emparelhamento, consulte este [artigo](virtual-network-create-udr-arm-ps.md) e defina a UDR (rota definida pelo usuário) para redirecionar o tráfego da VNet1 por meio de um dispositivo virtual para usar seus recursos. Quando você especificar o próximo endereço de salto na rota, poderá defini-lo para o endereço IP do dispositivo virtual no emparelhamento entre a VNet e a HubVNet. Veja abaixo um exemplo:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

## Remover emparelhamento VNet

1.	Para remover o emparelhamento VNet, você precisa executar o seguinte cmdlet:

        Remove-AzureRmVirtualNetworkPeering  
    
        remove both links, as shown below:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2 

2. Assim que você remover um link em um emparelhamento VNet, o estado do link de emparelhamento será desconectado. Nesse estado, não é possível recriar o link até que o estado do link de emparelhamento mude para Iniciado. Recomendamos remover os dois links antes de recriar o emparelhamento VNet.

<!---HONumber=AcomDC_0824_2016-->