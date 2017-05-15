---
title: Como criar uma imagem gerenciada no Azure | Microsoft Docs
description: "Crie uma imagem gerenciada de uma VM ou um VHD generalizado no Azure. Imagens podem ser usadas para criar várias VMs que usam discos gerenciados."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: c3f5a66605b1a7059a7820ddda9463cb4277f055
ms.contentlocale: pt-br
ms.lasthandoff: 04/27/2017


---
# <a name="capture-a-managed-image-of-a-generalized-vm-in-azure"></a>Capturar uma imagem gerenciada de uma VM generalizada no Azure

Um recurso de imagem gerenciada pode ser criado de uma VM generalizada que é armazenada como um disco gerenciado ou um disco não gerenciado em uma conta de armazenamento. Em seguida, a imagem pode ser usada para criar várias VMs que usam discos gerenciados para o armazenamento. 


## <a name="prerequisites"></a>Pré-requisitos
Você precisa já ter [generalizado a VM](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e ela deve estar parada/desalocada. Generalizar uma VM remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem.



## <a name="create-a-managed-image-in-the-portal"></a>Criação de uma imagem gerenciada no portal 

1. Abra o [portal](https://portal.azure.com).
2. Clique no sinal de mais para criar um recurso novo.
3. No filtro da pesquisa, digite **Imagem**.
4. Selecione a **Imagem** dos resultados.
5. Na folha **Imagem**, clique em **Criar**.
6. No **Nome**, digite um nome para a imagem.
7. Se você tiver mais de uma assinatura, selecione a correta na lista suspensa **Assinatura**.
7. Em **Grupo de Recursos**, escolha **Criar novo** e digite um nome ou escolha **Existente** e escolha na lista suspensa um grupo de recursos a ser usado.
8. Em **Local**, escolha o local de seu grupo de recursos.
9. Em **Tipo de sistema operacional** selecione o tipo de sistema operacional: Windows ou Linux.
11. Em **Armazenamento de blobs**, clique em **Procurar** para procurar o VHD no armazenamento do Azure.
12. Em **Tipo de conta** escolha entre Standard_LRS ou Premium_LRS. O Standard usa unidades de disco rígido e premium usa unidades de estado sólido. Ambos usam o armazenamento redundante localmente.
13. Em **cache de disco** selecione a opção de cache de disco apropriada. As opções são **nenhum**, **somente leitura** e **leitura\gravação**.
14. Opcional: você também pode adicionar um disco de dados existente à imagem clicando em **+ Adicionar disco de dados**.  
15. Quando terminar as seleções, clique em **Criar**.
16. Depois que a imagem for criada, você verá como um recurso de **imagem** na lista de recursos no grupo de recursos que você escolheu.



## <a name="create-a-managed-image-of-a-vm-using-powershell"></a>Criação de uma imagem gerenciada de uma VM usando o Powershell

Criar uma imagem diretamente da VM garante que a imagem inclui todos os discos associados à VM, incluindo o disco do sistema operacional e os discos de dados.


Antes de iniciar, confira se você tem a versão mais recente do módulo AzureRM.Compute do PowerShell. Execute o comando a seguir para instalá-lo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Para saber mais, confira [Azure PowerShell Versioning](/powershell/azure/overview) (Controle de versão do Azure PowerShell).


1. Defina algumas variáveis. 
    ```powershell
    $vmName = "myVM"
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $imageName = "myImage"
    ```
2. Verifique se a VM foi desalocada.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Defina o status da máquina virtual como **Generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized
    ```
    
4. Obtenha a máquina virtual. 

    ```powershell
    $vm = Get-AzureRmVM -Name $vmName -ResourceGroupName $rgName
    ```

5. Crie a configuração de imagem.

    ```powershell
    $image = New-AzureRmImageConfig -Location $location -SourceVirtualMachineId $vm.ID 
    ```
6. Crie a imagem.

    ```powershell
    New-AzureRmImage -Image $image -ImageName $imageName -ResourceGroupName $rgName
    ```    



## <a name="create-a-managed-image-of-a-vhd-in-powershell"></a>Crie uma imagem gerenciada de um VHD no PowerShell

Crie uma imagem gerenciada usando o VHD do sistema operacional generalizado.


1.  Primeiro, defina os parâmetros comuns:

    ```powershell
    $rgName = "myResourceGroupName"
    $vmName = "myVM"
    $location = "West Central US" 
    $imageName = "yourImageName"
    $osVhdUri = "https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vhd"
    ```
2. Pare/desaloque a VM.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
    ```
    
3. Marque a VM como generalizada.

    ```powershell
    Set-AzureRmVm -ResourceGroupName $rgName -Name $vmName -Generalized    
    ```
4.  Crie a imagem usando o VHD do sistema operacional generalizado.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsType Windows -OsState Generalized -BlobUri $osVhdUri
    $image = New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```


## <a name="create-a-managed-image-from-a-snapshot-using-powershell"></a>Crie uma imagem gerenciada de um instantâneo usando o Powershell

Você também pode criar uma imagem gerenciada de um instantâneo do VHD de uma VM generalizada.

    
1. Defina algumas variáveis. 

    ```powershell
    $rgName = "myResourceGroup"
    $location = "EastUS"
    $snapshotName = "mySnapshot"
    $imageName = "myImage"
    ```

2. Crie o instantâneo.

   ```powershell
   $snapshot = Get-AzureRmSnapshot -ResourceGroupName $rgName -SnapshotName $snapshotName
   ```
   
3. Crie a configuração de imagem.

    ```powershell
    $imageConfig = New-AzureRmImageConfig -Location $location
    $imageConfig = Set-AzureRmImageOsDisk -Image $imageConfig -OsState Generalized -OsType Windows -SnapshotId $snapshot.Id
    ```
4. Crie a imagem.

    ```powershell
    New-AzureRmImage -ImageName $imageName -ResourceGroupName $rgName -Image $imageConfig
    ```    
    

## <a name="next-steps"></a>Próximas etapas
- Agora você pode [criar uma VM a partir de uma imagem gerenciada generalizada](create-vm-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).    


