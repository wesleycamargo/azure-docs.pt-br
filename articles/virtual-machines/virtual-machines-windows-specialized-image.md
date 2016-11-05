---
title: Criar uma cópia de sua VM do Windows | Microsoft Docs
description: Saiba como criar uma cópia de sua máquina virtual do Azure executando Windows, no modelo de implantação do Gerenciador de Recursos, criando uma * imagem especializada*.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-resource-manager

ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn

---
# Criar uma máquina virtual do Windows no modelo de implantação do Azure Resource Manager
Este artigo mostra como criar uma cópia da VM (máquina virtual) do Azure que executam o Windows. Especificamente, ele aborda como fazer isso no Modelo de implantação do Azure Resource Manager usando o Azure PowerShell e o Portal do Azure. Ele mostra como criar uma imagem *especializada* da VM do Azure que mantém as contas de usuário e outros dados de estado da sua VM original. Uma imagem especializada é útil para portar a VM do Windows do modelo de implantação clássico para o modelo de implantação do Resource Manager ou criar uma cópia de backup da VM do Windows criada no modelo de implantação do Resource Manager. Você pode copiar o sistema operacional e os discos de dados dessa maneira e, em seguida, configurar os recursos de rede para criar a nova máquina virtual.

Se precisar criar implantações em massa de VMs do Windows semelhantes, você deverá usar uma imagem *generalizada*. Para tal, consulte [Como capturar uma máquina virtual Windows](virtual-machines-windows-capture-image.md).

## Antes de começar
Verifique se você atende os seguintes pré-requisitos antes de iniciar as etapas:

