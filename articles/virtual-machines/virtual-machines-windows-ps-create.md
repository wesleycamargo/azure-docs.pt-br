<properties
	pageTitle="Criar uma VM do Azure usando o PowerShell | Microsoft Azure"
	description="Use o Azure Powershell e o Azure Resource Manager para criar facilmente uma nova VM executando o Windows Server."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/12/2016"
	ms.author="davidmu"/>

# Criar uma VM do Windows usando o Gerenciador de Recursos e o PowerShell

Este artigo mostra como criar rapidamente uma Máquina Virtual do Azure que executa o Windows Server e seus recursos associados usando o Gerenciador de Recursos e o PowerShell.

Deve levar cerca de 30 minutos para executar as etapas neste artigo.

## Etapa 1: instalar o PowerShell do Azure

Consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do PowerShell do Azure, selecionar a assinatura que você deseja usar e entrar em sua conta do Azure.
        
## Etapa 2: criar um grupo de recursos

Todos os recursos devem estar implantados em um grupo de recursos. Consulte [Visão geral do Azure Resource Manager](../resource-group-overview.md) para obter mais informações.

1. Obtenha uma lista dos locais disponíveis onde os recursos podem ser criados.

	    Get-AzureLocation | sort Name | Select Name

2. Substitua o valor **$locName** por um local na lista, por exemplo, **Centro dos EUA**. Crie a variável.

        $locName = "location name"
        
3. Substitua o valor **$rgName** pelo nome do novo grupo de recursos. Crie a variável e o grupo de recursos.

        $rgName = "resource group name"
        New-AzureRmResourceGroup -Name $rgName -Location $locName
    
## Etapa 3: criar uma conta de armazenamento

Uma conta de armazenamento é necessária para armazenar o disco rígido virtual associado à máquina virtual criada.

1. Substitua o valor **$stName** (letras minúsculas e números somente) pelo nome da conta de armazenamento. Teste a exclusividade do nome.

        $stName = "storage account name"
        Test-AzureName -Storage $stName

    Se esse comando retornar **False**, o nome proposto será exclusivo.
    
2. Agora, execute o comando para criar a conta de armazenamento.
    
        $storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_LRS" -Location $locName
        
## Etapa 4: criar uma rede virtual

Todas as máquinas virtuais devem ser associadas a uma rede virtual.

1. Substitua o valor **$subnetName** pelo nome da sub-rede. Crie a variável e a sub-rede.
    	
        $subnetName = "subnet name"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
2. Substitua o valor **$vnetName** pelo nome da rede virtual. Crie a variável e a rede virtual com a sub-rede.

        $vnetName = "virtual network name"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
        
## Etapa 5: Criar um endereço IP público e interface de rede

Para habilitar a comunicação com a máquina virtual na rede virtual, é necessário um endereço IP público e uma interface de rede.

1. Substitua o valor **$ipName** pelo nome do endereço IP público. Criar a variável e o endereço IP público.

        $ipName = "public IP address name"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Substitua o valor **$nicName** pelo nome da interface da rede. Crie a variável e a interface de rede.

        $nicName = "network interface name"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
        
## Etapa 6: Criar uma máquina virtual

Agora que você tem todas as peças no lugar, é hora de criar a máquina virtual.

1. Execute o comando para definir o nome da conta de administrador e a senha para a máquina virtual.

        $cred = Get-Credential -Message "Type the name and password of the local administrator account."
        
2. Substitua o valor **$vmName** pelo nome da rede virtual. Crie a variável e a configuração da máquina virtual.

        $vmName = "virtual machine name"
        $vm = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A1"
        
    Consulte [Tamanhos das máquinas virtuais no Azure](virtual-machines-windows-sizes.md) para obter uma lista dos tamanhos disponíveis para uma máquina virtual.
    
3. Substitua o valor **$compName** pelo nome do computador da máquina virtual. Criar a variável e adicione as informações do sistema operacional à configuração.

        $compName = "computer name"
        $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $compName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
        
4. Defina a imagem a ser usada para provisionar a máquina virtual.

        $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        
    Consulte [Navegar e selecionar as imagens da máquina virtual do Windows no Azure com o PowerShell ou a CLI](virtual-machines-windows-cli-ps-findimage.md) para obter mais informações sobre como selecionar as imagens a usar.
        
5. Adicione a interface de rede que você criou à configuração.

        $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
        
6. Substitua o valor **$blobPath** pelo caminho e nome de arquivo no armazenamento do disco rígido virtual. Normalmente, o arquivo vhd é armazenado em um contêiner, por exemplo, "vhds/WindowsVMosDisk.vhd". Crie as variáveis.

        $blobPath = "vhd path and file name"
        $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
        
7. Substitua o valor **$diskName** pelo nome do disco do sistema operacional. Crie a variável e adicione as informações do disco à configuração.

        $diskName = "windowsvmosdisk"
        $vm = Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
        
8. Finalmente, crie a máquina virtual.

        New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

    Você deve ver o grupo de recursos e todos os seus recursos no portal do Azure, e um status de êxito na janela do PowerShell:

        RequestId  IsSuccessStatusCode  StatusCode  ReasonPhrase
        ---------  -------------------  ----------  ------------
                                  True          OK  OK
                                  
## Próximas etapas

- Se houver problemas com a implantação, a próxima etapa será examinar [Solucionando os problemas de implantações do grupo de recursos com o Portal do Azure](../resource-manager-troubleshoot-deployments-portal.md)
- Saiba como gerenciar a máquina virtual que você acabou de criar examinando [Gerenciar as máquinas virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).
- Aproveite o uso de um modelo para criar uma máquina virtual usando as informações em [Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos](virtual-machines-windows-ps-template.md)

<!---HONumber=AcomDC_0420_2016-->