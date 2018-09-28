---
title: Pesquisar em todos os recursos com o Azure Log Analytics | Microsoft Docs
description: Este artigo descreve como você pode consultar nos recursos de vários espaços de trabalho e no aplicativo App Insights em sua assinatura.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/17/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 716f96b7723a947c35eb54acd67f13261363fd38
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46972871"
---
# <a name="perform-cross-resource-log-searches-in-log-analytics"></a>Executar pesquisas de log de recursos cruzados no Log Analytics  

Anteriormente com o Azure Log Analytics, você poderia apenas analisar dados no espaço de trabalho atual e isso limitava sua capacidade de consultar em vários espaços de trabalho definidos em sua assinatura.  Além disso, você pode pesquisar somente itens de telemetria coletados do seu aplicativo baseado na web com o Application Insights diretamente no Application Insights ou do Visual Studio.  Isso também tornou um desafio para analisar nativamente dados de aplicativo e operacionais juntos.   

Agora você pode consultar não apenas em vários espaços de trabalho de Application Insights, mas também os dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados de todo o sistema.  Esses tipos de consultas só podem ser realizados no [Log Analytics](log-analytics-log-search-portals.md#log-analytics-page). O número de recursos (espaços de trabalho do Log Analytics e aplicativo do Application Insights) que você pode incluir em uma única consulta está limitado a 100. 

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em espaços de trabalho do Log Analytics e do Application Insights
Para fazer referência a outro espaço de trabalho em sua consulta, use o identificador [*espaço de trabalho*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) e, para um aplicativo do Application Insights, use o identificador [*aplicativo*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

### <a name="identifying-workspace-resources"></a>Identificar recursos do espaço de trabalho
Os exemplos a seguir demonstram consultas em espaços de trabalho do Log Analytics para retornar contagens resumidas de logs da tabela de Atualização em um espaço de trabalho chamado *contosoretail-it*. 

A identificação de um espaço de trabalho pode ser realizada de várias maneiras:

* Nome de recurso – é um nome legível do espaço de trabalho, também conhecido como *nome do componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A identificação de um espaço de trabalho por nome presume exclusividade em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do espaço de trabalho, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/contosoretail-it').Update | count `

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do espaço de trabalho – uma ID do espaço de trabalho é o identificador exclusivo e imutável atribuído a cada espaço de trabalho representado como um identificador global exclusivo (GUID).

    `workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do espaço de trabalho. Use a ID do Recurso quando o nome do recurso for ambíguo.  Para espaços de trabalho, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Por exemplo: 
    ``` 
    workspace("/subscriptions/e427519-5645-8x4e-1v67-3b84b59a1985/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Update | count
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
É possível consultar vários recursos de qualquer uma das instâncias de recursos; esses recursos podem ser aplicativos e espaços de trabalho combinados.
    
Exemplo de consulta em dois espaços de trabalho:    
    ```
    union Update, workspace("contosoretail-it").Update, workspace("b459b4u5-912x-46d5-9cb1-p43069212nb4").Update
    | where TimeGenerated >= ago(1h)
    | where UpdateState == "Needed"
    | summarize dcount(Computer) by Classification
    ```

## <a name="next-steps"></a>Próximas etapas

Examine a [referência de pesquisa de logs do Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) para exibir todas as opções de sintaxe de consulta disponíveis no Log Analytics.    
