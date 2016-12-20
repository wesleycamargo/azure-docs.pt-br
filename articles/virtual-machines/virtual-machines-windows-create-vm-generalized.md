---
title: Criar VM usando um VHD generalizado | Microsoft Docs
description: "Saiba como criar uma máquina virtual do Windows com base em uma imagem do VHD generalizado usando o Azure PowerShell no modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: b4808871-9ef1-49ea-a617-9154d417abb0
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: cb7f3a1bf44a18141294ab03677f7e733177c1b8


---
# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Criar uma VM com base em uma imagem do VHD generalizado
Uma imagem do VHD generalizado teve todas suas informações de conta pessoal removidas usando [Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Você pode criar um VHD generalizado executando o Sysprep em uma VM local e então [carregando o VHD no Azure](virtual-machines-windows-upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json), ou executando o Sysprep em uma VM do Azure existente e então [copiando o VHD](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Se você quiser criar uma VM com base em um VHD especializado, consulte [Create a VM from a specialized VHD](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Criar uma VM com base em um VHD especializado).

A maneira mais rápida para criar uma VM de um VHD generalizado é usar um [modelo de início rápido](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 

## <a name="prerequisites"></a>Pré-requisitos
Se você for usar um VHD carregado de uma VM local, como uma criada usando Hyper-V, verifique se você seguiu as orientações em [Preparar um VHD do Windows para upload no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Os VHDs carregados e os VHDs da VM do Azure existentes precisam ser generalizados para criar uma VM usando esse método. Para obter mais informações, consulte [Generalize a Windows virtual machine using Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Generalizar uma máquina virtual do Windows usando Sysprep). 

## <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD
O URI do VHD a usar está no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/**MyVhdName**.vhd. Neste exemplo, o VHD chamado **myVHD** está na conta de armazenamento **mystorageaccount** no contêiner **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. O exemplo a seguir cria uma sub-rede chamada **mySubnet** no grupo de recursos **myResourceGroup** com o prefixo de endereço **10.0.0.0/24**.  
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a rede virtual. O exemplo a seguir cria uma rede virtual chamada **myVnet** na localização **Oeste dos EUA** com o prefixo de endereço **10.0.0.0/16**.  
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e um adaptador de rede
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Criar um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Este exemplo cria uma NIC chamada **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para fazer logon em sua VM usando RDP, é preciso ter uma regra de segurança que permita acesso RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [Abrir portas para uma VM no Azure usando PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual
Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Criar a VM
O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM carregada como a fonte da nova instalação.

</br>

```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential

    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"

    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"

    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"

    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"

    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"

    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant
    # storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage,
    # Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"

    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows

    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **Navegar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


