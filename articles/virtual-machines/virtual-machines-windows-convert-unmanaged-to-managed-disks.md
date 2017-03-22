---
title: "Converter uma VM de discos não gerenciados para discos gerenciados - Azure | Microsoft Docs"
description: "Converter uma VM de discos não gerenciados em discos gerenciados usando o PowerShell no modelo de implantação do Resource Manager"
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
ms.date: 02/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 59798ae9412a7550c94f8fa67c39f504aad8d00c
ms.openlocfilehash: 3867c57d40a218c80403578d30cb999bf9f6cd38
ms.lasthandoff: 03/01/2017


---
# <a name="convert-a-vm-from-unmanaged-disks-to-managed-disks"></a>Converter uma VM de discos não gerenciados em discos gerenciados

Se você tiver VMs do Azure que usem discos não gerenciados nas contas de armazenamento e se quiser tirar proveito de [Managed Disks](../storage/storage-managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), você poderá converter as VMs. O processo converte o disco do sistema operacional e discos de dados anexado um discos não gerenciado que usa uma conta de armazenamento usando discos gerenciados. As VMs são desligadas e desalocadas, em seguida, usar o Powershell para converter a VM para usar discos gerenciados. Após a conversão, reinicie a máquina virtual e ele estará agora usando discos gerenciados.

Antes de começar, não se esqueça de ler [Plan for the migration to Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) (Planejar para a migração para Managed Disks).
Teste o processo de migração migrando uma máquina virtual de teste antes de executar a migração em produção, pois o processo de migração não é reversível.


> [!IMPORTANT] 
> Durante a conversão, você será desalocar a máquina virtual. Desalocar VM significa que ele terá um novo endereço IP quando ele é iniciado após a conversão. Se você tiver uma dependência em um IP fixo, use um IP reservado.


## <a name="managed-disks-and-azure-storage-service-encryption-sse"></a>Managed Disks e SSE (Criptografia de Serviço de Armazenamento do Azure)

Não é possível converter uma VM não gerenciada criada no modelo de implantação de Gerenciador de recursos para Managed Disks se qualquer um dos discos anexados não gerenciados está em uma conta de armazenamento, ou a qualquer momento foi, criptografada usando [criptografia de serviço de armazenamento do Azure (SSE)](../storage/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). As etapas a seguir especificam como converter VMs não gerenciadas que estão, ou estiveram, em uma conta de armazenamento criptografada:

**Discos de dados**:
1.    Desanexe o disco de dados da VM.
2.    Copie o VHD para uma conta de armazenamento que nunca foi habilitada para o SSE. Para copiar o disco para outra conta de armazenamento, use [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myDataDisk.vhd`
3.    Anexar o disco copiado para a máquina virtual e converter a VM.

**Disco do SO**:
1.    Pare desalocar a máquina virtual. Salve a configuração da VM, se necessário.
2.    Copie o VHD do sistema operacional para uma conta de armazenamento que nunca foi habilitada para o SSE. Para copiar o disco para outra conta de armazenamento, use [AzCopy](../storage/storage-use-azcopy.md):`AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:myVhd.vhd`
3.    Crie uma VM que utiliza discos gerenciados e anexar o arquivo VHD do disco do sistema operacional durante a criação.

## <a name="convert-vms-in-an-availability-set-to-managed-disks-in-a-managed-availability-set"></a>Converter máquinas virtuais em um conjunto de discos gerenciados em um conjunto de disponibilidade gerenciada de disponibilidade

Se as VMs que você deseja converter em discos gerenciados estão em um conjunto de disponibilidade, converta primeiro o conjunto de disponibilidade em um conjunto de disponibilidade gerenciado.

O script a seguir atualiza a conjunto de disponibilidade para ser um conjunto de disponibilidade gerenciada e, em seguida, desaloca, converte os discos e, em seguida, reiniciar cada VM na disponibilidade definida.

```powershell
$rgName = 'myResourceGroup'
$avSetName = 'myAvailabilitySet'

$avSet =  Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Managed

foreach($vmInfo in $avSet.VirtualMachinesReferences)
    {
   $vm =  Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}

   Stop-AzureRmVM -ResourceGroupName $rgName -Name  $vm.Name -Force

   ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
   
    }
