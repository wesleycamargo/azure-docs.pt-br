---
title: Pesquisar em todos os recursos com o Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como você pode consultar nos recursos de vários workspaces e no aplicativo App Insights em sua assinatura.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: magoedte
ms.openlocfilehash: 1140e9d0a9df717b8e249d8dad895baec955b754
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192967"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Executar pesquisas de log de recursos cruzados no Log Analytics  

Anteriormente com o Azure Log Analytics, você poderia apenas analisar dados no workspace atual e isso limitava sua capacidade de consultar em vários workspaces definidos em sua assinatura.  Além disso, você pode pesquisar somente itens de telemetria coletados do seu aplicativo baseado na web com o Application Insights diretamente no Application Insights ou do Visual Studio.  Isso também tornou um desafio para analisar nativamente dados de aplicativo e operacionais juntos.   

Agora você pode consultar não apenas em vários workspaces de Application Insights, mas também os dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados de todo o sistema.  Esses tipos de consultas só podem ser realizados no [Log Analytics](portals.md#log-analytics-page). O número de recursos (workspaces do Log Analytics e aplicativo do Application Insights) que você pode incluir em uma única consulta está limitado a 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em workspaces do Log Analytics e do Application Insights
Para fazer referência a outro workspace em sua consulta, use o identificador [*workspace*](https://docs.microsoft.com/azure/log-analytics/query-language/workspace-expression) e, para um aplicativo do Application Insights, use o identificador [*aplicativo*](https://docs.microsoft.com/azure/log-analytics/query-language/app-expression).  

### <a name="identifying-workspace-resources"></a>Identificar recursos do workspace
Os exemplos a seguir demonstram consultas em workspaces do Log Analytics para retornar contagens resumidas de logs da tabela de Atualização em um workspace chamado *contosoretail-it*. 

A identificação de um workspace pode ser realizada de várias maneiras:

* Nome de recurso – é um nome legível do workspace, também conhecido como *nome do componente*. 

    `workspace("contosoretail-it").Update | count`
 
    >[!NOTE]
    >A identificação de um workspace por nome presume exclusividade em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do workspace, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do workspace – uma ID do workspace é o identificador exclusivo e imutável atribuído a cada workspace representado como um identificador global exclusivo (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do workspace. Use a ID do Recurso quando o nome do recurso for ambíguo.  Para workspaces, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspacess/componentName*.  

    Por exemplo: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail-it").Update | count
    ```

### <a name="identifying-an-application"></a>Identificar um aplicativo
Os exemplos a seguir retornam uma contagem resumida de solicitações feitas em relação a um aplicativo chamado *fabrikamapp* no Application Insights. 

A identificação de um aplicativo no Application Insights pode ser realizada com a expressão *app(Identifier)*.  O argumento *Identificador* especifica o aplicativo usando um dos seguintes:

* Nome de recurso – é um nome legível do aplicativo, também conhecido como o *nome do componente*.  

    `app("fabrikamapp")`

* Nome qualificado – é o "nome completo" do aplicativo, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID – o GUID de aplicativo do aplicativo.

    `app("b459b4f6-912x-46d5-9cb1-b43069212ab4").requests | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do aplicativo. Use a ID do Recurso quando o nome do recurso for ambíguo. O formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*.  

    Por exemplo: 
    ```
    app("/subscriptions/b459b4f6-912x-46d5-9cb1-b43069212ab4/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

### <a name="performing-a-query-across-multiple-resources"></a>Executar uma consulta em vários recursos
Você pode consultar vários recursos de qualquer uma das suas instâncias de recursos, que podem ser espaços de trabalho e aplicativos combinados.
    
Exemplo de consulta em dois workspaces:    

```
union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="using-cross-resource-query-for-multiple-resources"></a>Usando consulta entre recursos para vários recursos
Ao usar consultas entre recursos para correlacionar dados de vários recursos do Log Analytics e do Application Insights, a consulta pode se tornar complexa e difícil de manter. Você deve utilizar [funções no Log Analytics](../../azure-monitor/log-query/functions.md) para separar a lógica de consulta do escopo dos recursos da consulta, o que simplifica a estrutura da consulta. O exemplo a seguir demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha pelo nome do aplicativo. 

Crie uma consulta como a seguinte que referencia o escopo dos recursos do Application Insights. O comando `withsource= SourceApp` adiciona uma coluna que designa o nome do aplicativo que enviou o log. [Salve a consulta como função](../../azure-monitor/log-query/functions.md#create-a-function) com o alias _applicationsScoping_.

```Kusto
// crossResource function that scopes my Application Insights resources
union withsource= SourceApp
app('Contoso-app1').requests, 
app('Contoso-app2').requests,
app('Contoso-app3').requests,
app('Contoso-app4').requests,
app('Contoso-app5').requests
```



Agora você pode [usar essa função](../../azure-monitor/log-query/functions.md#use-a-function) em uma consulta entre recursos, como a seguir. O alias de função _applicationsScoping_ retorna a união da tabela de solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo. O operador _parse_ é opcional neste exemplo. Ele extrai o nome do aplicativo do _SourceApp_ propriedade.

```Kusto
applicationsScoping 
| where timestamp > ago(12h)
| where success == 'False'
| parse SourceApp with * '(' applicationName ')' * 
| summarize count() by applicationName, bin(timestamp, 1h) 
| sort by count_ desc 
| render timechart
```
![Gráfico de tempo](media/cross-workspace-query/chart.png)

## <a name="next-steps"></a>Próximas etapas

Examine a [referência de pesquisa de logs do Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/kusto) para exibir todas as opções de sintaxe de consulta disponíveis no Log Analytics.    
