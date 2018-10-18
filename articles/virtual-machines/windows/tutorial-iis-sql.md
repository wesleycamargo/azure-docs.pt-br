---
title: Tutorial - Criar VMs que executam uma pilha SQL&#47;IIS&#47;.NET no Azure | Microsoft Docs
description: Neste tutorial, você aprende a instalar a pilha Azure SQL, IIS, .NET em uma máquina virtual Windows no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 02/27/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: cb423fb43e2e7620aef989c0ead99f9bdb5a58e3
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389353"
---
# <a name="tutorial-install-the-sql47iis47net-stack-in-a-windows-vm-with-azure-powershell"></a>Tutorial: Instalar a pilhaSQL&#47;IIS&#47;.NET em uma VM do Windows com o Microsoft Azure PowerShell

Neste tutorial, vamos instalar uma pilha SQL&#47;IIS&#47;.NET usando o Azure PowerShell. Essa pilha consiste em duas VMs que executam o Windows Server 2016, uma com o IIS e o .NET e a outra com o SQL Server.

> [!div class="checklist"]
> * Criar uma máquina virtual 
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo AzureRM.Compute versão 5.7.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM.Compute` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="create-a-iis-vm"></a>Criar uma VM do IIS 

Neste exemplo, usamos o cmdlet [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) no PowerShell Cloud Shell para criar rapidamente uma VM do Windows Server 2016 e então instalar o IIS e o .NET Framework. As VMs do IIS e do SQL compartilham um grupo de recursos e a rede virtual e assim criamos variáveis para esses nomes.


```azurepowershell-interactive
$vmName = "IISVM"
$vNetName = "myIISSQLvNet"
$resourceGroup = "myIISSQLGroup"
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name $vmName `
    -Location "East US" `
    -VirtualNetworkName $vNetName `
    -SubnetName "myIISSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -AddressPrefix 192.168.0.0/16 `
    -PublicIpAddressName "myIISPublicIpAddress" `
    -OpenPorts 80,3389 
```

Instale o IIS e o .NET Framework usando a extensão de script personalizado.

```azurepowershell-interactive
Set-AzureRmVMExtension `
    -ResourceGroupName $resourceGroup `
    -ExtensionName IIS `
    -VMName $vmName `
    -Publisher Microsoft.Compute `
    -ExtensionType CustomScriptExtension `
    -TypeHandlerVersion 1.4 `
    -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server,Web-Asp-Net45,NET-Framework-Features"}' `
    -Location EastUS
```

## <a name="create-another-subnet"></a>Criar outra sub-rede

Crie uma segunda sub-rede para a VM SQL. Obtenha a rede virtual usando [Get-AzureRmVirtualNetwork]{/powershell/module/azurerm.network/get-azurermvirtualnetwork}.

```azurepowershell-interactive
$vNet = Get-AzureRmVirtualNetwork `
   -Name $vNetName `
   -ResourceGroupName $resourceGroup
```

Crie uma configuração para a sub-rede usando [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig).


```azurepowershell-interactive
Add-AzureRmVirtualNetworkSubnetConfig `
   -AddressPrefix 192.168.0.0/24 `
   -Name mySQLSubnet `
   -VirtualNetwork $vNet `
   -ServiceEndpoint Microsoft.Sql
```

Atualize a rede virtual com as informações da nova sub-rede usando [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork)
   
```azurepowershell-interactive   
$vNet | Set-AzureRmVirtualNetwork
```

## <a name="azure-sql-vm"></a>VM de SQL do Azure

Use uma imagem pré-configurada do Azure marketplace de um servidor SQL para criar a VM SQL. Primeiro criamos a VM, em seguida, instalamos a Extensão do SQL Server na VM. 


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName $resourceGroup `
    -Name "mySQLVM" `
    -ImageName "MicrosoftSQLServer:SQL2016SP1-WS2016:Enterprise:latest" `
    -Location eastus `
    -VirtualNetworkName $vNetName `
    -SubnetName "mySQLSubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "mySQLPublicIpAddress" `
    -OpenPorts 3389,1401 
```

Use [Set-AzureRmVMSqlServerExtension](/powershell/module/azurerm.compute/set-azurermvmsqlserverextension) para adicionar a [extensão do SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) à VM do SQL.

```azurepowershell-interactive
Set-AzureRmVMSqlServerExtension `
   -ResourceGroupName $resourceGroup  `
   -VMName mySQLVM `
   -Name "SQLExtension" `
   -Location "EastUS"
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você instalou uma pilha SQL&#92;IIS&#92;.NET usando o Azure PowerShell. Você aprendeu como:

> [!div class="checklist"]
> * Criar uma máquina virtual 
> * Instalar o IIS e o SDK do .NET Core na VM
> * Criar uma VM que executa o SQL Server
> * Instalar a extensão do SQL Server

Vá para o próximo tutorial para saber como proteger um servidor Web do IIS com certificados SSL.

> [!div class="nextstepaction"]
> [Proteger o servidor Web do IIS com certificados SSL](tutorial-secure-web-server.md)

