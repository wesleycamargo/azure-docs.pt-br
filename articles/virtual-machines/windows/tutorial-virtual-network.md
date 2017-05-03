---
title: "Redes virtuais do Azure e Máquinas Virtuais do Windows | Microsoft Docs"
description: "Tutorial – Gerenciar redes virtuais do Azure e Máquinas Virtuais do Windows com o Azure PowerShell"
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
ms.date: 04/18/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: 7015a5317c631dad9079f2694051fa7fb28d232b
ms.lasthandoff: 04/26/2017

---

# <a name="manage-azure-virtual-networks-and-windows-virtual-machines-with-azure-powershell"></a>Gerenciar redes virtuais do Azure e Máquinas Virtuais do Windows com o Azure PowerShell

Neste tutorial, você aprenderá a criar várias máquinas virtuais (VMs) em uma rede virtual (VNet) e configurar a conectividade de rede entre elas. Quando estiver concluída, uma VM de 'front-end' será acessível pela Internet na porta 80 para conexões HTTP. Uma VM de 'back-end' com um banco de dados do SQL Server será isolada e somente poderá ser acessada da VM de front-end na porta 1433.

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="create-vnet"></a>Criar VNet

Uma VNet é uma representação da sua própria rede na nuvem. Uma VNet é um isolamento lógico da nuvem do Azure dedicada à sua assinatura. Em uma rede virtual, você deve encontrar sub-redes, regras de conectividade para essas sub-redes e conexões das VMs para as sub-redes.

Antes de criar outros recursos do Azure, será necessário criar um grupo de recursos com [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup?view=azurermps-3.8.0). O exemplo a seguir cria um grupo de recursos denominado `myRGNetwork` no local `westus`:

```powershell
New-AzureRmResourceGroup -ResourceGroupName myRGNetwork -Location westus
```

Uma sub-rede é um recurso filho de uma VNet e ajuda a definir segmentos de espaços de endereços em um bloco CIDR, usando prefixos de endereços IP. As NICs podem ser adicionadas a sub-redes e conectadas a VMs, fornecendo conectividade para diversas cargas de trabalho.

Crie uma sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0):

```powershell
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig `
  -Name myFrontendSubnet `
  -AddressPrefix 10.0.0.0/24
```

Crie uma VNET chamada `myVNet` usando `myFrontendSubnet` com [New-AzureRmVirtualNetwork](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetwork?view=azurermps-3.8.0):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myVNet `
  -AddressPrefix 10.0.0.0/16 `
  -Subnet $frontendSubnet
```

## <a name="create-front-end-vm"></a>Criar VM de front-end

Para uma VM se comunicar em uma VNet, ela precisará de uma interface de rede virtual (NIC). O `myFrontendVM` é acessado pela Internet, então ele também precisa de um endereço IP público. 

Crie um endereço IP público com [New-AzureRmPublicIpAddress](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermpublicipaddress?view=azurermps-3.8.0):

```powershell
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

Crie uma NIC com [New-AzureRmNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworkinterface?view=azurermps-3.8.0):


