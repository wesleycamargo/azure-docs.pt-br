---
title: 'Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o Azure PowerShell'
description: Neste tutorial, você aprenderá a implantar e configurar o Firewall do Azure usando o Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 1/30/2019
ms.author: victorh
customer intent: As an administrator, I want to control network access from an on-premises network to an Azure virtual network.
ms.openlocfilehash: 29af70988cf77b9fad47e5c2478e5c86529fe9cf
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55458203"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-in-a-hybrid-network-using-azure-powershell"></a>Tutorial: Implantar e configurar o Firewall do Azure em uma rede híbrida usando o Azure PowerShell

Ao conectar sua rede local a uma rede virtual do Azure para criar uma rede híbrida, a capacidade de controlar o acesso aos recursos da rede do Azure é uma parte importante de um plano geral de segurança.

É possível usar o Firewall do Azure para controlar o acesso de rede em uma rede híbrida usando as regras que definem tráfegos de rede permitidos e negados.

Para este tutorial, você deve criar três redes virtuais:

- **VNet-Hub**: o firewall está nessa rede virtual.
- **VNet-Spoke**: a rede virtual spoke representa a carga de trabalho localizada no Azure.
- **VNet-Onprem**: a rede virtual local representa uma rede local. Em uma implantação real, ela pode ser conectada por VPN ou conexão Rota. Para simplificar, este tutorial usa uma conexão de gateway de VPN, e uma rede virtual localizada no Azure é usada para representar uma rede local.

![Firewall em uma rede híbrida](media/tutorial-hybrid-ps/hybrid-network-firewall.png)

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Declarar as variáveis
> * Criar a rede virtual do hub de firewall
> * Criar a rede virtual spoke
> * Criar a rede virtual local
> * Configurar e implantar o firewall
> * Criar e conectar os gateways de VPN
> * Emparelhar as redes virtuais hub e spoke
> * Criar as rotas
> * Criar as máquinas virtuais
> * Testar o firewall

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial requer a execução do PowerShell localmente. Você precisa ter o módulo do Azure PowerShell versão 6.12.0 ou posterior instalado. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps). Depois de verificar a versão do PowerShell, execute `Login-AzureRmAccount` para criar uma conexão com o Azure.

Há três requisitos principais para que este cenário funcione corretamente:

- Uma UDR (Rota Definida pelo Usuário) na sub-rede spoke que aponta para o endereço IP do Firewall do Azure como o gateway padrão. A propagação de rotas BGP deve estar **Desabilitada** nessa tabela de rotas.
- Uma UDR na sub-rede do gateway do hub precisa apontar para o endereço IP do firewall como o próximo salto para as redes spoke.
- Nenhuma UDR é necessária na sub-rede do Firewall do Azure, já que ela aprende as rotas com o BGP.
- Verifique se você definiu **AllowGatewayTransit** ao emparelhar a VNet-Hub com a VNet-Spoke e **UseRemoteGateways** ao emparelhar a VNet-Spoke com a VNet-Hub.

