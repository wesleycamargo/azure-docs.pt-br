---
title: Rotear tráfego de rede - PowerShell | Microsoft Docs
description: Saiba como rotear tráfego com uma tabela de rotas utilizando o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: f91b143c75a82aa6760796770b3ae4d0e4ec53dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-powershell"></a>Rotear tráfego com uma tabela de rotas utilizando o PowerShell

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de um firewall. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede de rede virtual
> * Testar o roteamento
> * Solucionar problemas de roteamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este artigo exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma rede virtual. Para saber mais sobre as rotas padrão do Azure, consulte [Rotas do sistema](virtual-networks-udr-overview.md). Para substituir o roteamento padrão do Azure, você cria uma tabela de rotas que contém rotas e associa a tabela de rotas a uma sub-rede de rede virtual.

Antes de criar uma tabela de rotas, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* para todos os recursos criados neste artigo. 

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Criar uma tabela de rotas com [New-AzureRmRouteTable](/powershell/module/azurerm.network/new-azurermroutetable). O exemplo a seguir cria uma tabela de rotas denominada *myRouteTablePublic*.

```azurepowershell-interactive
$routeTablePublic = New-AzureRmRouteTable `
  -Name 'myRouteTablePublic' `
  -ResourceGroupName myResourceGroup `
  -location EastUS
```

## <a name="create-a-route"></a>Criar uma rota

Uma tabela de rotas contém zero ou mais rotas. Crie uma rota recuperando o objeto de tabela de rotas com [Get-AzureRmRouteTable](/powershell/module/azurerm.network/get-azurermroutetable), crie uma rota com [Add-AzureRmRouteConfig](/powershell/module/azurerm.network/add-azurermrouteconfig) e, em seguida, grave a configuração de rota na tabela de rotas com [Set-AzureRmRouteTable](/powershell/module/azurerm.network/set-azurermroutetable). 

```azurepowershell-interactive
Get-AzureRmRouteTable `
  -ResourceGroupName "myResourceGroup" `
  -Name "myRouteTablePublic" `
  | Add-AzureRmRouteConfig `
  -Name "ToPrivateSubnet" `
  -AddressPrefix 10.0.1.0/24 `
  -NextHopType "VirtualAppliance" `
  -NextHopIpAddress 10.0.2.4 `
 | Set-AzureRmRouteTable
```

A rota direcionará todo o tráfego destinado ao prefixo de endereço 10.0.1.0/24 por meio de uma solução de virtualização de rede com o endereço IP 10.0.2.4. A solução de virtualização de rede e sub-rede com o prefixo de endereço especificado serão criadas em etapas posteriores. A rota substitui o roteamento padrão do Azure, que roteia o tráfego entre sub-redes diretamente. Cada rota especifica um tipo do próximo salto. O tipo do próximo salto instrui o Azure como rotear o tráfego. Nesse exemplo, o tipo do próximo salto é *VirtualAppliance*. Para saber mais sobre todos os tipos disponíveis do próximo salto no Azure, e quando utilizá-los, consulte [tipos do próximo salto](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede. Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma rede virtual nomeada *myVirtualNetwork* com o prefixo de endereço *10.0.0.0/16*.

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie três sub-redes criando três configurações de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O exemplo a seguir cria três configurações de sub-rede para sub-redes *Pública*, *Privada* e *DMZ*:

```azurepowershell-interactive
$subnetConfigPublic = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Public `
  -AddressPrefix 10.0.0.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigPrivate = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name Private `
  -AddressPrefix 10.0.1.0/24 `
  -VirtualNetwork $virtualNetwork

$subnetConfigDmz = Add-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -AddressPrefix 10.0.2.0/24 `
  -VirtualNetwork $virtualNetwork
```

Os prefixos de endereço devem estar dentro do prefixo de endereço definido para a rede virtual. Os prefixos de endereço de sub-rede não podem se sobrepor entre si.

Grave as configurações de sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/Set-AzureRmVirtualNetwork), que cria as sub-redes na rede virtual:

```azurepowershell-interactive
$virtualNetwork | Set-AzureRmVirtualNetwork
```

É possível associar uma tabela de rotas para zero ou mais sub-redes. Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. O tráfego de saída de uma sub-rede é roteado com base nas rotas padrão do Azure e quaisquer rotas personalizadas adicionadas a uma tabela de rotas associadas a uma sub-rede. Associe a tabela de rotas *myRouteTablePublic* à sub-rede *Pública* com [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork).

```azurepowershell-interactive
Set-AzureRmVirtualNetworkSubnetConfig `
  -VirtualNetwork $virtualNetwork `
  -Name 'Public' `
  -AddressPrefix 10.0.0.0/24 `
  -RouteTable $routeTablePublic | `
Set-AzureRmVirtualNetwork
```

