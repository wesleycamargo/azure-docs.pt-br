---
title: Gerenciar VMs do Azure usando o PowerShell | Microsoft Docs
description: "Gerencie máquinas virtuais do Azure usando o PowerShell."
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 48930854-7888-4e4c-9efb-7d1971d4cc14
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/07/2017
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c8ed83c6c0219995030408f276d4ef7d83703c7c
ms.lasthandoff: 04/27/2017


---
# <a name="manage-azure-virtual-machines-using-powershell"></a>Gerenciar máquinas virtuais do Azure usando o PowerShell

Este artigo mostra tarefas comuns de gerenciamento que você pode executar em uma máquina virtual do Azure.

Confira [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

> [!NOTE]
> Talvez seja necessário reinstalar o Azure PowerShell para usar a funcionalidade neste artigo. Os recursos de Managed Disks estão na versão 3.5 e posterior.
> 
> 

Crie estas variáveis para executar os comandos com mais facilidade e alterar os valores de acordo com seu ambiente:
    
```powershell
$myResourceGroup = "myResourceGroup"
$myVM = "myVM"
$location = "centralus"
```

## <a name="display-information-about-a-virtual-machine"></a>Exibir informações sobre uma máquina virtual

Obtenha informações sobre uma máquina virtual.

```powershell
Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -DisplayHint Expand
```

É retornado algo semelhante a este exemplo:

    ResourceGroupName             : myResourceGroup
    Id                            : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM
    VmId                          : #########-####-####-####-############
    Name                          : myVM
    Type                          : Microsoft.Compute/virtualMachines
    Location                      : centralus
    Tags                          : {}
    DiagnosticsProfile            :
      BootDiagnostics             :
      Enabled                     : True
      StorageUri                  : https://mystorage1.blob.core.windows.net/
    AvailabilitySetReference      : 
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/availabilitySets/myAV1
    Extensions[0]                 :
      Id                          : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Compute/
                                    virtualMachines/myVM/extensions/BGInfo
      Name                        : BGInfo
      Type                        : Microsoft.Compute/virtualMachines/extensions
      Location                    : centralus
      Publisher                   : Microsoft.Compute
      VirtualMachineExtensionType : BGInfo
      TypeHandlerVersion          : 2.1
      AutoUpgradeMinorVersion     : True
      ProvisioningState           : Succeeded
    HardwareProfile               : 
      VmSize                      : Standard_DS1_v2
    NetworkProfile          
      NetworkInterfaces[0]        :
        Id                        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC
    OSProfile                     : 
      ComputerName                : myVM
      AdminUsername               : myaccount1
      WindowsConfiguration        :
        ProvisionVMAgent          : True
         EnableAutomaticUpdates   : True
    ProvisioningState             : Succeeded
    StorageProfile                :
      ImageReference              :
        Publisher                 : MicrosoftWindowsServer
        Offer                     : WindowsServer
        Sku                       : 2012-R2-Datacenter
        Version                   : latest   
      OsDisk                      :
        OsType                    : Windows
        Name                      : myosdisk
        Vhd                       : 
          Uri                     : http://mystore1.blob.core.windows.net/vhds/myosdisk.vhd
        Caching                   : ReadWrite
        CreateOption              : FromImage
    NetworkInterfaceIDs[0]        : /subscriptions/{subscription-id}/resourceGroups/
                                    myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNIC

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual

Você pode interromper uma máquina virtual de duas maneiras. Você pode parar uma máquina virtual e manter todas as suas configurações, mas continuará a ser cobrado por ela, ou pode parar uma máquina virtual e desalocá-la. Quando uma máquina virtual é desalocada, todos os recursos associados a ela também são desalocadas e a cobrança para eles termina.

### <a name="stop-and-deallocate"></a>Parar e desalocar
    
```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

É solicitado que você faça a confirmação:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
    Enter **Y** to stop the virtual machine.

Após alguns minutos, é retornado algo semelhante a este exemplo:

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

### <a name="stop-and-stay-provisioned"></a>Parar e permanecer provisionado

```powershell
Stop-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM -StayProvisioned
```

É solicitado que você faça a confirmação:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):
 
Digite **Y** para parar a máquina virtual.

Após alguns minutos, é retornado algo semelhante a este exemplo:

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:11:57 PM
    EndTime     : 9/13/2016 12:14:40 PM
    Error       :

## <a name="start-a-virtual-machine"></a>Iniciar uma máquina virtual
 
Inicie uma máquina virtual, caso ela tenha sido interrompida.

```powershell
Start-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

Após alguns minutos, é retornado algo semelhante a este exemplo:

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:32:55 PM
    EndTime     : 9/13/2016 12:35:09 PM
    Error       :

Se você quiser reiniciar uma máquina virtual que já está em execução, use **Restart-AzureRmVM**, descrito a seguir.

## <a name="restart-a-virtual-machine"></a>Reiniciar uma máquina virtual

Reinicie uma máquina virtual em execução.

```powershell
Restart-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
```

É retornado algo semelhante a este exemplo:

    OperationId :
    Status      : Succeeded
    StartTime   : 9/13/2016 12:54:40 PM
    EndTime     : 9/13/2016 12:55:54 PM
    Error       :    

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Adicionar um disco de dados a uma máquina virtual
    
### <a name="managed-data-disk"></a>Disco de dados gerenciados

```powershell
$diskConfig = New-AzureRmDiskConfig -AccountType PremiumLRS -Location $location -CreateOption Empty -DiskSizeGB 128
$dataDisk = New-AzureRmDisk -DiskName "myDataDisk1" -Disk $diskConfig -ResourceGroupName $myResourceGroup
$vm = Get-AzureRmVM -Name $myVM -ResourceGroupName $myResourceGroup
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Depois de executar Add-AzureRmVMDataDisk, você deverá ver algo como este exemplo:

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Depois de executar Update-AzureRmVM, você verá algo semelhante a este exemplo:

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

### <a name="unmanaged-data-disk"></a>Disco de dados não gerenciados

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
Add-AzureRmVMDataDisk -VM $vm -Name "myDataDisk1" -VhdUri "https://mystore1.blob.core.windows.net/vhds/myDataDisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

Depois de executar Add-AzureRmVMDataDisk, você deverá ver algo como este exemplo:

    ResourceGroupName        : myResourceGroup
    Id                       : /subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Compute/virtualMachines/myVM
    VmId                     : ########-####-####-####-############
    Name                     : myVM
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {Id}
    DiagnosticsProfile       : {BootDiagnostics}
    HardwareProfile          : {VmSize}
    NetworkProfile           : {NetworkInterfaces}
    OSProfile                : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
    ProvisioningState        : Succeeded
    StorageProfile           : {ImageReference, OsDisk, DataDisks}
    DataDiskNames            : {myDataDisk1}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/
                               Microsoft.Network/networkInterfaces/myNIC}

Depois de executar Update-AzureRmVM, você verá algo semelhante a este exemplo:

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK

## <a name="update-a-virtual-machine"></a>Atualizar uma máquina virtual

Este exemplo mostra como atualizar o tamanho da máquina virtual.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName $myResourceGroup -Name $myVM
$vm.HardwareProfile.vmSize = "Standard_DS2_v2"
Update-AzureRmVM -ResourceGroupName $myResourceGroup -VM $vm
```

É retornado algo semelhante a este exemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Consulte [Tamanhos das máquinas virtuais no Azure](sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter uma lista dos tamanhos disponíveis para uma máquina virtual.

## <a name="delete-a-virtual-machine"></a>Excluir uma máquina virtual

Excluir a máquina virtual.  

```powershell
Remove-AzureRmVM -ResourceGroupName $myResourceGroup –Name $myVM
```

> [!NOTE]
> Use o parâmetro **-Force** para ignorar o prompt de confirmação.
> 
> 

Se você não usou o parâmetro -Force, será solicitada sua confirmação:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes [N] No [S] Suspend [?] Help (default is "Y"):

É retornado algo semelhante a este exemplo:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## <a name="next-steps"></a>Próximas etapas

- Se houver problemas com a implantação, você pode ler [Troubleshoot common Azure deployment errors with Azure Resource Manager](../../resource-manager-common-deployment-errors.md) (Solucionar erros comuns de implantação do Azure com o Azure Resource Manager)
- Saiba como usar o Azure PowerShell para criar uma máquina virtual de [Criar uma VM do Windows usando o Resource Manager e o PowerShell](../virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Criar uma máquina virtual do Windows com um modelo do Resource Manager](ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

