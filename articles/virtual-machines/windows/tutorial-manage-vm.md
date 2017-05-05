---
title: "Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell | Microsoft Docs"
description: "Tutorial - Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell"
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 04/21/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: e0bfa7620feeb1bad33dd2fe4b32cb237d3ce158
ms.openlocfilehash: da5bcb0c4b848f27ae5997caf52e332cc4ce4c0a
ms.lasthandoff: 04/21/2017

---

# <a name="create-and-manage-windows-vms-with-the-azure-powershell-module"></a>Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell

Este tutorial aborda itens básicos de criação de Máquina Virtual do Azure, como a seleção de um tamanho de VM, seleção de uma imagem de VM e implantação de uma VM. Este tutorial também aborda as operações básicas de gerenciamento, como gerenciamento de estado, exclusão e redimensionamento de uma VM.

As etapas neste tutorial podem ser concluídas usando o módulo mais recente do [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

## <a name="create-resource-group"></a>Criar grupo de recursos

Crie um grupo de recursos com o comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. Você deve criar um grupo de recursos antes de criar uma máquina virtual. Neste exemplo, criaremos um grupo de recursos `myResourceGroupVM ` na região `westus`. 

```powershell
New-AzureRmResourceGroup -ResourceGroupName myResourceGroupVM -Location westeurope
```

O grupo de recursos é especificado ao criar ou modificar uma VM, que pode ser visto durante este tutorial.

## <a name="create-virtual-machine"></a>Criar máquina virtual

Uma máquina virtual precisa estar conectada a uma rede virtual. Você se comunica com a máquina virtual usando um endereço IP público por meio de uma placa da interface de rede.

### <a name="create-virtual-network"></a>Criar rede virtual

Crie uma sub-rede com [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig):

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
```

Crie uma rede virtual com [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork):

```powershell
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName myResourceGroupVM `
  -Location westeurope `
  -Name myVnet `
  -AddressPrefix 192.168.0.0/16 ` 
  -Subnet $subnetConfig
```
### <a name="create-public-ip-address"></a>Criar um endereço IP público

Crie um endereço IP público com [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress):

```powershell
$pip = New-AzureRmPublicIpAddress ` 
  -ResourceGroupName myResourceGroupVM `
  -Location westeurope ` 
  -AllocationMethod Static `
  -Name myPublicIPAddress
```

### <a name="create-network-interface-card"></a>Criar a placa da interface de rede

Crie a placa de interface de rede com [New-AzureRmNetworkInterface](/powershell/module/azurerm.network/new-azurermnetworkinterface):

```powershell
$nic = New-AzureRmNetworkInterface `
  -ResourceGroupName myResourceGroupVM  `
  -Location westeurope `
  -Name myNic `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id
```

### <a name="create-network-security-group"></a>Criar um grupo de segurança de rede

Um [grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md) (NSG) do Azure controla o tráfego de entrada e saída em uma ou mais máquinas virtuais. As regras de grupo de segurança de rede permitem ou negam o tráfego de rede em uma porta específica ou em um intervalo de porta. Essas regras também podem incluir um prefixo do endereço de origem para que somente o tráfego originado em uma fonte predefinida possa se comunicar com uma máquina virtual. Para acessar o servidor da Web do IIS que você está instalando, adicione uma regra NSG de entrada.

Para criar uma regra NSG de entrada, use [Add-AzureRmNetworkSecurityRuleConfig](/powershell/module/azurerm.network/add-azurermnetworksecurityruleconfig). O exemplo a seguir cria uma regra NSG chamada `myNSGRule` que abre a porta `3389` para a máquina virtual:

```powershell
$nsgRule = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNSGRule `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow
```

Crie a NSG usando `myNSGRule` com [New-AzureRmNetworkSecurityGroup](/powershell/module/azurerm.network/new-azurermnetworksecuritygroup):

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName myResourceGroupVM  -Location westeurope -Name myNetworkSecurityGroup -SecurityRules $nsgRule
```

Adicione a NSG à sub-rede na rede virtual com [Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig):

```powershell
Set-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -VirtualNetwork $vnet -NetworkSecurityGroup $nsg -AddressPrefix 192.168.1.0/24
```

Atualize a rede virtual com [Set-AzureRmVirtualNetwork](/powershell/module/azurerm.network/set-azurermvirtualnetwork):

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```

### <a name="create-virtual-machine"></a>Criar máquina virtual

Há várias opções disponíveis ao criar uma máquina virtual, como a imagem do sistema operacional, as credenciais administrativas e o dimensionamento do disco. Neste exemplo, uma máquina virtual é criada com um nome de `myVM` executando a versão mais recente do Windows Server 2016 Datacenter.

Defina o nome de usuário e a senha necessários para a conta de administrador na máquina virtual com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```powershell
$cred = Get-Credential
```

Crie a configuração inicial para a máquina virtual com [New-AzureRmVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig):

```powershell
$vm = New-AzureRmVMConfig -VMName myVM -VMSize Standard_D1
```

Adicione as informações do sistema operacional à configuração da máquina virtual com [Set-AzureRmVMOperatingSystem](/powershell/module/azurerm.compute/set-azurermvmoperatingsystem):

```powershell
$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName myVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
```

Adicione as informações da imagem á configuração da máquina virtual com [Set-AzureRmVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage):

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest
```

Adicione as configurações de disco do sistema operacional à configuração da máquina virtual com [Set-AzureRmVMOSDisk](/powershell/module/azurerm.compute/set-azurermvmosdisk):

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm -Name myOsDisk -DiskSizeInGB 128 -CreateOption FromImage -Caching ReadWrite
```

Adicione a placa de interface de rede que você criou anteriormente à configuração da máquina virtual com [Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface):

```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

Crie a máquina virtual com [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```powershell
New-AzureRmVM -ResourceGroupName myResourceGroupVM  -Location westeurope -VM $vm
```

## <a name="connect-to-vm"></a>Conectar-se a uma VM

Após a implantação, crie uma conexão de área de trabalho remota com a máquina virtual.

Execute os comandos a seguir para retornar o endereço IP público da máquina virtual. Anote esse endereço IP para se conectar a ele com o navegador para testar a conectividade à Web em uma etapa futura.

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroupVM  | Select IpAddress
```

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo `publicIPAddress` de sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```powershell
mstsc /v:<publicIpAddress>
```

## <a name="understand-vm-images"></a>Entender as imagens de VM

O Azure Marketplace inclui várias imagens de máquina virtual que podem ser usadas para criar uma nova máquina virtual. Nas etapas anteriores, uma máquina virtual foi criada usando a imagem do Windows Server 2016-Datacenter. Nesta etapa, o módulo do PowerShell é usado para pesquisar no marketplace por outras imagens do Windows, que também pode servir como base para novas VMs. Esse processo consiste em localizar o nome do editor, da oferta e da imagem (Sku). 

Use o comando [Get-AzureRmVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher) para retornar uma lista de editores de imagem.  

```powersehll
Get-AzureRmVMImagePublisher -Location "westus"
```

Use o comando [Get-AzureRmVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer) para retornar uma lista de ofertas de imagem. Com este comando, a lista retornada é filtrada no editor especificado. 

```powershell
Get-AzureRmVMImageOffer -Location "westus" -PublisherName "MicrosoftWindowsServer"
```

```powershell
Offer             PublisherName          Location
-----             -------------          -------- 
Windows-HUB       MicrosoftWindowsServer westus 
WindowsServer     MicrosoftWindowsServer westus   
WindowsServer-HUB MicrosoftWindowsServer westus   
```

O comando [Get-AzureRmVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) filtrará o nome do editor e da oferta para retornar uma lista com nomes de imagem.

```powershell
Get-AzureRmVMImageSku -Location "westus" -PublisherName "MicrosoftWindowsServer" -Offer "WindowsServer"
```

```powershell
Skus                            Offer         PublisherName          Location
----                            -----         -------------          --------
2008-R2-SP1                     WindowsServer MicrosoftWindowsServer westus  
2008-R2-SP1-BYOL                WindowsServer MicrosoftWindowsServer westus  
2012-Datacenter                 WindowsServer MicrosoftWindowsServer westus  
2012-Datacenter-BYOL            WindowsServer MicrosoftWindowsServer westus  
2012-R2-Datacenter              WindowsServer MicrosoftWindowsServer westus  
2012-R2-Datacenter-BYOL         WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter                 WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter-Server-Core     WindowsServer MicrosoftWindowsServer westus  
2016-Datacenter-with-Containers WindowsServer MicrosoftWindowsServer westus  
2016-Nano-Server                WindowsServer MicrosoftWindowsServer westus
```

Essas informações podem ser usadas para implantar uma VM com uma imagem específica. Este exemplo define o nome da imagem no objeto da VM. Consulte os exemplos anteriores neste tutorial para obter as etapas completas de implantação.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter-with-Containers -Version latest
```

## <a name="understand-vm-sizes"></a>Entender os tamanhos de VM

Um tamanho de máquina virtual determina a quantidade de recursos de computação, como memória, CPU e GPU que estão disponíveis para a máquina virtual. As máquinas virtuais precisam ser criadas com um tamanho apropriado para a carga de trabalho esperada. Se a carga de trabalho aumentar, uma máquina virtual existente pode ser redimensionada.

### <a name="vm-sizes"></a>Tamanhos de VM

A tabela a seguir categoriza tamanhos em casos de uso.  

| Tipo                     | Tamanhos           |    Descrição       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| Propósito geral         |DSv2, Dv2, DS, D, Av2, A0-7| CPU/memória equilibrados. Ideal para desenvolvimento/teste e para aplicativos de pequeno a médio porte e soluções de dados.  |
| Otimizado para computação      | Fs, F             | Relação de CPU/memória alta. Boa para aplicativos de tráfego médio, dispositivos de rede e processos em lote.        |
| Otimizado para memória       | GS, G, DSv2, DS, Dv2, D   | Relação de memória/núcleo alta. Ótima para banco de dados relacionais, caches médios a grandes e análises na memória.                 |
| Otimizado para armazenamento       | Ls                | Alta taxa de transferência de disco e de E/S. Ideal para Big Data, SQL e bancos de dados NoSQL.                                                         |
| GPU           | NV, NC            | VMs especializadas, destinadas para renderização gráfica e edição de vídeo pesadas.       |
| Alto desempenho | H, A8-11          | Nossas VMs de CPU mais potentes com adaptadores de rede de alto rendimento (RDMA) opcionais. 


### <a name="find-available-vm-sizes"></a>Encontrar tamanhos de VM disponíveis

Para ver uma lista de tamanhos de VM disponíveis em uma região específica, use o comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize).

```powershell
Get-AzureRmVMSize -Location westus
```

## <a name="resize-a-vm"></a>Redimensionar uma VM

Após a implantação de uma VM, ela pode ser redimensionada para aumentar ou diminuir a alocação de recursos.

Antes de redimensionar uma VM, verifique se o tamanho desejado está disponível no cluster da VM atual. O comando [Get-AzureRmVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) retorna uma lista de tamanhos. 

```powershell
Get-AzureRmVMSize -ResourceGroupName myResourceGroupVM -VMName myVM 
```

Se o tamanho desejado estiver disponível, a VM poderá ser redimensionada a partir de um estado ligado. No entanto, ela é reinicializada durante a operação.

```powershell
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM 
$vm.HardwareProfile.VmSize = "Standard_D4"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
```

Se o tamanho desejado não estiver no cluster atual, a VM precisará ser desalocada antes que a operação de redimensionamento ocorra. Observe que quando a VM é ligada novamente, quaisquer dados no disco temporário são removidos, e o endereço IP público muda, a menos que um endereço IP estático esteja sendo usado. 

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroupVM  -VMName myVM
$vm.HardwareProfile.VmSize = "Standard_F4s"
Update-AzureRmVM -VM $vm -ResourceGroupName myResourceGroupVM 
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name $vm.name
```

## <a name="vm-power-states"></a>Estados de energia da VM

Uma VM do Azure pode ter um dentre vários estados de energia. Esse estado representa o estado atual da VM do ponto de vista do hipervisor. 

### <a name="power-states"></a>Estados de energia

| Estado de energia | Descrição
|----|----|
| Iniciando | Indica que a máquina virtual está sendo iniciada. |
| Executando | Indica que a máquina virtual está em execução. |
| Parando | Indica que a máquina virtual está sendo interrompida. | 
| Parada | Indica que a máquina virtual foi parada. Observe que máquinas virtuais no estado parado ainda incorrem em encargos de computação.  |
| Desalocando | Indica que a máquina virtual está sendo desalocada. |
| Desalocada | Indica que a máquina virtual foi completamente removidos do hipervisor, mas ainda está disponível no plano de controle. Máquinas virtuais no estado Desalocado não incorrem em encargos de computação. |
| - | Indica que o estado de energia da máquina virtual é desconhecido. |

### <a name="find-power-state"></a>Localizar o estado de energia

Para recuperar o estado de uma VM específica, use o comando [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm). Especifique um nome válido para uma máquina virtual e grupo de recursos. 

```powershell
Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Status | Select @{n="Status"; e={$_.Statuses[1].Code}}
```

Saída:

```powershell
Status
------
PowerState/running
```

## <a name="management-tasks"></a>Tarefas de gerenciamento

Durante o ciclo de vida de uma máquina virtual, é possível que você queira executar tarefas de gerenciamento, como inicialização, interrupção ou exclusão de uma máquina virtual. Além disso, é possível que você queira criar scripts para automatizar tarefas repetitivas ou complexas. Usando o Azure PowerShell, muitas tarefas comuns de gerenciamento podem ser executadas em linha de comando ou em scripts.

### <a name="stop-virtual-machine"></a>Como interromper uma máquina virtual

Pare e desaloque uma máquina virtual com [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm):

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroupVM -Name "myVM" -Force
```

Se você quiser manter a máquina virtual em um estado de provisionamento, use o parâmetro -StayProvisioned.

### <a name="start-virtual-machine"></a>Como iniciar uma máquina virtual

```powershell
Start-AzureRmVM -ResourceGroupName myResourceGroupVM  -Name myVM
```

### <a name="delete-resource-group"></a>Excluir grupo de recursos

Excluir um grupo de recursos exclui todos os recursos contidos nele.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroupVM  -Force
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu sobre o gerenciamento e criação da VM básica. Avança para o próximo tutorial para saber mais sobre os discos de VM.  

[Criar e gerenciar discos de VM](./tutorial-manage-data-disk.md)