Antes de implantar as tabelas de rotas para uso em produção, é recomendável que você cuidadosamente se familiarizar com o [roteamento no Azure](virtual-networks-udr-overview.md) e os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testar o roteamento

Para testar o roteamento, será necessário criar uma máquina virtual que serve como a solução de virtualização de rede em que a rota criada em uma etapa anterior passa. Após criar a solução de virtualização de rede, será necessário implantar uma máquina virtual nas sub-redes *Pública* e *Privada*. Em seguida, você roteará o tráfego da sub-rede *Pública* para a sub-rede *Privada* por meio da solução de virtualização de rede.

### <a name="create-a-network-virtual-appliance"></a>Criar uma solução de virtualização de rede

Uma máquina virtual executando um aplicativo de rede é frequentemente referida como uma solução de virtualização de rede. Soluções de virtualização de rede normalmente recebem o tráfego de rede, executam alguma ação e, em seguida, encaminham ou descartam o tráfego de rede com base em lógica configurada no aplicativo de rede. 

#### <a name="create-a-network-interface"></a>Criar um adaptador de rede

Em uma etapa anterior, você criou uma rota que especificou uma solução de virtualização como o tipo do próximo salto. Uma máquina virtual executando um aplicativo de rede é frequentemente referida como uma solução de virtualização de rede. Em ambientes de produção, a solução de virtualização de rede que você implanta é frequentemente uma máquina virtual pré-configurada. Várias soluções de virtualização de rede estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Neste artigo, é criada uma máquina virtual básica.

Uma máquina virtual tem uma ou mais interfaces de rede conectadas a ela, que permitem que a máquina virtual se comunique com a rede. Para uma interface de rede poder encaminhar o tráfego de rede enviado a ela, que não seja destinado a seu próprio endereço IP, o encaminhamento de IP deve ser habilitado para a interface de rede. Antes de criar uma interface de rede, você precisa recuperar a ID da rede virtual com [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork) e, em seguida, a ID de sub-rede com [Get-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig). Crie uma interface de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) na sub-rede *DMZ* com o encaminhamento de IP habilitado:

```azurepowershell-interactive
# Retrieve the virtual network object into a variable.
$virtualNetwork=Get-AzureRmVirtualNetwork `
  -Name myVirtualNetwork `
  -ResourceGroupName myResourceGroup

# Retrieve the subnet configuration into a variable.
$subnetConfigDmz = Get-AzureRmVirtualNetworkSubnetConfig `
  -Name DMZ `
  -VirtualNetwork $virtualNetwork

# Create the network interface.
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name 'myVmNva' `
  -SubnetId $subnetConfigDmz.Id `
  -EnableIPForwarding
```

#### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para criar uma máquina virtual e conectar uma interface de rede existente a ela, você deve primeiro criar uma configuração de máquina virtual com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig). A configuração inclui a interface de rede criada na etapa anterior. Quando solicitado um nome de usuário e uma senha, selecione o nome de usuário e a senha com os quais deseja iniciar sessão na máquina virtual. 

```azurepowershell-interactive
# Create a credential object.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a virtual machine configuration.
$vmConfig = New-AzureRmVMConfig `
  -VMName 'myVmNva' `
  -VMSize Standard_DS2 | `
  Set-AzureRmVMOperatingSystem -Windows `
    -ComputerName 'myVmNva' `
    -Credential $cred | `
  Set-AzureRmVMSourceImage `
    -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer `
    -Skus 2016-Datacenter `
    -Version latest | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id
```

Crie a máquina virtual usando a configuração de máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma máquina virtual nomeada *myVmNva*. 

```azurepowershell-interactive
$vmNva = New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -VM $vmConfig `
  -AsJob
```

A opção `-AsJob` cria a máquina virtual em segundo plano para que você continue na próxima etapa. Quando solicitado, insira o nome de usuário e a senha com os quais deseja iniciar sessão na máquina virtual. Em ambientes de produção, a solução de virtualização de rede que você implanta é frequentemente uma máquina virtual pré-configurada. Várias soluções de virtualização de rede estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

