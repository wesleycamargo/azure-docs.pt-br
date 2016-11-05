---
title: Gerenciar VMs usando o Resource Manager e o PowerShell | Microsoft Docs
description: Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/07/2016
ms.author: davidmu

---
# Gerenciar Máquinas Virtuais do Azure usando o Resource Manager e o PowerShell
## Instale o Azure PowerShell
Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura que deseja usar e entrar na sua conta do Azure.

## Definir variáveis
Todos os comandos no artigo requerem o nome do grupo de recursos no qual se encontra a máquina virtual e o nome da máquina virtual a ser gerenciada. Substitua o valor **$rgName** pelo nome do grupo de recursos que contém a máquina virtual. Substitua o valor **$vmName** pelo nome da VM. Crie as variáveis.

    $rgName = "resource-group-name"
    $vmName = "VM-name"

## Exibir informações sobre uma máquina virtual
Obter as informações da máquina virtual.

    Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Ele retorna algo semelhante a:

    ResourceGroupName        : rg1
    Id                       : /subscriptions/{subscription-id}/resourceGroups/
                               rg1/providers/Microsoft.Compute/virtualMachines/vm1
    Name                     : vm1
    Type                     : Microsoft.Compute/virtualMachines
    Location                 : centralus
    Tags                     : {}
    AvailabilitySetReference : {
                                  "id": "/subscriptions/{subscription-id}/resourceGroups/
                                  rg1/providers/Microsoft.Compute/availabilitySets/av1"
                               }
    Extensions               : []
    HardwareProfile          : {
                                  "vmSize": "Standard_A0"
                               }
    InstanceView             : null
    NetworkProfile           : {
                                  "networkInterfaces": [
                                    {
                                      "properties.primary": null,
                                      "id": "/subscriptions/{subscription-id}/resourceGroups/
                                      rg1/providers/Microsoft.Network/networkInterfaces/nc1"
                                    }
                                  ]
                               }
    OSProfile                : {
                                  "computerName": "vm1",
                                  "adminUsername": "myaccount1",
                                  "adminPassword": null,
                                  "customData": null,
                                  "windowsConfiguration": {
                                    "provisionVMAgent": true,
                                    "enableAutomaticUpdates": true,
                                    "timeZone": null,
                                    "additionalUnattendContents": [],
                                    "winRM": null
                                  },
                                  "linuxConfiguration": null,
                                  "secrets": []
                               }
    Plan                     : null
    ProvisioningState        : Succeeded
    StorageProfile           : {
                                  "imageReference": {
                                    "publisher": "MicrosoftWindowsServer",
                                    "offer": "WindowsServer",
                                    "sku": "2012-R2-Datacenter",
                                    "version": "latest"
                                  },
                                  "osDisk": {
                                    "osType": "Windows",
                                    "encryptionSettings": null,
                                    "name": "osdisk",
                                    "vhd": {
                                      "Uri": "http://sa1.blob.core.windows.net/vhds/osdisk1.vhd"
                                    }
                                    "image": null,
                                    "caching": "ReadWrite",
                                    "createOption": "FromImage"
                                  }
                                  "dataDisks": [],
                               }
    DataDiskNames            : {}
    NetworkInterfaceIDs      : {/subscriptions/{subscription-id}/resourceGroups/
                                rg1/providers/Microsoft.Network/networkInterfaces/nc1}

## Iniciar uma máquina virtual
Iniciar a máquina virtual.

    Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Após alguns minutos, ele retorna algo semelhante a:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## Parar uma máquina virtual
Parar a máquina virtual.

    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName

É solicitado que você faça a confirmação:

    Virtual machine stopping operation
    This cmdlet will stop the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Digite **Y** para parar a máquina virtual.

Após alguns minutos, ele retorna algo semelhante a:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## Reiniciar uma máquina virtual
Reinicie a máquina virtual.

    Restart-AzureRmVM -ResourceGroupName $rgName -Name $vmName

Ele retorna algo semelhante a:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## Excluir uma máquina virtual
Excluir a máquina virtual.

    Remove-AzureRmVM -ResourceGroupName $rgName –Name $vmName

> [!NOTE]
> Use o parâmetro **-Force** para ignorar o prompt de confirmação.
> 
> 

É solicitado que você confirme se você usou ou não o parâmetro -Force:

    Virtual machine removal operation
    This cmdlet will remove the specified virtual machine. Do you want to continue?
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Ele retorna algo semelhante a:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

## Redimensionar uma máquina virtual
Este exemplo mostra como atualizar o tamanho da máquina virtual.

    $vmSize = "Standard_A1"
    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    $vm.HardwareProfile.vmSize = $vmSize
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

Ele retorna algo semelhante a:

    RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
    ---------  -------------------  ----------  ------------
                              True          OK  OK

Consulte [Tamanhos das máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para obter uma lista dos tamanhos disponíveis para uma máquina virtual.

## Adicionar um disco de dados a uma máquina virtual
Este exemplo mostra como adicionar um disco de dados a uma máquina virtual existente.

    $vm = Get-AzureRmVM -ResourceGroupName $rgName -Name $vmName
    Add-AzureRmVMDataDisk -VM $vm -Name "disk-name" -VhdUri "https://mystore1.blob.core.windows.net/vhds/datadisk1.vhd" -LUN 0 -Caching ReadWrite -DiskSizeinGB 1 -CreateOption Empty
    Update-AzureRmVM -ResourceGroupName $rgName -VM $vm

O disco que você adiciona não é inicializado. Para inicializar o disco, você pode fazer logon e usar o gerenciamento de disco. Se você instalou o WinRM e um certificado nele durante a criação, você pode usar o PowerShell remoto para inicializar o disco. Você também pode usar uma extensão de script personalizado:

    $location = "location-name"
    $scriptName = "script-name"
    $fileName = "script-file-name"
    Set-AzureRmVMCustomScriptExtension -ResourceGroupName $rgName -Location $locName -VMName $vmName -Name $scriptName -TypeHandlerVersion "1.4" -StorageAccountName "mystore1" -StorageAccountKey "primary-key" -FileName $fileName -ContainerName "scripts"

O arquivo de script pode conter algo parecido com o seguinte para inicializar os discos:

    $disks = Get-Disk |   Where partitionstyle -eq 'raw' | sort number

    $letters = 70..89 | ForEach-Object { ([char]$_) }
    $count = 0
    $labels = @("data1","data2")

    foreach($d in $disks) {
        $driveLetter = $letters[$count].ToString()
        $d | 
        Initialize-Disk -PartitionStyle MBR -PassThru |
        New-Partition -UseMaximumSize -DriveLetter $driveLetter |
        Format-Volume -FileSystem NTFS -NewFileSystemLabel $labels[$count] `
            -Confirm:$false -Force 
        $count++
    }

## Próximas etapas
Se houver problemas com a implantação, a próxima etapa será examinar [Solucionando os problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)

<!---HONumber=AcomDC_0907_2016-->