<properties 
   pageTitle="Como criar rotas e habilitar o encaminhamento IP no Azure"
   description="Como criar rotas e habilitar o encaminhamento IP no Azure"
   services="virtual-networks"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" /> <tags 
   ms.service="virtual-networks"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/22/2015"
   ms.author="telmos" />

# Como criar rotas e habilitar o encaminhamento IP no Azure
Você pode usar dispositivos virtuais no Azure para lidar com o tráfego na rede virtual do Azure. No entanto, você precisa criar rotas que permitam que VMs e serviços de nuvem em sua rede virtual enviem pacotes ao dispositivo virtual, em vez do destino desejado para o pacote. Também é necessário habilitar o encaminhamento IP na VM do dispositivo virtual para que possa receber e encaminhar pacotes que não são endereçados à VM do dispositivo virtual real.

##Como gerenciar rotas
Você pode adicionar, remover e alterar rotas no Azure usando o PowerShell. Para criar uma rota, primeiro você deve criar uma tabela de rotas para hospedar a rota.

### Como criar uma tabela de rotas
Para criar uma tabela de rotas denominada *FrontEndSubnetRouteTable*, execute o seguinte comando do PowerShell:

	New-AzureRouteTable -Name FrontEndSubnetRouteTable `
	-Location usnorth `
	-Label "Route table for frontend subnet"

### Como adicionar uma rota a uma tabela de rotas
Para adicionar uma rota que define *10.1.1.10* como o próximo salto para a sub-rede *10.2.0.0/16* na tabela de rotas criada acima, execute o seguinte comando do PowerShell:

	Set-AzureRoute -RouteTableName FrontEndSubnetRouteTable `
	-RouteName FirewallRoute -AddressPrefix 10.2.0.0/16 `
	-NextHopType VirtualAppliance `
	-NextHopIpAddress 10.1.1.10

### Como associar uma rota a uma sub-rede
Uma tabela de rotas deve ser associada a uma ou mais sub-redes para que seja usada. Para associar a tabela de rotas *FrontEndSubnetRouteTable* a uma sub-rede denominada *FrontEndSubnet* na rede virtual *ProductionVnet*, execute o seguinte comando do PowerShell:

	Set-AzureSubnetRouteTable -VNetName ProductionVnet `
	-SubnetName FrontEndSubnet `
	-RouteTableName FrontEndSubnetRouteTable

### Como ver as rotas aplicadas em uma VM
Você pode consultar o Azure para ver as rotas reais aplicadas a uma instância de função ou VM específica. As rotas mostradas incluem rotas padrão que o Azure fornece, bem como rotas anunciadas por um Gateway de VPN. O limite de rotas mostrado é 800.

Para ver as rotas associadas a NIC primária em uma VM denominada *FirewallAppliance1*, execute o seguinte comando do PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable

Para ver as rotas associadas a uma NIC secundária denominada *backendnic* em uma VM denominada *FirewallAppliance1*, execute o seguinte comando do PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureEffectiveRouteTable -NetworkInterfaceName backendnic

Para ver as rotas associadas à NIC primária em uma instância de função denominada *myRole* que faz parte de um serviço de nuvem chamado *myservice*, execute o seguinte comando do PowerShell:

	Get-AzureEffectiveRouteTable -ServiceName myservice `
	-RoleInstanceName myRole

## Como gerenciar o encaminhamento IP
Conforme mencionado anteriormente, você precisa habilitar o encaminhamento IP em qualquer VM ou instância de função que atuará como um dispositivo virtual.

### Como habilitar o encaminhamento IP
Para habilitar o encaminhamento IP em uma VM denominada *FirewallAppliance1*, execute o seguinte comando do PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Enable

Para habilitar o encaminhamento IP em uma instância de função denominada *FirewallAppliance1* em um serviço de nuvem denominado *myService*, execute o seguinte comando do PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Enable

### Como desabilitar o encaminhamento IP
Para desabilitar o encaminhamento IP em uma VM denominada *FirewallAppliance1*, execute o seguinte comando do PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Set-AzureIPForwarding -Disable

Para desabilitar o encaminhamento IP em uma instância de função denominada *FirewallAppliance1* em um serviço de nuvem denominado *myService*, execute o seguinte comando do PowerShell:

	Set-AzureIPForwarding -ServiceName myService `
	-RoleName FirewallAppliance1 -Disable

### Como exibir o status do encaminhamento IP
Para exibir o status do encaminhamento IP em uma VM denominada *FirewallAppliance1*, execute o seguinte comando do PowerShell:

	Get-AzureVM -Name FirewallAppliance1 `
	| Get-AzureIPForwarding

## Consulte também

[Visão geral sobre encaminhamento IP e rotas definidas pelo usuário](../virtual-networks-udr-overview)

[ILIP \(IP público em nível de instância\)](../virtual-networks-instance-level-public-ip)

[Visão geral da Rede Virtual](https://msdn.microsoft.com/library/azure/jj156007.aspx) <!--HONumber=52-->
