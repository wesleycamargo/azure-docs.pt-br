---
title: Tutorial – Dimensionar automaticamente um conjunto de dimensionamento com o Azure PowerShell | Microsoft Docs
description: Saiba como dimensionar automaticamente um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell conforme a demanda de CPU aumenta e diminui
services: virtual-machine-scale-sets
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machine-scale-sets
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 128f3d366dc7de0870bc6f52ae6d0bbaf3a0fcb3
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2018
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Tutorial: Dimensionamento automático de um conjunto de dimensionamento de máquinas virtuais com o Azure PowerShell
Ao criar um conjunto de dimensionamento, o número de instâncias de VM que você deseja executar é definido. À medida que seu aplicativo precisar de alterações, você poderá aumentar ou diminuir automaticamente o número de instâncias de VM. A capacidade de autoescala permite acompanhar a demanda do cliente ou reagir a alterações de desempenho do aplicativo durante todo o ciclo de vida do aplicativo. Neste tutorial, você aprenderá a:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionamento automático com a redução da demanda

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se você optar por instalar e usar o PowerShell localmente, este tutorial exigirá o módulo do Azure PowerShell versão 5.6.0 ou posterior. Execute `Get-Module -ListAvailable AzureRM` para encontrar a versão. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps). Se você estiver executando o PowerShell localmente, também precisará executar o `Login-AzureRmAccount` para criar uma conexão com o Azure.


