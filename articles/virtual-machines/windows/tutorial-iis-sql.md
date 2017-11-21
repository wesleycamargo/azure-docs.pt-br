---
title: Crie VMs que executam uma pilha SQL&#92;IIS&#92;.NET no Azure | Microsoft Docs
description: "Tutorial - instalar uma pilha SQL IIS, .NET do Azure máquinas virtuais do Windows."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/24/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6f7ef46d9c40138c211427845423783fefde5dc3
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="install-a-sql92iis92net-stack-in-azure"></a>Instalar uma pilha SQL&#92;IIS&#92;.NET no Azure

Neste tutorial, vamos instalar uma pilha SQL&#92;IIS&#92;.NET usando o Azure PowerShell. Essa pilha consiste em duas VMs que executam o Windows Server 2016, uma com o IIS e o .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma máquina virtual usando New-AzVM
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server



## <a name="create-a-iis-vm"></a>Criar uma VM do IIS 

Neste exemplo, usamos o cmdlet [New-AzVM](https://www.powershellgallery.com/packages/AzureRM.Compute.Experiments) no PowerShell Cloud Shell para criar rapidamente uma VM do Windows Server 2016 e então instalar o IIS e o .NET Framework. As VMs do IIS e do SQL compartilham um grupo de recursos e a rede virtual e assim criamos variáveis para esses nomes.

Clique no botão **Experimente** no canto superior direito do bloco de código para iniciar o Cloud Shell nesta janela. Você será solicitado a fornecer credenciais para a máquina virtual no prompt de comando.

```azurepowershell-interactive
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzVm -Name myIISVM -ResourceGroupName $resourceGroup -VirtualNetworkName $vNetName 
```

Instale o IIS e o .NET Framework usando a extensão de script personalizado.

```azurepowershell-interactive

Set-AzureRmVMExtension -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName myIISVM `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="azure-sql-vm"></a>VM de SQL do Azure

Usamos uma imagem pré-configurada do Azure marketplace de um servidor SQL para criar a VM de SQL. Primeiro criamos a VM, em seguida, instalamos a Extensão do SQL Server na VM. 


```azurepowershell-interactive
# Create user object. You get a pop-up prompting you to enter the credentials for the VM.
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a subnet configuration
$vNet = Get-AzureRmVirtualNetwork -Name $vNetName -ResourceGroupName $resourceGroup
Add-AzureRmVirtualNetworkSubnetConfig -Name mySQLSubnet -VirtualNetwork $vNet -AddressPrefix "192.168.2.0/24"
Set-AzureRmVirtualNetwork -VirtualNetwork $vNet


# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location eastus `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow


# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location eastus `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name mySQLNic -ResourceGroupName $resourceGroup -Location eastus `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName mySQLVM -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName mySQLVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftSQLServer -Offer SQL2014SP2-WS2012R2 -Skus Enterprise -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM -ResourceGroupName $resourceGroup -Location eastus -VM $vmConfig
```

Use [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) para adicionar a [extensão do SQL Server](/sql/virtual-machines-windows-sql-server-agent-extension.md) à VM do SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension -ResourceGroupName $resourceGroup -VMName mySQLVM -name "SQLExtension"
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você instalou uma pilha SQL&#92;IIS&#92;.NET usando o Azure PowerShell. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma máquina virtual usando New-AzVM
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server

Vá para o próximo tutorial para saber como proteger um servidor Web do IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web do IIS com certificados SSL](tutorial-secure-web-server.md)

