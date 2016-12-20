---
title: "Carregar um VHD (disco rígido virtual ) do Windows no Resource Manager | Microsoft Docs"
description: "Saiba como carregar um VHD de máquina virtual do Windows do local para o Azure, usando o modelo de implantação do Resource Manager. Você pode carregar um VHD tanto de uma VM generalizada quanto de uma VM especializada."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 192c8e5a-3411-48fe-9fc3-526e0296cf4c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: fe188671ff1cb5ba93edbff316afd7b6e23a2dd1


---
# <a name="upload-a-windows-vhd-from-an-on-premises-vm-to-azure"></a>Carregar no Azure um VHD do Windows de uma VM local
Este artigo mostra como criar e carregar um VHD (disco rígido virtual) do Windows para ser usado na criação de uma VM do Azure. Você pode carregar um VHD de uma VM generalizada ou de uma VM especializada. 

Para obter mais detalhes sobre discos e VHDs no Azure, confira a seção [Sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prepare-the-vm"></a>Preparar a VM
Você pode carregar VHDs generalizados e especializados no Azure. Cada tipo requer a preparação da VM antes de iniciar.

* **VHD Generalizado** – um VHD generalizado teve todas suas informações de conta pessoal removidas usando o Sysprep. Se você pretende criar novas VMs usando o VHD como uma imagem, você deve:
  
  * [Preparar um VHD do Windows para carregar no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
  * [Generalizar uma máquina virtual usando o Sysprep](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* **VHD Especializado** – um VHD especializado mantém as contas de usuário, aplicativos e outros dados de estado de sua VM original. Se você pretende usar o VHD no estado em que se encontra para criar uma nova VM, certifique-se de que as seguintes etapas sejam concluídas. 
  
  * [Preparar um VHD do Windows para carregar no Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Não** generalizar a VM usando o Sysprep.
  * Remover quaisquer ferramentas e agentes de virtualização de convidado que estejam instalados na VM (ou seja, ferramentas do VMware).
  * Verifique se a VM está configurada para obter o endereço IP e as configurações de DNS por meio de DHCP. Isso garante que o servidor obtém um endereço IP na VNet quando ele é iniciado. 

## <a name="log-in-to-azure"></a>Fazer logon no Azure
Se você ainda não tiver a versão 1.4 ou superior do PowerShell instalada, leia [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

1. Abra o Azure PowerShell e conecte-se à sua conta do Azure. Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura. Substitua `<subscriptionID>` com a ID da assinatura correta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Obter a conta de armazenamento
Você precisa de uma conta de armazenamento no Azure para armazenar a imagem da VM carregada. Você pode usar uma conta de armazenamento existente ou criar uma nova. 

Para exibir as contas de armazenamento disponíveis, digite:

```powershell
Get-AzureRmStorageAccount
```

Se você quiser usar uma conta de armazenamento existente, vá para a seção [Carregar a imagem da VM](#upload-the-vm-vhd-to-your-storage-account).

Se você precisa criar uma conta de armazenamento, siga estas etapas:

1. Você precisa do nome do grupo de recursos no qual a conta de armazenamento deve ser criada. Para saber quais são todos os grupos de recursos que estão em sua assinatura, digite:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Para criar um grupo de recursos denominado **myResourceGroup** na região **Oeste dos EUA**, digite:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Crie uma conta de armazenamento com o nome **mystorageaccount** neste grupo de recursos usando o cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx):
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    Os valores válidos para -SkuName são:
   
   * **Standard_LRS** – Armazenamento com redundância local. 
   * **Standard_ZRS** – Armazenamento com redundância de zona.
   * **Standard_GRS** – Armazenamento com redundância geográfica. 
   * **Standard_RAGRS** – Armazenamento com redundância geográfica com acesso de leitura. 
   * **Premium_LRS** – Armazenamento com redundância local Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Carregar o VHD na sua conta de armazenamento
Use o cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) para carregar a imagem em um contêiner na conta de armazenamento. Este exemplo carrega o arquivo **myVHD.vhd** de `"C:\Users\Public\Documents\Virtual hard disks\"` em uma conta de armazenamento denominada **mystorageaccount** no grupo de recursos **myResourceGroup**. O arquivo será colocado em um contêiner chamado **mycontainer** e o novo nome do arquivo será **myUploadedVHD.vhd**.

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

## <a name="next-steps"></a>Próximas etapas
* [Criar uma VM no Azure de um VHD generalizado](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Criar uma VM no Azure de um VHD especializado](virtual-machines-windows-create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) anexando-o como um disco do sistema operacional quando você cria uma nova VM.




<!--HONumber=Nov16_HO3-->


