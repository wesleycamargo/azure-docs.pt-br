---
title: Carregamento de um VHD especializado no Azure a ser usado para criar uma nova VM | Microsoft Docs
description: Carregar um VHD especializado no Azure a ser usado para criar uma nova VM.
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
ms.date: 02/05/2017
ms.author: cynthn
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 0abff0e82aeb02b73ab21cd297abd1178e49944d
ms.lasthandoff: 04/27/2017


---

# <a name="how-to-upload-a-specialized-vhd-to-create-a-vm-in-azure"></a>Como carregar um VHD especializado para criar uma VM no Azure

Um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Você pode carregar um VHD especializado no Azure e usá-lo para criar uma VM que usa Managed Disks ou uma conta de armazenamento não gerenciada. Recomendamos que você use o [Managed Disks](../../storage/storage-managed-disks-overview.md) para tirar proveito dos recursos adicionais e gerenciamento simplificado que os Managed Disks oferecem.


> [!IMPORTANT]
> Antes de carregar qualquer VHD no Azure, você deve seguir as etapas em [Preparar um VHD ou VHDX do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


* Para obter informações sobre os preços dos tamanhos variados das VMs, confira [Preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
* Para obter mais informações sobre os preços de armazenamento, confira [Preços de armazenamento](https://azure.microsoft.com/pricing/details/storage/blobs/). 
* Para ver a disponibilidade de tamanhos de VM nas regiões do Azure, confira [Produtos disponíveis por região](https://azure.microsoft.com/regions/services/).
* Para ver os limites gerais em VMs do Azure, consulte [Limites de assinatura e serviços do Azure, cotas e restrições](../../azure-subscription-service-limits.md).

## <a name="before-you-begin"></a>Antes de começar
Caso use o PowerShell, verifique se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


## <a name="prepare-the-vm"></a>Preparar a VM
 
Se você pretende usar o VHD especializado no estado em que se encontra para criar uma nova VM, não deixe de concluir as etapas a seguir. 
  * Se você pretende usar discos gerenciados, consulte [Como planejar a migração em Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
  * [Preparar um VHD do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalizar a VM usando o Sysprep.
  * Remover quaisquer ferramentas e agentes de virtualização de convidado que estejam instalados na VM (ou seja, ferramentas do VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS por meio de DHCP. Isso garante que o servidor obtém um endereço IP na VNet quando ele é iniciado. 
  * Desligue a VM antes de continuar.

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Se você ainda não tiver a versão 1.4 ou superior do PowerShell instalada, leia [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure. Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura. Substitua `<subscriptionID>` com a ID da assinatura correta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Se for o usar o VHD para criar um disco gerenciado para uma VM, o local da conta de armazenamento deverá ser o mesmo local em que você criará a VM.

Para exibir as contas de armazenamento disponíveis, digite:

```powershell
Get-AzureRmStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção [Carregar a imagem da VM](#upload-the-vm-vhd-to-your-storage-account).

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos no qual a conta de armazenamento deve ser criada. Para saber quais são todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste dos EUA**, digite:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Os valores válidos para -SkuName são:
   
   * **Standard_LRS** – Armazenamento com redundância local. 
   * **Standard_ZRS** – Armazenamento com redundância de zona.
   * **Standard_GRS** – Armazenamento com redundância geográfica. 
   * **Standard_RAGRS** – Armazenamento com redundância geográfica com acesso de leitura. 
   * **Premium_LRS** – Armazenamento com redundância local Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento

Use o cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) para carregar o VHD em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` em uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **myResourceGroup**. O arquivo será colocado em um contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

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

Dependendo da conexão de rede e do tamanho do arquivo VHD, esse comando pode demorar um pouco para ser concluído

Salve o caminho do **URI de Destino** para usá-lo posteriormente caso queira criar um disco gerenciado ou uma nova VM usando o VHD carregado.

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD

Você também pode carregar um VHD na sua conta de armazenamento usando um dos seguintes métodos:

-   [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Carregamento de Blobs do Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)

-   [Referência de API REST do Serviço de Importação/Exportação do Armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)

    É recomendável usar o Serviço de Importação/Exportação se o tempo de carregamento estimado é de mais de sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo com base no tamanho dos dados e na unidade de transferência. 

    A Importação/Exportação pode ser usada para copiar para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.

    
## <a name="create-the-subnet-and-vnet"></a>Criar a VNet e a sub-rede

Crie a rede virtual e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubNet** no grupo de recursos **myResourceGroup** e define o prefixo de endereço como **10.0.0.0/24**.
   
    ```powershell
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
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. Adicione o disco do sistema operacional na configuração. Este exemplo define o tamanho do disco para **128 GB** e anexa o disco gerenciado como um disco do sistema operacional do **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Opcional: anexe discos gerenciados adicionais como discos de dados. Essa opção pressupõe que você criou os discos de dados gerenciados usando [Como criar discos de dados gerenciados](create-managed-disk-ps.md). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opção 2: anexar um VHD que está em uma conta de armazenamento existente

1. Defina o URI do VHD que você deseja usar. Neste exemplo, o arquivo VHD **myOsDisk.vhd** é mantido em uma conta de armazenamento nomeada **myStorageAccount** em um contêiner nomeado **myContainer**.

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
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
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se o comando for bem-sucedido, você verá uma saída como esta:

```
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
Para entrar em sua nova máquina virtual, navegue até a VM no [portal](https://portal.azure.com), clique em **Conectar**e abra o arquivo RDP da Área de Trabalho Remota. Use as credenciais da conta da máquina virtual original para entrar na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

