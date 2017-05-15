---
title: Gerenciar discos do Azure com o Azure PowerShell | Microsoft Docs
description: "Tutorial – Gerenciar discos do Azure com o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: d68fc54f3fd08b3d7d66a9cd1ddb91c340191468
ms.contentlocale: pt-br
ms.lasthandoff: 05/03/2017

---

# <a name="manage-azure-disks-with-powershell"></a>Gerenciar discos do Azure com o PowerShell

Neste tutorial, você aprenderá sobre os diferentes tipos de disco de VM, como selecionar uma configuração de disco e como criar e anexar discos a VMs do Azure. Este tutorial também aborda como tirar instantâneos de disco.  

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [Azure PowerShell](/powershell/azure/overview).

## <a name="default-azure-disks"></a>Discos padrão do Azure

Quando uma máquina virtual do Azure é criada, dois discos são automaticamente anexados à máquina virtual. 

**Disco do sistema operacional**: os discos do sistema operacional podem ser dimensionados para até 1 terabyte e hospedar o sistema operacional das máquinas virtuais.  O disco do SO é atribuído à letra de unidade *c:* por padrão. A configuração de cache do disco do SO é otimizada para desempenho do SO. O disco do SO **não deve** hospedar aplicativos nem dados. Para aplicativos e dados, use um disco de dados, que é detalhado posteriormente neste artigo.

**Disco temporário**: discos temporários usam uma unidade de estado sólido localizada no mesmo host do Azure que a VM. Os discos temporários são altamente eficazes e podem ser usados para operações como o processamento de dados temporário. No entanto, se a VM for movida para um novo host, todos os dados armazenados em um disco temporário serão removidos. O tamanho do disco temporário é determinado pelo tamanho da máquina virtual. Os discos temporários são atribuídos à letra de unidade *d:* por padrão.

### <a name="temporary-disk-sizes"></a>Tamanhos do disco temporário

| Tipo | Tamanho da VM | Tamanho máximo do disco temporário |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A e D | 800 |
| [Computação otimizada](sizes-compute.md) | Série F | 800 |
| [Memória otimizada](../virtual-machines-windows-sizes-memory.md) | Série D e G | 6144 |
| [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md) | Série L | 5630 |
| [GPU](sizes-gpu.md) | Série N | 1440 |
| [Alto desempenho](sizes-hpc.md) | Séries A e H | 2000 |

## <a name="azure-data-disks"></a>Discos de dados do Azure

Os discos de dados extras podem ser adicionados para instalação de aplicativos e armazenamento de dados. Os discos de dados devem ser usados em qualquer situação onde o armazenamento de dados durável e responsivo é desejado. Cada disco de dados tem uma capacidade máxima de 1 terabyte. O tamanho da máquina virtual determina quantos discos de dados podem ser anexados a uma VM. Para cada núcleo da VM, podem ser anexados dois discos de dados. 

### <a name="max-data-disks-per-vm"></a>Máximo de discos de dados por VM

| Tipo | Tamanho da VM | Máximo de discos de dados por VM |
|----|----|----|
| [Propósito geral](sizes-general.md) | Série A e D | 32 |
| [Computação otimizada](sizes-compute.md) | Série F | 32 |
| [Memória otimizada](../virtual-machines-windows-sizes-memory.md) | Série D e G | 64 |
| [Armazenamento otimizado](../virtual-machines-windows-sizes-storage.md) | Série L | 64 |
| [GPU](sizes-gpu.md) | Série N | 48 |
| [Alto desempenho](sizes-hpc.md) | Série A e H | 32 |

## <a name="vm-disk-types"></a>Tipos de disco da máquina virtual

O Azure fornece dois tipos de disco.

### <a name="standard-disk"></a>Disco Standard

Armazenamento padrão é apoiado por HDDs e oferece armazenamento econômico e eficaz. Os discos Standard são ideais para uma carga de trabalho econômica de desenvolvimento e teste.

