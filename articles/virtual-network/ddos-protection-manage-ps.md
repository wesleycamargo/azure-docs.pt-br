---
title: "Gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell | Microsoft Docs"
description: "Saiba como gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 33ff6cfcacd1632dc49b448e70361e1cb2ce1176
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/19/2018
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Gerenciar a Proteção contra DDoS do Azure Standard usando o Azure PowerShell

Saiba como habilitar e desabilitar a proteção contra DDoS (ataque de negação de serviço distribuído) e usar a telemetria para atenuar ataques contra DDoS com a Proteção contra DDoS Standard do Azure. A Proteção contra DDoS Standard protege os recursos do Azure como máquinas virtuais, balanceadores de carga e gateways de aplicativo que têm um [endereço IP público](virtual-network-public-ip-address.md) do Azure atribuído a eles. Para saber mais sobre a Proteção contra DDoS Standard e suas funcionalidades, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md). 

>[!IMPORTANT]
>A Proteção contra DDoS do Azure Standard (Proteção contra DDoS) está atualmente em versão prévia. Um número limitado de recursos do Azure dá suporte à Proteção contra DDoS e ela está disponível apenas em um número seleto de regiões. Para obter uma lista de regiões disponíveis, consulte [Visão geral da Proteção contra DDoS Standard](ddos-protection-overview.md). Você precisa [registrar-se para o serviço](http://aka.ms/ddosprotection) durante a versão prévia limitada para habilitar a Proteção contra DDoS para a sua assinatura. Após o registro, você é contatado pela equipe do DDoS do Azure, que o orientará pelo processo de habilitação.


## <a name="log-in-to-azure"></a>Fazer logon no Azure

Faça logon na sua assinatura do Azure com o comando `Login-AzureRmAccount` e siga as instruções na tela. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar. Se você precisa instalar ou atualizar o Azure PowerShell, confira [Instalar o módulo do Azure PowerShell](/powershell/azure/install-azurerm-ps).

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection-standard---new-virtual-network"></a>Habilitar a Proteção contra DDoS Standard – nova rede virtual

Para criar uma rede virtual com a Proteção contra DDoS habilitada, execute o exemplo a seguir:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -EnableDDoSProtection
```

Esse exemplo cria uma rede virtual com duas sub-redes e dois servidores DNS. O efeito de especificar os servidores DNS na rede virtual é que as NICs/VMs que são implantadas na rede virtual herdam esses servidores DNS como padrões. A Proteção contra DDoS é habilitada para todos os recursos protegidos na rede virtual.

> [!WARNING]
> Ao selecionar uma região, escolha uma região com suporte na lista em [Visão geral da Proteção contra DDoS Standard do Azure](ddos-protection-overview.md).

## <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Habilitar a Proteção contra DDoS em uma rede virtual existente

Para habilitar a Proteção contra DDoS em uma rede virtual existente, execute o exemplo a seguir:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks -Force
```

> [!WARNING]
> A rede virtual deve existir em uma região com suporte. Para obter uma lista de regiões com suporte, consulte [Visão geral da Proteção contra DDoS Standard do Azure](ddos-protection-overview.md).

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Desabilitar a Proteção contra DDoS em uma rede virtual

Para desabilitar a Proteção contra DDoS em uma rede virtual existente, execute o exemplo a seguir:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks" -Force
```

## <a name="review-the-ddos-protection-status-of-a-virtual-network"></a>Examinar o status da proteção contra DDoS de uma rede virtual 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Usar a telemetria da Proteção contra DDoS

A telemetria de um ataque é fornecida por meio do Azure Monitor em tempo real. A telemetria está disponível somente durante o tempo pelo qual um endereço IP público está sob mitigação. Você não verá telemetria antes nem depois de um ataque ser mitigado.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas em métricas de Proteção contra DDoS

Aproveitando a configuração de alerta do Azure Monitor, você pode selecionar qualquer uma das métricas de Proteção contra DDoS disponíveis para alertar quando há uma mitigação ativa durante um ataque.

#### <a name="configure-email-alert-rules-via-azure"></a>Configurar regras de alerta de email por meio do Azure

1. Obter uma lista das inscrições disponíveis. Verifique se você está trabalhando com a assinatura correta. Se não estiver, defina-a para a correta usando a saída de Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Para listar as regras existentes em um grupo de recursos, use o seguinte comando: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Para criar uma regra, você precisa ter as seguintes informações primeiro: 

    - A ID de Recurso do recurso para o qual você quer definir um alerta.
    - As definições de métricas disponíveis para esse recurso. Uma maneira de obter a ID de recurso é usar o portal do Azure. Supondo que o recurso já foi criado, selecione-o no Portal do Azure. Na próxima página, selecione *Propriedades* na seção *Configurações*. A **ID DE RECURSO** é um campo na página seguinte. Outra maneira é usar o [Azure Resource Manager](https://resources.azure.com/). Um exemplo de ID de Recurso para um IP público é: `/subscriptions/<Id>/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    O exemplo a seguir cria um alerta para um endereço IP público associado a um recurso em uma rede virtual. A métrica para a qual criar um alerta é **Sob ataque de DDoS ou não**. Esse é um valor booliano 1 ou 0. Um **1** significa que você está sob ataque. Um **0** significa que você não está sob ataque. O alerta é criado quando um ataque é iniciado nos últimos 5 minutos.

    Para criar um webhook ou enviar email quando um alerta é criado, primeiro crie o email e/ou o webhook. Depois de criar o email ou o webhook, crie a regra imediatamente com a marcação `-Actions`, conforme mostrado no exemplo a seguir. Não é possível associar o webhook ou emails a regras existentes usando o PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/<Id>/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail -Description "Under DDoS Attack"
    ```

4. Verifique se o alerta foi criado corretamente observando a regra:

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    ```

Você também pode aprender mais sobre [configuração de webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md?toc=%2fazure%2fvirtual-network%2ftoc.json) e [aplicativos lógicos](../logic-apps/logic-apps-overview.md) para criação de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar o registro em log para métricas de Proteção contra DDoS

Consulte as [amostras de início rápido do PowerShell](../monitoring-and-diagnostics/insights-powershell-samples.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para ajudá-lo a acessar e configurar o log de diagnósticos do Azure por meio do PowerShell.

## <a name="next-steps"></a>Próximas etapas

- [Saiba mais sobre os Logs de Diagnóstico do Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Analisar logs do Armazenamento do Azure com o Log Analytics](../log-analytics/log-analytics-azure-storage.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md?toc=%2fazure%2fvirtual-network%2ftoc.json)