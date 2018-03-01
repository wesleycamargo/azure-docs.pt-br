---
title: "Executar consultas em espaços de trabalho do Azure Log Analytics | Microsoft Docs"
description: "Este artigo descreve como você pode consultar em vários espaços de trabalho e especificar o aplicativo App Insights em sua assinatura."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2018
ms.author: magoedte
ms.openlocfilehash: 403448995c28ff7172d2c3abbf3b9d67341017b4
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="how-to-perform-queries-across-multiple-log-analytics-workspaces"></a>Como executar consultas em diversos espaços de trabalho do Azure Log Analytics

Anteriormente com o Azure Log Analytics, você poderia apenas analisar dados no espaço de trabalho atual e isso limitava sua capacidade de consultar em vários espaços de trabalho definidos em sua assinatura.  

Agora você pode consultar não apenas em vários espaços de trabalho de Application Insights, mas também os dados de um aplicativo Application Insights específico no mesmo grupo de recursos, outro grupo de recursos ou outra assinatura. Isso fornece uma exibição de seus dados de todo o sistema.  Só é possível executar esse tipo de consulta no [Portal avançado](log-analytics-log-search-portals.md#advanced-analytics-portal), não no portal do Azure.  

## <a name="querying-across-log-analytics-workspaces-and-from-application-insights"></a>Consultar em espaços de trabalho do Log Analytics e do Application Insights
Para fazer referência a outro espaço de trabalho em sua consulta, use o identificador [*espaço de trabalho*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/workspace()) e, para um aplicativo do Application Insights, use o identificador [*aplicativo*](https://docs.loganalytics.io/docs/Language-Reference/Scope-functions/app()).  

Por exemplo, a primeira consulta retorna contagens resumidas de atualizações necessárias à sua classificação da tabela de Atualização de espaço de trabalho atual e outro espaço de trabalho chamado *contosoretail-it*.  O segundo exemplo de consulta retorna uma contagem resumida de solicitações feitas em relação a um aplicativo chamado *fabrikamapp* no Application Insights. 

### <a name="identifying-workspace-resources"></a>Identificar recursos do espaço de trabalho
A identificação de um espaço de trabalho pode ser executada de várias maneiras:

* Nome de recurso – é um nome legível do espaço de trabalho, também conhecido como *nome do componente*. 

    `workspace("contosoretail").Update | count`
 
    >[!NOTE]
    >A identificação de um espaço de trabalho por seu nome presume que ele seja exclusivo em todas as assinaturas acessíveis. Se você tiver vários aplicativos com o nome especificado, a consulta falhará devido à ambiguidade. Nesse caso, você deve usar um dos outros identificadores.

* Nome qualificado – é o "nome completo" do espaço de trabalho, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `workspace('contoso/contosoretail/development').requests | count `

    >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID do espaço de trabalho – uma ID do espaço de trabalho é o identificador exclusivo e imutável atribuído a cada espaço de trabalho representado como um identificador global exclusivo (GUID).

    `workspace("b438b4f6-912a-46d5-9cb1-b44069212ab4").Update | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do espaço de trabalho. Use a ID do Recurso quando o nome do recurso for ambíguo.  Para espaços de trabalho, o formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/workspaces/componentName*.  

    Por exemplo: 
    ``` 
    workspace("/subscriptions/e427267-5645-4c4e-9c67-3b84b59a6982/resourcegroups/ContosoAzureHQ/providers/Microsoft.OperationalInsights/workspaces/contosoretail").Event | count
    ```

### <a name="identifying-an-application"></a>Identificar um aplicativo

A identificação de um aplicativo no Application Insights pode ser executada com a expressão *app(Identifier)*.  O argumento *Identificador* especifica o aplicativo usando um dos seguintes:

* Nome de recurso – é um nome legível do aplicativo, também conhecido como o *nome do componente*.  

    `app("fabrikamapp")`

* Nome qualificado – é o "nome completo" do aplicativo, composto pelo nome da assinatura, pelo grupo de recursos e pelo nome do componente neste formato: *subscriptionName/resourceGroup/componentName*. 

    `app("AI-Prototype/Fabrikam/fabrikamapp").requests | count`

     >[!NOTE]
    >Como os nomes de assinatura do Azure não são exclusivos, esse identificador pode ser ambíguo. 
    >

* ID – o GUID de aplicativo do aplicativo.

    `app("b438b4f6-912a-46d5-9cb1-b44069212ab4").requests | count`

* ID de recurso do Azure – a identidade exclusiva definida pelo Azure do aplicativo. Use a ID do Recurso quando o nome do recurso for ambíguo. O formato é: */subscriptions/subscriptionId/resourcegroups/resourceGroup/providers/microsoft.OperationalInsights/components/componentName*.  

    Por exemplo: 
    ```
    app("/subscriptions/7293b69-db12-44fc-9a66-9c2005c3051d/resourcegroups/Fabrikam/providers/microsoft.insights/components/fabrikamapp").requests | count
    ```

## <a name="next-steps"></a>Próximas etapas

Examine a [referência de pesquisa de logs do Log Analytics](https://docs.loganalytics.io/docs/Language-Reference) para exibir todas as opções de sintaxe de consulta disponíveis no Log Analytics.    