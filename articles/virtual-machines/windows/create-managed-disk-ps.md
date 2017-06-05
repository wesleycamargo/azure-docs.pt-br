---
title: Criar um disco gerenciado de um VHD no Azure | Microsoft Docs
description: "Crie um disco gerenciado de um VHD localizado atualmente em uma conta de armazenamento do Azure, usando o modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: 46b7b2a6fc3413e798cbad3e35837d1d52b63a2d
ms.contentlocale: pt-br
ms.lasthandoff: 05/18/2017


---

# <a name="create-managed-disks-from-unmanaged-disks-in-a-storage-account"></a>Criar discos gerenciados a partir de discos não gerenciados em uma conta de armazenamento

É possível criar um disco gerenciado a partir de um disco de dados existente ou de um disco de sistema operacional que está localizado atualmente em uma conta de armazenamento do Azure. Também é possível criar um disco vazio que pode ser usado como um novo disco de dados para uma VM. 

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


## <a name="create-a-managed-disk-from-a-vhd-in-an-azure-storage-account"></a>Criar um disco gerenciado de um VHD em uma conta de armazenamento do Azure

No exemplo, criamos um disco a partir de um VHD como disco gerenciado e o atribuímos ao parâmetro **$disk1** para uso posterior. 

O disco gerenciado será criado na localização **Oeste dos EUA**, em um grupo de recursos chamado **myResourceGroup**. O disco receberá o nome **myDisk** e será criado a partir de um arquivo VHD chamado **myDisk.vhd** carregado anteriormente em uma conta de armazenamento chamada **mystorageaccount**. O disco gerenciado será criado em um armazenamento LRS (localmente redundante) premium. StandardLRS e PremiumLRS são as únicas opções de **- AccountType** disponíveis para discos gerenciados. 

1.  Defina alguns parâmetros

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $diskName = "myDisk"
    $vhdUri = "https://mystorageaccount.blob.core.windows.net/vhds/myDisk.vhd"
    ```

2. Crie o disco de dados. 
    ```powershell
    $disk1 = New-AzureRmDisk -DiskName $diskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Import -SourceUri $vhdUri) -ResourceGroupName $rgName
    ```
    
    

## <a name="create-an-empty-data-disk-as-a-managed-disk"></a>Criar um disco de dados vazio como um disco gerenciado

No exemplo, criamos um disco de dados vazio como disco gerenciado e o atribuímos ao parâmetro **$dataDisk2** para uso posterior. Será necessário inicializar um disco de dados vazio fazendo logon na VM e usando diskmgmt.msc ou [usando remotamente um script e WinRM](attach-disk-ps.md#initialize-the-disk), após anexá-lo a uma VM em execução.

O disco de dados vazio será criado na localização **Centro-Oeste dos EUA**, em um grupo de recursos chamado **myResourceGroup**. O disco receberá o nome **myEmptyDataDisk**. O disco vazio será criado em um armazenamento LRS (localmente redundante) premium. StandardLRS e PremiumLRS são as únicas opções de **- AccountType** disponíveis para discos gerenciados.

O tamanho do disco neste exemplo é de 128 GB, mas você deve escolher um tamanho que atenda às necessidades dos aplicativos em execução em sua VM.

1.  Defina alguns parâmetros

    ```powershell
    $rgName = "myResourceGroup"
    $location = "West Central US"
    $dataDiskName = "myEmptyDataDisk"
    ```

2. Crie o disco de dados.
    ```powershell
    $dataDisk2 = New-AzureRmDisk -DiskName $dataDiskName -Disk (New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128) -ResourceGroupName $rgName
    ```
    
## <a name="next-steps"></a>Próximas etapas    
- Se você já tiver uma máquina virtual, poderá [anexar um disco de dados](attach-disk-portal.md).

