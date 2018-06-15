---
title: Exemplos do Azure PowerShell – Habilitar o dimensionamento automático baseado em host | Microsoft Docs
description: Exemplos do Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: efa608cb1efa3fed48113adea45f15a2cf075439
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
ms.locfileid: "30246348"
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-powershell"></a>Dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais com o PowerShell
Esse script cria um conjunto de dimensionamento de máquinas virtuais executando o Windows Server 2016 e usa métricas baseadas em host para dimensionar automaticamente conforme a carga da CPU muda.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/auto-scale-host-metrics/auto-scale-host-metrics.ps1 "Automatically scale a virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss) | Cria o conjunto de dimensionamento de máquinas virtuais e todos os recursos de suporte, incluindo a rede virtual, o balanceador de carga e as regras de NAT. |
| [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) | Obtém informações sobre um conjunto de dimensionamento de máquinas virtuais. |
| [Add-AzureRmVmssExtension](/powershell/module/azurerm.compute/add-azurermvmssextension) | Adiciona uma extensão de VM para um Script personalizado para instalar um aplicativo Web básico. |
| [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss) | Atualiza o modelo de conjunto de dimensionamento de máquinas virtuais para aplicar a extensão de VM. |
| [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) | Obtém informações sobre o endereço IP público atribuído usado pelo balanceador de carga. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell de conjunto de dimensionamento de máquinas virtuais podem ser encontrados na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../powershell-samples.md).