* **Você tem uma máquina virtual do Azure que executa o Windows**, criada usando o modelo de implantação clássico ou o modelo de implantação do Resource Manager. Você configurou o sistema operacional, anexou os discos de dados e fez outras personalizações como instalar os aplicativos necessários. Você usará essa VM para criar a cópia. Se precisar de ajuda para criar essa VM, consulte [Criar uma VM Windows com o Resource Manager](virtual-machines-windows-ps-create.md).
* Você tem o **Azure PowerShell 1.0 (ou posterior)** instalado em seu computador e conectou-se à sua assinatura do Azure. Para saber mais, consulte [Como instalar e configurar o PowerShell](../powershell-install-configure.md).
* Você baixou e instalou a **ferramenta AzCopy**. Para obter mais informações sobre essa ferramenta, consulte [Transferir dados com a ferramenta de linha de comando AzCopy](../storage/storage-use-azcopy.md).
* Você tem um **grupo de recursos** e uma **conta de armazenamento**, bem como um **contêiner de blob** criado nesse grupo de recursos para o qual copiar os VHDs. Para ver as etapas para usar uma conta de armazenamento existente ou criar uma nova, consulte [Criar ou localizar uma conta de armazenamento do Azure](virtual-machines-windows-upload-image.md#createstorage).

> [!NOTE]
> Etapas semelhantes se aplicam a uma VM criada usando qualquer um dos dois modelos de implantação como a imagem de origem. Quando aplicável, este artigo observa as pequenas diferenças.
> 
> 

## Copiar VHDs para sua conta de armazenamento do Gerenciador de Recursos
1. Libere os VHDs usados pela VM de origem realizando uma das opções a seguir:
   
   * Se quiser copiar sua máquina virtual de origem, pare e desaloque-a.
     
     * Para uma VM criada usando o modelo de implantação clássico, você pode usar o [portal](https://portal.azure.com) e clicar em **Procurar** > **Máquinas virtuais (clássicas)** > *sua VM* > **Parar** ou usar o comando do PowerShell `Stop-AzureVM -ServiceName <yourServiceName> -Name <yourVmName>`.
     * Para uma VM criada usando o modelo de implantação do Resource Manager, você pode entrar no portal e clicar em **Procurar** > **Máquinas virtuais** > *sua VM* > **Parar** ou usar o comando do PowerShell `Stop-AzureRmVM -ResourceGroupName <yourResourceGroup> -Name <yourVmName>`. Observe que o status da VM no portal muda de **Em execução** para **Parado (desalocado)**.
   * Se quiser migrar sua máquina virtual de origem, exclua a VM e use o VHD que restou. Procure sua máquina virtual no [portal](https://portal.azure.com) e clique em **Excluir**.
2. Localize as chaves de acesso para a conta de armazenamento que contém o VHD de origem, bem como a conta de armazenamento para a qual você copiará o VHD para criar a nova VM. A chave da conta da qual estamos copiando o VHD é chamada de *Chave de Origem* e a da conta para a qual ele será copiado é a *Chave de Destino*. Para obter mais informações sobre as chaves de acesso, consulte [Sobre as contas de armazenamento do Azure](../storage/storage-create-storage-account.md).
   
   * Se sua VM de origem tiver sido criada usando o modelo de implantação clássico, clique em **Procurar** > **Contas de armazenamento (clássicas)** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves**. Copie a chave rotulada como **CHAVE DE ACESSO PRIMÁRIA**.
   * Se sua VM de origem foi criada usando o modelo de implantação do Resource Manager ou para a conta de armazenamento que você usará para a nova VM, clique em **Procurar** > **Contas de armazenamento** > *sua conta de armazenamento* > **Todas as configurações** > **Chaves de acesso**. Copie a chave rotulada como **key1**.
3. Obtenha as URLs para acessar suas contas de armazenamento de origem e de destino. No portal, navegue até sua conta de armazenamento e clique em **Blobs**. Clique no contêiner que hospeda o VHD de origem (por exemplo, *vhds* para o modelo de implantação clássico) ou no contêiner para o qual deseja que o VHD seja copiado. Clique em **Propriedades** do contêiner e copie o texto rotulado como **URL**. Você precisará das URLs dos contêineres de origem e de destino. As URLs serão semelhantes a `https://myaccount.blob.core.windows.net/mycontainer`.
4. No computador local, abra uma janela de comando e navegue até a pasta em que o AzCopy está instalado. (Ela será semelhante a *C:\\Arquivos de Programas (x86) \\Microsoft SDKs\\Azure\\AzCopy*.) Nela, execute este comando: </br>
   
        AzCopy /Source:<URL_of_the_source_blob_container> /Dest:<URL_of_the_destination_blob_container> /SourceKey:<Access_key_for_the_source_storage> /DestKey:<Access_key_for_the_destination_storage> /Pattern:<File_name_of_the_VHD_you_are_copying>
   </br>

> [!NOTE]
> Você deve copiar o sistema operacional e discos de dados separadamente, usando o AzCopy conforme descrito na etapa anterior.
> 
> 

## Criar uma VM usando o VHD copiado
Usando o VHD copiado nas etapas acima, agora você pode usar o Azure PowerShell para criar uma VM do Windows baseada no Resource Manager em uma nova rede virtual. O VHD deve estar presente na mesma conta de armazenamento que a nova máquina virtual que será criada.

Configure uma rede virtual e uma NIC para sua nova VM, semelhante ao script a seguir. Use valores para as variáveis (representadas pelo símbolo **$**) conforme apropriado para o aplicativo.

    $pip = New-AzureRmPublicIpAddress -Name $pipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name $subnet1Name -AddressPrefix $vnetSubnetAddressPrefix

    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix $vnetAddressPrefix -Subnet $subnetconfig

    $nic = New-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id


Agora, defina as configurações de VM e use os VHDs copiados para criar uma nova máquina virtual. </br>

    #Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows

    #Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun)
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach

As URLs do disco do sistema operacional e dos dados se parecem com esta: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Você pode encontrá-las no portal navegando até o contêiner de armazenamento de destino, clicando no VHD de sistema operacional ou nos dados que foram copiados e copiando o conteúdo da URL.

    #Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm

Se o comando for bem-sucedido, você verá uma saída como esta:

    RequestId IsSuccessStatusCode StatusCode ReasonPhrase
    --------- ------------------- ---------- ------------
                             True         OK OK


Você deverá ver a VM recém-criada no [Portal do Azure](https://portal.azure.com) em **Procurar** > **Máquinas virtuais** ou usando os seguintes comandos do PowerShell:

    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name

Para entrar em sua nova máquina virtual, navegue até a VM no [portal](https://portal.azure.com), clique em **Conectar** e abra o arquivo RDP da Área de Trabalho Remota. Use as credenciais da conta da máquina virtual original para entrar na nova máquina virtual.

## Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Gerenciar máquinas virtuais usando o PowerShell e o Azure Resource Manager](virtual-machines-windows-ps-manage.md).

<!---HONumber=AcomDC_0928_2016-->