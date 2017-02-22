---
title: "Criar alertas para os serviços do Azure - CLI entre plataformas | Microsoft Docs"
description: "Dispare emails, notificações, chame URLs de sites (webhooks) ou automação quando as condições especificadas forem atendidas."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 5c6a2d27-7dcc-4f89-8752-9bb31b05ff35
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 8c9c9dea1248205aa6303e11e1166d5d38786c1b
ms.openlocfilehash: 073075d4c789438cc6dd6aa14027cbe50d6efa11


---
# <a name="create-alerts-in-azure-monitor-for-azure-services---cross-platform-cli"></a>Criar alertas no Azure Monitor para serviços do Azure - CLI entre plataformas
> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>

## <a name="overview"></a>Visão geral
Este artigo mostra como configurar alertas do Azure usando a CLI (Interface de linha de comando) entre plataformas.

> [!NOTE]
> O Azure Monitor é o novo nome do que era chamado "Azure Insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos a seguir ainda contêm os “insights”.
>
>

Você pode receber um alerta com base em métricas de monitoramento ou em eventos nos serviços do Azure.

* **Valores da métrica** - o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e posteriormente, quando essa condição não está sendo mais atendida.    
* **Eventos do log de atividades** - um alerta pode disparar em *cada* evento ou somente quando ocorre determinado número de eventos.

Você pode configurar um alerta para fazer o seguinte quando ele dispara:

* enviar um email para o administrador de serviços e os coadministradores
* enviar email para outros emails que você especificar.
* chamar um webhook
* iniciar a execução de um runbook do Azure (apenas no portal do Azure no momento)

Você pode configurar e obter informações sobre o uso de regras de alerta

* [Portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI (Interface de linha de comando)](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Você sempre pode receber ajuda sobre os comandos digitando um comando e colocando -help no final. Por exemplo:

    ```console
    azure insights alerts -help
    azure insights alerts actions email create -help
    ```

## <a name="create-alert-rules-using-the-cli"></a>Criar regras de alerta usando a CLI
1. Conclua os pré-requisitos e entre no Azure. Consulte [Exemplos de CLI do Azure Monitor](insights-cli-samples.md). Em resumo, instale a CLI e execute os comandos. Eles conectam você, mostram qual assinatura está usando e preparam para executar comandos do Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

1. Para listar as regras existentes em um grupo de recursos, use o seguinte formato **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
2. Para criar uma regra, primeiro precisa ter várias informações importantes.

   * A **ID de recurso** para o recurso que deve ter um alerta
   * As **definições de métricas** disponíveis para esse recurso

     Uma maneira de obter a ID de recurso é usar o portal do Azure. Supondo que o recurso já foi criado, selecione-o no portal. Na próxima folha, selecione *Propriedades* na seção *Configurações*. A *ID DE RECURSO* é um campo na folha seguinte. Outra maneira é usar o [Azure Resource Manager](https://resources.azure.com/).

     Um exemplo de ID de recurso para um aplicativo Web é

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Para obter uma lista de unidades e métricas disponíveis para essas métricas para o exemplo de recurso anterior, use o seguinte comando CLI:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* é a granularidade da medição disponível (intervalos de&1; minuto). O uso de diferentes granularidades oferece opções diferentes de métrica.
3. Para criar uma regra de alerta com base em métrica, use um comando da seguinte forma:

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    O exemplo a seguir configura um alerta em um recurso de site da Web. O alerta dispara sempre que ele recebe tráfego por cinco minutos de forma consistente e novamente quando ele não recebe tráfego por cinco minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
4. Para criar o webhook ou enviar email quando um alerta é disparado, primeiro crie o email e/ou os webhooks. Crie a regra imediatamente a seguir. Você não pode associar webhook ou emails a regras já criadas usando a CLI.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```
5. Para criar um alerta que é disparado em uma condição específica no log de atividades, use a forma:

    **insights alerts rule log set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;operationName&gt;*

    Por exemplo,

    ```console
    azure insights alerts rule log set myActivityLogRule eastus myresourceGroupName Microsoft.Storage/storageAccounts/listKeys/action
    ```

    O operationName corresponde a um tipo de evento para uma entrada no log de atividades. Os exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    Você pode usar o comando [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) do PowerShell para obter uma lista de possíveis operationNames. Como alternativa, você pode usar o portal do Azure para consultar o log de atividades e localizar operações anteriores específicas para as quais deseja criar um alerta. As operações mostradas na exibição de log de gráfico dos nomes amigáveis. Procure a entrada no JSON e extraia o valor de OperationName.   
6. Você pode verificar se os alertas foram criados corretamente examinando uma regra individual.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Para excluir regras, use um comando com esta forma:

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Esses comandos excluem as regras criadas anteriormente neste artigo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md) , incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks da Automação do Azure](../automation/automation-starting-a-runbook.md).
* Tenha uma [visão geral da coleta de logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas detalhadas de alta frequência em seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.



<!--HONumber=Jan17_HO5-->


