---
title: "Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos | Microsoft Docs"
description: "Saiba como transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos."
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 42bc4845-c564-4568-b72d-0614591ebd80
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: c9063fcc59d83cb2a6a159cf3a69234417138a5c
ms.openlocfilehash: 5cadc3ea77ba13d40876c7bc11d2aa1d6abe6b87


---
# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Transmitir Logs de Diagnóstico do Azure para os Hubs de Eventos
Os **[Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)** podem ser transmitidos em tempo real a qualquer aplicativo usando a opção interna "Exportar para Hubs de Eventos" no Portal, ou habilitando a ID da Regra de Barramento de Serviço em uma Configuração de Diagnóstico por meio de Cmdlets do Azure PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que você pode fazer com os Logs de Diagnóstico e os Hubs de Eventos
Veja algumas maneiras de usar o recurso de streaming para os Logs de Diagnóstico:

* **Transmitir logs para sistemas de registro em log e telemetria de terceiros** – Ao longo do tempo, a transmissão de Hubs de Eventos se tornará o mecanismo para direcionar seus Logs de Diagnóstico para SIEMs e soluções de análise de log de terceiros.
* **Exibir a integridade do serviço transmitindo dados de "afunilamento" para o Power BI** – Com os Hubs de Eventos, Stream Analytics e o Power BI, é fácil transformar seus dados de diagnóstico em informações quase em tempo real nos serviços do Azure. [Este artigo de documentação fornece uma ótima visão geral de como configurar um Hubs de Eventos, processar dados com o Stream Analytics e usar o Power BI como uma saída](../stream-analytics/stream-analytics-power-bi-dashboard.md). Veja algumas dicas para configurá-lo com os Logs de Diagnóstico:
  
  * Os Hubs de Eventos de uma categoria de Logs de Diagnóstico é criado automaticamente quando você marca a opção no portal ou habilita-o por meio do PowerShell, para que você possa selecionar os Hubs de Eventos no namespace do Barramento de Serviço com o nome que começa com “insights-”
  * Veja um exemplo de consulta do Stream Analytics que você pode usar para simplesmente analisar todos os dados de log em uma tabela do PowerBI:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

* **Compilar uma plataforma de registro em log e telemetria personalizada** – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de logs de diagnóstico. [Confira o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitar o streaming de Logs de Diagnóstico
Você pode habilitar programaticamente o streaming de Logs de Diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx). De qualquer forma, escolha um Namespace do Barramento de Serviço e um Hub de Eventos será criado no namespace para cada categoria de log que você habilitar. Uma **Categoria de Log** de Diagnóstico é um tipo de log que um recurso pode coletar. Você pode selecionar quais categorias de log deseja coletar para um determinado recurso no Portal do Azure, na folha Diagnóstico.

![Categorias de log no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [!WARNING]
> A habilitação e streaming de logs de diagnóstico dos recursos de computação (por exemplo, VMs ou Service Fabric) [exige um conjunto diferente de etapas](../event-hubs/event-hubs-streaming-azure-diags-data.md).
> 
> 

O namespace do barramento de serviço ou do hub de eventos não precisa estar na mesma assinatura que o recurso que emite os logs, contanto que o usuário que define a configuração tenha acesso RBAC apropriado a ambas as assinaturas.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell
Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](insights-powershell-samples.md), use o cmdlet `Set-AzureRmDiagnosticSetting` com estes parâmetros:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

A ID da Regra do Barramento de Serviço é uma cadeia de caracteres com este formato: `{service bus resource ID}/authorizationrules/{key name}` , `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.

### <a name="via-azure-cli"></a>Via CLI do Azure
Para habilitar o streaming por meio da [CLI do Azure](insights-cli-samples.md), use o comando `insights diagnostic set` da seguinte forma:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Use o mesmo formato para ID de Regra do Barramento de Serviço, conforme explicado para o Cmdlet do PowerShell.

### <a name="via-azure-portal"></a>Via Portal do Azure
Para habilitar o streaming por meio do Portal do Azure, navegue até as configurações de diagnóstico de um recurso e selecione “Exportar para o Hub de Eventos”.

![Exportar para Hubs de Eventos no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Para configurá-lo, selecione um Namespace de Barramento de Serviço existente. O namespace selecionado será o local onde os Hubs de Eventos serão criados (se este for seu primeiro streaming de logs de diagnóstico) ou transmitidos (se já houver recursos realizando o streaming dessa categoria log para esse namespace), e a política define as permissões do mecanismo de streaming. Atualmente, o streaming para um Hub de Eventos exige as Gerenciar, Enviar e Escutar. Você pode criar ou modificar políticas de acesso compartilhado do Namespace do Barramento de Serviço no portal clássico, na guia "Configurar" para seu Namespace de Barramento de Serviço. Para atualizar uma dessas Configurações de Diagnóstico, o cliente deve ter a permissão ListKey na Regra de Autorização do Barramento de Serviço.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como eu consumo os dados de log dos Hubs de Eventos?
Estes são os dados de saída de exemplo dos Hubs de Eventos:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome do elemento | Descrição |
| --- | --- |
| records |Uma matriz de todos os eventos de log nessa carga. |
| tempo real |A hora na qual o evento ocorreu. |
| categoria |Categoria do log desse evento. |
| resourceId |ID de recurso do recurso que gerou esse evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível do evento de log. |
| propriedades |Propriedades do evento. |

Veja uma lista de todos os provedores de recursos que oferecem suporte a streaming para o Hub de Eventos [aqui](monitoring-overview-of-diagnostic-logs.md).

## <a name="stream-data-from-compute-resources"></a>Transmitir dados de recursos de Computação
Também é possível transmitir logs de diagnóstico de recursos de Computação usando o agente do Diagnóstico do Microsoft Azure. [Consulte este artigo](../event-hubs/event-hubs-streaming-azure-diags-data.md) para saber como configurar isso.

## <a name="next-steps"></a>Próximas etapas
* [Saiba mais sobre os Logs de Diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
* [Introdução aos Hubs de Evento](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)




<!--HONumber=Dec16_HO2-->


