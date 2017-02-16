---
title: Criar uma VM do Azure usando o PowerShell | Microsoft Docs
description: Use o Azure Powershell e o Azure Resource Manager para criar facilmente uma nova VM executando o Windows Server.
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 14fe9ca9-e228-4d3b-a5d8-3101e9478f6e
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/21/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 94c18aa0c4fe38fb74931d5ed61fece207c8b5ce
ms.openlocfilehash: 701a5515cd1d52f7ca8d3562dabcdf0e4d31183d


---
# <a name="create-a-windows-vm-using-resource-manager-and-powershell"></a>Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell
Este artigo mostra como criar rapidamente uma Máquina Virtual do Azure que executa o Windows Server e os recursos que ela precisa usando o [Resource Manager](../azure-resource-manager/resource-group-overview.md) e o PowerShell. 

Todas as etapas neste artigo são necessárias para criar uma máquina virtual e devem demorar cerca de 30 minutos para serem executadas. Substitua os valores de parâmetro de exemplo nos comandos por nomes que façam sentido para o seu ambiente.

## <a name="step-1-install-azure-powershell"></a>Etapa 1: instalar o PowerShell do Azure
Confira [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## <a name="step-2-create-a-resource-group"></a>Etapa 2: criar um grupo de recursos
Todos os recursos devem estar contidos em um grupo de recursos, portanto, vamos criá-lo primeiro.  

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.
   
    ```powershell
    Get-AzureRmLocation | sort Location | Select Location
    ```
2. Defina o local para os recursos. Este comando define o local como **centralus**.
   
    ```powershell
    $location = "centralus"
    ```
3. Crie um grupos de recursos. Este comando cria o grupo de recursos denominado **myResourceGroup** no local que você definir.
   
    ```powershell
    $myResourceGroup = "myResourceGroup"
    New-AzureRmResourceGroup -Name $myResourceGroup -Location $location
    ```

## <a name="step-3-create-a-storage-account"></a>Etapa 3: criar uma conta de armazenamento
É necessário ter uma [conta de armazenamento](../storage/storage-introduction.md) para armazenar o disco rígido virtual usado pela máquina virtual criada por você. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.

1. Teste a exclusividade do nome da conta de armazenamento. Este comando testa o nome **myStorageAccount**.
   
    ```powershell
    $myStorageAccountName = "mystorageaccount"
    Get-AzureRmStorageAccountNameAvailability $myStorageAccountName
    ```
   
    Se esse comando retornar **True**, o nome proposto será exclusivo no Azure. 
2. Agora, crie a conta de armazenamento.
   
    ```powershell    
    $myStorageAccount = New-AzureRmStorageAccount -ResourceGroupName $myResourceGroup `
        -Name $myStorageAccountName -SkuName "Standard_LRS" -Kind "Storage" -Location $location
    ```

## <a name="step-4-create-a-virtual-network"></a>Etapa 4: criar uma rede virtual
Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie uma sub-rede para a rede virtual. Este comando cria uma sub-rede denominada **mySubnet** com um prefixo de endereço de 10.0.0.0/24.
   
    ```powershell
    $mySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnet" -AddressPrefix 10.0.0.0/24
    ```
2. Agora, crie a rede virtual. Este comando cria uma rede virtual chamada **myVnet** usando a sub-rede que você criou, e um prefixo de endereço **10.0.0.0/16**.
   
    ```powershell
    $myVnet = New-AzureRmVirtualNetwork -Name "myVnet" -ResourceGroupName $myResourceGroup `
        -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $mySubnet
    ```

## <a name="step-5-create-a-public-ip-address-and-network-interface"></a>Etapa 5: Criar um endereço IP público e interface de rede
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Crie o endereço IP público. Este comando cria um endereço IP público chamado **myPublicIp** com um método de alocação **Dinâmico**.
   
    ```powershell
    $myPublicIp = New-AzureRmPublicIpAddress -Name "myPublicIp" -ResourceGroupName $myResourceGroup `
        -Location $location -AllocationMethod Dynamic
    ```
2. Crie a interface de rede. Este comando cria uma interface de rede denominada **myNIC**.
   
    ```powershell
    $myNIC = New-AzureRmNetworkInterface -Name "myNIC" -ResourceGroupName $myResourceGroup `
        -Location $location -SubnetId $myVnet.Subnets[0].Id -PublicIpAddressId $myPublicIp.Id
    ```

## <a name="step-6-create-a-virtual-machine"></a>Etapa 6: Criar uma máquina virtual
Agora que você tem todas as peças no lugar, é hora de criar a máquina virtual.

1. Execute este comando para definir o nome da conta de administrador e a senha para a máquina virtual.

    ```powershell
    $cred = Get-Credential -Message "Type the name and password of the local administrator account."
    ```
   
    A senha deve ter entre 12 e 123 caracteres e ter pelo menos um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial. 
2. Crie o objeto de configuração para a máquina virtual. Este comando cria um objeto de configuração chamado **myVmConfig** que define o nome e o tamanho da VM.
   
    ```powershell
    $myVm = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS1_v2"
    ```
   
    Consulte [Tamanhos das máquinas virtuais no Azure](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para obter uma lista dos tamanhos disponíveis para uma máquina virtual.
3. Defina as configurações do sistema operacional da VM. Este comando define o nome do computador, o tipo de sistema operacional e as credenciais da conta da VM.
   
    ```powershell
    $myVM = Set-AzureRmVMOperatingSystem -VM $myVM -Windows -ComputerName "myVM" -Credential $cred `
        -ProvisionVMAgent -EnableAutoUpdate
    ```
4. Defina a imagem a ser usada para provisionar a VM. Este comando define a imagem do Windows Server a ser usada para a VM. 
   
    ```powershell
    $myVM = Set-AzureRmVMSourceImage -VM $myVM -PublisherName "MicrosoftWindowsServer" `
        -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
    ```
   
    Para saber mais sobre como selecionar as imagens a usar, consulte [Navegar e selecionar as imagens da máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
5. Adicione a interface de rede que você criou à configuração.
   
    ```powershell
    $myVM = Add-AzureRmVMNetworkInterface -VM $myVM -Id $myNIC.Id
    ```
6. Defina o nome e o local do disco rígido da VM. O arquivo de disco rígido virtual é armazenado em um contêiner. Este comando cria o disco em um contêiner chamado **vhds/myOsDisk1.vhd** na conta de armazenamento que você criou.
   
    ```powershell
    $blobPath = "vhds/myOsDisk1.vhd"
    $osDiskUri = $myStorageAccount.PrimaryEndpoints.Blob.ToString() + $blobPath
    ```
7. Adicione as informações de disco do sistema operacional à configuração da VM. Substitua o valor **$diskName** por um nome para o disco do sistema operacional. Crie a variável e adicione as informações do disco à configuração.
   
    ```powershell
    $myVM = Set-AzureRmVMOSDisk -VM $myVM -Name "myOsDisk1" -VhdUri $osDiskUri -CreateOption fromImage
    ```
8. Finalmente, crie a máquina virtual.
   
    ```powershell
    New-AzureRmVM -ResourceGroupName $myResourceGroup -Location $location -VM $myVM
    ```

## <a name="next-steps"></a>Próximas etapas
* Se houver problemas com a implantação, uma próxima etapa será examinar [Solução de problemas comuns de implantação do Azure com o Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Criar uma máquina virtual do Windows com um modelo do Resource Manager](virtual-machines-windows-ps-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Jan17_HO2-->


