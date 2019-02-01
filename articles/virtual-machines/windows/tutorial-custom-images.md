---
title: Tutorial – Criar imagens personalizadas de VM com o Azure PowerShell | Microsoft Docs
description: Neste tutorial, você aprenderá a usar o Azure PowerShell para criar uma imagem de máquina virtual personalizada no Azure
services: virtual-machines-windows
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: tysonn
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/14/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: f8585023b01de55acb6c1b43b45e27af914a0a96
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2019
ms.locfileid: "54884411"
---
# <a name="tutorial-create-a-custom-image-of-an-azure-vm-with-azure-powershell"></a>Tutorial: Criar uma imagem personalizada de uma VM do Azure com o Azure PowerShell

Imagens personalizadas são como imagens do marketplace, mas você mesmo as cria. As imagens personalizadas podem ser usadas para configurações de inicialização como o pré-carregamento de aplicativos, configurações de aplicativos e outras configurações do sistema operacional. Neste tutorial, você criará sua própria imagem personalizada de uma máquina virtual do Azure. Você aprenderá como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

## <a name="before-you-begin"></a>Antes de começar

As etapas abaixo detalham como pegar uma máquina virtual existente e transformá-la em uma imagem personalizada reutilizável que você pode usar para criar novas instâncias de VM.

Para concluir o exemplo neste tutorial, você deverá ter uma máquina virtual. Se necessário, este [exemplo de script](../scripts/virtual-machines-windows-powershell-sample-create-vm.md) pode criar uma para você. Ao trabalhar com este tutorial, substitua o grupo de recursos e os nomes de VM onde for necessário.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá a versão do módulo AzureRM 5.7.0 ou superior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

## <a name="prepare-vm"></a>Preparar VM

Para criar uma imagem de uma máquina virtual, você precisará preparar a VM generalizando a VM, desalocando e, em seguida, marcando a VM de origem como generalizada no Azure.

### <a name="generalize-the-windows-vm-using-sysprep"></a>Generalizar a VM Windows usando Sysprep

O Sysprep remove todas as informações pessoais da conta, entre outros itens, e prepara o computador para ser utilizado como uma imagem. Para obter detalhes sobre o Sysprep, confira [Como usar o Sysprep: Um introdução](https://technet.microsoft.com/library/bb457073.aspx).


1. Conectar-se à máquina virtual.
2. Abra uma janela de prompt de comando como administrador. Altere o diretório para *%windir%\system32\sysprep* e, a seguir, execute *sysprep.exe*.
3. Na caixa de diálogo **Ferramenta de Preparação do Sistema**, selecione *Entrar na Configuração Inicial pelo Usuário do Sistema (OOBE)* e verifique se a caixa de seleção *Generalizar* está marcada.
4. Em **Opções de Desligamento**, selecione *Desligar* e em **OK**.
5. Quando o Sysprep for concluído, desligará a máquina virtual. **Não reinicie a VM**.

### <a name="deallocate-and-mark-the-vm-as-generalized"></a>Desalocar e marcar a VM como generalizada

Para criar uma imagem, a VM precisa ser desalocada e marcada como generalizada no Azure.

Desaloque a VM usando [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm).

```azurepowershell-interactive
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Force
```

Defina o status da máquina virtual como `-Generalized` usando [Set-AzureRmVm](/powershell/module/azurerm.compute/set-azurermvm). 
   
```azurepowershell-interactive
Set-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM -Generalized
```


## <a name="create-the-image"></a>Criar a imagem

Agora, você pode criar uma imagem da VM usando [New-AzureRmImageConfig](/powershell/module/azurerm.compute/new-azurermimageconfig) e [New-AzureRmImage](/powershell/module/azurerm.compute/new-azurermimage). O exemplo a seguir cria uma imagem chamada *myImage* por meio de uma VM chamada *myVM*.

Obtenha a máquina virtual. 

```azurepowershell-interactive
$vm = Get-AzureRmVM -Name myVM -ResourceGroupName myResourceGroup
```

Crie a configuração de imagem.

```azurepowershell-interactive
$image = New-AzureRmImageConfig -Location EastUS -SourceVirtualMachineId $vm.ID 
```

Crie a imagem.

```azurepowershell-interactive
New-AzureRmImage -Image $image -ImageName myImage -ResourceGroupName myResourceGroup
``` 

 
## <a name="create-vms-from-the-image"></a>Criar VMs por meio da imagem

Agora que tem uma imagem, você pode criar uma ou mais VMs novas por meio da imagem. A criação de uma VM com base em uma imagem personalizada é semelhante à criação de uma VM usando uma imagem do Marketplace. Ao usar uma imagem do Marketplace, você precisa fornecer informações sobre a imagem, o provedor da imagem, a oferta, a SKU e a versão. Ao usar o parâmetro simplificado definido para o [novo AzureRMVM](/powershell/module/azurerm.compute/new-azurermvm) cmdlet, você só precisará fornecer o nome da imagem personalizada, desde que ela esteja no mesmo grupo de recursos. 

Este exemplo cria uma VM chamada *myVMfromImage* a partir de *myImage*, em *myResourceGroup*.


```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVMfromImage" `
    -ImageName "myImage" `
    -Location "East US" `
    -VirtualNetworkName "myImageVnet" `
    -SubnetName "myImageSubnet" `
    -SecurityGroupName "myImageNSG" `
    -PublicIpAddressName "myImagePIP" `
    -OpenPorts 3389
```

## <a name="image-management"></a>Gerenciamento de imagens 

Estes são alguns exemplos de tarefas comuns de imagem gerenciada e como para concluí-las usando o PowerShell.

Liste todas as imagens por nome.

```azurepowershell-interactive
$images = Get-AzureRMResource -ResourceType Microsoft.Compute/images 
$images.name
```

Exclua uma imagem. Este exemplo exclui a imagem denominada *myImage* do *myResourceGroup*.

```azurepowershell-interactive
Remove-AzureRmImage `
    -ImageName myImage `
    -ResourceGroupName myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você criou uma imagem de VM personalizada. Você aprendeu como:

> [!div class="checklist"]
> * Aplicar Sysprep e generalizar VMs
> * Criar uma imagem personalizada
> * Criar uma VM por meio de uma imagem personalizada
> * Listar todas as imagens na sua assinatura
> * Excluir uma imagem

Avance para o próximo tutorial para saber mais sobre a alta disponibilidade das máquinas virtuais.

> [!div class="nextstepaction"]
> [Criar VMs altamente disponíveis](tutorial-availability-sets.md)



