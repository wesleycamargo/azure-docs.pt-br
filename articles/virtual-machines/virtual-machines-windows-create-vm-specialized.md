---
title: "Criar uma cópia de sua VM do Windows | Microsoft Docs"
description: "Saiba como criar uma cópia de uma VM do Azure especializada executando Windows, no modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/21/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: a779f084e0ad6de71ad3e2de86a2fb85738b8fe6


---
# <a name="create-a-vm-from-a-specialized-vhd"></a>Criar uma VM com base em um VHD especializado
Crie uma nova VM anexando um VHD especializado como o disco do sistema operacional usando o Powershell. Um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

Se você quiser criar uma VM com base em um VHD generalizado, veja [Criar uma VM com base em uma imagem de VHD generalizado](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="create-the-subnet-and-vnet"></a>Criar a VNet e a sub-rede
Crie a rede virtual e a sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubNet** no grupo de recursos **myResourceGroup** e define o prefixo de endereço como **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Crie a VNet. Este exemplo define o nome de rede virtual para **myVnetName**, o local para **Oeste dos EUA** e o prefixo de endereço da rede virtual para **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público é definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para fazer logon em sua VM usando RDP, é preciso ter uma regra de segurança que permita acesso RDP na porta 3389. Como o VHD para a nova VM foi criado com base em uma VM especializada existente, depois que a VM é criada, você pode usar uma conta existente na máquina virtual de origem que tenha permissão para fazer logon usando o RDP.

Este exemplo define o nome NSG para **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Para obter mais informações sobre regras de NSGs e pontos de extremidade, veja [Abrir portas para uma VM no Azure usando PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="create-the-vm-configuration"></a>Criar a configuração da VM
Defina a configuração da VM à qual anexar o VHD copiado como o VHD do sistema operacional.

```powershell
# Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept 
# in a storage account named "myStorageAccount" in a container named "myContainer".
$osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"

# Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

# Add the NIC
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

# Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the 
# term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this 
# Windows-based VHD should be attached to the VM as the OS disk.
$osDiskName = $vmName + "osDisk"
$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Se você tiver discos de dados que precisem ser anexados à VM, você também deverá adicionar o seguinte: 

```powershell
# Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit 
# Number (Lun).
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

As URLs do disco do sistema operacional e dos dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-las no portal navegando até o contêiner de armazenamento de destino, clicando no VHD de sistema operacional ou nos dados que foram copiados e copiando o conteúdo da URL.

## <a name="create-the-vm"></a>Criar a VM
Crie a VM usando as configurações que acabamos de criar.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se o comando for bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Você deverá ver a VM recém-criada no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Para entrar em sua nova máquina virtual, navegue até a VM no [portal](https://portal.azure.com), clique em **Conectar**e abra o arquivo RDP da Área de Trabalho Remota. Use as credenciais da conta da máquina virtual original para entrar na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


