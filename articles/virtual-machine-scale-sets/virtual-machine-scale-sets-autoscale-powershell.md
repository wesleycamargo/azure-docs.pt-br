---
title: "Dimensionamento automático de conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell | Microsoft Docs"
description: "Como criar regras de dimensionamento automático para conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell"
services: virtual-machine-scale-sets
documentationcenter: 
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 88886cad-a2f0-46bc-8b58-32ac2189fc93
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: iainfou
ms.openlocfilehash: 1fbfbbc79a415af5e874c304412854849e134eb7
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de dimensionamento automático permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo.

Este artigo mostra como criar regras de dimensionamento automático com o Azure PowerShell que monitoram o desempenho das instâncias de VM no seu conjunto de dimensionamento. Essas regras de dimensionamento automático aumentam ou diminuem o número de instâncias de VM em resposta a essas métricas de desempenho. Também é possível executar essas etapas com a [CLI do Azure 2.0](virtual-machine-scale-sets-autoscale-cli.md) ou no [Portal do Azure](virtual-machine-scale-sets-autoscale-portal.md).


## <a name="prerequisites"></a>Pré-requisitos
Para criar regras de dimensionamento automático, você precisará de um conjunto de dimensionamento de máquinas virtuais existente. É possível criar um conjunto de dimensionamento com o [Portal do Azure](virtual-machine-scale-sets-portal-create.md), o [Azure PowerShell](virtual-machine-scale-sets-create.md#create-from-powershell) ou a [CLI do Azure 2.0](virtual-machine-scale-sets-create.md#create-from-azure-cli).

Para facilitar a criação de regras de dimensionamento automático, defina algumas variáveis para o conjunto de dimensionamento. O exemplo a seguir define variáveis para o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos denominado *myResourceGroup* e na região *Leste dos EUA*. Sua ID de assinatura é obtida com [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se você tiver várias assinaturas associadas à sua conta, somente a primeira será retornada. Ajuste os nomes e a ID da assinatura da seguinte maneira:

```powershell
$mySubscriptionId = (Get-AzureRmSubscription).Id
$myResourceGroup = "myResourceGroupScaleSet"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```


## <a name="create-a-rule-to-automatically-scale-out"></a>Crie uma regra para expandir automaticamente
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que aumenta o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for maior que 70% por um período de 5 minutos. Quando a regra dispara, o número de instâncias de VM aumentará em 20%. Em conjuntos de dimensionamento com um pequeno número de instâncias de VM, você poderia remover `-ScaleActionScaleType` e especificar apenas `-ScaleActionValue` para aumentar em *1* ou *2* instâncias. Em conjuntos de dimensionamento com um grande número de instâncias de VM, um aumento de 10% ou 20% de instâncias de VM pode ser mais apropriado.

Os seguintes parâmetros são usados para essa regra:

| Parâmetro               | Explicação                                                                                                         | Valor          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU |
| *-TimeGrain*            | Com que frequência as métricas são coletadas para análise.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média        |
| *-TimeWindow*           | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 10 minutos      |
| *-Operator*             | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que   |
| *-Threshold*            | O valor que faz com que a regra de dimensionamento automático dispare uma ação.                                                      | 70%            |
| *-ScaleActionDirection* | Define se o conjunto de dimensionamento deve ser dimensionado expandido ou reduzido quando a regra se aplica.                                             | Aumento       |
| *–ScaleActionScaleType* | Indica que o número de instâncias de VM que deve ser modificado por um valor de percentual.                                 | Porcentagem de modificação |
| *-ScaleActionValue*     | O percentual de instâncias de VM que deve ser alterado quando a regra disparar.                                            | 20             |
| *-ScaleActionCooldown*  | O tempo de espera antes da regra ser aplicada novamente para que as ações de dimensionamento automático tenham tempo para entrar em vigor. | 5 minutos      |

O exemplo a seguir cria um objeto chamado *myRuleScaleOut* que contém essa regra de expansão. O *-MetricResourceId* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```powershell
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic Average `
  -TimeWindow 00:10:00 `
  -Operator GreaterThan `
  -Threshold 70 `
  -ScaleActionDirection Increase `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-automatically-scale-in"></a>Criar uma regra para reduzir automaticamente
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

Crie uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que diminui o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for menor que 30% por um período de 10 minutos. Quando a regra disparar, o número de instâncias de VM é reduzido em 20%. O exemplo a seguir cria um objeto chamado *myRuleScaleDown* que contém essa regra de expansão. O *-MetricResourceId* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```powershell
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator LessThan `
  -MetricStatistic Average `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:10:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection Decrease `
  –ScaleActionScaleType PercentChangeCount `
  -ScaleActionValue 20
```


## <a name="define-an-autoscale-profile"></a>Definir um perfil de dimensionamento automático
Crie um perfil para associar as regras de dimensionamento automático a um conjunto de dimensionamento. O perfil de dimensionamento automático define a capacidade padrão, mínima e máxima do conjunto de dimensionamento e associa as regras de dimensionamento automático. Crie um perfil de dimensionamento automático com [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). O exemplo a seguir define a capacidade padrão e mínima de *2* instâncias de VM, bem como um máximo de *10*. As regras de expansão e redução criadas nas etapas anteriores são então anexadas:

```powershell
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de dimensionamento automático a um conjunto de dimensionamento
A etapa final é aplicar o perfil de dimensionamento automático ao seu conjunto de dimensionamento. Sua escala poderá ser expandida ou reduzida automaticamente com base na demanda de aplicativo. Aplique o perfil de dimensionamento automático com [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) da seguinte maneira:

```powershell
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="monitor-number-of-instances-in-a-scale-set"></a>Monitorar o número de instâncias em um conjunto de dimensionamento
Para ver o número e o status de instâncias de VM, exiba uma lista de instâncias no seu conjunto de dimensionamento com [Get-AzureRmVmssVM](/powershell/module/AzureRM.Compute/Get-AzureRmVmssVM). O status indica se a instância VM é provisionada à medida que o conjunto de dimensionamento é expandido automaticamente ou desprovisionada à medida que a escala é reduzida automaticamente. O exemplo a seguir exibe o status da instância VM para o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos denominado *myResourceGroup*:

```powershell
Get-AzureRmVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="autoscale-based-on-a-schedule"></a>Dimensionamento automático baseado em agendamento
Os exemplos anteriores dimensionaram automaticamente um conjunto de dimensionamento com métricas de host básicas como uso da CPU. Também é possível criar regras de dimensionamento automático baseadas em agendamentos. Essas regras baseadas em agendamento permitem expandir automaticamente o número de instâncias de VM antes de um aumento previsto na demanda do aplicativo, como horas de trabalho principal e, depois, reduz automaticamente o número de instâncias no momento em que uma demanda menor for prevista, como durante o fim de semana.

Para criar regras de dimensionamento automático com base em agendamento em vez de métricas do host, use o Portal do Azure. No momento, não é possível criar regras com base em agendamento com o Azure PowerShell.


## <a name="next-steps"></a>Próximas etapas
Neste artigo, você aprendeu a usar as regras de dimensionamento automático para escalar horizontalmente e expandir ou diminuir o *número* de instâncias de VM no seu conjunto de dimensionamento. Também é possível escalar verticalmente para aumentar ou diminuir o *tamanho* da instância VM. Para obter mais informações, consulte [Dimensionamento vertical automático com conjuntos de Dimensionamento de Máquinas Virtuais](virtual-machine-scale-sets-vertical-scale-reprovision.md).

Para obter informações sobre como gerenciar suas instâncias de VM, consulte [Gerenciar conjuntos de dimensionamento de máquinas virtuais com o Azure PowerShell](virtual-machine-scale-sets-windows-manage.md).

Para saber como gerar alertas quando suas regras de dimensionamento automático forem disparadas, consulte [Usar ações de dimensionamento automático para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-to-webhook-email.md). Também é possível [Usar logs de auditoria para enviar notificações de alerta por email e webhook no Azure Monitor](../monitoring-and-diagnostics/insights-auditlog-to-webhook-email.md).
