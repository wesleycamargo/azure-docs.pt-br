---
title: Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa | Microsoft Docs
description: Como converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa, usando a CLI do Azure.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: cynthn
ms.openlocfilehash: 0d777b5dcebfba7dbff7c9ea1f4fedad12b3cf1a
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283801"
---
# <a name="convert-azure-managed-disks-storage-from-standard-to-premium-and-vice-versa"></a>Converter o armazenamento de Managed Disks do Azure de padrão em premium, e vice-versa

O Managed Disks oferece três opções de armazenamento: [SSD Premium](../windows/premium-storage.md), SSD Padrão (Versão Prévia) e [HD Padrão](../windows/standard-storage.md). Ele permite alternar facilmente entre as opções com tempo de inatividade mínimo, com base nas suas necessidades de desempenho. Isso não dá suporte para discos não gerenciados. Porém, é possível [convertê-los em discos gerenciados](convert-unmanaged-to-managed-disks.md) facilmente para alternar com facilidade entre os tipos de disco.

Este artigo mostra como converter Managed Disks de padrão em premium, e vice-versa, usando a CLI do Azure. Se você precisar instalar ou atualizá-lo, confira [instalar a CLI do Azure](/cli/azure/install-azure-cli). 

## <a name="before-you-begin"></a>Antes de começar

* Como a conversão requer uma reinicialização da VM, programe a migração do armazenamento de discos durante uma janela de manutenção preexistente. 
* Se você estiver usando discos não gerenciados, primeiro [converta em discos gerenciados](convert-unmanaged-to-managed-disks.md), a fim de usar este artigo para alternar entre as opções de armazenamento. 


## <a name="convert-all-the-managed-disks-of-a-vm-from-standard-to-premium-and-vice-versa"></a>Converter todos os Managed Disks de uma VM de padrão em premium, e vice-versa

O exemplo a seguir mostra como alternar todos os discos de uma VM de armazenamento padrão para premium. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the virtual machine
rgName='yourResourceGroup'

#Name of the virtual machine
vmName='yourVM'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Deallocate the VM before changing the size of the VM
az vm deallocate --name $vmName --resource-group $rgName

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --resource-group $rgName --name $vmName --size $size

#Update the sku of all the data disks 
az vm show -n $vmName -g $rgName --query storageProfile.dataDisks[*].managedDisk -o tsv \
 | awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

#Update the sku of the OS disk
az vm show -n $vmName -g $rgName --query storageProfile.osDisk.managedDisk -o tsv \
| awk -v sku=$sku '{system("az disk update --sku "sku" --ids "$1)}'

az vm start --name $vmName --resource-group $rgName

```
## <a name="convert-a-managed-disk-from-standard-to-premium-and-vice-versa"></a>Converter um Managed Disk de padrão em premium, e vice-versa

Para sua carga de trabalho de desenvolvimento/teste, convém ter uma combinação de discos padrão e premium para reduzir os custos. Isso é possível atualizando para o armazenamento premium, somente os discos que requerem melhor desempenho. O exemplo a seguir mostra como alternar um único disco de uma VM de armazenamento padrão para premium e vice-versa. Para usar Managed Disks Premium, sua VM deve usar um [tamanho da VM](sizes.md) que dá suporte a armazenamento Premium. Este exemplo também pode mudar para um tamanho que dá suporte a armazenamento Premium.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Premium capable size 
#Required only if converting from standard to premium
size='Standard_DS2_v2'

#Choose between Standard_LRS and Premium_LRS based on your scenario
sku='Premium_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the size of the VM
az vm deallocate --ids $vmId 

#Change the VM size to a size that supports premium storage 
#Skip this step if converting storage from premium to standard
az vm resize --ids $vmId --size $size

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-a-managed-disk-from-standard-hdd-to-standard-ssd-and-vice-versa"></a>Converter um disco gerenciado de HD padrão para SSD padrão e vice-versa

O exemplo a seguir mostra como alternar um único disco de uma VM do HD Standard para SSD Standard.

 ```azurecli

#resource group that contains the managed disk
rgName='yourResourceGroup'

#Name of your managed disk
diskName='yourManagedDiskName'

#Choose between Standard_LRS and StandardSSD_LRS based on your scenario
sku='StandardSSD_LRS'

#Get the parent VM Id 
vmId=$(az disk show --name $diskName --resource-group $rgName --query managedBy --output tsv)

#Deallocate the VM before changing the disk type
az vm deallocate --ids $vmId 

# Update the sku
az disk update --sku $sku --name $diskName --resource-group $rgName 

az vm start --ids $vmId 
```

## <a name="convert-using-the-azure-portal"></a>Converter usando o portal do Azure

Também é possível converter discos não gerenciados em discos gerenciados usando o portal do Azure.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Selecione a VM na lista de VMs no portal.
3. Na folha da VM, selecione **Discos** no menu.
4. Na parte superior da folha **Discos**, selecione **Migrar para discos gerenciados**.
5. Se sua VM estiver em um conjunto de disponibilidade, haverá um aviso na folha **Migrar para discos gerenciados** informando que você precisa converter o conjunto de disponibilidade primeiro. O aviso deve ter um link em que você pode clicar para converter o conjunto de disponibilidade. Quando o conjunto de disponibilidade for convertido ou se sua VM não estiver em um conjunto de disponibilidade, clique em **Migrar** para iniciar o processo de migração de seus discos para discos gerenciados. 

A VM será interrompida e reiniciada após a conclusão da migração.

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](snapshot-copy-managed-disk.md).

