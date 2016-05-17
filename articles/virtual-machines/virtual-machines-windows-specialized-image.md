<properties
	pageTitle="Criar uma cópia de sua VM do Windows | Microsoft Azure"
	description="Saiba como criar uma cópia de sua máquina virtual do Azure executando Windows, no modelo de implantação do Gerenciador de Recursos, criando uma * imagem especializada*."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/26/2016"
	ms.author="dkshir"/>

# Como criar uma máquina virtual do Windows no modelo de implantação do Gerenciador de Recursos


Este artigo mostra como criar uma cópia de sua VM (máquina virtual) do Azure executando Windows no modelo de implantação do Gerenciador de Recursos usando o Azure PowerShell e o Portal do Azure. Ele mostra como criar uma imagem **_especializada_** da VM do Azure que mantém as contas de usuário e outros dados de estado da sua VM original. Uma imagem especializada é útil para cenários como portar a VM do Windows do modelo de implantação clássico para o modelo de implantação do Gerenciador de Recursos ou criar uma cópia de backup da VM do Windows criada no modelo de implantação do Gerenciador de Recursos. Você pode copiar o sistema operacional e os discos de dados dessa maneira e, em seguida, configurar os recursos de rede para criar a nova máquina virtual.

Se precisar criar implantações em massa de VMs do Windows similares, você precisará de uma imagem *generalizada*. Para isso, leia [Como capturar uma máquina virtual Windows para ser usada como um modelo do Gerenciador de Recursos](virtual-machines-windows-capture-image.md).



## Verifique estes itens antes de começar

Este artigo pressupõe que os seguintes pré-requisitos foram atendidos antes de você iniciar as etapas:

1. Você tem uma máquina virtual do Azure executando o Windows, criada usando o modelo de implantação clássico ou o modelo de implantação do Gerenciador de Recursos. Você configurou o sistema operacional, anexou os discos de dados e fez outras personalizações, como instalar os aplicativos necessários. Nós usaremos essa VM para criar a cópia. Se você precisar de ajuda para criar a VM de origem, leia [Como criar uma VM do Windows usando o Resource Manager](virtual-machines-windows-ps-create.md). 

1. Você tem o Azure PowerShell instalado em seu computador e fez logon na sua assinatura do Azure. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Você baixou e instalou a ferramenta AzCopy. Para obter mais informações sobre a ferramenta, leia [Transfer data with AzCopy commandline tool (Transferir dados com a ferramenta de linha de comando AzCopy)](../storage/storage-use-azcopy.md).

