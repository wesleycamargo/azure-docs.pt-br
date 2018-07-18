---
title: Usar a CLI do Azure de plataforma cruzada para criar alertas clássicos para serviços do Azure | Microsoft Docs
description: Dispare emails ou notificações, ou chame URLs de sites (webhooks) ou automação quando as condições especificadas forem atendidas.
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/24/2016
ms.author: robb
ms.component: alerts
ms.openlocfilehash: 8112b868bc8d2ca2a9478d38ee702d8b3350d48e
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287083"
---
# <a name="use-the-cross-platform-azure-cli-to-create-classic-metric-alerts-in-azure-monitor-for-azure-services"></a>Usar a CLI do Azure de plataforma cruzada para criar alertas de métrica clássicos nos serviços do Azure Monitor para Azure 

> [!div class="op_single_selector"]
> * [Portal](insights-alerts-portal.md)
> * [PowerShell](insights-alerts-powershell.md)
> * [CLI](insights-alerts-command-line-interface.md)
>
>
> [!NOTE]
> Este artigo descreve como criar alertas de métrica clássicos mais antigos. O Azure Monitor agora dá suporte a [alertas de métrica mais novos e melhores](monitoring-near-real-time-metric-alerts.md). Esses alertas podem monitorar várias métricas e permitir alertas em métricas dimensionais. O suporte da CLI do Azure para alertas de métrica mais recentes estará disponível em breve.
>
>

Este artigo mostra como configurar alertas de métrica clássica do Azure usando a CLI (interface de linha de comando) de plataforma cruzada.

> [!NOTE]
> O Azure Monitor é o novo nome do que era chamado de "Azure Insights" até 25 de setembro de 2016. No entanto, os namespaces e, portanto, os comandos descritos aqui ainda contêm a palavra "insights."

Você pode receber um alerta com base nas métricas dos serviços do Azure ou com base nos eventos que ocorrem no Azure.

* **Valores de métrica**: o alerta dispara quando o valor de uma métrica especificada ultrapassa um limite que você atribui em qualquer direção. Ou seja, ele dispara quando a condição é atendida pela primeira vez e, em seguida, quando essa condição não é mais atendida.    

* **Eventos do log de atividades**: um alerta pode disparar em *cada* evento ou quando determinados eventos ocorrem. Para saber mais sobre os logs de atividades, consulte [Criar alertas do log de atividades (clássico)](monitoring-activity-log-alerts.md). 

Você pode configurar um alerta de métrica clássico para fazer o seguinte quando ele disparar:

* Enviar notificações por email para o administrador e coadministradores de serviços. 
* Envie um email para endereços de email que você especificar.
* Chamar um webhook.
* Inicie a execução de um runbook do Azure (somente no portal do Azure atualmente).

É possível configurar e obter informações sobre regras de alerta de métricas clássicas usando o seguinte: 

* [O portal do Azure](insights-alerts-portal.md)
* [PowerShell](insights-alerts-powershell.md)
* [CLI do Azure](insights-alerts-command-line-interface.md)
* [API REST do Monitor do Azure](https://msdn.microsoft.com/library/azure/dn931945.aspx)

Você também pode obter ajuda para comandos, digitando um comando com **-help** no final. A seguir está um exemplo: 

```console
 azure insights alerts -help
 azure insights alerts actions email create -help
 ```

## <a name="create-alert-rules-by-using-azure-cli"></a>Criar regras de alerta usando a CLI do Azure
1. Depois de instalar os pré-requisitos, entre no Azure. Consulte [exemplos da CLI do Azure Monitor](insights-cli-samples.md) para obter os comandos necessários para começar. Esses comandos ajudam a fazer logon, mostrar a assinatura que está usando e prepará-lo para executar os comandos do Azure Monitor.

    ```console
    azure login
    azure account show
    azure config mode arm

    ```

2. Para listar regras existentes em um grupo de recursos, use o seguinte formato: 

   **azure insights alerts rule list** *[options] &lt;resourceGroup&gt;*

   ```console
   azure insights alerts rule list myresourcegroupname

   ```
3. Para criar uma regra, primeiro precisa ter várias informações importantes.
    * A **ID do recurso** para o qual você quer definir um alerta.
    * As **definições de métricas** que estão disponíveis para esse recurso.

     Uma maneira de obter a ID do recurso é usar o portal do Azure. Supondo que o recurso já esteja criado, selecione-o no portal. Em seguida, na próxima folha, na seção **Configurações**, selecione **Propriedades**. A **ID DE RECURSO** é um campo na folha seguinte. 
     
     Também é possível obter a ID do recurso usando o [Azure Resource Explorer](https://resources.azure.com/).

     A seguir, um exemplo de ID do recurso para um aplicativo Web:

     ```console
     /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
     ```

     Para obter uma lista das métricas e unidades disponíveis para as métricas no exemplo de recurso anterior, use o comando da CLI do Azure a seguir:  

     ```console
     azure insights metrics list /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename PT1M
     ```

     *PT1M* é a granularidade da medida disponível (em intervalos de 1 minuto). Haverá opções de métricas diferentes ao usar diferentes granularidades.
     
4. Para criar uma regra de alerta baseada em métrica, use um comando no formato a seguir:

    **azure insights alerts rule metric set** *[options] &lt;ruleName&gt; &lt;location&gt; &lt;resourceGroup&gt; &lt;windowSize&gt; &lt;operator&gt; &lt;threshold&gt; &lt;targetResourceId&gt; &lt;metricName&gt; &lt;timeAggregationOperator&gt;*

    O exemplo a seguir configura um alerta em um recurso do site. O alerta dispara sempre que ele recebe tráfego por cinco minutos de forma consistente e novamente quando ele não recebe tráfego por cinco minutos.

    ```console
    azure insights alerts rule metric set myrule eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total

    ```
5. Para criar um webhook ou enviar um email quando um alerta métrico clássico for acionado, crie primeiro o email ou o webhook. Crie a regra imediatamente a seguir. Não é possível associar webhooks ou emails com regras que já foram criadas.

    ```console
    azure insights alerts actions email create --customEmails myemail@contoso.com

    azure insights alerts actions webhook create https://www.contoso.com

    azure insights alerts rule metric set myrulewithwebhookandemail eastus myreasourcegroup PT5M GreaterThan 2 /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename BytesReceived Total
    ```

6. Você pode verificar se os alertas foram criados corretamente examinando uma regra individual.

    ```console
    azure insights alerts rule list myresourcegroup --ruleName myrule
    ```
7. Para excluir regras, use um comando no formato a seguir:

    **insights alerts rule delete** [options] &lt;resourceGroup&gt; &lt;ruleName&gt;

    Esses comandos excluem as regras que foram criadas anteriormente neste artigo.

    ```console
    azure insights alerts rule delete myresourcegroup myrule
    azure insights alerts rule delete myresourcegroup myrulewithwebhookandemail
    azure insights alerts rule delete myresourcegroup myActivityLogRule
    ```

## <a name="next-steps"></a>Próximas etapas
* [Obter uma visão geral do monitoramento do Azure](monitoring-overview.md), incluindo os tipos de informações que você pode coletar e monitorar.
* Saiba mais sobre como [configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [configurar alertas em eventos de log de atividades](monitoring-activity-log-alerts.md).
* Saiba mais sobre [runbooks da Automação do Azure](../automation/automation-starting-a-runbook.md).
* Tenha uma [visão geral da coleta de logs de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para coletar métricas detalhadas de alta frequência para seu serviço.
* Tenha uma [visão geral da coleção de métricas](insights-how-to-customize-monitoring.md) para verificar se o serviço está disponível e responsivo.
