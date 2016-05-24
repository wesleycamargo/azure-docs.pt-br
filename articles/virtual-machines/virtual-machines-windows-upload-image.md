<properties
	pageTitle="Carregar um VHD do Windows no Gerenciador de Recursos | Microsoft Azure"
	description="Saiba como carregar uma imagem de máquina virtual Windows a ser usada com o modelo de implantação do Resource Manager."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="dsk-2015"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/06/2016"
	ms.author="dkshir"/>

# Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager


Este artigo mostra como carregar um VHD (disco rígido virtual) com um sistema operacional Windows para que você possa usá-lo para criar novas VMs (máquinas virtuais) Windows usando o modelo de implantação do Azure Resource Manager. Para mais detalhes sobre discos e os VHDs no Azure, confira a seção [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).



## Pré-requisitos

Este artigo supõe que você:

- **Uma assinatura do Azure**: se você ainda não tiver uma, [abra uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) e [ative os benefícios do assinante do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

- **Azure PowerShell 1.0 ou superior**: se você ainda não tiver essa versão instalada, leia [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

- **Uma máquina virtual executando o Windows**: existem várias ferramentas para a criação de máquinas virtuais locais. Por exemplo, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). Para saber quais sistemas operacionais Windows têm suporte do Azure, confira [Suporte de software de servidor Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/kb/2721672).


## Verifique se a VM tem o formato de arquivo correto

O Azure pode aceitar apenas imagens para [máquinas virtuais de primeira geração](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) salvas no formato de arquivo VHD. O tamanho do VHD deve ser fixo e um número inteiro de megabytes, ou seja, um número divisível por 8. O tamanho máximo permitido para o VHD é 1.023 GB.

- Se você tiver uma imagem de VM Windows no formato VHDX, converta-o em um VHD usando um destes procedimentos:

	- Hyper-v: abra o Hyper-V e selecione seu computador local à esquerda. Em seguida, no menu superior, clique em **Ação** > **Editar disco…**. Navegue pelas telas ao clicar em **Avançar** e insira estas opções: *Caminho para o arquivo VHDX* > **Converter** > **VHD** > **Tamanho fixo** > *Caminho para o novo arquivo VHD*. Clique em **Concluir** para fechar.

	- [Cmdlet do PowerShell convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): leia a postagem do blog [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Convertendo o formato de arquivo .vhdx do Hyper-V para o formato .vhd) para obter mais informações.

- Se você tiver uma imagem da VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a no formato VHD usando o [Conversor de Máquina Virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Leia o blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK do VMware para VHD do Hyper-V) para obter mais informações.


## Preparar o VHD para carregamento

Esta seção mostra como generalizar a máquina virtual do Windows. Isso remove todas as informações da sua conta pessoal, entre outros itens. Normalmente, você desejará fazer isso quando quiser usar essa imagem de VM para implantar rapidamente máquinas virtuais semelhantes. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

1. Entre na máquina virtual Windows.

2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.

3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, faça o seguinte:

	1. Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a caixa de seleção **Generalizar** está marcada.

	2. Em **Opções de Desligamento**, selecione **Desligar**.

	3. Clique em **OK**.

	![Inicie o Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br> <a id="createstorage"></a>
## Criar ou localizar uma conta de armazenamento do Azure

Você precisará de uma conta de armazenamento do Azure para carregar a imagem da VM. Você pode usar uma conta de armazenamento existente ou criar uma nova. Você pode usar o Portal do Azure ou o PowerShell para fazer isso.

### Para criar ou localizar uma conta de armazenamento do Azure usando o Portal do Azure

1. Entre no [portal](https://portal.azure.com).

2. Clique em **Procurar** > **Contas de armazenamento**.

3. Verifique se existe uma conta de armazenamento que você deseja usar para carregar a imagem. Anote o nome da conta de armazenamento. Você poderá seguir para a seção [Carregar imagem da VM](#uploadvm) se estiver usando uma conta de armazenamento existente.

4. Se você quiser criar uma nova conta de armazenamento, clique em **Adicionar** e insira as seguintes informações:

	1. Insira o **Nome** para a conta de armazenamento. Ele deve conter entre 3 a 24 letras minúsculas e números somente. Esse nome se torna parte da URL que você usará para acessar blobs, arquivos e outros recursos da conta de armazenamento.
	
	2. Selecione *Resource Manager* como o **Modelo de implantação**.

	3. Selecione os valores apropriados de **Variante de conta**, **Desempenho** e **Replicação**. Você pode passar o mouse sobre os ícones de informações para saber mais sobre esses valores.

	4. Selecione *+ Novo* para o **Grupo de Recursos** ou um existente. Insira o nome do novo grupo de recursos se desejar criar um novo.

	5. Escolha o **Local** da conta de armazenamento e clique em **Criar**. A conta agora aparece no painel **Contas de armazenamento**.

		![Insira os detalhes da conta de armazenamento](./media/virtual-machines-windows-upload-image/portal_create_storage_account.png)

	6. Esta e as próximas etapas mostram como criar um contêiner de blob na conta de armazenamento. Isso é opcional, pois você também pode usar o comando do PowerShell que carrega a imagem para criar um novo contêiner de blobs para sua imagem. Se você não deseja criá-lo por conta própria, vá para a seção [Carregar imagem da VM](#uploadvm). Caso contrário, clique em **Blobs** no bloco **Serviços**.

		![Serviço Blob](./media/virtual-machines-windows-upload-image/portal_create_blob.png)

	7. Quando o painel Blob for exibido, clique em **+ Contêiner** para criar um novo contêiner de armazenamento de blobs. Insira o nome do contêiner e o tipo de acesso.

		![Criar novo blob](./media/virtual-machines-windows-upload-image/portal_create_container.png)

  		> [AZURE.NOTE] Por padrão, o contêiner é privado e pode ser acessado apenas pelo proprietário da conta. Para permitir acesso de leitura público aos blobs no contêiner, mas não das propriedades e dos metadados do contêiner, use a opção **Blob**. Para permitir o acesso de leitura público completo do contêiner e dos blobs, use a opção **Contêiner**.

	8. O painel **Serviço Blob** listará o novo contêiner de blob. Anote a URL desse contêiner. Você precisará dela para o comando do PowerShell de carregar a imagem. Dependendo do comprimento da URL e a resolução da tela, a URL pode ficar parcialmente oculta. Se isso acontecer, maximize o painel clicando no ícone **Maximizar** no canto superior direito.


### Para criar ou localizar uma conta de armazenamento do Azure usando o PowerShell

1. Abra o Azure PowerShell 1.0.x e conecte-se à sua conta do Azure.

		Login-AzureRmAccount

	Esse comando abrirá uma janela pop-up para inserir suas credenciais do Azure.

2. Se a ID da assinatura selecionada por padrão for diferente daquela na qual você quer trabalhar, use um dos comandos a seguir para definir a assinatura correta.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	or

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Você pode encontrar as assinaturas da sua conta do Azure usando o comando `Get-AzureRmSubscription`.

3. Localize as contas de armazenamento disponíveis nessa assinatura.

		Get-AzureRmStorageAccount

	Se você quiser usar uma conta de armazenamento existente, vá para a seção [Carregar imagem da VM](#uploadvm).

4. Se você quiser criar uma nova conta de armazenamento para manter essa imagem, siga estas etapas:

	1. Certifique-se de que você tenha um grupo de recursos para essa conta de armazenamento. Saiba quais são todos os grupos de recursos que estão em sua assinatura usando:

			Get-AzureRmResourceGroup

	2. Se você quiser criar um novo grupo de recursos, use este comando:

			New-AzureRmResourceGroup -Name YourResourceGroup -Location "West US"

	3. Crie uma nova conta de armazenamento no grupo de recursos usando:

			New-AzureRmStorageAccount -ResourceGroupName YourResourceGroup -Name YourStorageAccountName -Location "West US" -Type "Standard_GRS"


</br> <a id="uploadvm"></a>

## Carregue a imagem da VM para sua conta de armazenamento

Use estas etapas no Azure PowerShell para carregar a imagem da VM na sua conta de armazenamento. A imagem será carregada para um contêiner de armazenamento de blobs nessa conta. Você pode usar um contêiner existente ou criar um novo.

1. Entre no Azure PowerShell 1.0.x usando `Login-AzureRmAccount`. Certifique-se de que você esteja usando a assinatura correta usando `Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"`, conforme mencionado na seção anterior.

2. Adicione o VHD generalizado do Azure à conta de armazenamento usando o cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx):

		Add-AzureRmVhd -ResourceGroupName YourResourceGroup -Destination "<StorageAccountURL>/<BlobContainer>/<TargetVHDName>.vhd" -LocalFilePath <LocalPathOfVHDFile>

	Em que:
	- **StorageAccountURL** é a URL para a conta de armazenamento. Ela geralmente estará neste formato: `https://YourStorageAccountName.blob.core.windows.net`. Observe que será necessário usar o nome da conta de armazenamento nova ou existente no lugar de *YourStorageAccountName*.
	- **BlobContainer** é o contêiner de blobs em que você deseja armazenar as imagens. Caso o cmdlet não encontre um contêiner de blob existente com esse nome, ele criará um novo para você.
	- **TargetVHDName** é o nome que você deseja usar para salvar a imagem.
	- **LocalPathOfVHDFile** é o caminho completo e o nome do arquivo .vhd em seu computador local.

	Uma execução de `Add-AzureRmVhd` bem-sucedida será semelhante à seguinte:

		C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
		MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
		MD5 hash calculation is completed.
		Elapsed time for the operation: 00:03:35
		Creating new page blob of size 53687091712...
		Elapsed time for upload: 01:12:49

		LocalFilePath           DestinationUri
		-------------           --------------
		C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

	Esse comando levará algum tempo para ser concluído, dependendo da sua conexão de rede e do tamanho do arquivo VHD.

</br>
## Implantar uma nova VM usando a imagem capturada

Agora, você pode usar a imagem carregada para criar uma nova VM Windows. Estas etapas mostram como usar o Azure PowerShell e a imagem da VM carregada nas etapas acima para criar uma VM em uma nova rede virtual.

>[AZURE.NOTE] A imagem da VM deve estar presente na mesma conta de armazenamento que a máquina virtual real que será criada.

### Criar recursos da rede

Use o exemplo de script do PowerShell a seguir para configurar uma rede virtual e a NIC para sua nova VM. Use valores para as variáveis que são representadas pelo símbolo **$**, conforme apropriado para o aplicativo.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Criar uma nova VM

O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM carregada como a origem da nova instalação. </br>

	#Enter a new user name and password in the pop-up window for the following
	$cred = Get-Credential

	#Get the storage account where the uploaded image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	#Use "Get-Help New-AzureRmVMConfig" to know the available options for -VMsize
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from the image (-CreateOption fromImage), and give the URL of the uploaded image VHD for the -SourceImageUri parameter
	#You can find this URL in the result of the Add-AzureRmVhd cmdlet above
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Por exemplo, o fluxo de trabalho pode ser algo assim:

		C:\> $pipName = "testpip6"
		C:\> $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
		C:\> $subnet1Name = "testsub6"
		C:\> $nicname = "testnic6"
		C:\> $vnetName = "testvnet6"
		C:\> $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix
		C:\> $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig
		C:\> $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
		C:\> $vmName = "testupldvm6"
		C:\> $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A4"
		C:\> $computerName = "testupldcomp6"
		C:\> $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		C:\> $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
		C:\> $osDiskName = "testupos6"
		C:\> $osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
		C:\> $urlOfUploadedImageVhd = "https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd"
		C:\> $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows
		C:\> $result = New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
		C:\> $result
		RequestId IsSuccessStatusCode StatusCode ReasonPhrase
		--------- ------------------- ---------- ------------
		                         True         OK OK

Você deverá ver a VM recentemente criada no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Próximas etapas

Para gerenciar sua nova máquina virtual usando o Azure PowerShell, leia [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0511_2016-->