```powershell
$frontendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myFrontendNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

Defina o nome de usuário e a senha necessários para a conta de administrador na VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Crie as VMs com [New-AzureRmVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-3.8.0), [Set-AzureRmVMOperatingSystem](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmoperatingsystem?view=azurermps-3.8.0), [Set-AzureRmVMSourceImage](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmsourceimage?view=azurermps-3.8.0), [Set-AzureRmVMOSDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmosdisk?view=azurermps-3.8.0), [Add-AzureRmVMNetworkInterface](https://docs.microsoft.com/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?view=azurermps-3.8.0) e [New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm?view=azurermps-3.8.0). 

```powershell
$frontendVM = New-AzureRmVMConfig -VMName myFrontendVM -VMSize Standard_D1
$frontendVM = Set-AzureRmVMOperatingSystem -VM $frontendVM -Windows -ComputerName myFrontendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$frontendVM = Set-AzureRmVMSourceImage -VM $frontendVM -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
$frontendVM = Set-AzureRmVMOSDisk -VM $frontendVM -Name myFrontendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$frontendVM = Add-AzureRmVMNetworkInterface -VM $frontendVM -Id $frontendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $frontendVM
```

## <a name="install-web-server"></a>Instalar o servidor Web

Você pode instalar o IIS no `myFrontendVM` usando uma sessão de área de trabalho remota. Você precisa obter o endereço IP público da VM para acessá-la.

Você pode utilizar o endereço IP público da `myFrontendVM` com [Get-AzureRmPublicIPAddress](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermpublicipaddress?view=azurermps-3.8.0). O exemplo a seguir obtém o endereço IP para `myPublicIPAddress` criado anteriormente:

```powershell
Get-AzureRmPublicIPAddress -ResourceGroupName myRGNetwork -Name myPublicIPAddress | select IpAddress
```

Anote esse endereço IP para usá-lo em etapas futuras.

Use o seguinte comando para criar uma sessão de área de trabalho remota com `myFrontendVM`. Substitua `<publicIPAddress>` pelo endereço que você registrou anteriormente. Quando solicitado, insira as credenciais usadas quando você criou a VM.

```
mstsc /v:<publicIpAddress>
``` 

Agora que você fez logon na `myFrontendVM`, pode usar uma única linha do PowerShell para instalar o IIS e habilitar a regra de firewall local para permitir o tráfego da Web. Abra um promt do PowerShell e execute o seguinte comando:

Use [Install-WindowsFeature](https://technet.microsoft.com/itpro/powershell/windows/servermanager/install-windowsfeature) para executar a extensão do script personalizado que instala o servidor da Web do IIS:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Agora você pode usar o endereço IP público para navegar até a VM para ver o site do IIS.

![Site do IIS padrão](./media/tutorial-virtual-network/iis.png)

## <a name="manage-internal-traffic"></a>Gerenciar o tráfego interno

Um NSG (grupo de segurança de rede) contém uma lista de regras de segurança que permitem ou negam o tráfego de rede para recursos conectados a uma VNet. Os NSGs podem ser associados a sub-redes ou NICs individuais anexadas às VMs. Abrir ou fechar o acesso às VMs por meio de portas é feito usando regras de NSG. Quando você criou o `myFrontendVM`, a porta 3389 de entrada foi aberta automaticamente para conectividade RDP.

A comunicação interna de VMs pode ser configurada usando um NSG. Nesta seção, você aprenderá a criar uma sub-rede adicional na rede e atribuir um NSG a ela para permitir uma conexão de `myFrontendVM` para `myBackendVM` na porta 1433. A sub-rede é então atribuída à máquina virtual quando ela é criada.

Você pode limitar o tráfego interno para `myBackendVM` apenas de `myFrontendVM` criando um NSG para a sub-rede de back-end. O exemplo a seguir cria uma regra NSG chamada `myBackendNSGRule` com [New-AzureRmNetworkSecurityRuleConfig](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecurityruleconfig?view=azurermps-3.8.0):

```powershell
$nsgBackendRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myBackendNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 100 `
  -SourceAddressPrefix 10.0.0.0/24 `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 1433 `
  -Access Allow
```

Adicione um novo grupo de segurança de rede chamado `myBackendNSG` com [New-AzureRmNetworkSecurityGroup](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermnetworksecuritygroup?view=azurermps-3.8.0):

```powershell
$nsgBackend = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNSG `
  -SecurityRules $nsgBackendRule
```
## <a name="add-back-end-subnet"></a>Adicionar sub-rede de back-end

Adicionar `myBackEndSubnet` para `myVNet` com [Add-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0):

```powershell
Add-AzureRmVirtualNetworkSubnetConfig -Name myBackendSubnet `
  -VirtualNetwork $vnet `
  -AddressPrefix 10.0.1.0/24 `
  -NetworkSecurityGroup $nsgBackend
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName myRGNetwork -Name myVNet
```

## <a name="create-back-end-vm"></a>Criar VM de back-end

A maneira mais fácil de criar a máquina virtual de back-end é usar uma imagem do SQL Server. Este tutorial apenas cria a VM com o servidor de banco de dados, mas não fornece informações sobre como acessá-lo.

Criar `myBackendNic`:

```powershell
$backendNic = New-AzureRmNetworkInterface `
  -ResourceGroupName myRGNetwork `
  -Location westus `
  -Name myBackendNic `
  -SubnetId $vnet.Subnets[1].Id
```

Defina o nome de usuário e a senha necessários para a conta de administrador na VM com Get-Credential:

```powershell
$cred = Get-Credential
```

Criar `myBackendVM`:

```powershell
$backendVM = New-AzureRmVMConfig -VMName myBackendVM -VMSize Standard_D1
$backendVM = Set-AzureRmVMOperatingSystem -VM $backendVM -Windows -ComputerName myBackendVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$backendVM = Set-AzureRmVMSourceImage -VM $backendVM -PublisherName MicrosoftSQLServer -Offer SQL2016-WS2016 -Skus Enterprise -Version latest
$backendVM = Set-AzureRmVMOSDisk -VM $backendVM -Name myBackendOSDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
$backendVM = Add-AzureRmVMNetworkInterface -VM $backendVM -Id $backendNic.Id
New-AzureRmVM -ResourceGroupName myRGNetwork -Location westus -VM $backendVM
```

A imagem que é usada tem o SQL Server instalado, mas não é usada neste tutorial. Ela está incluída para mostrar como você pode configurar uma VM para lidar com o tráfego da Web e uma VM para lidar com o gerenciamento de banco de dados.


