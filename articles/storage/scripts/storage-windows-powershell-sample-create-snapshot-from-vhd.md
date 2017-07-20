---
title: "Exemplo de Script do Azure PowerShell -  Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo | Microsoft Docs"
description: "Exemplo de Script do Azure PowerShell -  Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo"
services: managed-disks
documentationcenter: storage
author: ramankum
manager: kavithag
editor: ramankum
tags: azure-service-management
ms.assetid: 
ms.service: managed-disks-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 3db2ba858e5c685bb2b53a4f09423acdcdb10c22
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017

---

# <a name="create-a-snapshot-from-a-vhd-to-create-multiple-identical-managed-disks-in-small-amount-of-time-with-powershell"></a>Criar um instantâneo de um VHD para criar discos gerenciados idênticos múltiplos em pouco tempo com PowerShell

Esse script cria um instantâneo de um arquivo VHD em uma conta de armazenamento na mesma assinatura ou em uma diferente. Utilize esse script para importar um VHD especializado (não generalizado/de sysprep) para um instantâneo e, em seguida, utilize o instantâneo para criar discos gerenciados idênticos múltiplos em pouco tempo. Além disso, utilize-o para importar um VHD de dados para um instantâneo e, em seguida, use o instantâneo para criar discos gerenciados múltiplos em pouco tempo. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[principal](../../../powershell_scripts/storage/create-snapshots-from-vhd-in-different-subscription/create-snapshots-from-vhd-in-different-subscription.ps1 "Criar instantâneo do VHD")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um VHD em outra assinatura. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Cria a configuração do disco que é usada para criação do disco. Inclui o tipo de armazenamento, o local, o ID do recurso da conta de armazenamento em que o VHD principal está armazenado e o URI do VHD do VHD pai. |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |

## <a name="next-steps"></a>Próximas etapas

[Criar um disco gerenciado a partir do instantâneo](./../../storage/scripts/storage-windows-powershell-sample-create-managed-disk-from-snapshot.md?toc=%2fpowershell%2fmodule%2ftoc.json)


[Criar uma máquina virtual anexando um disco gerenciado como disco do SO](./../../virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../../virtual-machines/windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
