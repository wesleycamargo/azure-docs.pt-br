---
title: Carregar um VHD do Windows no Gerenciador de Recursos | Microsoft Docs
description: Saiba como carregar uma imagem de máquina virtual Windows a ser usada com o modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 08/02/2016
ms.author: cynthn

---
# Carregar uma imagem de VM Windows no Azure para implantações do Resource Manager
Este artigo mostra como criar e carregar uma imagem de VHD (disco rígido virtual) do Windows que permite criar máquinas virtuais rapidamente. Para mais detalhes sobre discos e os VHDs no Azure, confira a seção [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).

## Pré-requisitos
Este artigo supõe que você:

* **Uma assinatura do Azure**: caso ainda não tenha, [abra uma conta do Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F) ou [ative os benefícios do assinante do MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).
* **Azure PowerShell 1.4 ou superior**: se você ainda não tiver essa versão instalada, leia [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).
* **Uma máquina virtual executando o Windows**: existem várias ferramentas para a criação de máquinas virtuais locais. Por exemplo, consulte [Instalar a função Hyper-V e configurar uma máquina virtual](http://technet.microsoft.com/library/hh846766.aspx). Para obter informações sobre os sistemas operacionais Windows com suporte no Azure, consulte [Suporte de software de servidor Microsoft para máquinas virtuais do Microsoft Azure](https://support.microsoft.com/kb/2721672).
* Verifique se as funções de servidor em execução na VM dão suporte ao Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles). Faça backup da VM antes de executar o Sysprep.

