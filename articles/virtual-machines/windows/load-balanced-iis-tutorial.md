---
title: "Tutorial - Compilar um aplicativo altamente disponível em VMs do Azure | Microsoft Docs"
description: "Saiba como criar um aplicativo altamente disponível e seguro em três VMs Windows com um balanceador de carga no Azure"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/30/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 61fd0dc45cfa2d320713819be3db811be58ab77e
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017

---

# <a name="build-a-load-balanced-highly-available-application-on-windows-virtual-machines-in-azure"></a>Criar um aplicativo altamente disponível com balanceamento de carga em máquinas virtuais Windows no Azure

Neste tutorial, você criará um aplicativo altamente disponível e resistente a eventos de manutenção. O aplicativo usa um balanceador de carga, um conjunto de disponibilidade e três VMs (máquinas virtuais) Windows. Este tutorial instala o IIS, mas você pode usá-lo para implantar outra estrutura de aplicativo usando os mesmos componentes e diretrizes de alta disponibilidade. 

## <a name="step-1---azure-prerequisites"></a>Etapa 1: pré-requisitos do Azure

Para concluir este tutorial, verifique se você instalou o último módulo do [Azure PowerShell](/powershell/azure/overview).

Primeiro, faça logon em sua assinatura do Azure com o comando Login-AzureRmAccount e siga as instruções na tela.

```powershell
Login-AzureRmAccount
```

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Antes de criar outros recursos do Azure, será necessário criar um grupo de recursos com [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). O seguinte exemplo cria um grupo de recursos chamado `myResourceGroup` na região `westeurope`: 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroup -Location westeurope
```

## <a name="step-2---create-availability-set"></a>Etapa 2: criar conjunto de disponibilidade

As máquinas virtuais podem ser criadas em domínios lógicos de falhas e de atualização. Cada domínio lógico representa uma parte do hardware do datacenter subjacente do Azure. Quando você cria duas ou mais VMs, seus recursos de computação e armazenamento são distribuídos entre esses domínios. Essa distribuição mantém a disponibilidade de seu aplicativo, caso um componente de hardware precise de manutenção. Os conjuntos de disponibilidade permitem que você defina esses domínios lógicos de falha e de atualização.

Crie um conjunto de disponibilidade com [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). O exemplo a seguir cria um conjunto de disponibilidade chamado `myAvailabilitySet`:

```powershell
$availabilitySet = New-AzureRmAvailabilitySet `
  -ResourceGroupName myResourceGroup `
  -Name myAvailabilitySet `
  -Location westeurope `
  -Managed `
  -PlatformFaultDomainCount 3 `
  -PlatformUpdateDomainCount 2
```

## <a name="step-3---create-load-balancer"></a>Etapa 3: criar um balanceador de carga

Um Azure Load Balancer distribui o tráfego entre um conjunto de VMs definidas usando regras de balanceador de carga. Uma investigação de integridade monitora uma determinada porta em cada VM e distribui o tráfego somente para uma VM operacional.

### <a name="create-public-ip-address"></a>Criar um endereço IP público

Para acessar seu aplicativo na Internet, atribua um endereço IP público para o balanceador de carga. Crie um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress). O exemplo a seguir cria um endereço IP público chamado `myPublicIP`:

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -AllocationMethod Static `
  -Name myPublicIP
```

### <a name="create-load-balancer"></a>Criar um balanceador de carga

Crie um endereço IP de front-end com [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig). O seguinte exemplo cria um endereço IP de front-end chamado `myFrontEndPool`: 

```powershell
$frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name myFrontEndPool -PublicIpAddress $pip
```

Crie um pool de endereços de back-end com [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig). O seguinte exemplo cria um pool de endereços de back-end chamado `myBackEndPool`:

```powershell
$backendPool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name myBackEndPool
```

Agora, crie o balanceador de carga com [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer). O exemplo a seguir cria um balanceador de carga chamado `myLoadBalancer` usando o endereço `myPublicIP`:

```powershell
$lb = New-AzureRmLoadBalancer `
  -ResourceGroupName myResourceGroup `
  -Name myLoadBalancer `
  -Location westeurope `
  -FrontendIpConfiguration $frontendIP `
  -BackendAddressPool $backendPool
```

### <a name="create-health-probe"></a>Criar uma investigação de integridade

Para permitir que o balanceador de carga monitore o status de seu aplicativo, use uma investigação de integridade. A investigação de integridade adiciona ou remove dinamicamente VMs da rotação do balanceador de carga com base na resposta às verificações de integridade. Por padrão, uma VM é removida da distribuição do balanceador de carga após duas falhas consecutivas em intervalos de 15 segundos.

