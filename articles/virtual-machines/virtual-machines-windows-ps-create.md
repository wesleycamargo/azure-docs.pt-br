---
title: Criar uma VM do Azure usando o PowerShell | Microsoft Docs
description: Use o Azure Powershell e o Azure Resource Manager para criar facilmente uma nova VM executando o Windows Server.
services: virtual-machines-windows
documentationcenter: ''
author: davidmu1
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/27/2016
ms.author: davidmu

---
# Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell
Este artigo mostra como criar rapidamente uma Máquina Virtual do Azure que executa o Windows Server e os recursos que ela precisa usando o [Gerenciador de Recursos](../resource-group-overview.md) e o PowerShell.

Todas as etapas neste artigo são necessárias para criar uma máquina virtual e devem demorar cerca de 30 minutos para serem executadas.

## Etapa 1: instalar o PowerShell do Azure
Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para saber mais sobre como instalar a versão mais recente do Azure PowerShell, selecionar a assinatura e entrar em sua conta.

## Etapa 2: criar um grupo de recursos
Primeiro, crie um grupo de recursos.

1. Obtenha uma lista dos locais disponíveis nos quais os recursos podem ser criados.
   
        Get-AzureRmLocation | sort Location | Select Location
   
    Você deverá ver algo como este exemplo:
   
        Location
        --------
        australiaeast
        australiasoutheast
        brazilsouth
        canadacentral
        canadaeast
        centralindia
        centralus
        eastasia
        eastus
        eastus2
        japaneast
        japanwest
        northcentralus
        northeurope
        southcentralus
        southeastasia
        southindia
        westeurope
        westindia
        westus
2. Substitua o valor **$locName** por um local na lista. Crie a variável.
   
        $locName = "centralus"
3. Substitua o valor **$rgName** por um nome para o novo grupo de recursos. Crie a variável e o grupo de recursos.
   
        $rgName = "mygroup1"
        New-AzureRmResourceGroup -Name $rgName -Location $locName

## Etapa 3: criar uma conta de armazenamento
É necessário ter uma [conta de armazenamento](../storage/storage-introduction.md) para armazenar o disco rígido virtual usado pela máquina virtual criada por você.

1. Substitua o valor de **$stName** por um nome para a conta de armazenamento. Teste a exclusividade do nome.
   
        $stName = "mystorage1"
        Get-AzureRmStorageAccountNameAvailability $stName
   
    Se esse comando retornar **True**, o nome proposto será exclusivo no Azure. Os nomes da conta de armazenamento devem ter entre 3 e 24 caracteres e podem conter apenas números e letras minúsculas.
2. Agora, execute o comando para criar a conta de armazenamento.
   
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -SkuName "Standard_LRS" -Kind "Storage" -Location $locName

## Etapa 4: criar uma rede virtual
Todas as máquinas virtuais fazem parte de uma [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Substitua o valor de **$subnetName** por um nome para a sub-rede. Crie a variável e a sub-rede.
   
        $subnetName = "mysubnet1"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
2. Substitua o valor de **$subnetName** por um nome para a rede virtual. Crie a variável e a rede virtual com a sub-rede.
   
        $vnetName = "myvnet1"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
   
    Use valores que fazem sentido para seu aplicativo e ambiente.

## Etapa 5: Criar um endereço IP público e interface de rede
Para habilitar a comunicação com a máquina virtual na rede virtual, é necessário um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Substitua o valor de **$ipName** pelo nome do endereço IP público. Criar a variável e o endereço IP público.
   
        $ipName = "myIPaddress1"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
2. Substitua o valor de **$nicName** pelo nome do adaptador de rede. Crie a variável e a interface de rede.
   
        $nicName = "mynic1"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## Etapa 6: Criar uma máquina virtual
Agora que você tem todas as peças no lugar, é hora de criar a máquina virtual.

1. Execute o comando para definir o nome da conta de administrador e a senha para a máquina virtual.
   
        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
   
    A senha deve ter entre 12 e 123 caracteres e ter pelo menos um caractere minúsculo, um caractere maiúsculo, um número e um caractere especial.
2. Substitua o valor **$vmName** por um nome para a máquina virtual. Crie a variável e a configuração da máquina virtual.
   
        $vmName = "myvm1"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
   
    Consulte [Tamanhos das máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para obter uma lista dos tamanhos disponíveis para uma máquina virtual.
3. Substitua o valor **$compName** por um nome de computador para a máquina virtual. Criar a variável e adicione as informações do sistema operacional à configuração.
   
        $compName = "myvm1"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
4. Defina a imagem a ser usada para provisionar a máquina virtual.
   
        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
   
    Para saber mais sobre como selecionar as imagens a usar, consulte [Navegar e selecionar as imagens da máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines-windows-cli-ps-findimage.md).
5. Adicione a interface de rede que você criou à configuração.
   
        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
6. Substitua o valor **$blobPath** por um caminho e nome de arquivo no armazenamento do disco rígido virtual. Normalmente, o arquivo do disco rígido virtual é armazenado em um contêiner, por exemplo, **vhds/WindowsVMosDisk.vhd**. Crie as variáveis.
   
        $blobPath = "vhds/WindowsVMosDisk.vhd"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
7. Substitua o valor **$diskName** por um nome para o disco do sistema operacional. Crie a variável e adicione as informações do disco à configuração.
   
        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
8. Finalmente, crie a máquina virtual.
   
        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm
   
    Você deve ver o grupo de recursos e todos os seus recursos no portal do Azure, e um status de êxito na janela do PowerShell:
   
        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK

## Próximas etapas
* Se houver problemas com a implantação, uma próxima etapa será examinar [Solucionando problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
* Saiba como gerenciar a máquina virtual que você criou examinando [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).
* Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Criar uma máquina virtual do Windows com um modelo do Resource Manager](virtual-machines-windows-ps-template.md)

<!---HONumber=AcomDC_1005_2016-->