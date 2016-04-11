<properties
	pageTitle="Capturar uma VM do Windows no Gerenciador de Recursos | Microsoft Azure"
	description="Saiba como capturar uma imagem de uma máquina virtual (VM) do Azure baseada no Windows criada com o modelo de implantação do Gerenciador de Recursos do Azure."
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
	ms.date="01/29/2016"
	ms.author="dkshir"/>

# Como capturar uma máquina virtual do Windows no modelo de implantação do Gerenciador de Recursos

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-capture-image.md).


Este artigo mostra como usar o Azure PowerShell para capturar uma máquina virtual (VM) do Azure executando Windows para que você a use para criar outras máquinas virtuais. Esta imagem inclui o disco do sistema operacional e os discos de dados anexados à máquina virtual. Ela não inclui os recursos da rede virtual de que você precisará para criar uma VM do Windows, de modo que será preciso configurá-los antes de criar outra máquina virtual que use a imagem. Essa imagem também será preparada para ser uma [imagem generalizada do Windows](https://technet.microsoft.com/library/hh824938.aspx).


## Pré-requisitos

Estas etapas pressupõem que você já criou uma máquina virtual do Azure no modelo de implantação do Gerenciador de Recursos e configurou o sistema operacional, inclusive anexou todos os discos de dados e fez outras personalizações, como a instalação de aplicativos. Se não tiver feito isso ainda, leia [Como criar uma VM do Windows com o Gerenciador de Recursos e o PowerShell](virtual-machines-windows-ps-create.md). Você pode criar facilmente uma máquina virtual do Windows usando o [Portal do Azure](https://portal.azure.com). Leia [Como criar uma máquina virtual do Windows no portal do Azure](virtual-machines-windows-hero-tutorial.md).


## Preparar a VM para captura de imagem

Esta seção mostra como generalizar a máquina virtual do Windows. Isso remove todas as informações da sua conta pessoal, entre outros itens. Normalmente, você desejará fazer isso quando quiser usar essa imagem de VM para implantar rapidamente máquinas virtuais semelhantes.

1. Conecte-se à sua máquina virtual do Windows. No [Portal do Azure](https://portal.azure.com), navegue por **Procurar** > **Máquinas virtuais** > *Sua máquina virtual do Windows* > **Conectar**.

2. Abra uma janela de Prompt de comando como administrador.

3. Altere o diretório para `%windir%\system32\sysprep` e execute sysprep.exe.

4. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, faça o seguinte:

	- Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a opção **Generalizar** está marcada. Para obter mais informações sobre como usar o Sysprep, consulte [Como usar Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

	- Em **Opções de Desligamento**, selecione **Desligar**.

	- Clique em **OK**.

	![Executar o Sysprep](./media/virtual-machines-windows-capture-image/SysprepGeneral.png)

   O Sysprep desliga a máquina virtual. O status muda para **Parado** no Portal do Azure.

</br>
## Capturar a VM

É possível capturar a VM do Windows generalizada usando o Azure PowerShell ou a nova ferramenta Gerenciador do Azure Resource Manager. Esta seção mostra as etapas para ambos.

### Usando o PowerShell

Este artigo pressupõe que você instalou a versão 1.0.x do Azure PowerShell. É recomendável usar esta versão, uma vez que os novos recursos do Gerenciador de Recursos não serão adicionados às versões mais antigas do PowerShell. Leia [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0/) para saber mais sobre as diferenças de versão.

1. Abra o Azure PowerShell 1.0.x e conecte-se à sua conta do Azure.

		Login-AzureRmAccount

	Esse comando abrirá uma janela pop-up para inserir as credenciais do Azure.

2. Se a ID da assinatura selecionada por padrão for diferente daquela na qual você quer trabalhar, use um dos métodos a seguir para definir a assinatura correta.

		Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	ou o

		Select-AzureRmSubscription -SubscriptionId "xxxx-xxxx-xxxx-xxxx"

	Você pode encontrar as assinaturas da sua conta do Azure usando o comando `Get-AzureRmSubscription`.

3. Agora, você precisará desalocar os recursos usados por essa máquina virtual utilizando este comando:

		Stop-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM

	Você verá que o *Status* da VM no Portal do Azure mudou de **Parado** para **Parado (desalocado)**.

	>[AZURE.TIP] Também é possível descobrir o status da sua máquina virtual no PowerShell usando:</br> `$vm = Get-AzureRmVM -ResourceGroupName YourResourceGroup -Name YourWindowsVM -status`</br> `$vm.Statuses`</br> O campo **DisplayStatus** corresponde ao **Status** mostrado no Portal do Azure.

4. Em seguida, você precisa definir o status da máquina virtual como **Generalizado**. Observe que você precisará fazer isso porque a etapa de generalização acima (`sysprep`) não faz isso de maneira que o Azure possa entender.

		Set-AzureRmVm -ResourceGroupName YourResourceGroup -Name YourWindowsVM -Generalized

	>[AZURE.NOTE] O estado generalizado, conforme definido acima, não será mostrado no portal. No entanto, você pode verificá-lo usando o comando Get-AzureRmVM, conforme mostrado na dica acima.

5. Capture a imagem da máquina virtual em um contêiner de armazenamento de destino usando este comando.

		Save-AzureRmVMImage -ResourceGroupName YourResourceGroup -VMName YourWindowsVM -DestinationContainerName YourImagesContainer -VHDNamePrefix YourTemplatePrefix -Path Yourlocalfilepath\Filename.json

	A variável `-Path` é opcional. Você pode usá-la para salvar o modelo JSON localmente. A variável `-DestinationContainerName` é o nome do contêiner em que você quer manter as imagens. A URL da imagem armazenada será semelhante a `https://YourStorageAccountName.blob.core.windows.net/system/Microsoft.Compute/Images/YourImagesContainer/YourTemplatePrefix-osDisk.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`. Ela será criada na mesma conta de armazenamento da máquina virtual original.

	>[AZURE.NOTE] Para encontrar o local da imagem, abra o modelo de arquivo JSON local. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **imagem** > **uri** para obter o caminho completo da imagem. A partir de agora, não há maneira fácil de verificar essas imagens no portal, uma vez que o contêiner *system* na conta de armazenamento está oculto. Por esse motivo, embora a variável `-Path` seja opcional, definitivamente convém usá-la para salvar o modelo localmente e descobrir facilmente a URL da imagem. Como alternativa, você pode descobrir isso usando uma ferramenta chamada **Gerenciador de Armazenamento do Azure**, que é explicada nas etapas da próxima seção.


### Usando o Gerenciador de Recursos do Azure (Preview)

O [Gerenciador de Recursos do Azure (Visualização)](https://azure.microsoft.com/blog/azure-resource-explorer-a-new-tool-to-discover-the-azure-api/) é uma nova ferramenta que você pode usar para gerenciar os recursos do Azure criados no modelo de implantação do Gerenciador de Recursos. Com essa ferramenta, você pode facilmente:

- Descobrir as APIs do Gerenciamento de Recursos do Azure.
- Obter a documentação das APIs.
- Fazer chamadas de API diretamente em suas assinaturas do Azure.

Para saber mais sobre tudo o que você pode fazer com essa avançada ferramenta, assista ao vídeo em [Azure Resource Manager Explorer with David Ebbo](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Resource-Manager-Explorer-with-David-Ebbo).

Você pode usar o Gerenciador de Recursos para capturar a máquina virtual, como uma alternativa ao método do PowerShell.

1. Abra o [site do Gerenciador de Recursos](https://resources.azure.com/) e conecte-se à sua conta do Azure.

2. No lado superior direito da ferramenta, selecione **Leitura/Gravação** para permitir operações _PUT_ e _POST_. Ela é definida como **Somente Leitura** por padrão, o que significa que você pode apenas realizar operações _GET_.

	![Leitura/gravação do Gerenciador de Recursos](./media/virtual-machines-windows-capture-image/ArmExplorerReadWrite.png)

3. Em seguida, encontre sua máquina virtual do Windows. Você pode digitar o nome na *Caixa de Pesquisa* na parte superior da ferramenta ou pode navegar pelo menu à esquerda em **assinaturas** > *sua assinatura do Azure* > **resourceGroups** > *seu grupo de recursos* > **provedores** > **Microsoft.Compute** > **virtualMachines** > *sua máquina virtual do Windows*. Ao clicar na máquina virtual no painel de navegação esquerdo, você verá seu modelo no lado direito da ferramenta.

4. No lado superior direito da página do modelo, você deverá ver guias para várias operações disponíveis para essa máquina virtual. Clique na guia para **Ações (POST/DELETE)**.

	![Menu Ação do Gerenciador de Recursos](./media/virtual-machines-windows-capture-image/ArmExplorerActionMenu.png)

   Você verá uma lista de todas as ações que podem ser realizadas na máquina virtual.

	![Resource Explorer Action items](./media/virtual-machines-windows-capture-image/ArmExplorerActionItems.png)

5. Desaloque a máquina virtual clicando no botão de ação para **desalocar**. O status da sua VM mudará de **Parado** para **Parado (desalocado)**.

6. Marque a máquina virtual como generalizada clicando no botão de ação para **generalizar**. Você pode verificar as alterações de status clicando no menu **InstanceView** sob o nome da sua máquina virtual, no lado esquerdo, e navegando até a seção **status** no lado direito.

7. Ao clicar no botão de ação **capturar**, você pode definir os valores para capturar a imagem. Os valores de preenchimento podem ser parecidos com os seguintes:

	![Captura do Gerenciador de Recursos](./media/virtual-machines-windows-capture-image/ArmExplorerCaptureAction.png)

	Clique no botão de ação **capturar** para capturar a imagem da sua máquina virtual. Isso cria um novo VHD para a imagem, bem como um arquivo de modelo JSON. Até o momento, eles não são acessíveis por meio do Gerenciador de Recursos ou do [Portal do Azure](https://portal.azure.com).

8. Para acessar o novo VHD da imagem, bem como o modelo, baixe e instale a ferramenta do Azure para gerenciar recursos de armazenamento, o [Gerenciador de Armazenamento do Azure](http://storageexplorer.com/). O instalador instalará o Gerenciador de Armazenamento do Azure localmente no seu computador.

	- Abra o Gerenciador de Armazenamento e conecte-se à sua assinatura do Azure. Ele deverá mostrar todas as contas de armazenamento disponíveis para a sua assinatura.

	- No lado esquerdo, você deverá ver a conta de armazenamento da máquina virtual que capturamos nas etapas acima. Clique duas vezes no menu **sistema** abaixo dela. Você deverá ver o conteúdo da pasta **sistema** no lado direito.

		![Sistema do Gerenciador de Armazenamento](./media/virtual-machines-windows-capture-image/StorageExplorer1.png)

	- Clique duas vezes em **Microsoft.Compute** > **Imagens**, que mostrará a você todas as suas pastas de imagem. Clique duas vezes no nome da pasta que você inseriu para a variável **destinationContainerName** ao capturar a imagem do Gerenciador de Recursos. Ela mostrará o VHD e o arquivo de modelo JSON.

	- A partir desse ponto, você pode descobrir a URL ou baixar o VHD/modelo clicando nele com o botão direito do mouse.

		![Modelo do Gerenciador de Armazenamento](./media/virtual-machines-windows-capture-image/StorageExplorer2.png)


## Implantar uma nova VM a partir da imagem capturada

Agora, você pode usar a imagem capturada para criar uma nova VM do Windows. Estas etapas mostram como usar o Azure PowerShell e a imagem da VM, capturada nas etapas acima para criar a VM em uma nova rede virtual.

>[AZURE.NOTE] A imagem da VM deve estar presente na mesma conta de armazenamento que a máquina virtual real que será criada.

### Criar recursos da rede

Use o exemplo de script do PowerShell a seguir para configurar uma rede virtual e a NIC para sua nova VM. Use valores para as variáveis (representadas pelo símbolo **$**), conforme apropriado para o aplicativo.

	$pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

	$subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

	$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

	$nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

### Criar uma nova VM

O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM capturada como fonte para a nova instalação. </br>

	#Enter a new user name and password in the pop-up for the following
	$cred = Get-Credential

	#Get the storage account where the captured image is stored
	$storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName

	#Set the VM name and size
	$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

	#Set the Windows operating system configuration and add the NIC
	$vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate

	$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	#Create the OS disk URI
	$osDiskUri = '{0}vhds/{1}{2}.vhd' -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName

	#Configure the OS disk to be created from image (-CreateOption fromImage) and give the URL of the captured image VHD for the -SourceImageUri parameter.
	#We found this URL in the local JSON template in the previous sections.
	$vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfCapturedImageVhd -Windows

	#Create the new VM
	New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Você deverá ver a VM recentemente criada no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas virtuais** OU usando os seguintes comandos do PowerShell:

	$vmList = Get-AzureRmVM -ResourceGroupName $rgName
	$vmList.Name


## Próximas etapas

Para gerenciar sua nova máquina virtual com o Azure PowerShell, leia [Manage virtual machines using Azure Resource Manager and PowerShell](virtual-machines-windows-ps-manage.md).

<!-----------HONumber=AcomDC_0330_2016-->