Consulte a seção [Criar Rotas](#create-routes) deste tutorial para ver como essas rotas são criadas.

>[!NOTE]
>O Firewall do Azure deve ter conectividade direta com a Internet. Se você tiver habilitado o túnel forçado para local por meio do ExpressRoute ou do Gateway de Aplicativo, será necessário configurar a UDR 0.0.0.0/0 com o valor **NextHopType** definido como **Internet** e, em seguida, atribuí-lo a **AzureFirewallSubnet**.

>[!NOTE]
>O tráfego entre VNETs diretamente emparelhadas é roteado diretamente, mesmo se uma UDR aponta para o Firewall do Azure como o gateway padrão. Para enviar o tráfego de sub-rede para sub-rede para o firewall nesse cenário, uma UDR precisa conter o prefixo de rede da sub-rede de destino explicitamente em ambas as sub-redes.

Para examinar a documentação de referência do Azure PowerShell relacionada, consulte a [Referência do Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/new-azfirewall).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="declare-the-variables"></a>Declarar as variáveis

O exemplo a seguir declara as variáveis usando os valores deste tutorial. Em alguns casos, é preciso substituir alguns valores pelos seus próprios para trabalhar em sua assinatura. Modifique as variáveis, se for necessário, e depois copie e cole em seu console do PowerShell.

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

# Variables for the spoke virtual network

$VnetNameSpoke = "VNet-Spoke"
$SNnameSpoke = "SN-Workload"
$VNetSpokePrefix = "10.6.0.0/16"
$SNSpokePrefix = "10.6.0.0/24"
$SNSpokeGWPrefix = "10.6.1.0/24"

# Variables for the on-premises virtual network

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


## <a name="create-the-firewall-hub-virtual-network"></a>Criar a rede virtual do hub de firewall

Primeiro, crie um grupo de recursos para conter os recursos deste tutorial:

```azurepowershell
  New-AzureRmResourceGroup -Name $RG1 -Location $Location1
  ```

Defina as sub-redes a serem incluídas na rede virtual:

```azurepowershell
$FWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameHub -AddressPrefix $SNHubPrefix
$GWsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWHubPrefix
```

Agora, crie a rede virtual do hub de firewall:

```azurepowershell
$VNetHub = New-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetHubPrefix -Subnet $FWsub,$GWsub
```

Solicite um endereço IP público a ser alocado para o gateway de VPN que você criará para sua rede virtual. Observe que *AllocationMethod* é **Dinâmico**. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente para o gateway de VPN. 

  ```azurepowershell
  $gwpip1 = New-AzureRmPublicIpAddress -Name $GWHubpipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="create-the-spoke-virtual-network"></a>Criar a rede virtual spoke

Defina as sub-redes a serem incluídas na rede virtual spoke:

```azurepowershell
$Spokesub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameSpoke -AddressPrefix $SNSpokePrefix
$GWsubSpoke = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNSpokeGWPrefix
```

Criar a rede virtual spoke:

```azurepowershell
$VNetSpoke = New-AzureRmVirtualNetwork -Name $VnetNameSpoke -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetSpokePrefix -Subnet $Spokesub,$GWsubSpoke
```

## <a name="create-the-on-premises-virtual-network"></a>Criar a rede virtual local

Defina as sub-redes a serem incluídas na rede virtual:

```azurepowershell
$Onpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNNameOnprem -AddressPrefix $SNOnpremPrefix
$GWOnpremsub = New-AzureRmVirtualNetworkSubnetConfig -Name $SNnameGW -AddressPrefix $SNGWOnpremPrefix
```

Agora, crie a rede virtual local:

```azurepowershell
$VNetOnprem = New-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1 `
-Location $Location1 -AddressPrefix $VNetOnpremPrefix -Subnet $Onpremsub,$GWOnpremsub
```

Solicite um endereço IP público para ser alocado para o gateway que será criado para a rede virtual. Observe que *AllocationMethod* é **Dinâmico**. Você não pode especificar o endereço IP que deseja usar. Ele é alocado dinamicamente ao gateway. 

  ```azurepowershell
  $gwOnprempip = New-AzureRmPublicIpAddress -Name $GWOnprempipName -ResourceGroupName $RG1 `
  -Location $Location1 -AllocationMethod Dynamic
```

## <a name="configure-and-deploy-the-firewall"></a>Configurar e implantar o firewall

Agora, implante o firewall na rede virtual do hub.

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

<!--- $Rule3 = New-AzureRmFirewallNetworkRule -Name "AllowPing" -Protocol ICMP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort *--->

```azurepowershell
$Rule1 = New-AzureRmFirewallNetworkRule -Name "AllowWeb" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 80

$Rule2 = New-AzureRmFirewallNetworkRule -Name "AllowRDP" -Protocol TCP -SourceAddress $SNOnpremPrefix `
   -DestinationAddress $VNetSpokePrefix -DestinationPort 3389

$NetRuleCollection = New-AzureRmFirewallNetworkRuleCollection -Name RCNet01 -Priority 100 `
   -Rule $Rule1,$Rule2 -ActionType "Allow"
$Azfw.NetworkRuleCollections = $NetRuleCollection
Set-AzureRmFirewall -AzureFirewall $Azfw
```

## <a name="create-and-connect-the-vpn-gateways"></a>Criar e conectar os gateways de VPN

As redes virtuais locais e de hub são conectadas por meio de gateways VPN.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Criar um gateway de VPN para a rede virtual do hub

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a usar.

  ```azurepowershell
  $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetnameHub -ResourceGroupName $RG1
  $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
  $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameHub `
  -Subnet $subnet1 -PublicIpAddress $gwpip1
  ```

Agora crie o gateway de VPN para a rede virtual do hub. As configurações de rede a rede exigem um RouteBased VpnType. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Criar um gateway de VPN para a rede virtual local

Crie a configuração do gateway de VPN. A configuração do gateway de VPN define a sub-rede e o endereço IP público a usar.

  ```azurepowershell
$vnet2 = Get-AzureRmVirtualNetwork -Name $VNetnameOnprem -ResourceGroupName $RG1
$subnet2 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfNameOnprem `
  -Subnet $subnet2 -PublicIpAddress $gwOnprempip
  ```

Agora crie um gateway de VPN para a rede virtual local. As configurações de rede a rede exigem um RouteBased VpnType. A criação de um gateway de VPN pode levar 45 minutos ou mais, dependendo da SKU do gateway de VPN selecionado.

```azurepowershell
New-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1 `
-Location $Location1 -IpConfigurations $gwipconf2 -GatewayType Vpn `
-VpnType RouteBased -GatewaySku basic
```

### <a name="create-the-vpn-connections"></a>Criar conexões de VPN

Agora você pode criar as conexões de VPN entre o hub e os gateways locais

#### <a name="get-the-vpn-gateways"></a>Obter gateways de VPN

```azurepowershell
$vnetHubgw = Get-AzureRmVirtualNetworkGateway -Name $GWHubName -ResourceGroupName $RG1
$vnetOnpremgw = Get-AzureRmVirtualNetworkGateway -Name $GWOnpremName -ResourceGroupName $RG1
```

#### <a name="create-the-connections"></a>Criar as conexões

Nesta etapa, você criará a conexão da rede virtual do hub à rede virtual local. Você verá uma chave compartilhada referenciada nos exemplos. Você pode usar seus próprios valores para a chave compartilhada. O importante é que a chave compartilhada deve corresponder em ambas as conexões. Criar uma conexão pode levar alguns minutos para ser concluída.

```azurepowershell
New-AzureRmVirtualNetworkGatewayConnection -Name $ConnectionNameHub -ResourceGroupName $RG1 `
-VirtualNetworkGateway1 $vnetHubgw -VirtualNetworkGateway2 $vnetOnpremgw -Location $Location1 `
-ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'
```
Crie a conexão da rede virtual local à de hub. Esta etapa é semelhante à anterior, exceto que você cria a conexão da VNet-Onprem à VNet-hub. Verifique se que as chaves compartilhadas correspondem. Depois de alguns minutos, a conexão deverá ser estabelecida.

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

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>Emparelhar as redes virtuais hub e spoke

Agora emparelhe as redes virtuais hub e spoke.

```azurepowershell
# Peer hub to spoke
Add-AzureRmVirtualNetworkPeering -Name HubtoSpoke -VirtualNetwork $VNetHub -RemoteVirtualNetworkId $VNetSpoke.Id -AllowGatewayTransit

# Peer spoke to hub
Add-AzureRmVirtualNetworkPeering -Name SpoketoHub -VirtualNetwork $VNetSpoke -RemoteVirtualNetworkId $VNetHub.Id -AllowForwardedTraffic -UseRemoteGateways
```

## <a name="create-the-routes"></a>Criar as rotas

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

Agora crie a carga de trabalho do spoke e as máquinas virtuais locais e coloque-as nas sub-redes apropriadas.

### <a name="create-the-workload-virtual-machine"></a>Criar a máquina virtual de carga de trabalho

Crie uma máquina virtual na rede virtual spoke, executando o IIS, sem endereço IP público e permitindo pings.
Quando solicitado, digite um nome de usuário e senha da máquina virtual.

```azurepowershell
# Create an inbound network security group rule for ports 3389 and 80
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
```

<!---#Create a host firewall rule to allow ping in
Set-AzureRmVMExtension `
    -ResourceGroupName $RG1 `
    -ExtensionName IIS `
    -VMName VM-Spoke-01 `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4"}' `
    -Location $Location1--->

### <a name="create-the-on-premises-virtual-machine"></a>Criar a máquina virtual local

Trata-se de uma máquina virtual simples que pode ser usada para se conectar usando a Área de Trabalho Remota para o endereço IP público. A partir daí, você conecta o servidor local por meio do firewall. Quando solicitado, digite um nome de usuário e senha da máquina virtual.

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

No portal do Azure, conecte a máquina virtual **VM-Onprem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
Abra um navegador da Web na **VM-Onprem** e navegue até o IP privado http://\<VM-spoke-01\>.

Você deverá ver a página padrão dos Serviços de Informações da Internet.

Na **VM-Onprem**, abra uma área de trabalho remota para **VM-spoke-01** no endereço IP privado.

Sua conexão deve ter êxito e você deve conseguir entrar usando o nome de usuário e a senha escolhidos.

Agora que você verificou se as regras de firewall estão funcionando:

<!---- You can ping the server on the spoke VNet.--->
- Você pode procurar o servidor Web na rede virtual spoke.
- Você pode conectar o servidor na rede virtual spoke usando o RDP.

Em seguida, altere a ação de coleção de regras da rede do firewall para **Deny** a fim de verificar se as regras do firewall funciona como o esperado. Execute o script a seguir para alterar a ação de coleção de regras para **Negar**.

```azurepowershell
$rcNet = $azfw.GetNetworkRuleCollectionByName("RCNet01")
$rcNet.action.type = "Deny"

Set-AzureRmFirewall -AzureFirewall $azfw
```

Agora execute os testes novamente. Todos devem falhar nesse momento. Feche as áreas de trabalho remotas existentes antes de testar a alteração das regras.

## <a name="clean-up-resources"></a>Limpar recursos

Você pode manter seus recursos de firewall para o próximo tutorial ou se não forem mais necessários, exclua o grupo de recursos **FW-Hybrid-Test** para excluir todos os recursos relacionados ao firewall.

## <a name="next-steps"></a>Próximas etapas

Em seguida,você pode monitorar os logs do Firewall do Azure.

> [!div class="nextstepaction"]
> [Tutorial: Monitorar os logs do Firewall do Azure](./tutorial-diagnostics.md)
