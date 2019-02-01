---
title: Desanexar um disco de dados de uma VM Windows – Azure | Microsoft Docs
description: Desanexe um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/17/2018
ms.author: cynthn
ms.subservice: disks
ms.openlocfilehash: e27826629873566bf7b746649923c25e6ab70827
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457149"
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows

Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](premium-storage.md#pricing-and-billing).
>
>

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.


## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell

É possível remover um disco de dados *hot* usando o PowerShell, mas certifique-se de que nada esteja usando ativamente o disco antes de desanexá-lo da VM.

Neste exemplo, removemos o disco nomeado **myDisk** da VM **myVM** no grupo de recursos **myResourceGroup**. Primeiro, remova o disco usando o cmdlet [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk). Em seguida, atualize o estado da máquina virtual, usando o cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "myDisk"
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.


## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

1. No menu à esquerda, selecione **Máquinas Virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e clique em **Parar** para desalocar a VM.
3. No painel de máquina virtual, selecione **Discos**.
4. Na parte superior do painel **Discos**, selecione **Editar**.
5. No painel **Discos**, mais à direita do disco de dados que você deseja desanexar, clique no botão Desanexar ![Imagem do botão Desanexar](./media/detach-disk/detach.png).
5. Depois que o disco for removido, clique em **Salvar** na parte superior do painel.
6. No painel da máquina virtual, clique em **Visão Geral** e, em seguida, clique no botão **Iniciar** na parte superior do painel para reiniciar a VM.

O disco permanecerá no armazenamento, mas não estará mais conectado a uma máquina virtual.

## <a name="next-steps"></a>Próximas etapas
Se você quiser reutilizar o disco de dados, você poderá simplesmente [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

