---
title: Filtrar tráfego – tutorial – Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá como filtrar o tráfego para uma sub-rede, com um grupo de segurança de rede, usando o PowerShell.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to filter network traffic to virtual machines that perform similar functions, such as web servers.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/30/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 8e04ed7ad16b673597b62d947c3f782ee0d27c7c
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2018
---
# <a name="tutorial-filter-network-traffic-with-a-network-security-group-using-powershell"></a>Tutorial: filtrar tráfego com um grupo de segurança de rede usando o PowerShell

É possível filtrar o tráfego para entrada e saída de uma sub-rede de rede virtual com um grupo de segurança de rede. Grupos de segurança de rede contêm regras de segurança que filtram o tráfego por endereço IP, porta e protocolo. As regras de segurança são aplicadas aos recursos implantados em uma sub-rede. Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Criar um grupo de segurança de rede e regras de segurança
> * Criar uma rede virtual e associar um grupo de segurança de rede a uma sub-rede
> * Implantar VMs (máquinas virtuais) em uma sub-rede
> * Testar filtros de tráfego

Se preferir, você pode concluir este tutorial usando a [CLI do Azure](tutorial-filter-network-traffic-cli.md).

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.4.1 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para localizar a versão instalada. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Um grupo de segurança de rede contém regras de segurança. As regras de segurança especificam uma origem e um destino. Origens e destinos podem ser grupos de segurança de aplicativos.

### <a name="create-application-security-groups"></a>Criar grupos de segurança de aplicativos

Primeiro, crie um grupo de recursos para todos os recursos criados neste tutorial com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos no local *eastus*: 


```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location EastUS
```

Crie um grupo de segurança de aplicativo com [New-AzureRmApplicationSecurityGroup](/powershell/module/azurerm.network/new-azurermapplicationsecuritygroup). Um grupo de segurança de aplicativos permite agrupar servidores com requisitos de filtragem de porta semelhantes. O exemplo a seguir cria dois grupos de segurança de aplicativos.

```azurepowershell-interactive
$webAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgWebServers `
  -Location eastus

$mgmtAsg = New-AzureRmApplicationSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Name myAsgMgmtServers `
  -Location eastus
```

### <a name="create-security-rules"></a>Criar regras de segurança

Crie uma regra de segurança com [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). O exemplo a seguir cria uma regra que permite a entrada de tráfego da Internet para o grupo de segurança de aplicativo *myWebServers* pelas portas 80 e 443:

```azurepowershell-interactive
$webRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-Web-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $webAsg.id `
  -DestinationPortRange 80,443

The following example creates a rule that allows traffic inbound from the internet to the *myMgmtServers* application security group over port 3389:

$mgmtRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name "Allow-RDP-All" `
  -Access Allow `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 110 `
  -SourceAddressPrefix Internet `
  -SourcePortRange * `
  -DestinationApplicationSecurityGroupId $mgmtAsg.id `
  -DestinationPortRange 3389
```

Neste tutorial, o RDP (porta 3389) é exposto à Internet para a VM *myAsgMgmtServers*. Para ambientes de produção, em vez de expor a porta 3389 à Internet, é recomendável conectar os recursos do Azure que você quer gerenciar utilizando uma conexão de rede [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [privada](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="create-a-network-security-group"></a>Criar um grupo de segurança de rede

Crie um grupo de segurança de rede com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). O exemplo a seguir cria um grupo de segurança de rede nomeado *myNsg*: 

```powershell-interactive
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -Name myNsg `
  -SecurityRules $webRule,$mgmtRule
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O exemplo a seguir cria uma virtual nomeada *myVirtualNetwork*:

```azurepowershell-interactive
$virtualNetwork = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location EastUS `
  -Name myVirtualNetwork `
  -AddressPrefix 10.0.0.0/16
```

Crie uma configuração de sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) e, em seguida, grave a configuração de sub-rede na rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork). O exemplo a seguir adiciona uma sub-rede nomeada *mySubnet* à rede virtual e associa o grupo de segurança de rede *myNsg* a ela:

```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -VirtualNetwork $virtualNetwork `
  -AddressPrefix "10.0.2.0/24" `
  -NetworkSecurityGroup $nsg
$virtualNetwork | Set-AzureRmVirtualNetwork
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Antes de criar as VMs, recupere o objeto de rede virtual com a sub-rede com [Get-AzureRmVirtualNetwork](/powershell/module/azurerm.network/get-azurermvirtualnetwork):

```powershell-interactive
$virtualNetwork = Get-AzureRmVirtualNetwork `
 -Name myVirtualNetwork `
 -Resourcegroupname myResourceGroup
```
Crie um endereço IP público para cada VM com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

$publicIpWeb = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmWeb

$publicIpMgmt = New-AzureRmPublicIpAddress `
  -AllocationMethod Dynamic ` -ResourceGroupName myResourceGroup `
  -Location eastus ` -Name myVmMgmt


Crie dois adaptadores de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface) e atribua um endereço IP público ao adaptador de rede. O exemplo a seguir cria um adaptador de rede, associa o endereço IP público *myVmWeb* a ele e o transforma em um membro do grupo de segurança de aplicativo *myAsgWebServers*:

```powershell-interactive
$webNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $webAsg.Id `
  -PublicIpAddressId $publicIpWeb.Id
