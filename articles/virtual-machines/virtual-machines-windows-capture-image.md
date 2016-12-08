---
title: Capturar uma imagem de VM de uma VM do Azure generalizada | Microsoft Docs
description: "Saiba como capturar uma imagem de VM de uma VM do Azure generalizada criada no modelo de implantação do Resource Manager"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: afdae4a1-6dfb-47b4-902a-f327f9bfe5b4
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 689f413ebcab734024322d4c6f46c0c824840017


---
# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Como capturar uma imagem de VM de uma VM do Azure generalizada | Microsoft Azure
Este artigo mostra como usar o Azure PowerShell para criar uma imagem de uma VM do Azure generalizada. Depois você pode usar a imagem para criar outra VM. Esta imagem inclui o disco do SO e os discos de dados anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, de modo que você precisará configurar esses recursos quando criar a nova VM. 

## <a name="prerequisites"></a>Pré-requisitos
* Você já precisa ter [generalizado a VM](virtual-machines-windows-generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Generalizar uma VM remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem.
* Você precisa ter a versão 1.0.x ou mais recente do Azure PowerShell instalada. Se você ainda não tiver instalado o PowerShell, leia [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para ver as etapas de instalação.

## <a name="log-in-to-azure-powershell"></a>Fazer logon no Azure PowerShell
1. Abra o Azure PowerShell e conecte-se à sua conta do Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
   
    Uma janela pop-up é aberta para inserir as credenciais da conta do Azure.
2. Obtenha as IDs de assinatura das assinaturas disponíveis.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Defina a assinatura correta usando a ID da assinatura.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Desalocar a VM e definir o estado como generalizada
1. Desaloque os recursos da VM.
   
    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```
   
    O *Status* da VM no Portal do Azure muda de **Parado** para **Parado (desalocado)**.
2. Defina o status da máquina virtual como **Generalizado**. 
   
    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```
3. Verifique o status da VM. A seção **OSState/generalizado** da VM deve ter **DisplayStatus** definido como **VM generalizada**.  
   
    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem
1. Copie a imagem da máquina virtual para o contêiner de armazenamento de destino usando este comando. A imagem é criada na mesma conta de armazenamento que a máquina virtual original. O parâmetro `-Path` salva uma cópia do modelo JSON localmente. O parâmetro `-DestinationContainerName` é o nome do contêiner em que você quer manter as imagens. Se o contêiner não existir, ele será criado para você.
   
    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```
   
    Você pode obter a URL da imagem no modelo do arquivo JSON. Vá para a seção **recursos** > **storageProfile** > **osDisk** > **image** > **uri** para obter o caminho completo da imagem. A URL da imagem parece com esta: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
   
    Você também pode verificar o URI no portal. A imagem é copiada em um contêiner denominado **sistema** em sua conta de armazenamento. 

## <a name="next-steps"></a>Próximas etapas
* Agora você pode [criar uma VM da imagem](virtual-machines-windows-create-vm-generalized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