## <a name="create-a-scale-set"></a>Criar um conjunto de escala
Para facilitar a criação de regras de autoescala, defina algumas variáveis para o conjunto de dimensionamento. O exemplo a seguir define variáveis para o conjunto de dimensionamento chamado *myScaleSet* no grupo de recursos denominado *myResourceGroup* e na região *Leste dos EUA*. Sua ID de assinatura é obtida com [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription). Se você tiver várias assinaturas associadas à sua conta, somente a primeira será retornada. Ajuste os nomes e a ID da assinatura da seguinte maneira:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Defina o nome de usuário e a senha de um administrador para as instâncias de VM com [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```

Agora, crie um conjunto de dimensionamento de máquinas virtuais com [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Para distribuir o tráfego para as instâncias de VM individuais, um balanceador de carga também é criado. O balanceador de carga inclui regras para distribuir o tráfego na porta TCP 80, além de permitir o tráfego de área de trabalho remota na porta TCP 3389 e comunicação remota do PowerShell na porta TCP 5985:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer" `
  -Credential $cred
```

Leva alguns minutos para criar e configurar todos os recursos e as VMs do conjunto de dimensionamento.

## <a name="create-a-rule-to-autoscale-out"></a>Criar uma regra de dimensionamento automático para aumento
Se a demanda do aplicativo aumentar, a carga em instâncias de VM no seu conjunto de dimensionamento também aumentará. Se esse aumento de carga for consistente, em vez de apenas uma demanda breve, configure as regras de dimensionamento automático para aumentar o número de instâncias de VM no conjunto de dimensionamento. Quando essas instâncias de VM forem criadas e os aplicativos implantados, o conjunto de dimensionamento começará a distribuir o tráfego para eles por meio do balanceador de carga. Você controla quais métricas são monitoradas, como CPU ou disco, por quanto tempo a carga do aplicativo deve atender a determinado limite e quantas instâncias de VM devem ser adicionadas ao conjunto de dimensionamento.

Vamos criar uma regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que aumenta o número de instâncias de VM em um conjunto de dimensionamento definido quando a carga da CPU média for maior que 70% por um período de 5 minutos. Quando a regra é disparada, o número de instâncias de VM aumenta em 20%.

Os seguintes parâmetros são usados para essa regra:

| Parâmetro               | Explicação                                                                                                         | Valor          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-MetricName*           | A métrica de desempenho a ser monitorada, na qual as ações do conjunto de dimensionamento serão aplicadas.                                                   | Porcentagem de CPU |
| *-TimeGrain*            | Com que frequência as métricas são coletadas para análise.                                                                   | 1 minuto       |
| *-MetricStatistic*      | Define como as métricas coletadas devem ser agregadas para análise.                                                | Média        |
| *-TimeWindow*           | O tempo monitorado antes de comparar os valores de métrica e de limite.                                   | 5 minutos      |
| *-Operator*             | Operador usado para comparar os dados da métrica com o limite.                                                     | Maior que   |
| *-Threshold*            | O valor que faz com que a regra de autoescala dispare uma ação.                                                      | 70%            |
| *-ScaleActionDirection* | Define se o conjunto de dimensionamento deve ser dimensionado expandido ou reduzido quando a regra se aplica.                                             | Aumento       |
| *–ScaleActionScaleType* | Indica que o número de instâncias de VM deve ser modificado por um valor específico.                                    | Alterar contagem   |
| *-ScaleActionValue*     | O percentual de instâncias de VM que deve ser alterado quando a regra disparar.                                            | 3              |
| *-ScaleActionCooldown*  | O tempo de espera antes da regra ser aplicada novamente para que as ações de autoescala tenham tempo para entrar em vigor. | 5 minutos      |

O exemplo a seguir cria um objeto chamado *myRuleScaleOut* que contém essa regra de expansão. O *-MetricResourceId* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Criar uma regra de dimensionamento automático para redução
À noite ou durante o fim de semana, a demanda do seu aplicativo pode diminuir. Se essa diminuição de carga for consistente durante determinado período, configure as regras de dimensionamento automático para reduzir o número de instâncias de VM no conjunto de dimensionamento. Esta ação de redução diminui o custo para executar seu conjunto de dimensionamento, visto que apenas o número de instâncias necessárias para atender à demanda atual é executado.

Crie outra regra com [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) que diminui o número de instâncias de VM em um conjunto de dimensionamento quando a carga da CPU média for menor que 30% por um período de 5 minutos. Quando a regra disparar, o número de instâncias de VM é reduzido em um. O exemplo a seguir cria um objeto chamado *myRuleScaleDown* que contém essa regra de expansão. O *-MetricResourceId* usa as variáveis definidas anteriormente para a ID de assinatura, o nome do grupo de recursos e o nome do conjunto de dimensionamento:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  –ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definir um perfil de autoescala
Crie um perfil para associar as regras de dimensionamento automático a um conjunto de dimensionamento. O perfil de dimensionamento automático define a capacidade padrão, mínima e máxima do conjunto de dimensionamento e associa as regras de dimensionamento automático. Crie um perfil de dimensionamento automático com [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). O exemplo a seguir define a capacidade padrão e mínima de *2* instâncias de VM, bem como um máximo de *10*. As regras de expansão e redução criadas nas etapas anteriores são então anexadas:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rules $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-rules-to-a-scale-set"></a>Aplicar regras de dimensionamento automático a um conjunto de dimensionamento
A etapa final é aplicar o perfil de dimensionamento automático ao seu conjunto de dimensionamento. O conjunto de dimensionamento poderá ser expandido ou reduzido automaticamente com base na demanda do aplicativo. Aplique o perfil de dimensionamento automático com [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting) da seguinte maneira:

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfiles $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Gerar carga de CPU no conjunto de dimensionamento
Para testar as regras de dimensionamento automático, gere carga da CPU nas instâncias de VM no conjunto de dimensionamento. Essa carga de CPU simulada faz com que as regras de dimensionamento automático escalem horizontalmente e aumentem o número de instâncias de VM. Conforme a carga simulada de CPU vai sendo reduzida, as regras de dimensionamento automático para redução diminuem o número de instâncias de VM.

Para listar as portas NAT que devem se conectar a instâncias de VM em um conjunto de dimensionamento, primeiro obtenha o objeto do balanceador de carga com [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Em seguida, exiba as regras NAT de entrada com [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

A saída de exemplo a seguir mostra o nome da instância, o endereço IP público do balanceador de carga e o número de porta para onde as regras de NAT encaminham o tráfego:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

O *Nome* da regra está de acordo com o nome da instância de VM conforme mostrado em um comando [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm) anterior. Por exemplo, para se conectar à instância de VM *0*, use *myRDPRule.0* e conecte-se à porta *50001*. Para se conectar à instância de VM *1*, use o valor de *myRDPRule.1* e conecte-se à porta *50002*.

Exiba o endereço IP público do balanceador de carga com [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Saída de exemplo:

```powershell
IpAddress
---------
52.168.121.216
```

Crie uma conexão remota para sua primeira instância de VM. Especifique seu próprio endereço IP público e número da porta da instância de VM necessária, conforme mostrado com os comandos anteriores. Quando solicitado, insira as credenciais usadas quando você criou o conjunto de dimensionamento (por padrão,nos comandos de exemplo eles são *azureuser* e  *P@ssw0rd!*). Se você usa o Azure Cloud Shell, execute esta etapa de um prompt do PowerShell local ou do Cliente Área de Trabalho Remota. O exemplo abaixo se conecta a uma instância de VM em *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Depois de conectado, abra o Internet Explorer na barra de tarefas.

- Selecione **OK** para aceitar o prompt de *Usar configurações de segurança, privacidade e compatibilidade recomendadas*
- Digite *http://download.sysinternals.com/files/CPUSTRES.zip* na barra de endereços.
- Como a Configuração de Segurança Reforçada do Internet Explorer está habilitada, escolha **Adicionar** o domínio *http://download.sysinternals.com* à lista de sites confiáveis.
- Quando for solicitado o download do arquivo, selecione **Abrir** e selecione e **Execute** a ferramenta *CPUSTRES. EXE*.

Para gerar alguma carga de CPU, marque duas caixas para os threads **Ativo**. No menu suspenso **Atividade** para ambos os threads, selecione *Máximo*. Você pode abrir o Gerenciador de Tarefas para confirmar se a carga da CPU na VM está a 100%.

![O utilitário Estresse da CPU gera carga na instância de VM](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Deixe a sessão de conexão da área de trabalho remota aberta e abra outra sessão de conexão da área de trabalho remota. Conecte-se à segunda instância de VM com o número da porta listado do cmdlet [AzureRmLoadBalancerInboundNatRuleConfig Get](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig) anterior:

```powershell
mstsc /v 52.168.121.216:50002
```

Depois de conectado à segunda instância de VM, repita as etapas anteriores para baixar e executar *CPUSTRES.EXE*. Mais uma vez, inicie dois threads **Ativo** e defina a atividade como *Máximo*.

Para permitir que a ferramenta **Estresse da CPU** continue em execução, deixe ambas as sessões de conexão da área de trabalho remota abertas.


## <a name="monitor-the-active-autoscale-rules"></a>Monitorar as regras de dimensionamento automático ativas
Para monitorar o número de instâncias de VM em seu conjunto de dimensionamento, use **while**. Demoram 5 minutos para o dimensionamento automático começar o processo de escala horizontal em resposta à carga da CPU gerada por **CPUStress* em cada uma das instâncias de VM:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Depois que o limite de CPU foi atingido, as regras de dimensionamento automático aumentam o número de instâncias de VM no conjunto de dimensionamento. A saída abaixo mostra três VMs criadas com o aumento de dimensionamento do conjunto de dimensionamento:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

Na sua sessão de conexão da área de trabalho remota para cada uma das instâncias de VM, feche a ferramenta **Estresse da CPU**. A carga da CPU média em todo o conjunto de dimensionamento volta ao normal. Após mais cinco minutos, as regras de dimensionamento automático reduzem o número de instâncias de VM. As ações de redução removem instâncias de VM começando pelas IDs mais altas. A saída de exemplo abaixo mostra uma instância de VM excluída pela redução do conjunto de dimensionamento:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Saia de *while* com `Ctrl-c`. O conjunto de dimensionamento continua a reduzir a cada cinco minutos e a remover uma instância de VM até que a contagem mínima de instâncias, duas, seja alcançada.


## <a name="clean-up-resources"></a>Limpar recursos
Para remover o conjunto de dimensionamento e os recursos adicionais, exclua o grupo de recursos e todos os seus recursos usando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). O parâmetro `-Force` confirma que você deseja excluir os recursos sem um prompt adicional para fazer isso. O parâmetro `-AsJob` retorna o controle ao prompt sem aguardar a conclusão da operação.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Próximas etapas
Neste tutorial você aprendeu a reduzir ou escalar aplicativos horizontal e automaticamente um conjunto de dimensionamento com o Azure PowerShell:

> [!div class="checklist"]
> * Usar o dimensionamento automático com um conjunto de dimensionamento
> * Criar e usar regras de dimensionamento automático
> * Testar instâncias de VM sob estresse e disparar regras de dimensionamento automático
> * Redimensionamento automático com a redução da demanda

Para obter mais exemplos de conjuntos de dimensionamento de máquinas virtuais em ação, consulte os seguintes scripts de exemplo do exemplo do Azure PowerShell:

> [!div class="nextstepaction"]
> [Exemplos de script de conjunto de dimensionamento para o Azure PowerShell](powershell-samples.md)