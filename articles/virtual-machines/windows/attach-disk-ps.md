---
title: Anexar um disco de dados a uma VM do Windows no Azure usando o PowerShell | Microsoft Docs
description: "Como anexar novos discos de dados, ou existente, a uma VM do Windows usando o PowerShell com o modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 19a95e0299f49d908fa666f23637f5a4cc1d9c96
ms.lasthandoff: 04/27/2017


---

# <a name="attach-a-data-disk-to-a-windows-vm-using-powershell"></a>Anexar um disco de dados a uma VM do Windows usando o PowerShell

Este artigo mostra como anexar discos novos e existentes a uma máquina virtual Windows usando PowerShell. Se sua VM usar discos gerenciados, anexe discos de dados gerenciados adicionais. Você também pode anexar discos de dados não gerenciados a uma VM que utiliza discos não gerenciados em uma conta de armazenamento.

Antes de fazer isso, revise estas dicas:
* O tamanho da máquina virtual controla quantos discos de dados você pode anexar a ela. Para obter detalhes, consulte [Tamanhos das máquinas virtuais](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Para usar o Armazenamento Premium, você precisará de uma VM habilitada para Armazenamento Premium, como a série DS ou GS. Você pode usar discos de contas de armazenamento Premium e Standard com essas máquinas virtuais. O armazenamento Premium está disponível em determinadas regiões. Para obter detalhes, confira [Armazenamento Premium: armazenamento de alto desempenho para as cargas de trabalho das máquinas virtuais do Azure](../../storage/storage-premium-storage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


## <a name="add-an-empty-data-disk-to-a-virtual-machine"></a>Adicionar um disco de dados vazio a uma máquina virtual

Este exemplo mostra como adicionar um disco de dados vazio a uma máquina virtual existente.

### <a name="using-managed-disks"></a>Usar discos gerenciados

```powershell
$rgName = 'myResourceGroup'
$vmName = 'myVM'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk1'

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Empty -DiskSizeGB 128

$dataDisk1 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

### <a name="using-unmanaged-disks-in-a-storage-account"></a>Usar discos não gerenciados em uma conta de armazenamento

```powershell
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm
```


### <a name="initialize-the-disk"></a>Inicializar o disco

Depois de adicionar um disco vazio, é necessário inicializá-lo. Para inicializar o disco, você pode fazer logon em uma VM e usar o gerenciamento de disco. Se você habilitou o WinRM e um certificado na VM durante a criação, você pode usar o PowerShell remoto para inicializar o disco. Você também pode usar uma extensão de script personalizado: 

```powershell
    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"
```
        
O arquivo de script pode conter algo parecido com este código para inicialização dos discos:

```powershell
    $disks = Get-Disk | Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { [char]$_ }
    $count = 0
    $labels = "data1","data2"

    foreach ($disk in $disks) {
        $driveLetter = $letters[$count].ToString()
        $disk | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] -Confirm:$false -Force
    $count++
    }
```


## <a name="attach-an-existing-data-disk-to-a-vm"></a>Anexar um disco de dados existente a uma VM

Você também pode anexar um VHD existente como um disco de dados gerenciados para uma máquina virtual. 

### <a name="using-managed-disks"></a>Usar discos gerenciados

```powershell
$rgName = 'myRG'
$vmName = 'ContosoMdPir3'
$location = 'West Central US' 
$storageType = 'PremiumLRS'
$dataDiskName = $vmName + '_datadisk2'
$dataVhdUri = 'https://mystorageaccount.blob.core.windows.net/vhds/managed_data_disk.vhd' 

$diskConfig = New-AzureRmDiskConfig -AccountType $storageType -Location $location -CreateOption Import -SourceUri $dataVhdUri -DiskSizeGB 128

$dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName $rgName

$vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName 

$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk2.Id -Lun 2

Update-AzureRmVM -VM $vm -ResourceGroupName $rgName
```

## <a name="next-steps"></a>Próximas etapas

Criar um [instantâneo](snapshot-copy-managed-disk.md).

