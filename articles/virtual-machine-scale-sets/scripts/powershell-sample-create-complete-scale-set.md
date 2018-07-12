---
title: Exemplos do Azure PowerShell - Criar um conjunto de dimensionamento de máquinas virtuais completo| Microsoft Docs
description: Exemplos do Azure PowerShell
services: virtual-machine-scale-sets
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/29/2018
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: c92b922adeb2e3cd80a271062704feb3bdb4c949
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38652149"
---
# <a name="create-a-complete-virtual-machine-scale-set-with-powershell"></a>Criar um conjunto de dimensionamento de máquinas virtuais completo com o PowerShell
Esse script cria um conjunto de dimensionamento de máquinas virtuais que executa o Windows Server 2016. Recursos individuais são configurados e criados, em vez de usar as [opções de criação de recursos internos disponíveis aqui em New-AzureRmVmss](powershell-sample-create-simple-scale-set.md). Depois de executar o script, é possível acessar a VM via RDP.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script de exemplo
[!code-powershell[main](../../../powershell_scripts/virtual-machine-scale-sets/complete-scale-set/complete-scale-set.ps1 "Create a complete virtual machine scale set")]

## <a name="clean-up-deployment"></a>Limpar a implantação
Execute o comando a seguir para remover o grupo de recursos, o conjunto de dimensionamento e todos os recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName
```

## <a name="script-explanation"></a>Explicação sobre o script
Esse script usa os seguintes comandos para criar a implantação. Cada item em que a tabela contém links para a documentação específica do comando.

| Comando | Observações |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Cria um grupo de recursos no qual todos os recursos são armazenados. |
| [New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig) | Cria uma configuração de sub-rede. Essa configuração é usada com o processo de criação de rede virtual. |
| [New-AzureRmVirtualNetwork](/powershell/module/azurerm.network/new-azurermvirtualnetwork) | Cria uma rede virtual. |
| [New-AzureRmPublicIpAddress](/powershell/module/azurerm.network/new-azurermpublicipaddress) | Cria um endereço IP público. |
| [New-AzureRmLoadBalancerFrontendIpConfig](/powershell/module/azurerm.network/new-azurermloadbalancerfrontendipconfig) | Cria uma configuração de IP front-end para um balanceador de carga. |
| [New-AzureRmLoadBalancerBackendAddressPoolConfig](/powershell/module/azurerm.network/new-azurermloadbalancerbackendaddresspoolconfig) | Cria uma configuração de pool de endereços de back-end para um balanceador de carga. |
| [New-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerinboundnatruleconfig) | Cria uma configuração de regra NAT de entrada para um balanceador de carga. |
| [New-AzureRmLoadBalancer](/powershell/module/azurerm.network/new-azurermloadbalancer) | Cria um balanceador de carga. |
| [Add-AzureRmLoadBalancerProbeConfig](/powershell/module/azurerm.network/new-azurermloadbalancerprobeconfig) | Cria uma configuração de investigação para um balanceador de carga. |
| [Add-AzureRmLoadBalancerRuleConfig](/powershell/module/azurerm.network/new-azurermloadbalancerruleconfig) | Cria uma configuração de regra para um balanceador de carga. |
| [Set-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Set-AzureRmLoadBalancer) | Atualize o balanceador de carga com as informações fornecidas. |
| [New-AzureRmVmssIpConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssIpConfig) | Crie uma configuração de IP para as instâncias de VM do conjunto de dimensionamento. As instâncias de VM estão conectadas ao pool de back-end do balanceador de carga, ao pool NAT e à sub-rede da rede virtual. |
| [New-AzureRmVmssConfig](/powershell/module/AzureRM.Compute/New-AzureRmVmssConfig) | Criar a configuração do conjunto de dimensionamento. Essa configuração inclui informações como o número de instâncias de VM a ser criado, o SKU da VM (tamanho) e o modo de política de atualização. A configuração é adicionada por outros cmdlets e é usada durante a criação do conjunto de dimensionamento. |
| [Set-AzureRmVmssStorageProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Defina a imagem a ser usada para as instâncias de VM e adicione-a à configuração do conjunto de dimensionamento. |
| [Set-AzureRmVmssOsProfile](/powershell/module/AzureRM.Compute/Set-AzureRmVmssStorageProfile) | Defina o nome de usuário administrativo e as credenciais de senha e o prefixo de nomenclatura da VM. Adicione esses valores à configuração do conjunto de dimensionamento. |
| [Add-AzureRmVmssNetworkInterfaceConfiguration](/powershell/module/AzureRM.Compute/Add-AzureRmVmssNetworkInterfaceConfiguration) | Adicione uma interface de rede virtual às instâncias de VM, com base na configuração de IP. Adicione esses valores à configuração do conjunto de dimensionamento. |
| [New-AzureRmVmss](/powershell/module/AzureRM.Compute/New-AzureRmVmss) | Crie o conjunto de dimensionamento com base nas informações fornecidas na configuração do conjunto de dimensionamento. |
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Remove um grupo de recursos e todos os recursos contidos nele. |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações sobre o módulo do Azure PowerShell, confira [Documentação do Azure PowerShell](/powershell/azure/overview).

Outros exemplos de script do PowerShell de conjunto de dimensionamento de máquinas virtuais podem ser encontrados na [documentação do conjunto de dimensionamento de máquinas virtuais do Azure](../powershell-samples.md).
