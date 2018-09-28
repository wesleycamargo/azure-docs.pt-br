---
title: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o Azure PowerShell
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o portal do Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 919051a945d423a104b286e9c5703c5b749cf026
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46946452"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o Azure PowerShell


Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Configurar o ambiente de rede
> * Configurar e implantar o firewall
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar o firewall

Para este tutorial, você deve criar três VNets:
- **VNet-Hub**: o firewall está nessa VNet.
- **VNet-Spoke**: a VNet spoke representa a carga de trabalho localizada no Azure.
- **VNet-Onprem**: a VNet OnPrem representa uma rede local. Em uma implantação real, ela pode ser conectada por VPN ou conexão ExpressRoute. Para simplificar, este tutorial usa uma conexão de gateway de VPN e uma VNet localizada no Azure é usada para representar uma rede local.

![Firewall em uma rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

## <a name="key-requirements"></a>Requisitos de chave

Há três requisitos principais para que este cenário funcione corretamente:

- Uma Rota Definida pelo Usuário na sub-rede spoke que aponta para o endereço IP do Firewall do Azure como o gateway padrão. A propagação de rotas BGP deve estar **Desabilitada** nessa tabela de rotas.
- Uma Rota Definida pelo Usuário na sub-rede do gateway do hub deve apontar para o endereço IP do firewall como o próximo salto para as redes spoke.
- Nenhuma Rota Definida pelo Usuário é necessária na sub-rede do Firewall do Azure, já que ela aprende as rotas a partir do BGP.
- Verifique se você definiu **AllowGatewayTransit** ao emparelhar a VNet-Hub com a VNet-Spoke e **UseRemoteGateways** ao emparelhar a VNet-Spoke com a VNet-Hub.

Consulte a seção [Criar Rotas](#create-routes) deste tutorial para ver como essas rotas são criadas.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

## <a name="declare-the-variables"></a>Declarar as variáveis
O exemplo a seguir declara as variáveis usando os valores deste tutorial. Na maioria dos casos, substitua os valores pelos seus próprios. No entanto, se você estiver executando as etapas para se familiarizar com esse tipo de configuração, poderá usar essas variáveis. Modifique as variáveis, se for necessário, e depois copie e cole em seu console do PowerShell.

```azurepowershell
$RG1 = "FW-Hybrid-Test"
$Location1 = "East US"

# Variables for the firewall hub VNet

$VNetnameHub = "VNet-hub"
$SNnameHub = "AzureFirewallSubnet"
$VNetHubPrefix = "10.5.0.0/16"
$SNHubPrefix = "10.5.0.0/24"
$SNGWHubPrefix = "10.5.1.0/24"
$GWHubName = "GW-hub"
$GWHubpipName = "VNet-hub-GW-pip"
$GWIPconfNameHub = "GW-ipconf-hub"
$ConnectionNameHub = "hub-to-Onprem"

# Variables for the spoke VNet

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the OnPrem VNet

$VNetnameOnprem = "Vnet-Onprem"
$SNNameOnprem = "SN-Corp"
$VNetOnpremPrefix = "192.168.0.0/16"
$SNOnpremPrefix = "192.168.1.0/24"
$SNGWOnpremPrefix = "192.168.2.0/24"
$GWOnpremName = "GW-Onprem"
$GWIPconfNameOnprem = "GW-ipconf-Onprem"
$ConnectionNameOnprem = "Onprem-to-hub"
$GWOnprempipName = "VNet-Onprem-GW-pip"

$SNnameGW = "GatewaySubnet"
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos
Crie um grupo de recursos para conter todos os recursos necessários para este tutorial:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

## <a name="create-and-configure-the-firewall-hub-vnet"></a>Criar e configurar a Vnet do hub de firewall

Defina as sub-redes a serem incluídas na VNet:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Agora, crie a VNet do hub de firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```
Solicite um endereço IP público a ser alocado para o gateway de VPN que você criará para sua VNet. Observe que *AllocationMethod* é **Dinâmico**. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente para o gateway de VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```
## <a name="create-and-configure-the-spoke-vnet"></a>Criar e configurar a VNet spoke

Defina as sub-redes a serem incluídas na VNet spoke:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Crie a VNet spoke:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implantar o firewall

Agora, implante o firewall na VNet do hub.

```azurepowershell
# Get a Public IP for the firewall
$FWpip = New-AzureRmPublicIpAddress -Name "fw-pip" -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Static -Sku Standard
# Create the firewall
$Azfw = New-AzureRmFirewall -Name AzFW01 -ResourceGroupName $RG1 -Location $Location1 -VirtualNetworkName $VNetnameHub -PublicIpName fw-pip

#Save the firewall private IP address for future use

$AzfwPrivateIP = $Azfw.IpConfigurations.privateipaddress
$AzfwPrivateIP

```

### <a name="configure-network-rules"></a>Configurar regras de rede

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80
$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *
$Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2,$Rule3 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```
### <a name="configure-an-application-rule"></a>Configurar uma regra de aplicativo

```azurepowershell
$Rule4 = New-AzureRmFirewallApplicationRule -Name "AllowBing" -Protocol "Http:80","Https:443" `
   -SourceAddress $SNOnpremPrefix -TargetFqdn "bing.com"

$AppRuleCollection = New-AzureRmFirewallApplicationRuleCollection -Name RCApp01 -Priority 100 `
   -Rule $Rule4 -ActionType "Allow"
$Azfw.ApplicationRuleCollections = $AppRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw

```

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e conectar os gateways de VPN

O hub e as VNets Locais são conectados por meio de gateways de VPN.

### <a name="create-a-vpn-gateway-for-the-hub-vnet"></a>Criar um gateway de VPN para a VNet do hub

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a usar.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Agora crie o gateway de VPN para a VNet do hub. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-onprem-vnet"></a>Criar um gateway de VPN para VNet Local

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a usar.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Agora crie o gateway de VPN para a VNet Local. As configurações de rede virtual com rede virtual exigem um VpnType RouteBased. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```
### <a name="create-the-vpn-connections"></a>Criar conexões de VPN
Agora você pode criar conexões de VPN entre o hub e os gateways Locais

#### <a name="get-the-vpn-gateways"></a>Obter gateways de VPN
```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Criar as conexões

Nesta etapa, você criará a conexão da VNet do hub com a VNet Local. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Crie a conexão Local com a VNet do hub. Esta etapa é semelhante à anterior, exceto que você cria a conexão da VNet Local com a VNet do hub. Verifique se que as chaves compartilhadas correspondem. Depois de alguns minutos, a conexão deverá ser estabelecida.

  ```azurepowershell
  New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameOnprem -ResourceGroupName $RG1 `
  -VirtualNetworkGateway1 $vnetOnpremgw -VirtualNetworkGateway2 $vnetHubgw -Location $Location1 `
  -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
  ```
#### <a name="verify-the-connection"></a>Verificar conexão
 
Você pode verificar se a conexão foi bem-sucedida usando o cmdlet *Get-AzureRmVirtualNetworkGatewayConnection*, com ou sem *-Debug*. Use o seguinte exemplo de cmdlet, configurando os valores para coincidirem com os seus. Se solicitado, selecione **A** para executar **Todos**. No exemplo, *-Name* refere-se ao nome da conexão que você deseja testar.

```azurepowershell
Get-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1
```

Após a conclusão do cmdlet, exiba os valores. No exemplo abaixo, o status da conexão é exibido como *Conectado* e é possível ver os bytes de entrada e saída.

```
"connectionStatus": "Connected",
"ingressBytesTransferred": 33509044,
"egressBytesTransferred": 4142431
```

## <a name="create-and-configure-the-onprem-vnet"></a>Criar e configurar a VNet Local

Defina as sub-redes a serem incluídas na VNet:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Agora, crie a VNet Local:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```
Solicite um endereço IP público para ser alocado para o gateway que você criará para a VNet. Observe que *AllocationMethod* é **Dinâmico**. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente ao gateway. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="peer-the-hub-and-spoke-vnets"></a>Emparelhar as VNets do hub e do spoke

Agora, emparelhe as VNets do hub e do spoke.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```
## <a name="create-routes"></a>Criar rotas

Em seguida, crie duas rotas: 
- Uma rota da sub-rede do gateway do hub até a sub-rede do spoke pelo endereço IP de firewall
- Uma rota padrão da sub-rede do spoke pelo endereço IP de firewall

```azurepowershell
#Create a route table
$routeTableHubSpoke = New-AzureRmRouteTable `
  -Name 'UDR-Hub-Spoke' `
  -ResourceGroupName $RG1 `
  -location $Location1

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-Hub-Spoke `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix $VNetSpokePrefix `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetHub `
  -Name $SNnameGW `
  -AddressPrefix $SNGWHubPrefix `
  -RouteTable $routeTableHubSpoke | `
Set-AzureRmVirtualNetwork

#Now create the default route

#Create a table, with BGP route propagation disabled
$routeTableSpokeDG = New-AzureRmRouteTable `
  -Name 'UDR-DG' `
  -ResourceGroupName $RG1 `
  -location $Location1 `
  -DisableBgpRoutePropagation

#Create a route
Get-AzureRmRouteTable `
  -ResourceGroupName $RG1 `
  -Name UDR-DG `
  | Add-AzureRmRouteConfig `
  -Name "ToSpoke" `
  -AddressPrefix 0.0.0.0/0 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress $AzfwPrivateIP `
 | Set-AzureRmRouteTable

#Associate the route table to the subnet

Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $VNetSpoke `
  -Name $SNnameSpoke `
  -AddressPrefix $SNSpokePrefix `
  -RouteTable $routeTableSpokeDG | `
Set-AzureRmVirtualNetwork
```
## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Agora crie a carga de trabalho do spoke e as máquinas virtuais Locais, e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho
Crie uma máquina virtual na VNet do spoke executando o IIS, sem endereço IP público, e permitindo pings.
Quando solicitado, digite um nome de usuário e senha da máquina virtual.

```azurepowershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name Allow-RDP  -Protocol Tcp `
  -Direction Inbound -Priority 200 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 3389 -Access Allow
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name Allow-web  -Protocol Tcp `
  -Direction Inbound -Priority 202 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix $SNSpokePrefix -DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $RG1 -Location $Location1 -Name NSG-Spoke02 -SecurityRules $nsgRuleRDP,$nsgRuleWeb

#Create the NIC
$NIC = New-AzureRmNetworkInterface -Name spoke-01 -ResourceGroupName $RG1 -Location $Location1 -SubnetId $VnetSpoke.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id

#Define the virtual machine
$VirtualMachine = New-AzureRmVMConfig -VMName VM-Spoke-01 -VMSize "Standard_DS2"
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName Spoke-01 -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzureRmVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2016-Datacenter' -Version latest

#Create the virtual machine
New-AzureRmVM -ResourceGroupName $RG1 -Location $Location1 -VM $VirtualMachine -Verbose

#Install IIS on the VM
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
    -Location $Location1

#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1
```

### <a name="create-the-onprem-virtual-machine"></a>Criar a máquina virtual Local
É uma máquina virtual simples que você pode conectar usando a Área de Trabalho Remota para o endereço IP público. A partir daí, você pode conectar o servidor Local por meio do firewall. Quando solicitado, digite um nome de usuário e senha da máquina virtual.
```azurepowershell
New-AzureRmVm `
    -ResourceGroupName $RG1 `
    -Name "VM-Onprem" `
    -Location $Location1 `
    -VirtualNetworkName $VNetnameOnprem `
    -SubnetName $SNNameOnprem `
    -OpenPorts 3389 `
    -Size "Standard_DS2"
```

## <a name="test-the-firewall"></a>Testar o firewall
Primeiro, obtenha e anote o endereço IP privado da máquina virtual **VM-spoke-01**.

```azurepowershell
$NIC.IpConfigurations.privateipaddress
```

1. No portal do Azure, conecte a máquina virtual **VM-Onprem**.
2. Abra um prompt de comando do Windows PowerShell na **VM-Onprem** e execute ping no IP privado para **VM-spoke-01**.

   Você deve obter uma resposta.
1. Abra um navegador da Web em **VM-Onprem** e navegue até o IP privado http://\<VM-spoke-01\>

   Você deverá ver a página padrão dos Serviços de Informações da Internet.

3. Na **VM-Onprem**, abra uma área de trabalho remota para **VM-spoke-01** no endereço IP privado.

   Sua conexão deve ter êxito e você deve conseguir entrar usando o nome de usuário e a senha escolhidos.

Agora que você verificou se as regras de firewall estão funcionando:

- Você pode executar ping no servidor na VNet do spoke.
- Pode procurar o servidor Web na VNet do spoke.
- Pode conectar o servidor na VNet do spoke usando o RDP.

Em seguida, altere a ação de coleção de regras da rede do firewall para **Deny** a fim de verificar se as regras do firewall funciona como o esperado. Execute o script a seguir para alterar a ação de coleção de regras para **Deny**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

$rcApp = $azfw.GetApplicationRuleCollectionByName("RCApp01")
$rcApp.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```
Agora execute os testes novamente. Todos devem falhar nesse momento. Feche as áreas de trabalho remotas existentes antes de testar a alteração das regras.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou se não forem mais necessários, exclua o grupo de recursos **FW-Hybrid-Test** para excluir todos os recursos relacionados ao firewall.


## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Configurar o ambiente de rede
> * Configurar e implantar o firewall
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar o firewall

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
