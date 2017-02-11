---
title: "Criar uma VM do Windows com várias NICs | Microsoft Docs"
description: "Saiba como criar uma VM do Windows com várias NICs anexadas usando o Azure PowerShell ou modelos do Gerenciador de Recursos."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 45a45b616b4de005da66562c69eef83f2f48cc79
ms.openlocfilehash: d06a5c281b9390ae7d7f3a9ef18b27b62355a33f


---
# <a name="creating-a-windows-vm-with-multiple-nics"></a>Criando uma VM do Windows com várias NICs
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma VM com várias NICs anexadas a ela. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts do PowerShell, leia mais sobre a [implantação de VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamanhos de VM](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

> [!WARNING]
> Você deverá anexar várias NICs quando criar a VM – não é possível adicionar NICs a uma VM existente. Você pode [criar uma VM com base nos discos virtuais originais](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e criar várias NICs conforme implantar a VM.
> 
> 

## <a name="create-core-resources"></a>Criar recursos fundamentais
Verifique se você tem a versão mais recente do [Azure PowerShell instalada e configurada](/powershell/azureps-cmdlets-docs). Faça logon na sua Conta do Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `mystorageaccount` e `myVM`.

Em primeiro lugar, crie um grupo de recursos. O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `WestUs`:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Crie uma conta de armazenamento para manter suas VMs. O exemplo a seguir cria uma conta de armazenamento chamada `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Crie a rede virtual e as sub-redes
Defina duas sub-redes da rede virtual – uma para o tráfego de front-end e uma para o tráfego de back-end. O exemplo a seguir cria duas sub-redes, chamadas `mySubnetFrontEnd` e `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Crie a rede virtual e as respectivas sub-redes. O exemplo a seguir cria uma rede virtual chamada `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Crie múltiplas NICs
Crie duas NICs, anexando uma NIC à sub-rede de front-end e uma NIC à sub-rede de back-end. O exemplo a seguir cria duas NICs, chamadas `myNic1` e `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Normalmente, você também criaria um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. O artigo [mais detalhado sobre VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) o orientará na criação de um Grupo de segurança de rede e na atribuição de NICs.

## <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora, comece a criar sua configuração de VM. Cada tamanho de VM tem um limite para o número total de NICs que podem ser adicionada a uma VM. Leia mais sobre [Tamanhos de VM do Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Primeiro, defina suas credenciais de VM para a variável `$cred` da seguinte maneira:

```powershell
$cred = Get-Credential
```

O exemplo a seguir define uma VM chamada `myVM` e usa um tamanho de VM que dá suporte a até duas NICs (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Crie o restante de sua configuração de VM. O exemplo a seguir cria uma VM do Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Anexe as duas NICs que você criou anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configure o armazenamento e o disco virtual para sua nova VM:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Por fim, crie uma VM:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Criando várias NICs usando modelos do Gerenciador de Recursos
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../resource-group-create-multiple.md). 

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `myNic1`, `MyNic2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximas etapas
Certifique-se de rever os [Tamanhos de VM do Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte. 

Lembre-se de que você não pode adicionar mais NICs a uma VM existente; você deve criar todas as NICs quando implantar a VM. Tome cuidado ao planejar suas implantações para certificar-se de que tenha toda a conectividade de rede necessária desde o início.




<!--HONumber=Dec16_HO2-->


