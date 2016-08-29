<properties
	pageTitle="Criar uma imagem da VM de uma VM do Azure | Microsoft Azure"
	description="Saiba como criar uma imagem generalizada da VM de uma VM existente do Azure criada no modelo de implantação do Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="cynthn"/>

# Como criar uma imagem de VM de uma VM existente do Azure


Este artigo mostra como usar o Azure PowerShell para criar uma imagem generalizada de uma VM existente do Azure. Depois você pode usar a imagem para criar outra VM. Esta imagem inclui o disco do sistema operacional e os discos de dados anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, de modo que você precisará configurar esses recursos quando criar uma VM usando a imagem. Esse processo cria uma [imagem generalizada do Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Pré-requisitos

- Estas etapas pressupõem que você já tenha uma máquina virtual do Azure no modelo de implantação do Resource Manager que deseja usar para criar a imagem. Você precisa do nome da VM e do nome do grupo de recursos. É possível obter uma lista dos grupos de recursos na sua assinatura digitando o cmdlet `Get-AzureRmResourceGroup` do PowerShell. É possível obter uma lista de VMs em sua assinatura digitando `Get-AzureRMVM`.

- Você precisa ter a versão 1.0.x do Azure PowerShell instalada. Se você ainda não tiver instalado o PowerShell, leia [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para ver as etapas de instalação.

- Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

## Preparar a VM de origem 

Esta seção mostra como generalizar a máquina virtual do Windows para que ela possa ser usada como uma imagem.

> [AZURE.WARNING] Você não pode fazer logon na VM por RDP depois que ela for generalizada, pois o processo remove todas as contas de usuário. As alterações são irreversíveis.

1. Conecte-se à sua máquina virtual do Windows. No [Portal do Azure](https://portal.azure.com), navegue por **Procurar** > **Máquinas virtuais** > Sua máquina virtual do Windows > **Conectar**.

2. Abra uma janela de Prompt de comando como administrador.

3. Altere o diretório para `%windir%\system32\sysprep` e execute sysprep.exe.

4. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, faça o seguinte:

	- Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

	- Em **Opções de Desligamento**, selecione **Desligar**.

	- Clique em **OK**.

	![Executar o Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   O Sysprep desliga a máquina virtual. O status muda para **Parado** no Portal do Azure.


## Fazer logon no Azure PowerShell

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.

		Login-AzureRmAccount

	Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.

2. Obtenha as IDs de assinatura das assinaturas disponíveis.

		Get-AzureRmSubscription

3. Defina a assinatura correta usando a ID da assinatura.

		Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"


## Desalocar a VM e definir o estado como generalizada		

1. Desaloque os recursos da VM.

		Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>

	O *Status* da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)**.

2. Defina o status da máquina virtual como **Generalizado**.

		Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized

3. Verifique o status da VM. A seção **OSState/generalizado** da VM deve ter **DisplayStatus** definido como **VM generalizada**.
		
		$vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -status
		$vm.Statuses

		
## Criar a imagem 

1. Copie a imagem da máquina virtual para o contêiner de armazenamento de destino usando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. A variável `-Path` salva uma cópia do modelo JSON localmente. A variável `-DestinationContainerName` é o nome do contêiner em que você quer manter as imagens. Se o contêiner não existir, ele será criado para você.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -Name YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	Você pode obter a URL da imagem no modelo do arquivo JSON. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **imagem** > **uri** para obter o caminho completo da imagem. A URL da imagem parece com esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
	
	Você também pode verificar o URI no portal. A imagem é copiada para um blob denominado **sistema** em sua conta de armazenamento.

2. Crie uma variável para o caminho até a imagem.

		$imageURI = "<https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd>"


## Criar uma rede virtual

Crie a rede virtual e a sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).
		

1. Substitua o valor das variáveis pelas suas próprias informações. Forneça o prefixo do endereço da sub-rede no formato CIDR. Crie as variáveis e a sub-rede.

    	$rgName = "<resourceGroup>"
		$location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
        
2. Substitua o valor de **$vnetName** por um nome para a rede virtual. Forneça o prefixo do endereço da rede virtual no formato CIDR. Crie a variável e a rede virtual com a sub-rede.

        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $locName -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet
        
            
## Criar um endereço IP público e um adaptador de rede

Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Substitua o valor de **$ipName** pelo nome do endereço IP público. Criar a variável e o endereço IP público.

        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
        
2. Substitua o valor de **$nicName** pelo nome do adaptador de rede. Crie a variável e a interface de rede.

        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


## Criar a VM

O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM carregada como a fonte da nova instalação.

>[AZURE.NOTE] A VM precisa estar na mesma conta de armazenamento que o VHD original.

</br>

	
	
	
	#Create variables
	# Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
	$cred = Get-Credential
	
	# Name of the storage account 
	$storageAccName = "<storageAccountName>"
	
	# Name of the virtual machine
	$vmName = "<vmName>"
	
	# Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
	$vmSize = "<vmSize>"
	
	# Computer name for the VM
	$computerName = "<computerName>"
	
	# Name of the disk that holds the OS
	$osDiskName = "<osDiskName>"
	
	# Assign a SKU name
	# Valid values for -SkuName are: **Standard_LRS** - locally redundant storage, **Standard_ZRS** - zone redundant storage, **Standard_GRS** - geo redundant storage, **Standard_RAGRS** - read access geo redundant storage, **Premium_LRS** - premium locally redundant storage. 
	$skuName = "<skuName>"
	
	# Create a new storage account for the VM
	New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $storageAccName -Location $location -SkuName $skuName -Kind "Storage"

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You set this variable when you uploaded the VHD
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $imageURI -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Ao concluir, a nova VM criada deverá aparecer no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas Virtuais** ou usando os seguintes comandos do PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Próximas etapas

Para gerenciar sua nova máquina virtual com o Azure PowerShell, confira [Gerenciar máquinas virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0817_2016-->