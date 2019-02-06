---
title: Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa | Microsoft Docs
description: Como converter os Managed Disks do Azure de padrão em premium, e vice-versa, usando o Azure PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: ramankumarlive
manager: kavithag
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/04/2018
ms.author: ramankum
ms.subservice: disks
ms.openlocfilehash: 4c13708ad785a2291da3db61d739f604a2c3bb88
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55475883"
---
# <a name="update-the-storage-type-of-a-managed-disk"></a>Atualizar o tipo de armazenamento de um disco gerenciado

O Azure Managed Disks oferece três opções de tipo de armazenamento: [SSD Premium](../windows/premium-storage.md), [SSD Standard](../windows/disks-standard-ssd.md) e [HDD Standard](../windows/standard-storage.md). Você pode alternar um disco gerenciado entre tipos de armazenamento com tempo de inatividade mínimo, com base nas suas necessidades de desempenho. Alternar entre tipos de armazenamento não é suportado por um disco não gerenciado; no entanto, você pode facilmente [converter um disco não gerenciado em um disco gerenciado](convert-unmanaged-to-managed-disks.md).

Este artigo mostra como converter um disco gerenciado de padrão para premium e vice-versa, usando o Azure PowerShell. Se você precisar instalar ou atualizar o PowerShell, consulte [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.8.1).

## <a name="prerequisites"></a>Pré-requisitos

* Como a conversão exige uma reinicialização da máquina virtual (VM), você deve agendar a migração do armazenamento de discos durante uma janela de manutenção pré-existente. 
* Se você estiver usando um disco não gerenciado, primeiro [converta-o em um disco gerenciado](convert-unmanaged-to-managed-disks.md) para permitir alterná-lo entre os tipos de armazenamento. 
* Os exemplos neste artigo exigem o módulo Azure PowerShell versão 6.0.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Execute [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) para criar uma conexão com o Azure.


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium"></a>Converter todos os discos gerenciados de uma VM de standard para premium

O exemplo a seguir mostra como alternar todos os discos de uma VM de armazenamento padrão para premium. Para usar discos gerenciados por premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte o armazenamento premium. Este exemplo também muda para um tamanho que suporte armazenamento premium:

```azurepowershell-interactive
# Name of the resource group that contains the VM
$rgName = 'yourResourceGroup'

# Name of the your virtual machine
$vmName = 'yourVM'

# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'

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

## <a name="convert-a-managed-disk-from-standard-to-premium"></a>Converter um disco gerenciado de padrão em premium

Para sua carga de trabalho de desenvolvimento/teste, você poderá ter uma combinação de discos standard e premium para reduzir os custos. Para fazer isso, atualize somente os discos que requerem melhor desempenho para o armazenamento Premium. O exemplo a seguir mostra como alternar um único disco de uma VM de armazenamento padrão para premium e vice-versa. Para usar discos gerenciados por premium, sua VM deve usar um [tamanho de VM](sizes.md) que suporte o armazenamento premium. Este exemplo também mostra como alternar para um tamanho que dá suporte a armazenamento premium:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between StandardLRS and PremiumLRS based on your scenario
$storageType = 'Premium_LRS'
# Premium capable size 
$size = 'Standard_DS2_v2'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

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

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd"></a>Converter um disco gerenciado de padrão HDD para SSD standard

O exemplo a seguir mostra como alternar um único disco de uma VM do HDD padrão para o SSD padrão e vice-versa:

```azurepowershell-interactive

$diskName = 'yourDiskName'
# resource group that contains the managed disk
$rgName = 'yourResourceGroupName'
# Choose between Standard_LRS and StandardSSD_LRS based on your scenario
$storageType = 'StandardSSD_LRS'

$disk = Get-AzureRmDisk -DiskName $diskName -ResourceGroupName $rgName

# Get parent VM resource
$vmResource = Get-AzureRmResource -ResourceId $disk.ManagedBy

# Stop and deallocate the VM before changing the storage type
Stop-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name -Force

$vm = Get-AzureRmVM -ResourceGroupName $vmResource.ResourceGroupName -Name $vmResource.Name 

# Update the storage type
$diskUpdateConfig = New-AzureRmDiskUpdateConfig -AccountType $storageType -DiskSizeGB $disk.DiskSizeGB
Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
-DiskName $disk.Name

Start-AzureRmVM -ResourceGroupName $vm.ResourceGroupName -Name $vm.Name
```

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando um [snapshot](snapshot-copy-managed-disk.md).

