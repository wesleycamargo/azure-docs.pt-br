---
title: "Dimensionar verticalmente os conjunto de dimensionamento de máquinas virtuais do Azure | Microsoft Docs"
description: "Como dimensionar verticalmente uma máquina virtual em resposta a alertas de monitoramento com a Automação do Azure"
services: virtual-machine-scale-sets
documentationcenter: 
author: gatneil
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: negat
ms.openlocfilehash: 6e4733e023d1dc27fb099216f9afea07fe07446c
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>Dimensionamento automático vertical com conjuntos de dimensionamento de máquinas virtuais
Este artigo descreve como dimensionar verticalmente os [Conjuntos de Escala de Máquina Virtual](https://azure.microsoft.com/services/virtual-machine-scale-sets/) do Azure com ou sem reprovisionamento. Para dimensionar verticalmente as VMs que não estão em conjuntos de dimensionamento, consulte [Dimensionar verticalmente uma máquina virtual Linux do Azure com a Automação do Azure](../virtual-machines/windows/vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Dimensionamento vertical, também conhecido como *escalar verticalmente* e *reduzir verticalmente*, significa aumentar ou diminuir o tamanho da VM (máquina virtual) em resposta a uma carga de trabalho. Compare esse comportamento com o [dimensionamento horizontal](virtual-machine-scale-sets-autoscale-overview.md), também conhecido como *aumento* e *redução*, em que o número de VMs é alterado dependendo da carga de trabalho.

Reprovisionamento significa remover uma VM existente e substituí-la por uma nova. Ao aumentar ou diminuir o tamanho das VMs em um conjunto de dimensionamento de máquinas virtuais, em alguns casos, é possível redimensionar as VMs existentes e manter seus dados, enquanto em outros casos, você precisará implantar novas VMs com o novo tamanho. Este documento aborda ambos os casos.

O dimensionamento vertical pode ser útil quando:

* Um serviço baseado em máquinas virtuais é subutilizados (por exemplo, aos finais de semana). Reduzir o tamanho da VM pode reduzir os custos mensais.
* Aumentar o tamanho da VM para lidar com maior demanda sem criar VMs adicionais.

Você pode definir o dimensionamento vertical para ser disparado com base em alertas baseados em métricas do conjunto de dimensionamento de máquinas virtuais. Quando o alerta for ativado, ele acionará um webhook que disparará um runbook para dimensionar o conjunto de dimensionamento para mais ou para menos. É possível configurar o dimensionamento vertical seguindo estas etapas:

1. Crie uma conta de Automação do Azure com a funcionalidade executar como.
2. Importe runbooks de dimensionamento vertical da Automação do Azure para conjuntos de dimensionamento de máquinas virtuais em sua assinatura.
3. Adicione um webhook ao seu Runbook.
4. Adicione um alerta em seu conjunto de dimensionamento de máquinas virtuais usando uma notificação de webhook.

> [!NOTE]
> O dimensionamento automático vertical só pode ocorrer em determinadas faixas de tamanhos de VM. Compare as especificações de cada tamanho antes de decidir dimensionar de um para outro (o número mais alto nem sempre indica uma VM maior). Você pode optar por dimensionar entre os seguintes pares de tamanhos:
> 
> | Par de dimensionamento de tamanhos de VM |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>Criar uma Conta de Automação do Azure com a funcionalidade executar como
A primeira coisa que você precisa fazer é criar uma conta da Automação do Azure que hospede os runbooks usados para dimensionar as instâncias do conjunto de dimensionamento de máquinas virtuais. Recentemente, a [Automação do Azure](https://azure.microsoft.com/services/automation/) introduziu o recurso "Conta Executar como" que faz a configuração da entidade de serviço para executar automaticamente os runbooks em nome de um usuário. Para obter mais informações, confira:

* [Autenticar Runbooks com uma conta Executar como do Azure](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>Importar os Runbooks de Escala Vertical da Automação do Azure para sua assinatura
Os runbooks necessários para escalar verticalmente seus conjuntos de dimensionamento de máquinas virtuais já estão publicados na Galeria de Runbooks da Automação do Azure. Para importá-los para sua assinatura, siga as etapas neste artigo:

* [Galerias de runbook e de módulos para a Automação do Azure](../automation/automation-runbook-gallery.md)

Escolha a opção de Procurar na Galeria no menu Runbooks:

![Runbooks a serem importados][runbooks]

Os Runbooks que precisam ser importados são mostrados. Selecione o Runbook dependendo de se você quer dimensionar verticalmente com ou sem reprovisionamento:

![Galeria de Runbooks][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Adicionar um webhook ao seu Runbook
Depois de importar os runbooks, adicione um webhook no runbook para que ele possa ser acionado por um alerta de um conjunto de dimensionamento de máquinas virtuais. Os detalhes da criação de um webhook para seu Runbook são descritos neste artigo:

* [Webhooks da Automação do Azure](../automation/automation-webhooks.md)

> [!NOTE]
> Copie o URI do webhook antes de fechar a caixa de diálogo do webhook, pois você precisará desse endereço na próxima seção.
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>Adicionar um alerta ao conjunto de dimensionamento de máquinas virtuais
Abaixo está um script do PowerShell que mostra como adicionar um alerta a um conjunto de dimensionamento de máquinas virtuais. Consulte o seguinte artigo para obter o nome da métrica em que o alerta será acionado: [Métricas comuns de dimensionamento automático do Azure Monitor](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md).

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> É recomendável configurar uma janela de tempo razoável para o alerta para evitar disparar o dimensionamento vertical e qualquer interrupção de serviço associada com muita frequência. Considere uma janela de pelo menos 20 a 30 minutos ou mais. Considere usar o dimensionamento horizontal se você precisar evitar qualquer interrupção.
> 
> 

Para obter mais informações sobre como criar alertas, consulte os seguintes artigos:

* [Exemplos de início rápido do PowerShell do Azure Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Exemplos de início rápido da CLI multiplataforma do Azure Monitor](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>Resumo
Este artigo mostrou exemplos simples de dimensionamento vertical. Com essa base (conta de Automação, Runbooks, webhooks e alertas) você pode conectar uma grande variedade de eventos com um conjunto personalizado de ações.

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
