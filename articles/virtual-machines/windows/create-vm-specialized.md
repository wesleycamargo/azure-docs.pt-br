---
title: Criar uma VM de um disco especializado no Azure | Microsoft Docs
description: "Crie uma nova VM anexando um disco gerenciado, ou não gerenciado, especializado no modelo de implantação do Resource Manager."
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
ms.date: 02/06/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 78f993ce9bab6266479cdd121eeea4965724d9bd
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-vm-from-a-specialized-disk"></a>Criar uma VM por meio de um disco especializado

Crie uma nova VM anexando um disco especializado como o disco do sistema operacional usando o Powershell. Um disco especializado é uma cópia do VHD de uma VM existente que mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Use qualquer [disco gerenciado](../../storage/storage-managed-disks-overview.md) especializado ou um disco especializado não gerenciado para criar a nova VM.

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


## <a name="create-the-subnet-and-vnet"></a>Criar a VNet e a sub-rede

Crie a rede virtual e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

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
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público é definido como **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
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

Para obter mais informações sobre regras de NSGs e pontos de extremidade, veja [Abrir portas para uma VM no Azure usando PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Como definir o nome e tamanho da VM

Este exemplo define o nome da VM para "myVM" e o tamanho da VM para "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Como adicionar o NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Como configurar o disco do sistema operacional

O sistema operacional especializado pode ser um VHD que você [carregou no Azure](upload-image.md) ou um [VHD copiado de uma VM do Azure existente](vhd-copy.md). 

Escolha uma das duas opções:
- **Opção 1**: criar um disco gerenciado especializado de um VHD especializado em uma conta de armazenamento existente para usar como o disco do sistema operacional.

ou o 

- **Opção 2**: usar um VHD especializado armazenado em sua própria conta de armazenamento (um disco não gerenciado). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opção 1: criar um disco gerenciado de um disco especializado não gerenciado

1. Crie um disco gerenciado do VHD especializado existente na sua conta de armazenamento. Este exemplo usa **myOSDisk1** para o nome do disco, coloca o disco no armazenamento **StandardLRS** e usa **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** como o URI para o VHD de origem.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreateOption Import `
    -SourceUri https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd) `
    -ResourceGroupName $rgName
    ```

2. Adicione o disco do sistema operacional na configuração. Este exemplo define o tamanho do disco para **128 GB** e anexa o disco gerenciado como um disco do sistema operacional do **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Opcional: anexe discos gerenciados adicionais como discos de dados. Essa opção pressupõe que você criou os discos de dados gerenciados usando [Como criar discos de dados gerenciados](create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opção 2: anexar um VHD que está em uma conta de armazenamento existente

1. Defina o URI do VHD que você deseja usar. Neste exemplo, o arquivo VHD **myOsDisk.vhd** é mantido em uma conta de armazenamento nomeada **myStorageAccount** em um contêiner nomeado **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Adicione o disco do sistema operacional usando a URL do VHD do sistema operacional copiado. Neste exemplo, quando o disco do sistema operacional é criado, o termo "osDisk" é acrescentado ao nome da VM para criar o nome do disco do sistema operacional. Este exemplo também especifica que este VHD baseado em Windows deve ser anexado à VM como o disco do sistema operacional.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Opcional: se você tiver discos de dados que precisam ser anexados à VM, adicione os discos de dados usando as URLs de VHDs de dados e o número de unidade lógica (Lun) apropriado.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Ao usar uma conta de armazenamento, as URLs do disco do sistema operacional e dos dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-las no portal navegando até o contêiner de armazenamento de destino, clicando no VHD de sistema operacional ou nos dados que foram copiados e copiando o conteúdo da URL.


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
Para entrar em sua nova máquina virtual, navegue até a VM no [portal](https://portal.azure.com), clique em **Conectar**e abra o arquivo RDP da Área de Trabalho Remota. Use as credenciais da conta da máquina virtual original para entrar na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).


