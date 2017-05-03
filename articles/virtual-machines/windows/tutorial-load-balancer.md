---
title: "Como balancear a carga de máquinas virtuais Windows no Azure | Microsoft Docs"
description: "Saiba como usar o Azure Load Balancer para criar um aplicativo altamente disponível e seguro em três VMs Windows"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/17/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: e9a91a9a933104e4f885b009490ec35468398662
ms.lasthandoff: 04/21/2017

---

# <a name="how-to-load-balance-windows-virtual-machines-in-azure-to-create-a-highly-available-application"></a>Como balancear a carga de máquinas virtuais Windows no Azure para criar um aplicativo altamente disponível
Neste tutorial, você aprenderá sobre os diferentes componentes do Azure Load Balancer que distribuem o tráfego e fornecem alta disponibilidade. Para ver o balanceador de carga em ação, você deverá criar um site do IIS simples que é executado em três máquinas virtuais do Windows (VMs).

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [Azure PowerShell](/powershell/azureps-cmdlets-docs/).


## <a name="azure-load-balancer-overview"></a>Visão geral do Azure Load Balancer
Um Azure Load Balancer é um balanceador de carga de Camada 4 (TCP, UDP) que fornece alta disponibilidade distribuindo o tráfego de entrada entre VMs íntegros. Uma investigação de integridade do balanceador de carga monitora uma determinada porta em cada VM e distribui o tráfego somente para uma VM operacional.

Você define uma configuração de IP de front-end que contém um ou mais endereços IP públicos. Essa configuração de IP de front-end permite que seu balanceador de carga e os aplicativos estejam acessíveis pela Internet. 

As máquinas virtuais conectam-se a um balanceador de carga usando a placa de interface de rede virtual (NIC). Para distribuir o tráfego para as máquinas virtuais, um pool de endereços de back-end contém os endereços IP das NICs virtuais conectadas ao balanceador de carga.

Para controlar o fluxo do tráfego, você precisará definir regras do balanceador de carga para portas específicas e protocolos que mapeiam para suas VMs.


## <a name="create-azure-load-balancer"></a>Criar o Azure Load Balancer
Esta seção fornece detalhes sobre como criar e configurar cada componente do balanceador de carga. Antes de criar seu balanceador de carga, crie um grupo de recursos com [New-AzureRmResourceGroup](/powershell/resourcemanager/AzureRM.Resources/new-azurermresourcegroup). O exemplo a seguir cria um grupo de recursos denominado `myResourceGroupLoadBalancer` no local `westus`:

```powershell
New-AzureRmResourceGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus
```

### <a name="create-a-public-ip-address"></a>Criar um endereço IP público
Para acessar seu aplicativo na Internet, você precisará de um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/resourcemanager/azurerm.network/new-azurermpublicipaddress). O exemplo a seguir cria um endereço IP público chamado `myPublicIP` no grupo de recursos `myResourceGroupLoadBalancer`:

```powershell
$publicIP = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-a-load-balancer"></a>Criar um balanceador de carga
Crie um endereço IP de front-end com [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerfrontendipconfig). O seguinte exemplo cria um endereço IP de front-end chamado `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig `
  -Name myFrontEndPool `
  -PublicIpAddress $publicIP
```

Crie um pool de endereços de back-end com [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). O seguinte exemplo cria um pool de endereços de back-end chamado `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Agora, crie o balanceador de carga com [New-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/new-azurermloadbalancer). O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer` usando o endereço `myPublicIP`:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myLoadBalancer `
  -Location westus `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-a-health-probe"></a>Criar uma investigação de integridade
Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos. Crie uma investigação de integridade com base em um protocolo ou página de verificação de integridade específica ao seu aplicativo. 

O exemplo a seguir cria uma investigação de TCP. Você também pode criar investigações de HTTP personalizadas mais obter verificações de integridade mais refinadas. Ao usar uma investigação de HTTP personalizada, você deverá criar a página de verificação de integridade, como `healthcheck.aspx`. A investigação deve retornar a reposta **HTTP 200 OK** para o balanceador de carga a fim de manter o host em rotação.

Para criar uma investigação de integridade de TCP, use [Add-AzureRmLoadBalancerProbeConfig](/powershell/resourcemanager/azurerm.network/add-azurermloadbalancerprobeconfig). O exemplo a seguir cria uma investigação de integridade chamada `myHealthProbe` que monitora cada VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-a-load-balancer-rule"></a>Criar uma regra de balanceador de carga
Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs. Definir a configuração de IP de front-end para o tráfego de entrada e o pool de IP de back-end para receber o tráfego, junto com as portas de origem e de destino necessárias. Para ter certeza de que apenas VMs íntegras recebem tráfego, defina também a investigação de integridade a ser usada.

Crie uma regra de balanceador de carga com [Add-AzureRmLoadBalancerRuleConfig](/powershell/resourcemanager/AzureRM.Network/add-azurermloadbalancerruleconfig). O seguinte exemplo cria uma regra de balanceador de carga chamada `myLoadBalancerRule` e faz o balanceamento de carga do tráfego na porta `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Atualize o balanceador de carga com [Set-AzureRmLoadBalancer](/powershell/resourcemanager/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```


## <a name="configure-virtual-network"></a>Configurar rede virtual
Antes de implantar algumas VMs e poder testar o balanceador, crie os recursos de suporte de rede virtual. Para saber mais sobre redes virtuais, veja o tutorial [Gerenciar Redes Virtuais do Azure](tutorial-virtual-network.md).

### <a name="create-network-resources"></a>Criar recursos da rede
Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/new-azurermvirtualnetwork). O seguinte exemplo cria uma rede virtual chamada `myVnet` com `mySubnet`:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

Criar uma regra de grupo de segurança de rede com [New-AzureRmNetworkSecurityRuleConfig](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecurityruleconfig), em seguida, crie um grupo de segurança de rede com [New-AzureRmNetworkSecurityGroup](/powershell/resourcemanager/azurerm.network/new-azurermnetworksecuritygroup). Adicione o grupo de segurança de rede para a sub-rede com [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetworksubnetconfig) e, em seguida, atualize a rede virtual com [Set-AzureRmVirtualNetwork](/powershell/resourcemanager/azurerm.network/set-azurermvirtualnetwork). 

O exemplo a seguir cria uma regra de grupo de segurança de rede chamada `myNetworkSecurityGroup` e aplica-se a `mySubnet`:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Location westus `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

As NICs virtuais são criadas com [New-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/new-azurermnetworkinterface). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir). Você pode criar VMs e NICs virtuais adicionais a qualquer momento e adicioná-las ao balanceador de carga:

