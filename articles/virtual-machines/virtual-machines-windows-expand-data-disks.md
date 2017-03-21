---
title: Expandir um disco de dados anexado a uma VM Windows no Azure | Microsoft Docs
description: "Expanda o tamanho de um disco de dados que está anexado a uma máquina virtual Windows usando o PowerShell."
services: virtual-machines-windows
documentationcenter: na
author: cynthn
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/02/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: f7816f6e2120c02d970de640b397a1d2d570e35d
ms.lasthandoff: 03/04/2017


---

# <a name="increase-the-size-of-a-data-disk-attached-to-a-windows-vm"></a>Aumentar o tamanho de um disco de dados anexado a uma VM Windows

Se você precisar aumentar o tamanho do disco de dados anexado à máquina virtual, será possível aumentar o tamanho usando o PowerShell. Depois de aumentar o tamanho do disco de dados nas configurações de VM do Azure, também é necessário alocar o novo espaço em disco na VM.


## <a name="use-powershell-to-increase-the-size-of-a-managed-data-disk"></a>Usar o PowerShell para aumentar o tamanho de um disco de dados gerenciado

Para aumentar o tamanho de um disco de dados gerenciado, use os seguintes cmdlets do PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMReseourceGroup](/powershell/Get-AzureRMReseourceGroup) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |
<br>

O script a seguir explicará como obter as informações da VM, selecionando o disco de dados e especificando o novo tamanho.

```powershell
# Select resource group
     
    $rg = Get-AzureRMReseourceGroup | Out-GridView `
        -Title "Select the resource group" `
        -PassThru
     
    $rgName = $rg.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM -ResourceGroupName $rgName `
        | Out-GridView `
            -Title "Select a VM" `
             -PassThru

# Select data disk
     
    $disk = $vm.dataDiskNames | Out-GridView `
        -Title "Select a data disk" `
        -PassThru
    
# Specify a larger size for the data disk 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size
    
    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" -DiskSizeInGB $size

# View the new size of the data disk(s)
    
    $vm.StorageProfile.DataDisks

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    
    Start-AzureRmVM -ResourceGroupName $rgName -VMName $vm.name

```

## <a name="use-powershell-to-increase-the-size-of-an-unmanaged-data-disk"></a>Usar o PowerShell para aumentar o tamanho de um disco de dados não gerenciado

Para aumentar o tamanho de discos de dados não gerenciados em uma conta de armazenamento, use os seguintes cmdlets do PowerShell:

|                                                                    |                                                            |
|--------------------------------------------------------------------|------------------------------------------------------------|
| [Get-AzureRMStorageAccount](/powershell/Get-AzureRMStorageAccount) | [Get-AzureRMVM](/powershell/getazurermvm)                  |
| [Stop-AzureRMVM](/powershell/stop-azurermvm)                       | [Set-AzureRmVMDataDisk](/powershell/Set-AzureRmVMDataDisk) |
| [Update-AzureRmVM](/powershell/update-azurermvm)                   | [Start-AzureRmVM](/powershell/start-azurermvm)             |

<br>

O script a seguir explicará como obter as informações da VM e da conta de armazenamento, selecionando o disco de dados e especificando o novo tamanho.

```powershell

# Select Azure Storage Account
     
    $storageAccount =
        Get-AzureRMStorageAccount | Out-GridView `
            -Title "Select Azure Storage Account" `
            -PassThru
     
    $rgName = $storageAccount.ResourceGroupName

# Select the VM
     
    $vm = Get-AzureRMVM `
    -ResourceGroupName $rgName | Out-GridView `
            -Title "Select a VM …" `
            -PassThru

# Select Data Disk to resize
     
    $disk =
        $vm.DataDiskNames | Out-GridView `
            -Title "Select a data disk to resize" `
            -PassThru
     
    
# Specify a larger data disk size 
       
    $size =  Read-Host `
        -Prompt "New size in GB"

# Stop and Deallocate VM prior to resizing data disk
     
    $vm | Stop-AzureRMVM -Force

# Set the new disk size

    Set-AzureRmVMDataDisk -VM $vm -Name "$disk" `
        -DiskSizeInGB $size

# Update the configuration in Azure
    
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Start the VM
    Start-AzureRmVM -ResourceGroupName $rgName `
    -VMName $vm.name
    
```

## <a name="allocate-the-unallocated-disk-space"></a>Alocar o espaço em disco não alocado 

Depois de aumentar a unidade, você precisa alocar o novo espaço não alocado na VM. Para alocar o espaço, é possível conectar-se à VM usando o Gerenciamento de Disco (diskmgmt.msc). Ou se você habilitou o WinRM e um certificado na VM durante a criação, é possível usar o PowerShell remoto para inicializar o disco. Você também pode usar uma extensão de script personalizado: 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
O arquivo de script pode conter algo parecido com este código para aumentar a alocação de unidade para o tamanho máximo dos discos:

```powershell
$driveLetter= "F"

$MaxSize = (Get-PartitionSupportedSize -DriveLetter $driveLetter).sizeMax

Resize-Partition -DriveLetter $driveLetter -Size $MaxSize
```

## <a name="next-steps"></a>Próximas etapas
- [Saiba mais sobre discos e VHDs](../storage/storage-about-disks-and-vhds-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
