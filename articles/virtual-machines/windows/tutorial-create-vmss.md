---
title: Criar um conjunto de dimensionamento de máquinas virtuais para Windows no Azure | Microsoft Docs
description: Criar e implantar um aplicativo altamente disponível em VMs Windows usando um conjunto de dimensionamento de máquinas virtuais
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/29/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dd573a90e49197d59e6228359f57fcd4cc3f69e2
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="create-a-virtual-machine-scale-set-and-deploy-a-highly-available-app-on-windows"></a>Criar um conjunto de dimensionamento de máquinas virtuais e implantar um aplicativo altamente disponível no Windows
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. Dimensione o número de VMs no conjunto de dimensionamento manualmente ou defina regras para o dimensionamento automático com base no uso de recursos, como CPU, demanda de memória ou tráfego de rede. Neste tutorial, você implantará um conjunto de dimensionamento de máquinas virtuais no Azure. Você aprenderá como:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para definir um site do IIS para dimensionar
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou diminuir o número de instâncias em um conjunto de dimensionamento
> * Criar regras de dimensionamento automático

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se você escolher instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.6 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Connect-AzureRmAccount` para criar uma conexão com o Azure.


## <a name="scale-set-overview"></a>Visão geral do conjunto de escala
Um conjunto de dimensionamento de máquinas virtuais permite implantar e gerenciar um conjunto de máquinas virtuais idênticas de dimensionamento automático. As VMs em um conjunto de dimensionamento são distribuídas entre domínios de falha lógica e domínios de atualização em um ou mais *grupos de posicionamento*. Esses são grupos de VMs configuradas de maneira semelhante, da mesma forma que os [conjuntos de disponibilidade](tutorial-availability-sets.md).

VMs são criadas conforme necessário em um conjunto de escala. Você define regras de dimensionamento automático para controlar como e quando as VMs são adicionadas ou removidas do conjunto de dimensionamento. Essas regras podem ser disparados com base em métricas como carga de CPU, utilização de memória ou tráfego de rede.

Escala define suporte até 1.000 VMs quando você usar uma imagem da plataforma Windows Azure. Para cargas de trabalho com requisitos significativos de personalização de VM ou instalação, você pode querer [criar uma imagem de VM personalizada](tutorial-custom-images.md). Você pode criar até 300 VMs em um conjunto de dimensionamento ao usar uma imagem personalizada.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). O exemplo a seguir cria um conjunto de dimensionamento chamado *myScaleSet* que usa a imagem da plataforma do *Datacenter do Windows Server 2016*. São criados automaticamente os recursos de rede do Azure para rede virtual, endereço IP público e balanceador de carga. Quando solicitado, forneça suas próprias credenciais administrativas desejadas para as instâncias de VM no conjunto de dimensionamento:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -Location "EastUS" `
  -VMScaleSetName "myScaleSet" `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -UpgradePolicy "Automatic"
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.


## <a name="deploy-sample-application"></a>Implantar um aplicativo de exemplo
Para testar seu conjunto de dimensionamento, instale um aplicativo Web básico. A Extensão de Script Personalizado do Azure para baixar e executar um script que instala o IIS nas instâncias de VM. Essa extensão é útil para a configuração de implantação de postagem, instalação de software ou qualquer outra configuração/tarefa de gerenciamento. Para obter mais informações, consulte a [Visão geral da Extensão de Script Personalizado](extensions-customscript.md).

Usar a Extensão de Script Personalizado para instalar um servidor Web IIS básico. Aplique a Extensão do Script Personalizado que instala o IIS da seguinte maneira:

```azurepowershell-interactive
# Define the script for your Custom Script Extension to run
$publicSettings = @{
    "fileUris" = (,"https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate-iis.ps1");
    "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File automate-iis.ps1"
}

# Get information about the scale set
$vmss = Get-AzureRmVmss `
            -ResourceGroupName "myResourceGroupScaleSet" `
            -VMScaleSetName "myScaleSet"

# Use Custom Script Extension to install IIS and configure basic website
Add-AzureRmVmssExtension -VirtualMachineScaleSet $vmss `
    -Name "customScript" `
    -Publisher "Microsoft.Compute" `
    -Type "CustomScriptExtension" `
    -TypeHandlerVersion 1.8 `
    -Setting $publicSettings

# Update the scale set and apply the Custom Script Extension to the VM instances
Update-AzureRmVmss `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $vmss
```


## <a name="test-your-scale-set"></a>Testar seu conjunto de dimensionamento
Para ver sua escala definida em ação, obtenha o endereço IP público de balanceador de carga com [Get-AzureRmPublicIPAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress). O seguinte exemplo obtém o endereço IP de *myPublicIP*, criado como parte do conjunto de dimensionamento:

```azurepowershell-interactive
Get-AzureRmPublicIPAddress `
    -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myPublicIPAddress" | select IpAddress
```

Insira o endereço IP público em um navegador da Web. O aplicativo Web é exibido, incluindo o nome do host da VM para a qual o balanceador de carga distribui o tráfego:

![Execução do site do IIS](./media/tutorial-create-vmss/running-iis-site.png)

Para ver a escala definida em ação, você pode forçar atualização seu navegador da web para ver o balanceador de carga distribuir tráfego entre todas as VMs que executam seu aplicativo.