### <a name="premium-disk"></a>Disco Premium

Os discos Premium são apoiados por disco de baixa latência e alto desempenho baseado em SSD. Perfeitos para VMs que executam carga de trabalho de produção. O Armazenamento Premium dá suporte às VMs das séries DS, DSv2, GS e FS. Os discos Premium são apresentados em três tipos (P10, P20 e P30), o tamanho do disco determina o tipo de disco. Na seleção do tamanho de um disco, o valor é arredondado para o próximo tipo. Por exemplo, se o tamanho for inferior a 128 GB, o tipo de disco será P10, entre 129 e 512, será P20 e acima de 512, será P30. 

### <a name="premium-disk-performance"></a>Desempenho do disco Premium

|Tipo de disco de armazenamento Premium | P10 | P20 | P30 |
| --- | --- | --- | --- |
| Tamanho do disco (arredondado) | 128 GB | 512 GB | 1.024 GB (1 TB) |
| IOPS por disco | 500 | 2.300 | 5.000 |
Taxa de transferência por disco | 100 MB/s | 150 MB/s | 200 MB/s |

Embora a tabela acima identifique a IOPS máxima por disco, um nível mais alto de desempenho pode ser obtido com a distribuição de vários discos de dados. Por exemplo, 64 discos de dados podem ser anexados à VM Standard_GS5. Se cada um desses discos for dimensionado como um P30, será possível chegar a um máximo de 80.000 IOPS. Para obter informações detalhadas sobre o máximo de IOPS por VM, confira [Tamanhos da VM Linux](./sizes.md).

## <a name="create-and-attach-disks"></a>Criar e anexar discos

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

Crie a configuração inicial com [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/new-azurermdiskconfig). O exemplo a seguir configura um disco com tamanho de 128 gigabytes.

```powershell
$diskConfig = New-AzureRmDiskConfig -Location EastUS -CreateOption Empty -DiskSizeGB 128
```

Crie o disco de dados com o comando [New-AzureRmDisk](/powershell/module/azurerm.compute/new-azurermdisk).

```powershell
$dataDisk = New-AzureRmDisk -ResourceGroupName myResourceGroup -DiskName myDataDisk -Disk $diskConfig
```

Obtenha a máquina virtual à qual deseja adicionar o disco de dados com o comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm).

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Adicione o disco de dados à configuração da máquina virtual com o comando [Add-AzureRmVMDataDisk](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```powershell
$vm = Add-AzureRmVMDataDisk -VM $vm -Name myDataDisk -CreateOption Attach -ManagedDiskId $dataDisk.Id -Lun 1
```

Atualize a máquina virtual com o comando [Update-AzureRmVM](/powershell/module/azurerm.compute/add-azurermvmdatadisk).

```powershell
Update-AzureRmVM -ResourceGroupName myResourceGroup -VM $vm
```

## <a name="prepare-data-disks"></a>Preparar discos de dados

Depois que um disco é anexado à máquina virtual, o sistema operacional precisa ser configurado para usar o disco. O exemplo a seguir mostra como configurar manualmente o primeiro disco adicionado à VM. Esse processo também pode ser automatizado usando a [extensão de script personalizado](./tutorial-automate-vm-deployment.md).

### <a name="manual-configuration"></a>Configuração manual

Crie uma conexão RDP com a máquina virtual. Abra o PowerShell e execute este script.

```powershell
Get-Disk | Where partitionstyle -eq 'raw' | `
Initialize-Disk -PartitionStyle MBR -PassThru | `
New-Partition -AssignDriveLetter -UseMaximumSize | `
Format-Volume -FileSystem NTFS -NewFileSystemLabel "myDataDisk" -Confirm:$false
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre discos da máquina virtual. Vá para o próximo tutorial para saber como automatizar a configuração da máquina virtual.

[Automatizar a configuração da VM](./tutorial-automate-vm-deployment.md)

