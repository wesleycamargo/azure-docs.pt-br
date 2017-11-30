---
title: "Desanexar um disco de dados de uma VM Windows – Azure | Microsoft Docs"
description: "Saiba como desanexar um disco de dados de uma máquina virtual no Azure usando o modelo de implantação do Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 13180343-ac49-4a3a-85d8-0ead95e2028c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 7013e7ff3cb14dcad8e3e9a926bcee771180259d
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/18/2017
---
# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados de uma máquina virtual Windows
Quando não precisar mais de um disco de dados conectado a uma máquina virtual, você poderá desanexá-lo facilmente. Essa ação remove o disco da máquina virtual, mas não o remove do armazenamento.

> [!WARNING]
> Se você desanexar um disco, ele não será excluído automaticamente. Se você se inscreveu para o armazenamento Premium, você continuará incorrendo em encargos de armazenamento para o disco. Para obter mais informações, consulte [Preços e cobrança ao usar o Armazenamento Premium](premium-storage.md#pricing-and-billing).
>
>

Se desejar usar os dados existentes no disco novamente, você pode reanexá-lo à mesma máquina virtual ou anexá-lo a uma outra máquina virtual.

## <a name="detach-a-data-disk-using-the-portal"></a>Desanexar um disco de dados usando o portal

1. No menu à esquerda, selecione **Máquinas Virtuais**.
2. Selecione a máquina virtual que tem o disco de dados que você deseja desanexar e clique em **Parar** para desalocar a VM.
3. No painel de máquina virtual, selecione **Discos**.
4. Na parte superior do painel **Discos**, selecione **Editar**.
5. No painel **Discos**, mais à direita do disco de dados que você deseja desanexar, clique no botão Desanexar ![Imagem do botão Desanexar](./media/detach-disk/detach.png).
5. Depois que o disco for removido, clique em **Salvar** na parte superior do painel.
6. No painel da máquina virtual, clique em **Visão Geral** e, em seguida, clique no botão **Iniciar** na parte superior do painel para reiniciar a VM.



O disco permanece no armazenamento mas não esteja conectado a uma máquina virtual.

## <a name="detach-a-data-disk-using-powershell"></a>Desanexar um disco de dados usando o PowerShell
Neste exemplo, o primeiro comando obtém a máquina virtual chamada **MyVM07** no grupo de recursos **RG11** usando o cmdlet [Get-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) e a armazena na variável **$VirtualMachine**.

A segunda linha remove o disco de dados chamado DataDisk3 da máquina virtual usando o cmdlet [AzureRmVMDataDisk remover](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

A terceira linha atualiza o estado da máquina virtual, usando o cmdlet [Update-AzureRmVM](/powershell/module/azurerm.compute/update-azurermvm) para concluir o processo de remoção do disco de dados.

```azurepowershell-interactive
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07"
Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
Update-AzureRmVM -ResourceGroupName "RG11" -VM $VirtualMachine
```

Para obter mais informações, consulte [Remove-AzureRmVMDataDisk](/powershell/module/azurerm.compute/remove-azurermvmdatadisk).

## <a name="next-steps"></a>Próximas etapas
Se você quiser reutilizar o disco de dados, você poderá simplesmente [anexá-lo a outra VM](attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

