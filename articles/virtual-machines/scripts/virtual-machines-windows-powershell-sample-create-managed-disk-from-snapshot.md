---
title: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um instantâneo | Microsoft Docs
description: Amostra de script do Azure PowerShell – Criar um disco gerenciado com base em um instantâneo
services: virtual-machines-windows
documentationcenter: storage
author: ramankumarlive
manager: kavithag
editor: tysonn
tags: azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 06/05/2017
ms.author: ramankum
ms.openlocfilehash: 9f9523381c6e5b684572080e340f67fabd4e200f
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427807"
---
# <a name="create-a-managed-disk-from-a-snapshot-with-powershell"></a>Criar um disco gerenciado com base em um instantâneo com o PowerShell

Esse script cria um disco gerenciado com base em um instantâneo. Use-o para restaurar uma máquina virtual de instantâneos do sistema operacional e de discos de dados. Crie discos gerenciados do sistema operacional e de dados com base nos respectivos instantâneos e, em seguida, crie uma nova máquina virtual anexando os discos gerenciados. Restaure também discos de dados de uma VM existente anexando os discos de dados criados com base em instantâneos.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá que você esteja usando o módulo do Azure PowerShell versão 4.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se precisar instalar ou atualizar, consulte [Instalar o Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure. 

## <a name="sample-script"></a>Script de exemplo

[!code-powershell[main](../../../powershell_scripts/virtual-machine/create-managed-disk-from-snapshot/create-managed-disk-from-snapshot.ps1 "Create managed disk from snapshot")]


## <a name="script-explanation"></a>Explicação sobre o script

Esse script usa os comandos a seguir para criar um disco gerenciado com base em um instantâneo. Cada comando da tabela é vinculado à documentação específica do comando.

| Comando | Observações |
|---|---|
| [Get-AzureRmSnapshot](/powershell/module/azurerm.compute/Get-AzureRmSnapshot) | Obtém as propriedades do instantâneo.  |
| [New-AzureRmDiskConfig](/powershell/module/azurerm.compute/New-AzureRmDiskConfig) | Cria uma configuração de disco que é usada para a criação do disco. Inclui a ID do recurso do instantâneo pai, o local que é o mesmo local do instantâneo pai e o tipo de armazenamento.  |
| [New-AzureRmDisk](/powershell/module/azurerm.compute/New-AzureRmDisk) | Cria um disco utilizando a configuração do disco, o nome do disco e o nome do grupo de recursos passados como parâmetros. |


## <a name="next-steps"></a>Próximas etapas

[Criar uma máquina virtual com base em um disco gerenciado](./virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks.md?toc=%2fpowershell%2fmodule%2ftoc.json)

Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Amostras de script do PowerShell da máquina virtual adicionais podem ser encontrados na [documentação da VM Windows do Azure](../windows/powershell-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