```

O exemplo a seguir cria um adaptador de rede, associa o endereço IP público *myVmMgmt* a ele e o transforma em um membro do grupo de segurança de aplicativo *myAsgMgmtServers*:

```powershell-interactive
$mgmtNic = New-AzureRmNetworkInterface `
  -Location eastus `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  -SubnetId $virtualNetwork.Subnets[0].Id `
  -ApplicationSecurityGroupId $mgmtAsg.Id `
  -PublicIpAddressId $publicIpMgmt.Id
```

Crie duas VMs na rede virtual para poder validar a filtragem de tráfego em uma etapa posterior. 

Crie uma configuração de VM com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig) e, em seguida, crie a VM com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O exemplo a seguir cria uma VM que servirá como um servidor Web. A opção `-AsJob` cria a VM em segundo plano, para que você possa prosseguir para a próxima etapa: 

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$webVmConfig = New-AzureRmVMConfig `
  -VMName myVmWeb `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmWeb `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $webNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $webVmConfig `
  -AsJob
```

Crie uma VM para servir como um servidor de gerenciamento:

```azurepowershell-interactive
# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create the web server virtual machine configuration and virtual machine.
$mgmtVmConfig = New-AzureRmVMConfig `
  -VMName myVmMgmt `
  -VMSize Standard_DS1_V2 | `
Set-AzureRmVMOperatingSystem -Windows `
  -ComputerName myVmMgmt `
  -Credential $cred | `
Set-AzureRmVMSourceImage `
  -PublisherName MicrosoftWindowsServer `
  -Offer WindowsServer `
  -Skus 2016-Datacenter `
  -Version latest | `
Add-AzureRmVMNetworkInterface `
  -Id $mgmtNic.Id
New-AzureRmVM `
  -ResourceGroupName myResourceGroup `
  -Location eastus `
  -VM $mgmtVmConfig
```

A criação da máquina virtual demora alguns minutos. Não prossiga para a próxima etapa até que o Azure tenha concluído a criação da VM.

## <a name="test-traffic-filters"></a>Testar filtros de tráfego

Use [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) para retornar o endereço IP público de uma VM. O exemplo a seguir retorna o endereço IP público da VM *myVmMgmt*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmMgmt `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Use o comando a seguir para criar uma sessão de logon remota com a VM *myVmMgmt* do computador local. Substitua `<publicIpAddress>` pelo o endereço IP retornado do comando anterior.

```
mstsc /v:<publicIpAddress>
```

Abra o arquivo RDP baixado. Se solicitado, selecione **Conectar**.

Insira o nome de usuário e senha especificados ao criar a VM (talvez seja necessário selecionar **Mais escolhas**, em seguida, **Usar uma conta diferente**, para especificar as credenciais inseridas ao criar a VM) e selecione **OK**. Você pode receber um aviso do certificado durante o processo de logon. Selecione **Sim** para prosseguir com a conexão. 
   
É uma conexão com êxito porque a porta 3389 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgMgmtServers* ao qual o adaptador de rede conectado à VM *myVmMgmt* está.

Use o comando a seguir para criar uma conexão de área de trabalho remota para a VM *myVmWeb*, a partir da VM *myVmMgmt* com o seguinte comando, a partir do PowerShell:

``` 
mstsc /v:myvmWeb
```

É uma conexão com êxito porque uma regra de segurança padrão dentro de cada grupo de segurança de rede permite o tráfego em todas as portas entre todos os endereços IP em uma rede virtual. Não é possível criar uma conexão de área de trabalho remota para a VM *myVmWeb* da Internet porque a regra de segurança para *myAsgWebServers* não permite a entrada da porta 3389 da Internet.

Use o comando a seguir para instalar os Serviços de Informações da Internet da Microsoft na VM *myVmWeb* a partir do PowerShell:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Após a conclusão da instalação dos Serviços de Informações da Internet, desconecte-se da VM *myVmWeb*, que direciona para a conexão da área de trabalho remota da VM *myVmMgmt*. Para exibir a tela de boas-vindas dos Serviços de Informações da Internet, abra um navegador da Internet e navegue até http://myVmWeb.

Desconecte a VM *myVmMgmt*.

No seu computador, insira o comando a seguir do PowerShell para recuperar o endereço IP público do servidor *myVmWeb*:

```azurepowershell-interactive
Get-AzureRmPublicIpAddress `
  -Name myVmWeb `
  -ResourceGroupName myResourceGroup `
  | Select IpAddress
```

Para confirmar se é possível acessar o servidor Web *myVmWeb* de fora do Azure, abra um navegador da Internet no seu computador e navegue até `http://<public-ip-address-from-previous-step>`. É uma conexão com êxito porque a porta 80 tem permissão de entrada da Internet para o grupo de segurança de aplicativo *myAsgWebServers* ao qual o adaptador de rede conectado à VM *myVmWeb* está.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá utilizar [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) para remover o grupo de recursos e todos os recursos que ele contém:

```azurepowershell-interactive 
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou um grupo de segurança de rede e o associou a uma sub-rede de rede virtual. Para saber mais sobre grupos de segurança de rede, consulte [Visão geral do grupo de segurança de rede](security-overview.md) e [Gerenciar um grupo de segurança de rede](virtual-network-manage-nsg-arm-ps.md).

O Azure roteia o tráfego entre sub-redes por padrão. Em vez disso, é possível escolher rotear o tráfego entre sub-redes por meio de uma VM, servindo como um firewall, por exemplo. Para saber mais sobre como criar uma tabela de rotas, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Criar uma tabela de rotas](./tutorial-create-route-table-portal.md)