O Azure atribuiu 10.0.2.4 como o endereço IP privado da máquina virtual, porque 10.0.2.4 é o primeiro endereço IP disponível na sub-rede *DMZ* de *myVirtualNetwork*.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas máquinas virtuais na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da solução de virtualização de rede em uma etapa posterior. 

Crie uma máquina virtual na sub-rede *Pública* com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma máquina virtual nomeada *myVmWeb* na sub-rede *Pública* da rede virtual *myVirtualNetwork*. 

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Public" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmWeb" `
  -AsJob
```

O Azure atribuiu 10.0.0.4 como o endereço IP privado da máquina virtual, porque 10.0.1.4 é o primeiro endereço IP disponível na sub-rede *Pública* de *myVirtualNetwork*.

Crie uma máquina virtual na sub-rede *Privada*.

```azurepowershell-interactive
New-AzureRmVm `
  -ResourceGroupName "myResourceGroup" `
  -Location "East US" `
  -VirtualNetworkName "myVirtualNetwork" `
  -SubnetName "Private" `
  -ImageName "Win2016Datacenter" `
  -Name "myVmMgmt"
```

A criação da máquina virtual demora alguns minutos. O Azure atribuiu 10.0.1.4 como o endereço IP privado da máquina virtual, porque 10.0.1.4 é o primeiro endereço IP disponível na sub-rede *Privada* de *myVirtualNetwork*. 

Não continue com a próxima etapa até que a máquina virtual seja criada e o Azure retorne a saída ao PowerShell.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Rotear o tráfego por meio de uma solução de virtualização de rede

Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para habilitar o ICMP de entrada para as máquinas virtuais *myVmWeb* e *myVmMgmt* por meio do firewall do Windows para usar o tracert para testar a comunicação entre as máquinas virtuais em uma etapa posterior:

```powershell-interactive
Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmWeb `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS

Set-AzureRmVMExtension `
  -ResourceGroupName myResourceGroup `
  -VMName myVmMgmt `
  -ExtensionName AllowICMP `
  -Publisher Microsoft.Compute `
  -ExtensionType CustomScriptExtension `
  -TypeHandlerVersion 1.8 `
  -SettingString '{"commandToExecute": "netsh advfirewall firewall add rule name=Allow-ping protocol=icmpv4 dir=in action=allow"}' `
  -Location EastUS
```

Os comandos anteriores podem levar alguns minutos para serem concluídos. Não continue com a próxima etapa até que os comandos sejam concluídos e a saída seja retornada para o PowerShell. Embora o Tracert seja utilizado para testar o roteamento neste artigo, não é recomendável o ICMP através do Firewall do Windows para implantações de produção.

Conecte-se ao endereço IP público de uma máquina virtual da Internet. Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma máquina virtual. O exemplo a seguir retorna o endereço IP público da máquina virtual *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use o comando a seguir para criar uma sessão de área de trabalho remota com a máquina virtual *myVmMgmt* do seu computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Um arquivo .rdp (protocolo RDP) é criado, baixado para seu computador e aberto. Insira o nome de usuário e senha especificados ao criar a máquina virtual (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a máquina virtual) e selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Clique em **Sim** ou em **Continuar** para prosseguir com o processo de conexão. 

Você habilitou o encaminhamento de IP no Azure para a interface de rede da máquina virtual em [Habilitar encaminhamento de IP](#enable-ip-forwarding). Dentro da máquina virtual, o sistema operacional, ou um aplicativo executado dentro da máquina virtual, também pode ser capaz de encaminhar o tráfego. Quando você implanta uma solução de virtualização rede em um ambiente de produção, a solução geralmente filtra, registra ou executa alguma outra função antes de encaminhar o tráfego. No entanto, neste artigo, o sistema operacional simplesmente encaminha todo o tráfego que recebe. Habilite o encaminhamento de IP no sistema operacional do *myVmNva*:

No prompt de comando na máquina virtual *myVmNva*, área de trabalho remota para a máquina virtual *myVmNva*:

``` 
mstsc /v:myVmNva
```
    
Para habilitar o encaminhamento de IP no sistema operacional da máquina virtual *myVmNva*, digite o seguinte comando no PowerShell da máquina virtual *myVmNva*:

```powershell
Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
```
    
Reinicie a máquina virtual *myVmNva*, que também desconectará a sessão de área de trabalho remota, deixando-o dentro da sessão de área de trabalho remota que você abriu para a máquina virtual *myVmMgmt*.

Depois de reiniciar a máquina virtual *myVmNva*, use o seguinte comando para testar o roteamento do tráfego para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*.

```
tracert myvmweb
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myvmweb.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
over a maximum of 30 hops:
    
