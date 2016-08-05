<properties 
   pageTitle="Controlar o roteamento e usar dispositivos virtuais usando o PowerShell no modelo de implantação clássico | Microsoft Azure"
   description="Aprenda a controlar o roteamento em VNets usando o PowerShell no modelo de implantação clássica"
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="telmos" />

#Controlar o roteamento e usar dispositivos virtuais (clássico) usando o PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo aborda o modelo de implantação clássico.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

O exemplo de comando do Azure PowerShell abaixo espera um ambiente simples já criado com base no cenário acima. Se você quiser executar os comandos da forma como eles aparecem neste documento, crie o ambiente descrito em [criar uma VNet (clássica) usando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Criar o UDR para a sub-rede de front-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de front-end com base no cenário acima, siga as etapas abaixo.

3. Execute o cmdlet **`New-AzureRouteTable`** para criar uma tabela de rota para a sub-rede de front-end.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	Saída:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Execute o cmdlet **`Set-AzureRoute`** para criar uma rota na tabela de rotas criada acima para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para a VM **FW1** (192.168.0.4).
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	Saída:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Execute o cmdlet **`Set-AzureSubnetRouteTable`** para associar a tabela de rotas criada acima à sub-rede de **FrontEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## Criar o UDR para a sub-rede de back-end
Para criar a tabela de rotas e a rota necessária para a sub-rede de back-end com base no cenário acima, siga as etapas abaixo.

3. Execute o cmdlet **`New-AzureRouteTable`** para criar uma tabela de rota para a sub-rede de back-end.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. Execute o cmdlet **`Set-AzureRoute`** para criar uma rota na tabela de rotas criada acima para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para a VM **FW1** (192.168.0.4).

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. Execute o cmdlet **`Set-AzureSubnetRouteTable`** para associar a tabela de rotas criada acima à sub-rede de **BackEnd**.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName BackEnd `
			-RouteTableName UDR-BackEnd

## Habilitar o encaminhamento IP na VM FW1
Para habilitar o encaminhamento de IP na VM FW1, siga as etapas abaixo.

1. Execute o cmdlet **`Get-AzureIPForwarding`** para verificar o status de encaminhamento de IP

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Saída:

		Disabled

2. Execute o comando **`Set-AzureIPForwarding`** para habilitar o encaminhamento de IP para a VM *FW1*.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!----HONumber=AcomDC_0720_2016-->