<properties
   pageTitle="Configurar várias NICs em uma VM do Windows | Microsoft Azure"
   description="Saiba como criar uma VM com várias NICs anexadas usando o Azure PowerShell ou modelos do Gerenciador de Recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="08/04/2016"
   ms.author="iainfou"/>

# Criando uma VM com diversas NICs
Você pode criar uma VM (máquina virtual) no Azure que tenha várias NICs (interfaces de rede virtual) anexadas a ela. Um cenário comum seria ter sub-redes diferentes para conectividade de front-end e de back-end ou uma rede dedicada a uma solução de monitoramento ou de backup. Este artigo fornece comandos rápidos para criar uma VM com várias NICs anexadas a ela. Para obter informações detalhadas, incluindo como criar várias NICs dentro de seus próprios scripts do PowerShell, leia mais sobre a [implantação de VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamanhos de VM](virtual-machines-windows-sizes.md) dão suporte a um número variável de NICs, sendo assim, dimensione sua VM adequadamente.

>[AZURE.WARNING] Você deverá anexar várias NICs quando criar a VM – não é possível adicionar NICs a uma VM existente. Você pode [criar uma nova VM com base nos discos virtuais originais](virtual-machines-windows-specialized-image.md) e criar várias NICs conforme implantar a VM.

## Criar recursos fundamentais
Verifique se você tem a versão mais recente do [Azure PowerShell instalada e configurada](../powershell-install-configure.md).

Em primeiro lugar, crie um grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name TestRG -Location WestUS
```

Crie uma conta de armazenamento para manter suas VMs:

```powershell
$storageAcc = New-AzureRmStorageAccount -Name teststorage `
    -ResourceGroupName TestRG -Kind Storage -SkuName Premium_LRS -Location WestUS
```

## Crie a rede virtual e as sub-redes
Defina duas sub-redes da rede virtual – uma para o tráfego de front-end e uma para o tráfego de back-end. Crie sua rede virtual com essas sub-redes:

```powershell
$frontEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "FrontEnd" `
    -AddressPrefix 192.168.1.0/24
$backEndSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "BackEnd" `
    -AddressPrefix 192.168.2.0/24


$vnet = New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
    -AddressPrefix 192.168.0.0/16 -Location WestUS `
    -Subnet $frontEndSubnet,$backEndSubnet
```


## Crie múltiplas NICs
Crie duas NICs, anexando uma NIC à sub-rede de front-end e uma NIC à sub-rede de back-end:

```powershell
$frontEnd = $vnet.Subnets|?{$_.Name -eq 'FrontEnd'}
$NIC1 = New-AzureRmNetworkInterface -Name NIC1 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $FrontEnd.Id

$backEnd = $vnet.Subnets|?{$_.Name -eq 'BackEnd'}
$NIC2 = New-AzureRmNetworkInterface -Name NIC2 -ResourceGroupName TestRG `
        -Location WestUS -SubnetId $BackEnd.Id
```

Normalmente, você também criaria um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerenciar e distribuir o tráfego entre suas VMs. O artigo [mais detalhado sobre VMs com várias NICs](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) o orientará na criação de um Grupo de segurança de rede e na atribuição de NICs.


## Criar a máquina virtual
Agora, comece a criar sua configuração de VM. Cada tamanho de VM tem um limite para o número total de NICs que podem ser adicionada a uma VM. Leia mais sobre [Tamanhos de VM do Windows](virtual-machines-windows-sizes.md). O exemplo a seguir usa um tamanho de VM que dá suporte a até duas NICs (`Standard_DS2_v2`):

```powershell
$cred = Get-Credential

$vmConfig = New-AzureRmVMConfig -VMName TestVM -VMSize "Standard_DS2_v2"

$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName TestVM `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
```

Anexe as duas NICs que você criou anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $NIC2.Id
```

Configure o armazenamento e o disco virtual para sua nova VM:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption fromImage
```

Por fim, crie uma VM:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName TestRG -Location WestUS
```

## Criando várias NICs usando modelos do Gerenciador de Recursos
Os modelos do Azure Resource Manager usam arquivos JSON declarativos para definir o seu ambiente. Você pode ler uma [visão geral do Azure Resource Manager](../resource-group-overview.md). Os modelos do Gerenciador de Recursos oferecem uma maneira de criar várias instâncias de um recurso durante a implantação, como a criação de várias NICs. Você usa *copiar* para especificar o número de instâncias a serem criadas:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre a [criação de várias instâncias usando *copiar*](../resource-group-create-multiple.md).

Você também pode usar um `copyIndex()` para acrescentar um número a um nome de recurso, o que lhe permite criar `NIC1`, `NIC2` etc. Veja a seguir um exemplo de como acrescentar o valor de índice:

```bash
"name": "[concat('NIC-', copyIndex())]", 
```

Você pode ler um exemplo completo em [Criando várias NICs usando modelos do Gerenciador de Recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## Próximas etapas
Certifique-se de rever os [Tamanhos de VM do Windows](virtual-machines-windows-sizes.md) ao tentar criar uma VM com várias NICs. Preste atenção ao número máximo de NICs a que cada VM dá suporte.

Lembre-se de que você não pode adicionar mais NICs a uma VM existente; você deve criar todas as NICs quando implantar a VM. Tome cuidado ao planejar suas implantações para certificar-se de que tenha toda a conectividade de rede necessária desde o início.

<!---HONumber=AcomDC_0817_2016-->