1     1 ms     1 ms     1 ms  10.0.0.4
  
Trace complete.
```

Você pode ver que o tráfego é roteado diretamente da máquina virtual *myVmMgmt* para a máquina virtual *myVmWeb*. As rotas padrão do Azure roteiam o tráfego diretamente entre sub-redes.

Use o seguinte comando para a área de trabalho remota na máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*:

``` 
mstsc /v:myVmWeb
```

Para testar o roteamento do tráfego para a máquina virtual *myVmMgmt* da máquina virtual *myVmWeb*, insira o seguinte comando a partir de um prompt de comando:

```
tracert myvmmgmt
```

A resposta é semelhante ao seguinte exemplo:

```
Tracing route to myvmmgmt.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
over a maximum of 30 hops:
        
1    <1 ms     *        1 ms  10.0.2.4
2     1 ms     1 ms     1 ms  10.0.1.4
        
Trace complete.
```

Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da solução de virtualização de rede. O segundo salto é 10.0.1.4, o endereço IP privado da máquina virtual *myVmMgmt*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.

Feche a sessão da área de trabalho remota para as máquinas virtuais *myVmWeb* e *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de roteamento

Como você aprendeu nas etapas anteriores, o Azure aplica rotas padrão que opcionalmente podem ser substituídas pelas suas próprias rotas. Às vezes, o tráfego pode não ser roteado como você espera que seja. Use [New-AzureRmNetworkWatcher](/powershell/module/azurerm.network/new-azurermnetworkwatcher) para habilitar um Observador de Rede na região Leste dos EUA se você ainda não tiver um Observador de Rede nessa região:

```azurepowershell-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region:
$nw = New-AzureRmNetworkWatcher `
 -Location eastus `
 -Name myNetworkWatcher_eastus `
 -ResourceGroupName myResourceGroup
```

Use [Get-AzureRmNetworkWatcherNextHop](/powershell/module/azurerm.network/get-azurermnetworkwatchernexthop) para determinar como o tráfego é roteado entre duas máquinas virtuais. Por exemplo, o comando a seguir testa o roteamento de tráfego da máquina virtual *myVmWeb* (10.0.0.4) para a máquina virtual *myVmMgmt* (10.0.1.4):

```azurepowershell-interactive
$vmWeb = Get-AzureRmVM `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup

Get-AzureRmNetworkWatcherNextHop `
  -DestinationIPAddress 10.0.1.4 `
  -NetworkWatcherName myNetworkWatcher_eastus `
  -ResourceGroupName myResourceGroup `
  -SourceIPAddress 10.0.0.4 `
  -TargetVirtualMachineId $vmWeb.Id
```
A seguinte saída será retornada após um curto período:

```azurepowershell
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

A saída do informa que o endereço IP do próximo salto para o tráfego de *myVmWeb* para *myVmMgmt* é 10.0.2.4 (a máquina virtual *myVmNva*), que o tipo do próximo salto é *VirtualAppliance* e que a tabela de rotas que causa o roteamento é *myRouteTablePublic*.

As rotas efetivas para cada interface de rede são uma combinação das rotas padrão do Azure e quaisquer rotas que você definir. Para ver todas as rotas efetivas para uma interface de rede em uma máquina virtual, com [Get-AzureRmEffectiveRouteTable](/powershell/module/azurerm.network/get-azurermeffectiveroutetable). Por exemplo, para mostrar as rotas efetivas para a interface de rede *myVmWeb* na máquina virtual *myVmWeb*, digite o seguinte comando:

```azurepowershell-interactive
Get-AzureRmEffectiveRouteTable `
  -NetworkInterfaceName myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Format-Table
```

Todas as rotas padrão e a rota que você adicionou na etapa anterior são retornadas.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, utilize [Remove-AzureRmResourcegroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma solução de virtualização de rede que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Avance para o próximo artigo para aprender como restringir o acesso à rede para recursos PaaS do Azure.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](virtual-network-service-endpoints-configure.md#azure-powershell)