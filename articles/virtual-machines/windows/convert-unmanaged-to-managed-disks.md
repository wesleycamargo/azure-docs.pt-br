---
title: "Converter uma VM do Windows de discos não gerenciados em Managed Disks - Azure | Microsoft Docs"
description: "Como converter uma VM do Windows de discos não gerenciados em Azure Managed Disks usando o PowerShell no modelo de implantação do Resource Manager"
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
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 636d4f7c5da72973a7837718cfb42dda93bba2cc
ms.contentlocale: pt-br
ms.lasthandoff: 07/01/2017

---

# <a name="convert-a-windows-vm-from-unmanaged-disks-to-azure-managed-disks"></a>Converter uma VM do Windows de discos não gerenciados em Azure Managed Disks

Se você tiver máquinas virtuais (VMs) do Windows (máquinas virtuais) existentes que usam discos não gerenciados, você pode converter as VMs para usar [Azure Managed Disks](../../storage/storage-managed-disks-overview.md). Esse processo converte o disco do sistema operacional e os discos de dados anexados.

Este artigo mostra como converter VMs com o Azure PowerShell. Se você precisa instalar ou atualizar, confira [Instalar e configurar o Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Antes de começar


* Revisão [Planejar a migração para os Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Converter VMs de instância única
Esta seção aborda como converter suas VMs de instância única do Azure de discos não gerenciados em discos gerenciados. (Consulte a próxima seção se suas VMs estão em uma conjunto de disponibilidade.) 

1. Desaloque a VM com o cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). O seguinte exemplo desaloca a VM `myVM` no grupo de recursos chamado `myResourceGroup`: 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Converter a VM em discos gerenciados com o cdmlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). O processo a seguir converte a VM anterior incluindo o disco do sistema operacional e eventuais discos de dados:

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Inicie a VM após a conversão em discos gerenciados com [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). O exemplo a seguir reinicia a VM anterior:

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Converter VMs em um conjunto de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

1. Converter o conjunto de disponibilidade com o cdmlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). O exemplo a seguir atualiza o conjunto de disponibilidade nomeado `myAvailabilitySet` no grupo de recursos nomeado`myResourceGroup`:

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Se a região em que o conjunto de disponibilidade está localizado tem apenas 2 domínios de falha gerenciados, mas o número de domínios de falha não gerenciado é 3, este comando mostra um erro semelhante à "O total de domínio de falha especificado é 3 e deve estar no intervalo de 1 a 2". Para resolver o erro, atualize o domínio de falha para 2 e atualize `Sku` para `Aligned` da seguinte maneira:

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Desaloque e converta as VMs no conjunto de disponibilidade. O script a seguir desaloca cada VM com o cdmlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), converte-o com [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk)e reiniciá-lo com [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm).

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  }
  ```


## <a name="convert-standard-managed-disks-to-premium"></a>Converter discos gerenciados padrão para Premium
Depois de converter sua VM em discos gerenciados, você também pode alternar entre os tipos de armazenamento. Você também pode ter uma combinação de discos que usam o armazenamento standard e Premium. No exemplo a seguir, mostramos como alternar do armazenamento padrão para o Premium. Para usar o Managed Disks Premium, sua VM deve usar um [tamanho VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que suporta armazenamento Premium.

```powershell
$rgName = 'myResourceGroup'
$vmName = 'YourVM'
$size = 'Standard_DS2_v2'
$vm = Get-AzureRmVM -Name $vmName -rgName $resourceGroupName

# Stop deallocate the VM before changing the size
Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force

# Change VM size to a size supporting Premium storage
$vm.HardwareProfile.VmSize = $size
Update-AzureRmVM -VM $vm -ResourceGroupName $rgName

# Get all disks in the resource group of the VM
$vmDisks = Get-AzureRmDisk -ResourceGroupName $rgName 

# For disks that belong to the VM selected, convert to Premium storage
foreach ($disk in $vmDisks)
{
    if ($disk.OwnerId -eq $vm.Id)
    {
        $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
        Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $rgName `
        -DiskName $disk.Name
    }
}

Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
```

## <a name="troubleshooting"></a>Solucionar problemas

Se houver um erro durante a conversão, ou se uma VM está em um estado de falha devido a problemas na conversão anterior, execute o `ConvertTo-AzureRmVMManagedDisk` cmdlet novamente. Normalmente, uma repetição simples desbloqueia a situação.


## <a name="managed-disks-and-azure-storage-service-encryption"></a>Managed Disks e Criptografia de Serviço de Armazenamento do Azure

Você não pode usar as etapas anteriores para converter um disco não gerenciado em um disco gerenciado se o disco não gerenciado está em uma conta de armazenamento que esteve usando [Criptografia de Serviço de Armazenamento do Azure](../../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). As etapas a seguir especificam como copiar e usar discos não gerenciados que estiveram em uma conta de armazenamento criptografada:

1. Copiar o disco rígido virtual (VHD) usando [AzCopy](../../storage/storage-use-azcopy.md) para uma conta de armazenamento que nunca esteve habilitada para criptografia de serviço de armazenamento do Azure.

2. Use a VM copiada de uma das seguintes maneiras:

  * Crie uma VM que usa discos gerenciados e especifique este arquivo VHD durante a criação com `New-AzureRmVm`

  * Anexe o VHD copiado com `Add-AzureRmVmDataDisk` para uma VM em execução com discos gerenciados


## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).