```

## <a name="convert-existing-azure-vms-to-managed-disks-of-the-same-storage-type"></a>Converter as VMs do Azure existentes para discos gerenciados do mesmo tipo de armazenamento

> [!IMPORTANT]
> Depois de executar o procedimento a seguir, há um único blob que permanece no contêiner /vhds padrão. O nome do arquivo é "VMName.xxxxxxx.status". Esse arquivo é criado pelo Azure apenas quando você instalou [extensões de VM](virtual-machines-windows-classic-agents-and-extensions.md) na VM. Não exclua esse objeto de status restante. Um trabalho futuro deverá resolver esse problema.

Esta seção aborda como converter suas VMs do Azure existente de discos não gerenciados nas contas de armazenamento para discos gerenciados quando estiver usando o mesmo tipo de armazenamento. Você pode usar este processo para ir de discos (SSD) não gerenciados Premium para discos gerenciados Premium, ou de discos (HDD) não gerenciados standard para discos gerenciados standard. 

1. Criar variáveis e desalocar a máquina virtual. Este exemplo define o nome do grupo de recursos para **myResourceGroup** e o nome da VM para **myVM**.

    ```powershell
    $rgName = "myResourceGroup"
    $vmName = "myVM"
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
   
    O *Status* da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)**.
    
2. Converta todos os discos associados à VM, incluindo o disco do sistema operacional e discos de dados.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
    ```


## <a name="migrate-existing-azure-vms-using-standard-unmanaged-disks-to-premium-managed-disks"></a>Migrar VMs do Azure que usam discos não gerenciados standard para discos gerenciados Premium

Esta seção mostra como converter suas VMs do Azure existentes em discos não gerenciados Standard para discos Premium gerenciado. Para usar o Managed Disks Premium, sua máquina virtual deve usar um [tamanho MV](virtual-machines-windows-sizes.md) que dá suporte a armazenamento Premium.


1.  Primeiro, defina os parâmetros comuns. Verifique se o [tamanho da VM](virtual-machines-windows-sizes.md) você selecione dá suporte a armazenamento Premium.

    ```powershell
    $resourceGroupName = 'YourResourceGroupName'
    $vmName = 'YourVMName'
    $size = 'Standard_DS2_v2'
    ```
1.  Obter a VM com discos não gerenciados

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $resourceGroupName
    ```
    
1.  Pare (Desaloque) a VM.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```

1.  Atualize o tamanho da VM para tamanho armazenamento Premium com capacidade disponível na região onde a VM está localizada.

    ```powershell
    $vm.HardwareProfile.VmSize = $size
    Update-AzureRmVM -VM $vm -ResourceGroupName $resourceGroupName
    ```

1.  Converta a máquina virtual com discos não gerenciados em Managed Disks. 

    Se você receber um erro interno do servidor, tente 2 a 3 vezes antes de atingir o para nossa equipe de suporte.

    ```powershell
    ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $resourceGroupName -VMName $vmName
    ```
1. Pare (desaloque) a VM.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName -Force
    ```
2.  Atualize todos os discos para armazenamento Premium.

    ```powershell
    $vmDisks = Get-AzureRmDisk -ResourceGroupName $resourceGroupName 
    foreach ($disk in $vmDisks) 
        {
        if($disk.OwnerId -eq $vm.Id)
            {
             $diskUpdateConfig = New-AzureRmDiskUpdateConfig –AccountType PremiumLRS
             Update-AzureRmDisk -DiskUpdate $diskUpdateConfig -ResourceGroupName $resourceGroupName `
             -DiskName $disk.Name
            }
        }
    ```
1. Inicie a VM.

    ```powershell
    Start-AzureRmVM -ResourceGroupName $resourceGroupName -Name $vmName
    ```
    
Você também pode ter uma combinação de discos que usam o armazenamento standard e Premium.
    

## <a name="next-steps"></a>Próximas etapas

Faça uma cópia somente leitura de uma VM usando [instantâneos](virtual-machines-windows-snapshot-copy-managed-disk.md).


