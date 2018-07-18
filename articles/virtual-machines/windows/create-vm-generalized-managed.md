---
title: Criar uma VM de uma imagem gerenciada no Azure | Microsoft Docs
description: Crie uma máquina virtual do Windows a partir de uma imagem de gerenciada generalizada usando o Azure PowerShell ou o portal do Azure no modelo de implantação do Gerenciador de Recursos.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/27/2017
ms.author: cynthn
ms.openlocfilehash: 1d543bd9590664e74cff70cf55e8f7bd42f2c6f0
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="create-a-vm-from-a-managed-image"></a>Criar uma VM por meio de uma imagem gerenciada

Você pode criar várias VMs de uma imagem de VM gerenciada usando o PowerShell ou o portal do Azure. Uma imagem de VM gerenciada contém as informações necessárias para criar uma VM, incluindo o sistema operacional e os discos de dados. Os VHDs que formam a imagem, incluindo os discos do sistema operacional e quaisquer discos de dados, são armazenados como discos gerenciados. 

Você já precisa ter [criado uma imagem de VM gerenciada](capture-image-resource.md) a ser usada para criação da nova VM. 

## <a name="use-the-portal"></a>Usar o portal

1. Abra o [Portal do Azure](https://portal.azure.com).
2. No menu do lado esquerdo, selecione **Todos os recursos**. Você pode classificar os recursos por **Tipo** localizar facilmente as suas imagens.
3. Selecione a imagem que você deseja usar a partir da lista. A imagem da página **Visão geral** será aberta.
4. Clique em **+ Criar VM** a partir do menu.
5. Insira as informações da máquina virtual. O nome do usuário e a senha inseridos aqui são usados para fazer logon na máquina virtual. Ao concluir, clique em **OK**. Você pode criar a nova VM em um grupo de recursos existente ou escolher **Criar novo** para criar um novo grupo de recursos para armazenar a VM.
6. Selecione um tamanho para a VM. Para ver mais tamanhos, selecione **Exibir todos os** ou altere o filtro **Tipo de disco com suporte**. 
7. Em **Configurações**, faça as alterações necessárias e clique em **Ok**. 
8. Na página de resumo, você deve visualizar o nome da sua imagem listado para **Imagem privada**. Clique em **Ok** para iniciar a implantação da máquina virtual.


## <a name="use-powershell"></a>Usar o PowerShell

Você pode usar o PowerShell para criar uma máquina virtual a partir de uma imagem usando o conjunto de parâmetro simplificado definido para o novo cmdlet do [AzureRmVm](/powershell/module/azurerm.compute/new-azurermvm). A imagem precisa estar no mesmo grupo de recursos onde você deseja criar a máquina virtual.

Este exemplo requer o módulo do AzureRM, versão 5.6.0 ou posterior. Execute ` Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

O conjunto de parâmetro simplificado definido para o novo AzureRmVm requer apenas que você forneça um nome, nome de grupo e a imagem de recurso para criar uma máquina virtual a partir de uma imagem, mas ele usará o valor do parâmetro **-Nome** como o nome de todos os recursos que ele cria automaticamente. Neste exemplo, podemos fornecer nomes mais detalhados para cada recurso, mas permitir ao cmdlet criá-los automaticamente. Você também pode criar recursos, como a rede virtual, antecipadamente e passar o nome para o cmdlet. Ele usará os recursos existentes se puder encontrá-los pelo nome.

O exemplo a seguir cria uma máquina virtual chamada *myVMFromImage*, no grupo de recursos *myResourceGroup*, na imagem chamada *myImage*. 


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



## <a name="next-steps"></a>Próximas etapas
Para gerenciar sua nova máquina virtual com o Azure PowerShell, consulte [Criar e gerenciar VMs do Windows com o módulo do Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