## Verifique se a VM está no formato de arquivo correto
No Azure, você somente pode usar [máquinas virtuais da geração 1](http://blogs.technet.com/b/ausoemteam/archive/2015/04/21/deciding-when-to-use-generation-1-or-generation-2-virtual-machines-with-hyper-v.aspx) no formato de arquivo VHD. O VHD deve ter um tamanho fixo e um número inteiro de megabytes, ou seja, um número divisível por 8. O tamanho máximo permitido para o VHD é 1.023 GB.

* Se você tiver uma imagem de VM Windows no formato VHDX, converta-o em um VHD usando um destes procedimentos:
  
  * Hyper-v: abra o Hyper-V e selecione seu computador local à esquerda. Em seguida, no menu superior, clique em **Ação** > **Editar disco…**. Navegue pelas telas ao clicar em **Avançar** e insira estas opções: *Caminho para o arquivo VHDX* > **Converter** > **VHD** > **Tamanho fixo** > *Caminho para o novo arquivo VHD*. Clique em **Concluir** para fechar.
  * [Cmdlet do PowerShell convert-VHD](http://technet.microsoft.com/library/hh848454.aspx): leia a postagem do blog [Converting Hyper-V .vhdx to .vhd file formats](https://blogs.technet.microsoft.com/cbernier/2013/08/29/converting-hyper-v-vhdx-to-vhd-file-formats-for-use-in-windows-azure/) (Convertendo o formato de arquivo .vhdx do Hyper-V para o formato .vhd) para obter mais informações.
* Se você tiver uma imagem da VM Windows no [formato de arquivo VMDK](https://en.wikipedia.org/wiki/VMDK), converta-a no formato VHD usando o [Conversor de Máquina Virtual da Microsoft](https://www.microsoft.com/download/details.aspx?id=42497). Leia o blog [How to Convert a VMware VMDK to Hyper-V VHD](http://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx) (Como converter um VMDK do VMware para VHD do Hyper-V) para obter mais informações.

## Preparar o VHD para carregamento
Esta seção mostra como generalizar a máquina virtual do Windows. O sysprep remove todas as informações pessoais da conta, entre outros itens. Para obter detalhes sobre o Sysprep, consulte [Como usar o Sysprep: uma introdução](http://technet.microsoft.com/library/bb457073.aspx).

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\\system32\\sysprep** e, a seguir, execute `sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, faça o seguinte:
   
   1. Em **Ação de Limpeza do Sistema**, selecione **Entrar no Sistema OOBE** e verifique se a caixa de seleção **Generalizar** está marcada.
   2. Em **Opções de Desligamento**, selecione **Desligar**.
   3. Clique em **OK**.
      
      ![Inicie o Sysprep](./media/virtual-machines-windows-upload-image/sysprepgeneral.png)

</br>

## Fazer logon no Azure
1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.
   
        Login-AzureRmAccount
   
    Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
        Get-AzureRmSubscription
3. Defina a assinatura correta usando a ID da assinatura.
   
        Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"

## Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para hospedar a imagem da VM. Você pode usar uma conta de armazenamento existente ou criar uma nova.

Mostre as contas de armazenamento disponíveis.

        Get-AzureRmStorageAccount

Se você quiser usar uma conta de armazenamento existente, vá para a seção [Carregar a imagem da VM](#upload-the-vm-image-to-your-storage-account).

Se você quiser criar uma conta de armazenamento, siga estas etapas:

1. Certifique-se de que você tenha um grupo de recursos para essa conta de armazenamento. Saiba quais são todos os grupos de recursos que estão em sua assinatura usando:
   
        Get-AzureRmResourceGroup
2. Se você quiser criar um grupo de recursos, use este comando:
   
        New-AzureRmResourceGroup -Name <resourceGroupName> -Location <location>
3. Crie uma conta de armazenamento neste grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx):
   
        New-AzureRmStorageAccount -ResourceGroupName <resourceGroupName> -Name <storageAccountName> -Location "<location>" -SkuName "<skuName>" -Kind "Storage"

Os valores válidos para -SkuName são:

* **Standard\_LRS** – Armazenamento com redundância local.
* **Standard\_ZRS** – Armazenamento com redundância de zona.
* **Standard\_GRS** – Armazenamento com redundância geográfica.
* **Standard\_RAGRS** – Armazenamento com redundância geográfica com acesso de leitura.
* **Premium\_LRS** – Armazenamento com redundância local premium.

## Carregue a imagem da VM para sua conta de armazenamento
Use o cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para carregar a imagem em um contêiner na conta de armazenamento:

        $rgName = "<resourceGroupName>"
        $urlOfUploadedImageVhd = "<storageAccount>/<blobContainer>/<targetVHDName>.vhd"
        Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd -LocalFilePath <localPathOfVHDFile>

Em que:

* **storageAccount** é o nome da conta de armazenamento da imagem.
* **blobContainer** é o contêiner de blobs em que você deseja armazenar a imagem. Caso não seja encontrado um contêiner de blobs existente com esse nome, ele será criado.
* **targetVHDName** é o nome que você deseja usar para o arquivo VHD carregado.
* **localPathOfVHDFile** é o caminho completo e o nome do arquivo .vhd no computador local.

Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

        C:\> Add-AzureRmVhd -ResourceGroupName testUpldRG -Destination https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd -LocalFilePath "C:\temp\WinServer12.vhd"
        MD5 hash is being calculated for the file C:\temp\WinServer12.vhd.
        MD5 hash calculation is completed.
        Elapsed time for the operation: 00:03:35
        Creating new page blob of size 53687091712...
        Elapsed time for upload: 01:12:49

        LocalFilePath           DestinationUri
        -------------           --------------
        C:\temp\WinServer12.vhd https://testupldstore2.blob.core.windows.net/testblobs/WinServer12.vhd

Esse comando pode demorar um pouco para ser concluído, dependendo da conexão de rede e do tamanho do arquivo VHD.

## Criar uma rede virtual
Crie a rede virtual e a sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Substitua o valor das variáveis pelas suas próprias informações. Forneça o prefixo do endereço da sub-rede no formato CIDR. Crie as variáveis e a sub-rede.
   
        $rgName = "<resourceGroup>"
        $location = "<location>"
        $subnetName = "<subNetName>"
        $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix <0.0.0.0/0>
2. Substitua o valor de **$vnetName** por um nome para a rede virtual. Forneça o prefixo do endereço da rede virtual no formato CIDR. Crie a variável e a rede virtual com a sub-rede.
   
        $vnetName = "<vnetName>"
        $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix <0.0.0.0/0> -Subnet $singleSubnet

## Criar um endereço IP público e um adaptador de rede
Para habilitar a comunicação com a máquina virtual na rede virtual, são necessários um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e um adaptador de rede.

1. Substitua o valor de **$ipName** pelo nome do endereço IP público. Criar a variável e o endereço IP público.
   
        $ipName = "<ipName>"
        $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
2. Substitua o valor de **$nicName** pelo nome do adaptador de rede. Crie a variável e a interface de rede.
   
        $nicName = "<nicName>"
        $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id

## Criar a VM
O script do PowerShell a seguir mostra como definir as configurações da máquina virtual e usar a imagem da VM carregada como a fonte da nova instalação.

> [!NOTE]
> A VM precisa estar na mesma conta de armazenamento que o arquivo VHD carregado.
> 
> 

</br>

    #Create variables
    # Enter a new user name and password to use as the local administrator account for the remotely accessing the VM
    $cred = Get-Credential

    # Name of the storage account where the VHD file is and where the OS disk will be created
    $storageAccName = "<storageAccountName>"

    # Name of the virtual machine
    $vmName = "<vmName>"

    # Size of the virtual machine. See the VM sizes documentation for more information: https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "<vmSize>"

    # Computer name for the VM
    $computerName = "<computerName>"

    # Name of the disk that holds the OS
    $osDiskName = "<osDiskName>"

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
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption fromImage -SourceImageUri $urlOfUploadedImageVhd -Windows

    #Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm



Ao concluir, a nova VM criada deverá aparecer no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas Virtuais** ou usando os seguintes comandos do PowerShell:

    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name


## Próximas etapas
Para gerenciar a nova máquina virtual usando o Azure PowerShell, leia [Gerenciar Máquinas Virtuais usando o Azure Resource Manager e o PowerShell](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0831_2016-->