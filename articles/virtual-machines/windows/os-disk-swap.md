---
title: Trocar o disco do SO para uma VM do Azure com o PowerShell | Microsoft Docs
description: Mude o disco do sistema operacional usado por uma máquina virtual do Azure usando o PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: cynthn
ms.openlocfilehash: caa8fe2088995e3d30c9b808f639b9280e3a74be
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
ms.locfileid: "32195949"
---
# <a name="change-the-os-disk-used-by-an-azure-vm-using-powershell"></a>Mudar o disco do sistema operacional usado por uma VM do Azure usando o PowerShell

Se você tiver uma VM existente, mas deseja trocar o disco para um disco de backup ou outro disco do sistema operacional, você pode usar o PowerShell do Azure para trocar os discos do sistema operacional. Você não precisa excluir e recriar a VM. Você pode até usar um disco gerenciado em outro grupo de recursos, desde que ele não esteja em uso.

A VM precisa ser interrompida\desalocada, em seguida, a ID de recurso de disco gerenciado pode ser substituída com a ID de recurso de um disco gerenciado diferente.

Certifique-se de que o tipo de armazenamento e tamanho VM são compatíveis com o disco que você deseja anexar. Por exemplo, se o disco que você deseja usar estiver no Armazenamento Premium, a VM precisa ter capacidade de Armazenamento Premium (como um tamanho da série DS). 

Obter uma lista de discos em um grupo de recursos usando [Get-AzureRmDisk](/powershell/module/azurerm.compute/get-azurermdisk)

```azurepowershell-interactive
Get-AzureRmDisk -ResourceGroupName myResourceGroup | Format-Table -Property Name
```
 
Quando você tiver o nome do disco que você deseja usar, defina esse disco como o disco do sistema operacional para a VM. Este exemplo interrompe\desaloca a VM denominada *myVM* e atribui o disco denominado *newDisk* como o novo disco de sistema operacional. 
 
```azurepowershell-interactive 
# Get the VM 
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM 

# Make sure the VM is stopped\deallocated
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name $vm.Name -Force

# Get the new disk that you want to swap in
$disk = Get-AzureRmDisk -ResourceGroupName myResourceGroup -Name newDisk

# Set the VM configuration to point to the new disk  
Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $disk.Id -Name $disk.Name 

# Update the VM with the new OS disk
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm 

# Start the VM
Start-AzureRmVM -Name $vm.Name -ResourceGroupName myResourceGroup

```

**Próximas etapas**

Para criar uma cópia de um disco, consulte [Instantâneo de um disco](snapshot-copy-managed-disk.md).