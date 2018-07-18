---
title: Criar uma VM do Azure gerenciada a partir de um VHD local generalizado | Microsoft Docs
description: Carregar um VHD generalizado no Azure e usá-lo para criar novas VMs, no modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/26/2018
ms.author: cynthn
ms.openlocfilehash: 9ebe1f67c7c662af6d9e1888580149834a007200
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657464"
---
# <a name="upload-a-generalized-vhd-and-use-it-to-create-new-vms-in-azure"></a>Carregar um VHD generalizado e usá-lo para criar novas VMs no Azure

Este tópico orienta você a usar o PowerShell para carregar um VHD de uma VM generalizada no Azure, criar uma imagem do VHD e criar uma nova VM dessa imagem. Você pode carregar um VHD exportado de uma ferramenta de virtualização local ou de outra nuvem. Usar [Discos Gerenciados](managed-disks-overview.md) para a nova VM simplifica o gerenciamento da VM e fornece maior disponibilidade quando a VM é colocada em um conjunto de disponibilidade. 

Se você quiser usar um script de exemplo, consulte [Script de exemplo para carregar um VHD no Azure e criar uma nova VM](../scripts/virtual-machines-windows-powershell-upload-generalized-script.md)

## <a name="before-you-begin"></a>Antes de começar

- Antes de carregar qualquer VHD no Azure, você deve seguir as etapas em [Preparar um VHD ou VHDX do Windows para carregar no Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
- Examine [Planejar a migração para Discos Gerenciados](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks) antes de iniciar a migração para [Discos Gerenciados](managed-disks-overview.md).
- Este artigo requer o módulo do AzureRM, versão 5.6 ou posterior. Execute ` Get-Module -ListAvailable AzureRM.Compute` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).


## <a name="generalize-the-source-vm-using-sysprep"></a>Generalizar a VM fonte usando o Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre Sysprep, consulte a [Visão geral do Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).

Verifique se as funções de servidor em execução no computador são suportadas pelo Sysprep. Para obter mais informações, consulte [Suporte do Sysprep para funções de servidor](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)

> [!IMPORTANT]
> Se você estiver executando o Sysprep antes de carregar o VHD para o Azure pela primeira vez, verifique se você [preparou sua VM](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) antes de executar o Sysprep. 
> 
> 

1. Entre na máquina virtual Windows.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para **%windir%\system32\sysprep** e, a seguir, execute`sysprep.exe`.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione **Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)** e verifique se a caixa de seleção **Generalizar** está marcada.
4. Em **Opções de Desligamento**, selecione **Desligar**.
5. Clique em **OK**.
   
    ![Inicie o Sysprep](./media/upload-generalized-managed/sysprepgeneral.png)
6. Quando o Sysprep for concluído, desligará a máquina virtual. Não reinicie a VM.


## <a name="get-the-storage-account"></a>Obter a conta de armazenamento

Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Se for o usar o VHD para criar um disco gerenciado para uma VM, o local da conta de armazenamento deverá ser o mesmo local em que você criará a VM.

Para exibir as contas de armazenamento disponíveis, digite:

```azurepowershell
Get-AzureRmStorageAccount | Format-Table
```

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento

Use o cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para carregar o VHD em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo *myVHD.vhd* de *"C:\Users\Public\Documents\Virtual hard disks\"* para uma conta de armazenamento denominada *mystorageaccount* no grupo de recursos *myResourceGroup*. O arquivo será colocado em um contêiner chamado *mycontainer* e o novo nome do arquivo será *myUploadedVHD.vhd*.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se o comando tiver êxito, você receberá uma resposta semelhante a esta:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

Dependendo da conexão de rede e do tamanho do arquivo VHD, esse comando pode demorar um pouco para ser concluído

### <a name="other-options-for-uploading-a-vhd"></a>Outras opções para carregar um VHD
 
Você também pode carregar um VHD na sua conta de armazenamento usando um dos seguintes métodos:

- [AzCopy](http://aka.ms/downloadazcopy)
- [API do Blob da Cópia de Armazenamento do Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)
- [Carregamento de Blobs do Gerenciador de Armazenamento do Azure](https://azurestorageexplorer.codeplex.com/)
- [Referência de API REST do Serviço de Importação/Exportação do Armazenamento](https://msdn.microsoft.com/library/dn529096.aspx)
-   É recomendável usar o Serviço de Importação/Exportação se o tempo de carregamento estimado é de mais de sete dias. Você pode usar [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) para estimar o tempo com base no tamanho dos dados e na unidade de transferência. 
    A Importação/Exportação pode ser usada para copiar para a conta de armazenamento standard. Você precisará copiar do armazenamento standard para a conta de armazenamento premium usando uma ferramenta como o AzCopy.

> [!IMPORTANT]
> Se estiver usando o AzCopy para carregar o VHD no Azure, verifique se você definiu [/BlobType:page](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#blobtypeblock--page--append) antes de executar o script de upload. Se o destino é um blob e essa opção não está especificada, por padrão, o AzCopy cria um blob de blocos.
> 
> 



## <a name="create-a-managed-image-from-the-uploaded-vhd"></a>Criar uma imagem gerenciada usando o VHD carregado 

Crie uma imagem gerenciada usando o VHD do sistema operacional generalizado. Substitua os valores com suas próprias informações.


Primeiro, defina alguns parâmetros:

```powershell
$location = "East US" 
$imageName = "myImage"
```

Crie a imagem usando o VHD do sistema operacional generalizado.

```powershell
$imageConfig = New-AzureRmImageConfig `
   -Location $location
$imageConfig = Set-AzureRmImageOsDisk `
   -Image $imageConfig `
   -OsType Windows `
   -OsState Generalized `
   -BlobUri $urlOfUploadedImageVhd `
   -DiskSizeGB 20
New-AzureRmImage `
   -ImageName $imageName `
   -ResourceGroupName $rgName `
   -Image $imageConfig
```


## <a name="create-the-vm"></a>Criar a VM

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. Este exemplo cria uma VM chamada *myVM* a partir de *myImage*, em *myResourceGroup*.


```powershell
New-AzureRmVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -ImageName $imageName `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNSG" `
    -PublicIpAddressName "myPIP" `
    -OpenPorts 3389
```


## <a name="next-steps"></a>Próximas etapas

Logue na nova máquina virtual. Para obter mais informações, veja [Como se conectar e fazer logon em uma máquina virtual do Azure executando o Windows](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

