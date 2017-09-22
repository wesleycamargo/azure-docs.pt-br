---
title: "Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um instantâneo | Microsoft Docs"
description: "Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um instantâneo"
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 0ead3d80ee509bb8043824f29a9954bd57e095d1
ms.contentlocale: pt-br
ms.lasthandoff: 09/20/2017

---

# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Criar um disco gerenciado com base em um instantâneo com o PowerShell

Esse script cria um disco gerenciado com base em um instantâneo. Use-o para restaurar uma máquina virtual de instantâneos do sistema operacional e de discos de dados. Crie discos gerenciados do sistema operacional e de dados com base nos respectivos instantâneos e, em seguida, crie uma nova máquina virtual anexando os discos gerenciados. Restaure também discos de dados de uma VM existente anexando os discos de dados criados com base em instantâneos.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um instantâneo. Cada comando na tabela redireciona para a documentação específica do comando.

| Command | Observações |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | Obtém as propriedades do instantâneo.  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Cria uma configuração de disco que é usada para a criação do disco. Inclui a ID do recurso do instantâneo pai, o local que é o mesmo local do instantâneo pai e o tipo de armazenamento.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |


## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
