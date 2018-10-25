---
title: Criar uma VM do Windows de um VHD especializado no Azure | Microsoft Docs
description: Crie uma nova VM do Windows anexando um disco gerenciado especializado como o disco do sistema operacional usando o modelo de implantação do Gerenciador de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2018
ms.author: cynthn
ms.openlocfilehash: 53062ee6384113ef8c483bc9cc6b407559c35994
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/18/2018
ms.locfileid: "49406119"
---
# <a name="create-a-windows-vm-from-a-specialized-disk-by-using-powershell"></a>Criar uma VM do Windows a partir de um disco especializado usando o PowerShell

Crie uma nova VM anexando um disco gerenciado especializado como o disco do sistema operacional. Um disco especializado é uma cópia de um disco rígido virtual (VHD) de uma VM existente que contém as contas de usuário, aplicativos e outros dados de estado de sua VM original. 

Quando você usar um VHD especializado para criar uma nova VM, a nova VM retém o nome do computador da VM original. Outras informações específicas de computador também são mantidas e, em alguns casos, essas informações duplicadas podem causar problemas. Ao copiar uma VM, esteja ciente de quais tipos de informações específicas do computador seus aplicativos dependem.

Você tem várias opções:
* [Use um disco gerenciado existente](#option-1-use-an-existing-disk). Essa opção é útil se você tiver uma VM que não esteja funcionando corretamente. Você pode excluir a VM e reutilizar o disco gerenciado para criar uma nova VM. 
* [Carregar um VHD](#option-2-upload-a-specialized-vhd) 
* [Copie uma VM do Azure existente usando snapshots](#option-3-copy-an-existing-azure-vm)

Você também pode usar o portal do Azure para [criar uma nova VM a partir de um VHD especializado](create-vm-specialized-portal.md).

Este artigo mostra como usar discos gerenciados. Se você tiver uma implantação legada que exija o uso de uma conta de armazenamento, consulte [Criar uma VM a partir de um VHD especializado em uma conta de armazenamento](sa-create-vm-specialized.md).

## <a name="before-you-begin"></a>Antes de começar
Para usar o PowerShell, verifique se você possui a versão mais recente do módulo do PowerShell do AzureRM.Compute. 

```powershell
Install-Module AzureRM -RequiredVersion 6.0.0
```
Para obter mais informações, consulte [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

## <a name="option-1-use-an-existing-disk"></a>Opção 1: usar um disco existente

Se você tinha uma VM que foi excluída e quiser reutilizar o disco do sistema operacional para criar uma nova, use [Get-AzureRmDisk](https://docs.microsoft.com/powershell/module/azurerm.compute/get-azurermdisk?view=azurermps-6.8.1).

```powershell
$resourceGroupName = 'myResourceGroup'
$osDiskName = 'myOsDisk'
$osDisk = Get-AzureRmDisk `
-ResourceGroupName $resourceGroupName `
-DiskName $osDiskName
```
Agora você pode anexar esse disco como o disco do sistema operacional para uma [nova VM](#create-the-new-vm).

## <a name="option-2-upload-a-specialized-vhd"></a>Opção 2: carregar um VHD especializado

Você pode carregar o VHD de uma VM especializada criado com uma ferramenta de virtualização local, como o Hyper-V, ou em uma VM exportada de outra nuvem.

### <a name="prepare-the-vm"></a>Preparar a VM
Use o VHD como-é criar uma nova VM. 
  
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalize a VM usando o Sysprep.
  * Remova quaisquer ferramentas e agentes de virtualização de convidados instalados na VM (como ferramentas VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS do DHCP. Isso garante que o servidor obtenha um endereço IP dentro da rede virtual quando for inicializado. 


### <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisará de uma conta de armazenamento do Azure para armazenar o VHD carregado. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Mostre as contas de armazenamento disponíveis.

```powershell
Get-AzureRmStorageAccount
```

Para usar uma conta de armazenamento existente, vá para a seção [Upload the VHD](#upload-the-vhd-to-your-storage-account).

Criar uma conta de armazenamento.

1. Você precisará do nome do grupo de recursos em que a conta de armazenamento será criada. Use Get-AzureRmResourceGroup para ver todos os grupos de recursos que estão em sua assinatura.
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Crie um grupo de recursos chamado *myResourceGroup* na região *Oeste dos EUA*.

    ```powershell
    New-AzureRmResourceGroup `
       -Name myResourceGroup `
       -Location "West US"
    ```

2. Crie uma conta de armazenamento denominada *mystorageaccount* no novo grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).
   
    ```powershell
    New-AzureRmStorageAccount `
       -ResourceGroupName myResourceGroup `
       -Name mystorageaccount `
       -Location "West US" `
       -SkuName "Standard_LRS" `
       -Kind "Storage"
    ```

### <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento 
Use o cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) para carregar o VHD em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo *myVHD.vhd* dos discos rígidos "C: \ Users \ Public \ Documents \ Virtual\" para uma conta de armazenamento chamada *mystorageaccount* no *myResourceGroup* grupo de recursos. O arquivo será armazenado em um contêiner chamado *mycontainer* e o novo nome do arquivo será *myUploadedVHD.vhd*.

```powershell
$resourceGroupName = "myResourceGroup"
$urlOfUploadedVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $resourceGroupName `
   -Destination $urlOfUploadedVhd `
   -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se os comandos forem bem-sucedidas, você obterá uma resposta que é semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Esse comando pode demorar um pouco para ser concluído, dependendo da conexão de rede e do tamanho do arquivo VHD.

### <a name="create-a-managed-disk-from-the-vhd"></a>Criar um disco gerenciado do VHD

Crie um disco gerenciado a partir do VHD especializado em sua conta de armazenamento usando [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Este exemplo usa *myOSDisk1* para o nome do disco, coloca o disco no armazenamento *Standard_LRS*, e usa *https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd* como o URI para o VHD de origem.

Criar um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Crie o novo disco de sistema operacional no VHD carregado. 

```powershell
$sourceUri = 'https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd'
$osDiskName = 'myOsDisk'
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig -AccountType Standard_LRS  `
    -Location $location -CreateOption Import `
    -SourceUri $sourceUri) `
    -ResourceGroupName $destinationResourceGroup
```

## <a name="option-3-copy-an-existing-azure-vm"></a>Opção 3: copiar uma VM existente do Azure

Você pode criar uma cópia de uma VM que usa discos gerenciados, tirando um instantâneo da VM e usando esse instantâneo para criar um novo disco gerenciado e uma nova VM.


### <a name="take-a-snapshot-of-the-os-disk"></a>Tirar um instantâneo do disco do sistema operacional

Você pode tirar um instantâneo de uma VM inteira (incluindo todos os discos) ou de apenas um único disco. As etapas a seguir mostram como tirar um instantâneo apenas do disco do SO de sua VM com o cmdlet [New-AzureRmSnapshot](/powershell/module/azurerm.compute/new-azurermsnapshot). 

Primeiro, defina alguns parâmetros. 

 ```powershell
$resourceGroupName = 'myResourceGroup' 
$vmName = 'myVM'
$location = 'westus' 
$snapshotName = 'mySnapshot'  
```

Obtenha o objeto da VM.

```powershell
$vm = Get-AzureRmVM -Name $vmName `
   -ResourceGroupName $resourceGroupName
```
Obtenha o nome de disco do sistema operacional.

 ```powershell
$disk = Get-AzureRmDisk -ResourceGroupName $resourceGroupName `
   -DiskName $vm.StorageProfile.OsDisk.Name
```

Crie a configuração do instantâneo. 

 ```powershell
$snapshotConfig =  New-AzureRmSnapshotConfig `
   -SourceUri $disk.Id `
   -OsType Windows `
   -CreateOption Copy `
   -Location $location 
```

Crie o instantâneo.

```powershell
$snapShot = New-AzureRmSnapshot `
   -Snapshot $snapshotConfig `
   -SnapshotName $snapshotName `
   -ResourceGroupName $resourceGroupName
```


Para usar esse instantâneo para criar uma VM que precisa ser de alto desempenho, adicione o parâmetro `-AccountType Premium_LRS` ao comando New-AzureRmSnapshot. Esse parâmetro cria o instantâneo para que seja armazenado como um disco gerenciado Premium. Os discos gerenciados premium são mais caros que o padrão, portanto, verifique se você precisa do Premium antes de usar esse parâmetro.

### <a name="create-a-new-disk-from-the-snapshot"></a>Criar um novo disco a partir do instantâneo

Criar um disco gerenciado do snapshot usando [New-AzureRMDisk](/powershell/module/azurerm.compute/new-azurermdisk). Este exemplo usa *myOSDisk* para o nome do disco.

Criar um novo grupo de recursos para a nova VM.

```powershell
$destinationResourceGroup = 'myDestinationResourceGroup'
New-AzureRmResourceGroup -Location $location `
   -Name $destinationResourceGroup
```

Defina o nome de disco do sistema operacional. 

```powershell
$osDiskName = 'myOsDisk'
```

Criar o disco gerenciado.

```powershell
$osDisk = New-AzureRmDisk -DiskName $osDiskName -Disk `
    (New-AzureRmDiskConfig  -Location $location -CreateOption Copy `
    -SourceResourceId $snapshot.Id) `
    -ResourceGroupName $destinationResourceGroup
```


## <a name="create-the-new-vm"></a>Crie a nova VM 

Crie rede e outros recursos de máquina virtual a ser usado pela nova VM.

### <a name="create-the-subnet-and-virtual-network"></a>Crie a sub-rede e a rede virtual

Criar a [rede virtual](../../virtual-network/virtual-networks-overview.md) e uma sub-rede para a VM.

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada *mySubNet* no grupo de recursos *myDestinationResourceGroup* e defina o prefixo de endereço como *10.0.0.0/24*.
   
    ```powershell
    $subnetName = 'mySubNet'
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
       -Name $subnetName `
       -AddressPrefix 10.0.0.0/24
    ```
    
2. Crie a rede virtual. Este exemplo define o nome da rede virtual como *myVnetName*, o local como *West US* e o prefixo de endereço da rede virtual como *10.0.0.0/16*. 
   
    ```powershell
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork `
       -Name $vnetName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AddressPrefix 10.0.0.0/16 `
       -Subnet $singleSubnet
    ```    
    

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP
Para poder entrar em sua VM com o RDP (Remote Desktop Protocol), você precisará ter uma regra de segurança que permita acesso RDP na porta 3389. Em nosso exemplo, o VHD para a nova VM foi criado a partir de uma VM especializada existente, para que você possa usar uma conta que existia na máquina virtual de origem para o RDP.

Este exemplo define o nome do grupo de segurança de rede (NSG) para *myNsg* e o nome da regra de RDP para *myRdpRule*.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup `
   -ResourceGroupName $destinationResourceGroup `
   -Location $location `
   -Name $nsgName -SecurityRules $rdpRule
    
```

Para obter mais informações sobre pontos de extremidade e regras do NSG, consulte [abrir portas para uma VM no Azure usando o PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e uma NIC
Para permitir a comunicação com a máquina virtual na rede virtual, você precisará de um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público é definido como *myIP*.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress `
       -Name $ipName -ResourceGroupName $destinationResourceGroup `
       -Location $location `
       -AllocationMethod Dynamic
    ```       
    
2. Crie a NIC. Neste exemplo, o nome da NIC é definido como *myNicName*.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName `
       -ResourceGroupName $destinationResourceGroup `
       -Location $location -SubnetId $vnet.Subnets[0].Id `
       -PublicIpAddressId $pip.Id `
       -NetworkSecurityGroupId $nsg.Id
    ```
    


### <a name="set-the-vm-name-and-size"></a>Como definir o nome e tamanho da VM

Este exemplo define o nome da VM para *myVM* e o tamanho da VM para *Standard_A2*.

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Como adicionar o NIC
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    

### <a name="add-the-os-disk"></a>Adicionar o disco do sistema operacional 

Inclua o disco do sistema operacional na configuração usando [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk). Este exemplo define o tamanho do disco para *128 GB* e anexa o disco gerenciado como um disco do sistema operacional do *Windows*.
 
```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -StorageAccountType Standard_LRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
```

### <a name="complete-the-vm"></a>Concluir a VM 

Crie a VM usando [New-AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) com as configurações que acabamos de criar.

```powershell
New-AzureRmVM -ResourceGroupName $destinationResourceGroup -Location $location -VM $vm
```

Se esse comando for bem-sucedido, você verá uma saída como esta:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Você deve ver a VM recém-criada na [portal do Azure](https://portal.azure.com) sob **procurar** > **máquinas virtuais**, ou usando os seguintes comandos do PowerShell.

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $destinationResourceGroup
$vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md).