1. Você tem um grupo de recursos e uma conta de armazenamento, bem como um contêiner de blob criado nesse grupo de recursos para o qual copiar os VHDs. Leia a seção [Criar ou localizar uma conta de armazenamento do Azure](virtual-machines-windows-upload-image.md#createstorage) para ver as etapas de como usar uma conta de armazenamento existente ou criar uma nova.



> [AZURE.NOTE] Etapas semelhantes se aplicam a uma VM criada usando qualquer um dos dois modelos de implantação como a imagem de origem. Nós indicaremos as pequenas diferenças quando elas existirem.


## Copiar VHDs para sua conta de armazenamento do Gerenciador de Recursos


1. Primeiro, libere os VHDs usados pela VM de origem realizando uma das duas opções a seguir:

	- Se quiser **_copiar_** sua máquina virtual de origem, você precisará **parar** e **desalocá-la**.
	
		- Para uma VM criada usando o modelo de implantação clássico, você pode usar o [portal](https://portal.azure.com) e clicar em **Procurar** > **Máquinas virtuais (clássicas)** > *sua VM* > **Parar** ou usar o comando do PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`. 
		
		- Para uma VM em um modelo de implantação do Gerenciador de Recursos, você pode fazer logon no portal e clicar em **Procurar** > **Máquinas virtuais** > *sua VM* > **Parar** ou usar o comando do PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Observe que o *Status* da VM no portal muda de **Em execução** para **Parado (desalocado)**.

	
	- Ou, se quiser **_migrar_** sua máquina virtual de origem, **exclua** a VM e use o VHD que restou. **Procure** sua máquina virtual no [portal](https://portal.azure.com) e clique em **Excluir**.
	
1. Localize as chaves de acesso para a conta de armazenamento que contém o VHD de origem, bem como a conta de armazenamento para a qual você copiará o VHD para criar a nova VM. A chave da conta da qual estamos copiando o VHD é chamada de *Chave de Origem* e a da conta para a qual ele será copiado é a *Chave de Destino*. Leia [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md) para saber mais sobre as chaves de acesso.

	- Se a sua VM de origem tiver sido criada usando o modelo de implantação clássico, clique em **Procurar** > **Contas de armazenamento (clássicas)** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves** e copie a chave rotulada como **CHAVE DE ACESSO PRIMÁRIA**. 
	
	- Para uma VM criada usando o modelo de implantação do Gerenciador de Recursos ou para a conta de armazenamento que você usará para a nova VM, clique em **Procurar** > **Contas de armazenamento** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves de acesso** e copie a chave rotulada como **key1**.

1. Obtenha as URLs para acessar suas contas de armazenamento de origem e de destino. No portal, **Navegue** até sua conta de armazenamento e clique em **Blobs**. Clique no contêiner que hospeda o VHD de origem (por exemplo, *vhds* para o modelo de implantação clássico) ou no contêiner para o qual deseja que o VHD seja copiado. Clique nas **Propriedades** do contêiner e copie o texto rotulado como **URL**. Precisaremos das URLs dos contêineres de origem e de destino. As URLs serão semelhantes a `https://myaccount.blob.core.windows.net/mycontainer`.

1. No computador local, abra uma janela de comando e navegue até a pasta em que o AzCopy está instalado. Ela seria semelhante a *C:\\Arquivos de Programas (x86) \\Microsoft SDKs\\Azure\\AzCopy*. Da pasta, execute este comando: </br>

		AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
</br>

>[AZURE.NOTE] Você precisará copiar o sistema operacional e os discos de dados separadamente, usando o AzCopy conforme descrito acima.


## Criar uma VM usando o VHD copiado

Estas etapas mostram como usar o Azure PowerShell para criar uma VM do Windows baseada no Gerenciador de Recursos em uma nova rede virtual usando o VHD copiado nas etapas acima. O VHD deve estar presente na mesma conta de armazenamento que a nova máquina virtual que será criada.


Primeiro, configure uma rede virtual e uma NIC para sua nova VM, semelhante ao script a seguir. Use valores para as variáveis (representadas pelo símbolo **$**), conforme apropriado para o aplicativo.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Agora, defina as configurações de VM e use os VHDs copiados para criar uma nova máquina virtual, conforme mostrado abaixo. </br>

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Add the NIC
	$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

	#Add the OS disk using the URL of the copied OS VHD
	$osDiskName = $vmName + "osDisk"
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
	
	#Add data disks using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
	$dataDiskName = $vmName + "dataDisk"
	$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
	
As URLs para o disco do sistema operacional e os dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-la no portal navegando até o contêiner de armazenamento de destino, clicando no VHD de sistema operacional ou dados que foi copiado e copiando o conteúdo da **URL**.
	
	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
	
Se o comando for bem-sucedido, você verá uma saída semelhante a esta:

	RequestId IsSuccessStatusCode StatusCode ReasonPhrase
	--------- ------------------- ---------- ------------
	                         True         OK OK


Você deverá ver a VM recentemente criada no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas virtuais** OU usando os seguintes comandos do PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name

Para entrar em sua nova máquina virtual, **Navegue** até a VM no [portal](https://portal.azure.com), clique em **Conectar** e abra o arquivo RDP da *Área de Trabalho Remota*. Use as credenciais da conta da máquina virtual original para fazer logon na nova máquina virtual.


## Próximas etapas

Para gerenciar sua nova máquina virtual com o Azure PowerShell, leia [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0504_2016-->