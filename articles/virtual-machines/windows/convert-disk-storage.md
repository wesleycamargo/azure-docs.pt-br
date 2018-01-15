---
title: "Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa | Microsoft Docs"
description: "Como converter os Managed Disks do Azure de padrão em premium, e vice-versa, usando o Azure PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: ramankum
manager: kavithag
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/07/2017
ms.author: ramankum
ms.openlocfilehash: 407cfe7d9eee4e226938f383c04bb359a17290fc
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2018
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa

Os Managed Disks oferecem duas opções de armazenamento: [Premium](premium-storage.md) (baseado em SSD) e [Padrão](standard-storage.md) (baseado em HDD). O recurso permite alternar facilmente entre as duas opções com mínimo tempo de inatividade de acordo com suas necessidades de desempenho. Ele não está disponível para discos não gerenciados. Porém, você pode [convertê-los em Managed Disks](convert-unmanaged-to-managed-disks.md) facilmente para alternar com facilidade entre as duas opções.

Este artigo mostra como converter Managed Disks de padrão em premium, e vice-versa, usando o Azure PowerShell. Se você precisa instalá-lo ou atualizá-lo, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Antes de começar

* Como a conversão requer uma reinicialização da VM, programe a migração do armazenamento de discos durante uma janela de manutenção preexistente. 
* Se você estiver usando discos não gerenciados, primeiramente [converta-os em Managed Disks](convert-unmanaged-to-managed-disks.md) a fim de usar este artigo para alternar entre as duas opções de armazenamento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter todos os Managed Disks de uma VM de padrão em premium, e vice-versa

No exemplo a seguir, mostramos como alternar todos os discos de uma VM de armazenamento padrão para premium. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'

# Premium capable size
# Required only if converting storage from standard to premium
$size = 'Standard_DS2_v2'

# Stop and deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

$vm = Get-AzureRmVM -Name $vmName -resourceGroupName $rgName

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the selected VM, convert to premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.ManagedBy -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType $storageType
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um Managed Disk de padrão em premium, e vice-versa

Para sua carga de trabalho de desenvolvimento/teste, convém ter uma combinação de discos padrão e premium para reduzir os custos. Isso é possível atualizando para o armazenamento premium, somente os discos que requerem melhor desempenho. No exemplo a seguir, mostramos como alternar um único disco de uma VM de armazenamento padrão para premium, e vice-versa. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'PremiumLRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get the ARM resource to get name and resource group of the VM
$vmResource = Get-AzureRmResource -ResourceId $disk.diskId

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name -Force

$vm = Get-AzureRmVM $vmResource.ResourceGroupName -Name $vmResource.ResourceName 

# Change the VM size to a size that supports premium storage
# Skip this step if converting storage from premium to standard
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).

