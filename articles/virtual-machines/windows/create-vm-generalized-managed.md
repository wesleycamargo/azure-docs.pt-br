---
title: Criar uma VM de uma imagem gerenciada no Azure | Microsoft Docs
description: "Crie uma máquina virtual do Windows a partir de uma imagem de gerenciada generalizada usando o Azure PowerShell ou o portal do Azure no modelo de implantação do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM por meio de uma imagem gerenciada

Você pode criar várias VMs de uma imagem de VM gerenciada usando o PowerShell ou o portal do Azure. Uma imagem de VM gerenciada contém as informações necessárias para criar uma VM, incluindo o sistema operacional e os discos de dados. Os VHDs que formam a imagem, incluindo os discos do sistema operacional e quaisquer discos de dados, são armazenados como discos gerenciados. 

Você já precisa ter [criado uma imagem de VM gerenciada](capture-image-resource.md) a ser usada para criação da nova VM. 

## <a name="use-the-portal"></a>Usar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No menu do lado esquerdo, selecione **Todos os recursos**. Você pode classificar os recursos por **Tipo** localizar facilmente as suas imagens.
3. Selecione a imagem que você deseja usar a partir da lista. A imagem da página **Visão geral** será aberta.
4. Clique em **+ Criar VM** a partir do menu.
5. Insira as informações da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Ao concluir, clique em **OK**. Você pode criar a nova VM em um grupo de recursos existente ou escolher **Criar novo** para criar um novo grupo de recursos para armazenar a VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. 
7. Em **Configurações**, faça as alterações necessárias e clique em **Ok**. 
8. Na página de resumo, você deve visualizar o nome da sua imagem listado para **Imagem privada**. Clique em **Ok** para iniciar a implantação da máquina virtual.


## <a name="use-powershell"></a>Usar o PowerShell

### <a name="prerequisites"></a>Pré-requisitos

Verifique se você tem as últimas versões dos módulos AzureRM.Compute e AzureRM.Network do PowerShell. Abra um prompt do PowerShell como Administrador e execute o comando a seguir para instalá-los.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).



### <a name="collect-information-about-the-image"></a>Coletar informações sobre a imagem

Primeiramente, precisamos coletar informações básicas sobre a imagem e criar uma variável para ela. Este exemplo usa uma imagem de VM gerenciada chamada **myImage**, que está no grupo de recursos **myResourceGroup** no local **Centro-Oeste dos EUA**. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Criar uma rede virtual
Crie a vNet e a sub-rede da [rede virtual](../../virtual-network/virtual-networks-overview.md).

Crie a sub-rede. Este exemplo cria uma sub-rede chamada **mySubnet** com um prefixo de endereço de **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Crie a rede virtual. Este exemplo cria uma rede virtual chamada **myVnet** com o prefixo de endereço de **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Criar um endereço IP público e um adaptador de rede

Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

Criar um endereço IP público. Este exemplo cria um endereço IP público chamado **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Crie a NIC. Este exemplo cria uma NIC chamada **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para fazer logon em sua VM usando RDP, é preciso ter uma NSG (regra de segurança de rede) que permita acesso de RDP na porta 3389. 

Este exemplo cria um NSG chamado **myNsg** que contém uma regra chamada **myRdpRule** que permite tráfego RDP pela porta 3389. Para obter mais informações sobre NSGs, consulte [Abrir portas para uma VM no Azure usando PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual concluída. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Obter as credenciais para a VM

O cmdlet a seguir abrirá uma janela na qual você vai inserir um novo nome de usuário e senha para usar como a conta de administrador local para acessar remotamente a VM. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Definir variáveis para o nome da VM, o nome do computador e o tamanho da VM

Crie variáveis para o nome da VM e do computador. Este exemplo define o nome da VM como **myVM** e o nome do computador como **myComputer**.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Defina o tamanho da máquina virtual. Este exemplo cria a VM de tamanho **Standard_DS1_v2**. Veja a documentação sobre os [tamanhos de VM](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/) para obter mais informações.

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Adicione o nome e o tamanho da VM à configuração da VM.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Definir a imagem da VM como imagem de origem para a nova VM

Defina a imagem de origem usando a ID da imagem da VM gerenciada.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Defina a configuração do SO e adicione a NIC.

Insira o tipo de armazenamento (PremiumLRS ou StandardLRS) e o tamanho do disco do SO. Este exemplo define o tipo de conta como **PremiumLRS**, o tamanho do disco como **128 GB** e o cache do disco como **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Criar a VM

Crie a nova VM usando a configuração que criamos e armazenamos na variável **$vm**.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Verificar se a VM foi criada
Ao concluir, você deverá ver a VM recém-criada no [portal do Azure](https://portal.azure.com) em **Navegar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

