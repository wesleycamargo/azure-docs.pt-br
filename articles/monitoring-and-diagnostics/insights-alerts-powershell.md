---
title: "Usar o PowerShell para criar alertas para os serviços do Azure | Microsoft Docs"
description: "Use o PowerShell para criar alertas do Azure, que podem disparar notificações ou automação quando as condições especificadas são atendidas."
author: rboucher
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: d26ab15b-7b7e-42a9-81c8-3ce9ead5d252
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: db8ed8980335e2af9654bfe56b4e4c5807674040


---
# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Usar o PowerShell para criar alertas para os serviços do Azure
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
> 
> 

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do Azure usando PowerShell.  

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores da métrica** - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.    
* **Eventos do log de atividades** - um alerta pode disparar em *cada* evento ou somente quando ocorre determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* enviar um email para o administrador de serviços e os coadministradores
* enviar email para outros emails que você especificar.
* chamar um webhook
* iniciar a execução de um runbook do Azure (apenas no Portal do Azure)

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI (Interface de linha de comando)](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Para saber mais, digite ```get-help``` e depois o comando do PowerShell sobre o qual você deseja obter ajuda.

## <a name="create-alert-rules-in-powershell"></a>Criar regras de alerta no PowerShell
1. Fazer logon no Azure.   
   
    ```PowerShell
    Login-AzureRmAccount
   
    ```
2. Obter uma lista das inscrições disponíveis. Verifique se você está trabalhando com a assinatura correta. Se não estiver, defina a correta usando a saída de `Get-AzureRmSubscription`.
   
    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```
3. Para listar as regras existentes em um grupo de recursos, use o seguinte comando:
   
   ```PowerShell
   Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
   ```
4. Para criar uma regra, primeiro precisa ter várias informações importantes. 
   
   * A **ID de recurso** para o recurso que deve ter um alerta
   * As **definições de métricas** disponíveis para esse recurso
     
     Uma maneira de obter a ID de recurso é usar o portal do Azure. Supondo que o recurso já foi criado, selecione-o no portal. Na próxima folha, selecione *Propriedades* na seção *Configurações*. A ID DE RECURSO é um campo na folha seguinte. Outra maneira é usar o [Azure Resource Explorer](https://resources.azure.com/).
     
     Um exemplo de ID de recurso para um aplicativo Web é
     
     ```
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```
     
     Você pode usar `Get-AzureRmMetricDefinition` para exibir a lista de todas as definições de métrica para um recurso específico.
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id>
     ```
     
     O exemplo a seguir gera uma tabela com o nome de métrica e a unidade dessa métrica.
     
     ```PowerShell
     Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
     
     ```
     Uma lista completa das opções disponíveis para Get-AzureRmMetricDefinition está disponível executando Get-MetricDefinitions.
5. O exemplo a seguir configura um alerta em um recurso de site da Web. O alerta dispara sempre que ele recebe tráfego por cinco minutos de forma consistente e novamente quando ele não recebe tráfego por cinco minutos.
   
    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"
   
    ```
6. Para criar o webhook ou enviar email quando um alerta é disparado, primeiro crie o email e/ou os webhooks. Em seguida, crie imediatamente a regra com a marca -Actions,e conforme mostra o exemplo a seguir. Você não pode associar o webhook ou emails a regras já criadas usando o PowerShell.

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


1. Para criar um alerta disparado em uma condição específica no log de atividades, use comandos da seguinte forma
   
    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken
   
    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```
   
    O -OperationName corresponde a um tipo de evento no log de atividades. Os exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.
   
    Você pode usar o comando [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) do PowerShell para obter uma lista de possíveis operationNames. Como alternativa, você pode usar o portal do Azure para consultar o log de atividades e localizar operações anteriores específicas para as quais deseja criar um alerta. As operações mostradas na exibição de log de gráfico dos nomes amigáveis. Procure a entrada no JSON e extraia o valor de OperationName.   
2. Verifique se os alertas foram criados corretamente examinando regras individuais.
   
    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput
   
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```
3. Excluir seus alertas. Esses comandos excluem as regras criadas anteriormente neste artigo.
   
    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks da Automação do Azure](../automation/automation-starting-a-runbook.md).
* Tenha uma [visão geral da coleta de logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.




<!--HONumber=Nov16_HO3-->


