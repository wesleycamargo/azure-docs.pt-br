---
title: Unificar vários recursos do Application Insights do Azure Monitor | Microsoft Docs
description: Este artigo fornece detalhes sobre como usar uma função nos Logs do Azure Monitor para consultar vários recursos do Application Insights e visualizar esses dados.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: magoedte
ms.openlocfilehash: e3b118306b5a139ba31029bc6191368690b36666
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/14/2019
ms.locfileid: "54265202"
---
# <a name="unify-multiple-azure-monitor-application-insights-resources"></a>Unificar vários recursos do Application Insights do Azure Monitor 
Este artigo descreve como consultar e exibir todos os seus dados de log de aplicativo do Application Insights em um só lugar, mesmo quando eles estiverem em diferentes assinaturas do Azure, como uma substituição para a desativação do Conector do Application Insights.  

## <a name="recommended-approach-to-query-multiple-application-insights-resources"></a>Abordagem recomendada para consultar vários recursos do Application Insights 
Listar vários recursos do Application Insights em uma consulta pode ser complicado e difícil de manter. Em vez disso, você pode aproveitar a função para separar a lógica da consulta do escopo dos aplicativos.  

Este exemplo demonstra como você pode monitorar vários recursos do Application Insights e visualizar a contagem de solicitações com falha pelo nome do aplicativo. Antes de começar, execute esta consulta no workspace conectado aos recursos do Application Insights para obter a lista de aplicativos conectados: 

```
ApplicationInsights
| summarize by ApplicationName
```

Crie uma função usando o operador de união com a lista de aplicativos e, em seguida, salve a consulta como uma função com o alias *applicationsScoping*.  

```
union withsource=SourceApp 
app('Contoso-app1').requests,  
app('Contoso-app2').requests, 
app('Contoso-app3').requests, 
app('Contoso-app4').requests, 
app('Contoso-app5').requests 
| parse SourceApp with * "('" applicationName "')" *  
```

>[!NOTE]
>Você pode modificar os aplicativos listados a qualquer momento navegando até o gerenciador de consultas no portal de Logs e editando a função ou usando o cmdlet `SavedSearch` do PowerShell. O comando `withsource= SourceApp` adiciona aos resultados uma coluna que designa o aplicativo que enviou o log. 
>
>A consulta usa o esquema do Application Insights, embora ela seja executada no workspace, pois a função applicationsScoping retorna a estrutura de dados do Application Insights. 
>
>O operador de análise é opcional neste exemplo; ele extrai o nome do aplicativo da propriedade SourceApp. 

Agora você está pronto para usar a função applicationsScoping na consulta entre recursos. O alias de função retorna a união das solicitações de todos os aplicativos definidos. A consulta então filtra solicitações com falha e visualiza as tendências por aplicativo. ![Exemplo de resultados de consulta cruzada](media/unify-app-resource-data/app-insights-query-results.png)

## <a name="query-across-application-insights-resources-and-workspace-data"></a>Consulta entre dados do workspace e recursos do Application Insights 
Quando você interrompe o Conector e precisa executar consultas em um intervalo de tempo que foi reduzido pela retenção de dados do Application Insights (90 dias), você precisa realizar [consultas de recursos cruzados](../../azure-monitor/log-query/cross-workspace-query.md) no workspace e nos recursos do Application Insights por um período intermediário. Isso é válido até que os dados de seus aplicativos se acumulem devido à nova retenção de dados do Application Insights mencionada acima. A consulta requer algumas manipulações, pois os esquemas no Application Insights e no workspace são diferentes. Consulte a tabela nesta seção que destaca as diferenças de esquema. 

Por exemplo, se o conector parou de funcionar em 2018-11-01, quando você consultar logs em dados de aplicativos e recursos do Application Insights no workspace, sua consulta será construída como o exemplo a seguir:

```
applicationsScoping //this brings data from Application Insights resources 
| where timestamp between (datetime("2018-11-01") .. now()) 
| where success == 'False' 
| where duration > 1000 
| union ( 
    ApplicationInsights //this is Application Insights data in Log Analytics workspace 
    | where TimeGenerated < (datetime("2018-12-01") 
    | where RequestSuccess == 'False' 
    | where RequestDuration > 1000 
    | extend duration = RequestDuration //align to Application Insights schema 
    | extend timestamp = TimeGenerated //align to Application Insights schema 
    | extend name = RequestName //align to Application Insights schema 
    | extend resultCode = ResponseCode //align to Application Insights schema 
    | project-away RequestDuration , RequestName , ResponseCode , TimeGenerated 
) 
| project timestamp , duration , name , resultCode 
```

## <a name="application-insights-and-log-analytics-workspace-schema-differences"></a>Diferenças de esquema de workspace do Application Insights e do Log Analytics
A tabela a seguir mostra as diferenças de esquema entre o Log Analytics e o Application Insights.  

| Propriedades do workspace do Log Analytics| Propriedades de recurso do Application Insights|
|------------|------------| 
| AnonUserId | user_id|
| ApplicationId | appId|
| ApplicationName | appName|
| ApplicationTypeVersion | application_Version |
| AvailabilityCount | itemCount |
| AvailabilityDuration | duration |
| AvailabilityMessage | Message |
| AvailabilityRunLocation | location |
| AvailabilityTestId | ID |
| AvailabilityTestName | Nome |
| AvailabilityTimestamp |  timestamp |
| Navegador | client_browser |
| City | client_city |
| ClientIP | client_IP |
| Computador | cloud_RoleInstance | 
| País/Região | client_CountryOrRegion | 
| CustomEventCount | itemCount | 
| CustomEventDimensions | customDimensions |
| CustomEventName | Nome | 
| DeviceModel | client_Model | 
| DeviceType | client_Type | 
| ExceptionCount | itemCount | 
| ExceptionHandledAt | handledAt |
| ExceptionMessage | Message | 
| ExceptionType | Tipo |
| OperationID | operation_id |
| OperationName | operation_Name | 
| SO | client_OS | 
| PageViewCount | itemCount |
| PageViewDuration | duration | 
| PageViewName | Nome | 
| ParentOperationID | operation_Id | 
| RequestCount | itemCount | 
| RequestDuration | duration | 
| RequestID | ID | 
| RequestName | Nome | 
| RequestSuccess | sucesso | 
| ResponseCode | resultCode | 
| Função | cloud_RoleName |
| RoleInstance | cloud_RoleInstance |
| SessionId | session_Id | 
| SourceSystem | operation_SyntheticSource |
| TelemetryTYpe | Tipo |
| URL | _url |
| UserAccountId | user_AccountId |

## <a name="next-steps"></a>Próximas etapas

Use a [Pesquisa de Logs](../../azure-monitor/log-query/log-query-overview.md) para exibir informações detalhadas dos aplicativos do Application Insights.