## <a name="management-tasks"></a>Tarefas de gerenciamento
Durante todo o ciclo de vida do conjunto de dimensionamento, você poderá precisar executar uma ou mais tarefas de gerenciamento. Além disso, talvez você deseje criar scripts que automatizam várias tarefas do ciclo de vida. O Azure PowerShell fornece uma maneira rápida de realizar essas tarefas. Estas são algumas tarefas comuns.

### <a name="view-vms-in-a-scale-set"></a>Exibição de VMs em um conjunto de escala
Para exibir uma lista de instância de VM em um conjunto de dimensionamento, use [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) da seguinte forma:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet"
```

A saída de exemplo abaixo mostra duas instâncias de VM no conjunto de dimensionamento:

```powershell
ResourceGroupName                 Name Location             Sku InstanceID ProvisioningState
-----------------                 ---- --------             --- ---------- -----------------
MYRESOURCEGROUPSCALESET   myScaleSet_0   eastus Standard_DS1_v2          0         Succeeded
MYRESOURCEGROUPSCALESET   myScaleSet_1   eastus Standard_DS1_v2          1         Succeeded
```

Para exibir informações adicionais sobre uma instância específica de VM, adicione o parâmetro `-InstanceId` a [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). O exemplo abaixo exibe informações sobre a instância de VM *1*:

```azurepowershell-interactive
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroupScaleSet" -VMScaleSetName "myScaleSet" -InstanceId "1"
```


### <a name="increase-or-decrease-vm-instances"></a>Aumentar ou diminuir as instâncias de VM
Para ver o número de instâncias que você tem atualmente em um conjunto de dimensionamento, use [Get-AzureRmVmss](/powershell/module/azurerm.compute/get-azurermvmss) e consulte *sku.capacity*:

```azurepowershell-interactive
Get-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -VMScaleSetName "myScaleSet" | `
    Select -ExpandProperty Sku
```

Manualmente, é possível aumentar ou diminuir o número de máquinas virtuais no conjunto de dimensionamento com [Update-AzureRmVmss](/powershell/module/azurerm.compute/update-azurermvmss). O seguinte exemplo aumenta o número de VMs no conjunto de dimensionamento para *3*:

```azurepowershell-interactive
# Get current scale set
$scaleset = Get-AzureRmVmss `
  -ResourceGroupName "myResourceGroupScaleSet" `
  -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$scaleset.sku.capacity = 3
Update-AzureRmVmss -ResourceGroupName "myResourceGroupScaleSet" `
    -Name "myScaleSet" `
    -VirtualMachineScaleSet $scaleset
```

A atualização para o número especificado de instâncias no conjunto de dimensionamento leva alguns minutos.


### <a name="configure-autoscale-rules"></a>Configurar regras de dimensionamento automático
Em vez de dimensionar manualmente o número de instâncias no conjunto de dimensionamento, defina regras de dimensionamento automático. Essas regras monitoram as instâncias no conjunto de dimensionamento e respondem adequadamente com base nas métricas e nos limites que você define. O exemplo a seguir escala horizontalmente o número de instâncias em um quando a carga média da CPU é maior que 60% por um período superior a 5 minutos. Se, em seguida, a carga média da CPU cair abaixo de 30% em um período superior a 5 minutos, as instâncias serão reduzidas horizontalmente em uma instância:

```azurepowershell-interactive
# Define your scale set information
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
$myScaleSetId = (Get-AzureRmVmss -ResourceGroupName $myResourceGroup -VMScaleSetName $myScaleSet).Id 

# Create a scale up rule to increase the number instances after 60% average CPU usage exceeded for a 5-minute period
$myRuleScaleUp = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator GreaterThan `
  -MetricStatistic Average `
  -Threshold 60 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Increase `
  -ScaleActionValue 1

# Create a scale down rule to decrease the number of instances after 30% average CPU usage over a 5-minute period
$myRuleScaleDown = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId $myScaleSetId `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  -ScaleActionValue 1

# Create a scale profile with your scale up and scale down rules
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleUp,$myRuleScaleDown `
  -Name "autoprofile"

# Apply the autoscale rules
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId $myScaleSetId `
  -AutoscaleProfiles $myScaleProfile
```

Para obter mais informações de design sobre o uso do dimensionamento automático, consulte [Melhores práticas de dimensionamento automático](/azure/architecture/best-practices/auto-scaling).


## <a name="next-steps"></a>Próximas etapas
Neste tutorial, você criou um conjunto de dimensionamento de máquinas virtuais. Você aprendeu como:

> [!div class="checklist"]
> * Usar a Extensão de Script Personalizado para definir um site do IIS para dimensionar
> * Criar um balanceador de carga para o conjunto de dimensionamento
> * Criar um conjunto de dimensionamento de máquinas virtuais
> * Aumentar ou diminuir o número de instâncias em um conjunto de dimensionamento
> * Criar regras de dimensionamento automático

Avança para o próximo tutorial para saber mais sobre conceitos de máquinas virtuais de balanceamento de carga.

> [!div class="nextstepaction"]
> [Balancear carga de máquinas virtuais](tutorial-load-balancer.md)