Crie uma investigação de integridade com [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/add-azurermloadbalancerprobeconfig). O exemplo a seguir cria uma investigação de integridade chamada `myHealthProbe` que monitora cada VM:

```powershell
Add-AzureRmLoadBalancerProbeConfig -Name myHealthProbe `
  -LoadBalancer $lb `
  -Protocol tcp `
  -Port 80 `
  -IntervalInSeconds 15 `
  -ProbeCount 2
```

### <a name="create-load-balancer-rule"></a>Criar uma regra para o balanceador de carga

Uma regra de balanceador de carga é usada para definir como o tráfego é distribuído para as VMs.

Crie uma regra de balanceador de carga com [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/add-azurermloadbalancerruleconfig). O seguinte exemplo cria uma regra de balanceador de carga chamada `myLoadBalancerRule` e faz o balanceamento de carga do tráfego na porta `80`:

```powershell
Add-AzureRmLoadBalancerRuleConfig -Name myLoadBalancerRule `
  -LoadBalancer $lb `
  -FrontendIpConfiguration $lb.FrontendIpConfigurations[0] `
  -BackendAddressPool $lb.BackendAddressPools[0] `
  -Protocol Tcp `
  -FrontendPort 80 `
  -BackendPort 80
```

Atualize o balanceador de carga com [Set-AzureRmLoadBalancer](/powershell/module/azurerm.network/set-azurermloadbalancer):

```powershell
Set-AzureRmLoadBalancer -LoadBalancer $lb
```

## <a name="step-4---configure-networking"></a>Etapa 4: configurar a rede

Cada VM tem uma ou mais NICs (placas de interface de rede virtual) que se conecta a uma rede virtual. Essa rede virtual é protegida para filtrar o tráfego com base em regras de acesso definidos.

### <a name="create-virtual-network"></a>Criar rede virtual

Primeiro, configure uma sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig). O seguinte exemplo cria uma sub-rede chamada `mySubnet`:

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Para fornecer conectividade de rede às VMs, crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork). O seguinte exemplo cria uma rede virtual chamada `myVnet` com `mySubnet`:

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig
```

### <a name="configure-network-security"></a>Configurar a segurança de rede

Um [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) (NSG) do Azure controla o tráfego de entrada e saída em uma ou mais máquinas virtuais. As regras de grupo de segurança de rede permitem ou negam o tráfego de rede em uma porta específica ou em um intervalo de porta. Essas regras também podem incluir um prefixo do endereço de origem para que somente o tráfego originado em uma fonte predefinida possa se comunicar com uma máquina virtual.

Para permitir que o tráfego da Web acesse seu aplicativo, crie uma regra de grupo de segurança de rede com [New-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig). O exemplo a seguir cria uma regra de grupo de segurança de rede chamada `myNetworkSecurityGroupRule`:

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
```

Crie um grupo de segurança de rede com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup). O seguinte exemplo cria um NSG chamado `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myResourceGroup `
  -Location westeurope `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRule
```

Adicione o grupo de segurança de rede à sub-rede com [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
  -Name mySubnet `
  -NetworkSecurityGroup $nsg `
  -AddressPrefix 192.168.1.0/24
```

Atualize a rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-network-interface-cards"></a>Criar placas de interface de rede virtual

Os balanceadores de carga funcionam com o recurso NIC virtual, em vez da VM real. A NIC virtual é conectada ao balanceador de carga e depois anexada a uma VM.

Crie uma NIC virtual com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface). O exemplo a seguir cria três NICs virtuais. (Uma NIC virtual para cada VM criada para seu aplicativo nas etapas a seguir):


```powershell
for ($i=1; $i -le 3; $i++)
{
   New-AzureRmNetworkInterface -ResourceGroupName myResourceGroup `
     -Name myNic$i `
     -Location westeurope `
     -Subnet $vnet.Subnets[0] `
     -LoadBalancerBackendAddressPool $lb.BackendAddressPools[0]
}

```

## <a name="step-5---create-virtual-machines"></a>Etapa 5 – Criar máquinas virtuais

Com todos os componentes subjacentes aplicados, agora você pode criar VMs altamente disponíveis para executar seu aplicativo. 

Obtenha o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Crie as VMs com [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/new-azurermvmconfig), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmoperatingsystem), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmsourceimage), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/set-azurermvmosdisk), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/resourcemanager/azurerm.compute/v2.8.0/add-azurermvmnetworkinterface) e [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm). O seguinte exemplo cria três VMs:

