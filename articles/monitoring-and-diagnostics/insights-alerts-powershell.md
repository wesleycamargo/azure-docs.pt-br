---
title: Use o PowerShell para criar alertas clássicos para serviços do Azure | Microsoft Docs
description: Dispare emails ou notificações, ou chame URLs de sites (webhooks) ou automação quando as condições especificadas forem atendidas.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/28/2018
ms.author: robb
ms.component: alerts
ms.openlocfilehash: b08a8f66add45d64085ac05605fe3c7d7f91b705
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286192"
---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usar o PowerShell para criar alertas para os serviços do Azure
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte a [alertas de métrica mais novos e melhores](monitoring-near-real-time-metric-alerts.md). Esses alertas podem monitorar várias métricas e permitir alertas em métricas dimensionais. O suporte do PowerShell para alertas de métrica mais recentes estará disponível em breve.
>
>

Este artigo mostra como configurar alertas de métrica clássico do Azure usando PowerShell.  

Você pode receber um alerta com base nas métricas dos serviços do Azure ou receber alertas de eventos que ocorrem no Azure.

* **Valores da métrica**: o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e, em seguida, quando essa condição não é mais atendida.    
* **Eventos do log de atividades**: um alerta pode disparar em *cada* evento ou quando determinados eventos ocorrem. Para saber mais sobre alertas do log de atividades, consulte [Criar alertas do log de atividades (clássico)](monitoring-activity-log-alerts.md).

É possível configurar um alerta de métrica clássico para executar as seguintes tarefas quando acionado:

* Enviar notificações por email para o administrador e coadministradores de serviços.
* Envie um email para endereços de email adicionais que você especificar.
* Chamar um webhook.
* Iniciar a execução de um runbook do Azure (apenas no Portal do Azure).

É possível configurar e obter informações sobre regras de alerta nos locais a seguir: 

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI (interface de linha de comando) do Azure](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Para obter informações adicionais, você sempre poderá digitar ```Get-Help``` seguido pelo comando do PowerShell sobre o qual deseja obter ajuda.

## <a name="create-alert-rules-in-powershell"></a>Criar regras de alerta no PowerShell
1. Entre no Azure.   

    ```PowerShell
    Connect-AzureRmAccount

    ```
2. Obtenha uma lista das assinaturas disponíveis. Verifique se você está trabalhando com a assinatura correta. Se não, obtenha a assinatura correta usando a saída de `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Listar regras existentes em um grupo de recursos usando o comando a seguir:

   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Para criar uma regra, primeiro precisa ter várias informações importantes.

    - A **ID do recurso** para o qual você quer definir um alerta.
    - As **definições de métricas** que estão disponíveis para esse recurso.

     Uma maneira de obter a ID de recurso é usar o portal do Azure. Supondo que o recurso já esteja criado, selecione-o no portal. Em seguida, na próxima folha, na seção **Configurações**, selecione **Propriedades**. A **ID DE RECURSO** é um campo na folha seguinte. 
     
     Também é possível obter a ID do recurso usando o [Azure Resource Explorer](https://resources.azure.com/).

     A seguir, um exemplo de ID do recurso para um aplicativo Web:

     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Você pode usar `Get-AzureRmMetricDefinition` para exibir a lista de todas as definições de métrica para um recurso específico:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```

     O exemplo a seguir gera uma tabela com o nome de métrica e a unidade dessa métrica:

     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

     ```
     Para obter uma lista completa das opções disponíveis para Get-AzureRmMetricDefinition, execute `Get-Help Get-AzureRmMetricDefinition -Detailed`.
5. O exemplo a seguir configura um alerta em um recurso do site. O alerta dispara sempre que ele recebe tráfego por cinco minutos de forma consistente e novamente quando ele não recebe tráfego por cinco minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```
6. Para criar um webhook ou enviar um email quando um alerta for acionado, primeiro crie o email ou o webhook. Em seguida, crie imediatamente a regra com a marca -Actions, conforme mostrado no exemplo a seguir. Não é possível associar webhooks ou emails com regras que já foram criadas.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```

7. Observe as regras individuais para verificar se os alertas foram criados corretamente.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
8. Excluir seus alertas. Esses comandos excluem as regras criadas anteriormente neste artigo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md), incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba como [configurar alertas nos eventos do Log de Atividades](monitoring-activity-log-alerts.md).
* Saiba mais sobre [runbooks da Automação do Azure](../automation/automation-starting-a-runbook.md).
* Tenha uma [visão geral da coleta de logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