```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer `
     -Name myNic$i `
     -Location westus `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais
Para melhorar a alta disponibilidade do seu aplicativo, coloque suas VMs em um conjunto de disponibilidade.

Crie um conjunto de disponibilidade com [New-AzureRmAvailabilitySet](/powershell/resourcemanager/azurerm.compute/new-azurermavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myAvailabilitySet `
  -Location westus `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

Defina o nome de usuário e a senha de um administrador para as VMs com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Agora você pode criar VMs com [New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/new-azurermvm). O seguinte exemplo cria três VMs:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroupLoadBalancer -Location westus -VM $vm
}
```

Demora alguns minutos para criar e configurar todas as três VMs.

### <a name="install-iis-with-custom-script-extension"></a>Instalar o IIS com a extensão de script personalizado
Em um tutorial anterior sobre [Como personalizar uma máquina virtual do Windows](tutorial-automate-vm-deployment.md), você aprendeu a automatizar a personalização de VM com a Extensão do Script Personalizado para Windows. Você pode usar a mesma abordagem para instalar e configurar o IIS em suas VMs.

Use [Set-AzureRmVMExtension](/powershell/resourcemanager/azurerm.compute/set-azurermvmextension) para instalar a extensão de script personalizado. A extensão executa `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS e, em seguida, atualiza a página `Default.htm` para mostrar o nome do host da VM:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension `
     -ResourceGroupName myResourceGroupLoadBalancer `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
     -Location westus
}
```

## <a name="test-load-balancer"></a>Testar o balanceador de carga
Obtenha o endereço IP público do balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/resourcemanager/azurerm.network/get-azurermpublicipaddress). O exemplo a seguir obtém o endereço IP para `myPublicIP` criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress `
  -ResourceGroupName myResourceGroupLoadBalancer `
  -Name myPublicIP | select IpAddress
```

Você pode inserir o endereço IP público em um navegador da Web. O site é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego como no exemplo a seguir:

![Site do IIS em execução](./media/tutorial-load-balancer/running-iis-website.png)

Para ver o balanceador de carga distribuir tráfego entre todas as três VMs que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web.


## <a name="add-and-remove-vms"></a>Adicionar e remover as VMs
Talvez seja necessário fazer a manutenção nas VMs que executam seu aplicativo, como a instalação de atualizações do sistema operacional. Para lidar com o aumento de tráfego em seu aplicativo, talvez seja necessário adicionar outras VMs. Esta seção mostra como remover ou adicionar uma VM do balanceador de carga.

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga
Obtenha a placa de interface de rede com [Get-AzureRmNetworkInterface](/powershell/resourcemanager/azurerm.network/get-azurermnetworkinterface), em seguida, defina a propriedade `LoadBalancerBackendAddressPools` da NIC virtual como `$null`. Por fim, atualize a NIC virtual:

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroupLoadBalancer -Name myNic2
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

Para ver o balanceador de carga distribuir tráfego entre as duas VMs restantes que executam seu aplicativo, você poderá forçar a atualização de seu navegador da Web. Agora você pode executar a manutenção na VM, como instalação de atualizações do sistema operacional ou execução de uma reinicialização da VM.

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga
Após executar a manutenção da máquina virtual ou se precisar expandir a capacidade, defina a propriedade `LoadBalancerBackendAddressPools` da NIC virtual para o `BackendAddressPool` de [Get-AzureRMLoadBalancer]():

Obtenha o balanceador de carga:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroupLoadBalancer -Name myLoadBalancer 
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu a criar um site do IIS com carga balanceada. Avance para o próximo tutorial para aprender a gerenciar a rede de VM.

[Gerenciar rede de VM do Azure](./tutorial-virtual-network.md)
