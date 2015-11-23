<properties
	pageTitle="Criar uma VM do Windows | Microsoft Azure"
	description="Use modelos do Gerenciador de Recursos e o Azure PowerShell para criar uma nova máquina virtual do Windows."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Criar uma VM do Windows com o Gerenciador de Recursos e o PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

Este tópico descreve como criar rapidamente uma máquina virtual Azure baseada no Windows usando o Gerenciador de Recursos do Azure e o PowerShell.

## Criar sua primeira máquina virtual do Windows

Se você já instalou o Azure PowerShell, você deve ter o Azure PowerShell versão 1.0.0 ou posterior. Você pode verificar a versão do Azure PowerShell instalada com o comando este comando no prompt de comando do Azure PowerShell.

	Get-Module azure | format-table version

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Primeiro, você deve fazer logon no Azure com este comando.

	Login-AzureRmAccount

Especifique o endereço de email de sua conta do Azure e sua senha na caixa de diálogo de logon do Microsoft Azure.

Em seguida, se tiver várias assinaturas do Azure, você precisará definir sua assinatura do Azure. Para ver uma lista de suas assinaturas atuais, execute este comando.

	Get-AzureRmSubscription | sort SubscriptionName | Select SubscriptionName

Agora, substitua tudo entre aspas, inclusive os caracteres < and >, pelo nome da assinatura correta e execute estes comandos.

	$subscrName="<subscription name>"
	Select-AzureRmSubscription -SubscriptionName $subscrName –Current

Depois, você precisa criar uma conta de armazenamento. Você deve escolher um nome exclusivo que contenha apenas letras minúsculas e números. Você pode testar a exclusividade do nome da conta de armazenamento com este comando.

	Test-AzureName -Storage <Proposed storage account name>

Se este comando retornar "False", o nome proposto é exclusivo.

Você precisará especificar o local de um data center do Azure. Para obter uma lista de data centers do Azure, execute este comando.

	Get-AzureLocation | sort Name | Select Name

Agora, copie o seguinte bloco de comandos do PowerShell para um editor de texto. Preencha a conta de armazenamento e o local escolhidos, substituindo tudo entre aspas, inclusive os caracteres < and >.

	$stName = "<chosen storage account name>"
	$locName = "<chosen Azure location name>"
	$rgName = "TestRG"
	New-AzureRmResourceGroup -Name $rgName -Location $locName
	$storageAcc = New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet = New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzureRmPublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

Por fim, copie o conjunto de comandos acima para a área de transferência e clique com o botão direito do mouse no prompt de comando aberto do PowerShell do Azure. Isso emitirá o conjunto de comandos como uma série de comandos do PowerShell, solicitará o nome e a senha da conta de administrador local e criará sua máquina virtual do Azure.

Aqui está um exemplo do que você pode ver:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureRmResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureRmStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzureRmVirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzureRmPublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureRmNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureRmVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureRmVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Recursos adicionais

[Computação do Azure, Provedores de Rede e Armazenamento no Gerenciador de Recursos do Azure](virtual-machines-azurerm-versus-azuresm.md)

[Visão Geral do Gerenciador de Recursos do Azure](resource-group-overview.md)

[Criar uma máquina virtual do Windows com um modelo do Gerenciador de Recursos e o PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Criar máquinas virtuais do Windows com o PowerShell e o modelo de implantação clássico](virtual-machines-ps-create-preconfigure-windows-vms.md)

[Documentação de máquinas virtuais](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Como instalar e configurar o PowerShell do Azure](install-configure-powershell.md)

<!---HONumber=Nov15_HO3-->