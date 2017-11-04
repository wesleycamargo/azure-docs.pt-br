---
title: "Gerenciar conjuntos de dimensionamento de máquina virtual com o Azure PowerShell | Microsoft Docs"
description: "Cmdlets do Azure PowerShell comuns para gerenciar conjuntos de dimensionamento de máquina virtual, tais como, iniciar e parar uma instância ou alterar a capacidade do conjunto de dimensionamento."
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: iainfou
ms.openlocfilehash: 39836b207a84911d4749da8a084779d93949846b
ms.sourcegitcommit: cf4c0ad6a628dfcbf5b841896ab3c78b97d4eafd
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/21/2017
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Gerenciar um conjunto de dimensionamento da máquina virtual usando o Azure PowerShell
Durante todo o ciclo de vida do conjunto de dimensionamento de uma máquina virtual, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida. Este artigo fornece detalhes sobre alguns dos cmdlets comuns do Azure PowerShell que permitem executar essas tarefas.

Para concluir essas tarefas de gerenciamento, é necessário o último módulo do Azure PowerShell. Para obter informações sobre como instalar e usar a versão mais recente, consulte [Introdução ao Azure PowerShell](/powershell/azure/get-started-azureps). Se for preciso criar um conjunto de dimensionamento de máquinas virtuais, você pode [criar um conjunto de dimensionamento no portal do Azure](virtual-machine-scale-sets-portal-create.md).


## <a name="view-information-about-a-scale-set"></a>Exibir informações sobre um conjunto de dimensionamento
Para exibir as informações gerais sobre um conjunto de dimensionamento, use [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss). O exemplo a seguir obtém informações sobre o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```powershell
Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Exibição de VMs em um conjunto de escala
Para exibir uma lista de instância de VM em um conjunto de dimensionamento, use [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). O exemplo a seguir lista todas as instâncias de VM no conjunto de dimensionamento chamado *myScaleSet* e no grupo de recursos *myResourceGroup*. Forneça os seus próprios valores para esses nomes:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) e especifique uma instância para exibição. O exemplo a seguir exibe informações sobre a instância de VM *0* no conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos *myResourceGroup*. Insira seus próprios nomes da seguinte maneira:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>Alterar a capacidade de um conjunto de dimensionamento
Os comandos anteriores mostravam informações sobre o conjunto de escala e as instâncias de VM. Para aumentar ou diminuir o número de instâncias no conjunto de dimensionamento, você pode alterar a capacidade. O conjunto de dimensionamento cria ou remove automaticamente o número necessário de VMs e configura as VMs para receber o tráfego do aplicativo.

Primeiro, crie um objeto de conjunto de dimensionamento com [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss), em seguida, especifique um novo valor para `sku.capacity`. Para aplicar a alteração de capacidade, use [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O exemplo a seguir atualiza *myScaleSet* no grupo de recursos *myResourceGroup* para uma capacidade de *5* instâncias. Use seus próprios valores, da seguinte maneira:

```powershell
# Get current scale set
$vmss = Get-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -VirtualMachineScaleSet $vmss 
```

Demora alguns minutos para atualizar a capacidade do seu conjunto de dimensionamento. Se você diminuir a capacidade de um conjunto de dimensionamento, as máquinas virtuais com as IDs de instância mais alta são removidas primeiro.


## <a name="stop-and-start-vms-in-a-scale-set"></a>Parar e iniciar VMs em um conjunto de dimensionamento
Para parar uma ou mais VMs em um conjunto de dimensionamento, use [Stop-AzureRmVmss](/powershell/module/azurerm.compute/stop-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para parar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são paradas. Para parar várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir para a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Forneça seus valores conforme a seguir:

```powershell
Stop-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

Por padrão, as VMs paradas são desalocadas e não incorrem em encargos de computação. Se desejar que a VM permaneça em um estado de provisionamento quando interrompido, adicione o parâmetro `-StayProvisioned` ao comando anterior. VMs paradas que permanecem provisionadas incorrem em encargos de computação regulares.


### <a name="start-vms-in-a-scale-set"></a>Iniciar VMs em um conjunto de dimensionamento
Para iniciar uma ou mais VMs em um conjunto de dimensionamento, use [Start-AzureRmVmss](/powershell/module/azurerm.compute/start-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs a serem iniciadas. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são iniciadas. Para iniciar várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir inicia a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Forneça seus valores conforme a seguir:

```powershell
Start-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>Reiniciar VMs em um conjunto de dimensionamento
Para reiniciar uma ou mais VMs em um conjunto de dimensionamento, use [Restart-AzureRmVmss](/powershell/module/azurerm.compute/restart-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para reiniciar. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são reiniciadas. Para reiniciar várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir reinicia a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Forneça seus valores conforme a seguir:

```powershell
Restart-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>Remover VMs de um conjunto de dimensionamento
Para remover uma ou mais VMs em um conjunto de dimensionamento, use [Remove-AzureRmVmss](/powershell/module/azurerm.compute/remove-azurermvmss). O parâmetro `-InstanceId` permite que você especifique uma ou mais VMs para remover. Se você não especificar uma ID de instância, todas as VMs no conjunto de dimensionamento são removidas. Para remover várias VMs, separe cada ID de instância com uma vírgula.

O exemplo a seguir remove a instância *0* no conjunto de dimensionamento chamado *myScaleSet* e o grupo de recursos *myResourceGroup*. Forneça seus valores conforme a seguir:

```powershell
Remove-AzureRmVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Próximas etapas
Outras tarefas comuns dos conjuntos de dimensionamento incluem como [implantar um aplicativo](virtual-machine-scale-sets-deploy-app.md) e [atualizar instâncias de VM](virtual-machine-scale-sets-upgrade-scale-set.md). Você também pode usar o Azure PowerShell para [configurar as regras de dimensionamento automático](virtual-machine-scale-sets-autoscale-overview.md).