```powershell
for ($i=1; $i -le 3; $i++)
{
   $vm = New-AzureRmVMConfig -VMName myVM$i -VMSize Standard_D1 -AvailabilitySetId $availabilitySet.Id
   $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM$i -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
   $vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk$i -StorageAccountType StandardLRS -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
   $nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic$i
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM -ResourceGroupName myResourceGroup -Location westeurope -VM $vm
}

```

É necessário alguns minutos para criar e configurar as três VMs. A investigação de integridade do balanceador de carga detecta automaticamente quando o aplicativo está em execução em cada VM. Quando o aplicativo estiver em execução, a regra do balanceador de carga começará a distribuir o tráfego.

### <a name="install-the-app"></a>Instalar o aplicativo 

As extensões de máquina virtual do Azure são usadas para automatizar tarefas de configuração de máquina virtual, como instalar aplicativos e configurar o sistema operacional. A [extensão de script personalizado para o Windows](./../virtual-machines-windows-extensions-customscript.md) é usada para executar qualquer script do PowerShell na máquina virtual. O script pode ser armazenado no armazenamento do Azure, em qualquer ponto de extremidade HTTP acessível ou inserido na configuração da extensão de script personalizado. Ao usar a extensão de script personalizado, o agente de VM do Azure gerencia a execução do script.

Use [Set-AzureRmVMExtension](/powershell/module/azurerm.compute/set-azurermvmextension) para instalar a extensão de script personalizado. A extensão executa `powershell Add-WindowsFeature Web-Server` para instalar o servidor Web do IIS:

```powershell
for ($i=1; $i -le 3; $i++)
{
   Set-AzureRmVMExtension -ResourceGroupName myResourceGroup `
     -ExtensionName IIS `
     -VMName myVM$i `
     -Publisher Microsoft.Compute `
     -ExtensionType CustomScriptExtension `
     -TypeHandlerVersion 1.4 `
     -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server"}' `
     -Location westeurope
}
```

### <a name="test-your-app"></a>Testar seu aplicativo

Obtenha o endereço IP público do balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O exemplo a seguir obtém o endereço IP para `myPublicIP` criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myResourceGroup -Name myPublicIP | select IpAddress
```

Insira o endereço IP público em um navegador da Web. Com a regra do NSG em vigor, o site padrão do IIS é exibido. 

![Site do IIS padrão](media/load-balanced-iis-tutorial/iis.png)

## <a name="step-6--management-tasks"></a>Etapa 6 – Tarefas de gerenciamento

Talvez seja necessário fazer a manutenção nas VMs que executam seu aplicativo, como a instalação de atualizações do sistema operacional. Para lidar com o aumento de tráfego em seu aplicativo, talvez seja necessário adicionar outras VMs. Esta seção mostra como remover ou adicionar uma VM do balanceador de carga. 

### <a name="remove-a-vm-from-the-load-balancer"></a>Remover uma VM do balanceador de carga

Remova uma VM do pool de endereços de back-end redefinindo a propriedade LoadBalancerBackendAddressPools da placa de interface de rede.

Obtenha a placa de interface de rede com [Get-AzureRmNetworkInterface](/powershell/module/azurerm.network/get-azurermnetworkinterface):

```powershell
$nic = Get-AzureRmNetworkInterface -ResourceGroupName myResourceGroup -Name myNic2
``` 

Defina a propriedade LoadBalancerBackendAddressPools da placa de interface de rede como $null:

```powershell
$nic.Ipconfigurations[0].LoadBalancerBackendAddressPools=$null
```

Atualize a placa de interface de rede:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

### <a name="add-a-vm-to-the-load-balancer"></a>Adicionar uma VM ao balanceador de carga

Depois de executar a manutenção da VM ou se precisar expandir a capacidade, adicione a NIC de uma VM ao pool de endereços de back-end do balanceador de carga.

Obtenha o balanceador de carga:

```powershell
$lb = Get-AzureRMLoadBalancer -ResourceGroupName myResourceGroup -Name myLoadBalancer 
```

Adicione o pool de endereços de back-end do balanceador de carga à placa de interface de rede:

```powershell
$nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$lb.BackendAddressPools[0]
```

Atualize a placa de interface de rede:

```powershell
Set-AzureRmNetworkInterface -NetworkInterface $nic
```

## <a name="next-steps"></a>Próximas etapas

Amostras – [Scripts de exemplo do PowerShell na Máquina Virtual do Azure](./../virtual-machines-windows-powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

