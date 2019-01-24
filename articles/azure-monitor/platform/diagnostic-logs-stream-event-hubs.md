---
title: Transmitir logs de diagnóstico do Azure para um hub de eventos
description: Saiba como transmitir logs de diagnóstico do Azure para um hub de eventos.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/25/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: 1ef779e64995c6996538b68b892cb45f11788067
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54477457"
---
# <a name="stream-azure-diagnostic-logs-to-an-event-hub"></a>Transmitir logs de diagnóstico do Azure para um hub de eventos
Os **[logs de diagnóstico do Azure](diagnostic-logs-overview.md)** podem ser transmitidos quase em tempo real a qualquer aplicativo usando a opção interna "Exportar para os Hubs de Eventos" no Portal, ou habilitando a ID da Regra de Autorização do Hub de Eventos em uma configuração de diagnóstico por meio de cmdlets do Azure PowerShell ou da CLI do Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que você pode fazer com os logs de diagnóstico e os Hubs de Eventos
Veja algumas maneiras de usar o recurso de streaming para os Logs de Diagnóstico:

* **Transmitir logs para sistemas de registro em log e telemetria de terceiros** – você pode transmitir todos os logs de diagnóstico para um único hub de eventos a fim de enviar os dados de log por pipe para uma ferramenta de análise de logs ou um SIEM de terceiros.
* **Exibir a integridade do serviço transmitindo dados de "afunilamento" para o Power BI** – usando Hubs de Eventos, Stream Analytics e Power BI, é fácil transformar seus dados de diagnóstico em insights quase em tempo real nos seus serviços do Azure. [Este artigo de documentação apresenta uma excelente visão geral de como configurar Hubs de Eventos, processar dados com o Stream Analytics e usar o Power BI como saída](../../stream-analytics/stream-analytics-power-bi-dashboard.md). Aqui estão algumas dicas para configurá-lo com os logs de diagnóstico:

  * Um hub de eventos de uma categoria de logs de diagnóstico é criado automaticamente quando você marca a opção no portal ou habilita-a por meio do PowerShell, de modo que você possa selecionar o hub de evento no namespace com o nome que começa com **insights-**.
  * O código SQL a seguir é um exemplo de consulta do Stream Analytics que você pode usar para analisar todos os dados de log em uma tabela do Power BI:

    ```sql
    SELECT
    records.ArrayValue.[Properties you want to track]
    INTO
    [OutputSourceName – the Power BI source]
    FROM
    [InputSourceName] AS e
    CROSS APPLY GetArrayElements(e.records) AS records
    ```

* **Compilar uma plataforma de registro em log e telemetria personalizada** – Se você já tiver uma plataforma de telemetria personalizada ou estiver pensando em criar uma, a natureza altamente escalonável de publicação-assinatura dos Hubs de Eventos permite a flexibilidade de ingestão de logs de diagnóstico. [Confira o guia de Dan Rosanova sobre como usar os Hubs de Eventos em uma plataforma de telemetria de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Habilitar o streaming de logs de diagnóstico

Você pode habilitar programaticamente o streaming de logs de diagnóstico por meio do portal ou usando a [API REST do Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). De qualquer forma, você cria uma configuração de diagnóstico no qual especifica um namespace de Hubs de Eventos e as categorias de log e as métricas que deseja enviar para o namespace. Um hub de eventos é criado no namespace para cada categoria de log que você habilitar. Uma **categoria de log** de diagnóstico é um tipo de log que um recurso pode coletar.

> [!WARNING]
> A habilitação e streaming de logs de diagnóstico dos recursos de computação (por exemplo, VMs ou Service Fabric) [exige um conjunto diferente de etapas](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md).

O namespace dos Hubs de Eventos não precisa estar na mesma assinatura que o recurso que emite os logs, desde que o usuário que define a configuração tenha acesso RBAC adequado a ambas as assinaturas e que as duas assinaturas façam parte do mesmo locatário do ADD.

> [!NOTE]
> Atualmente, não há suporte para o envio da métrica multidimensional por meio das configurações de diagnóstico. As métricas com dimensões são exportadas como métricas dimensionais simples, agregadas nos valores da dimensão.
>
> *Por exemplo*: A métrica “Mensagens de Entrada” em um Hub de Eventos pode ser explorada e mapeada por nível da fila. No entanto, quando exportada por meio das configurações de diagnóstico, a métrica será representada como todas as mensagens de entrada em todas as filas no Hub de Eventos.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Transmitir logs de diagnóstico usando o portal

1. No portal, navegue até o Azure Monitor e clique em **Configurações de Diagnóstico**

    ![Seção de monitoramento do Azure Monitor](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-blade.png)

2. Se desejar filtrar a lista por tipo de recurso ou grupo de recursos, clique no recurso para o qual você deseja definir uma configuração de diagnóstica.

3. Se nenhuma configuração existir no recurso que você selecionou, será solicitada a criação de uma configuração. Clique em “Ativar diagnóstico”.

   ![Adicionar configuração de diagnóstico - nenhuma configuração existente](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-none.png)

   Se houver configurações existentes no recurso, você verá uma lista de configurações já definidas nesse recurso. Clique em "Adicionar configuração de diagnóstico".

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-multiple.png)

3. Dê um nome à sua configuração e marque a caixa **Fluxo para um hub de eventos** e então selecione um namespace de Hubs de Eventos.

   ![Adicionar configuração de diagnóstico - configurações existentes](media/diagnostic-logs-stream-event-hubs/diagnostic-settings-configure.png)

   O namespace selecionado será o local em que o hub de evento é criado (se este for seu primeiro streaming de logs de diagnóstico) ou transmitido (se já houver recursos realizando o streaming dessa categoria log para esse namespace) e a política define as permissões do mecanismo de streaming. Atualmente, o streaming para um hub de eventos exige as permissões para Gerenciar, Enviar e Escutar. Você pode criar ou modificar políticas de acesso compartilhado de namespace de Hubs de Eventos no portal na guia Configurar para seu namespace. Para atualizar uma dessas configurações de diagnóstico, o cliente deve ter a permissão ListKey na regra de autorização de Hubs de Evento. Você também pode, opcionalmente, especificar um nome de hub de eventos. Se você especificar um nome de hub de eventos, os logs serão roteados para esse hub de eventos em vez de um hub de eventos recentemente criado para cada categoria de log.

4. Clique em **Salvar**.

Após alguns instantes, a nova configuração aparecerá na lista de configurações desse recurso e os logs de diagnóstico serão transmitidos para esse hub de eventos assim que os novos dados de eventos forem gerados.

### <a name="via-powershell-cmdlets"></a>Via Cmdlets do PowerShell

Para habilitar o streaming por meio de [Cmdlets do Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), use o cmdlet `Set-AzureRmDiagnosticSetting` com estes parâmetros:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource ID] -EventHubAuthorizationRuleId [your Event Hub namespace auth rule ID] -Enabled $true
```

A ID de Regra da Autorização do Hub de Evento é uma cadeia de caracteres com este formato: `{Event Hub namespace resource ID}/authorizationrules/{key name}`, por exemplo, `/subscriptions/{subscription ID}/resourceGroups/{resource group}/providers/Microsoft.EventHub/namespaces/{Event Hub namespace}/authorizationrules/RootManageSharedAccessKey`. Atualmente, não é possível selecionar um nome de hub de eventos específico com o PowerShell.

### <a name="via-azure-cli"></a>Via CLI do Azure

Para habilitar o streaming por meio da [CLI do Azure](https://docs.microsoft.com/cli/azure/monitor?view=azure-cli-latest), você pode usar o comando [az monitor diagnostic-settings create](https://docs.microsoft.com/cli/azure/monitor/diagnostic-settings?view=azure-cli-latest#az-monitor-diagnostic-settings-create).

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

É possível adicionar categorias adicionais ao log de diagnóstico, adicionando dicionários à matriz JSON transmitida como o parâmetro `--logs`.

O argumento `--event-hub-rule` usa o mesmo formato que a ID da Regra de Autorização do Hub de Eventos, conforme explicado para o Cmdlet do PowerShell.

## <a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como eu consumo os dados de log dos Hubs de Eventos?

Aqui está um exemplo de dados de saída dos Hubs de Eventos:

```json
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

| Nome do elemento | DESCRIÇÃO |
| --- | --- |
| records |Uma matriz de todos os eventos de log nessa carga. |
| tempo real |A hora na qual o evento ocorreu. |
| categoria |Categoria do log desse evento. |
| ResourceId |ID de recurso do recurso que gerou esse evento. |
| operationName |Nome da operação. |
| level |Opcional. Indica o nível do evento de log. |
| propriedades |Propriedades do evento. |

É possível exibir uma lista de todos os provedores de recursos que dão suporte a streaming para o Hub de Eventos [aqui](diagnostic-logs-overview.md).

## <a name="stream-data-from-compute-resources"></a>Transmitir dados de recursos de Computação

Também é possível transmitir logs de diagnóstico de recursos de Computação usando o agente do Diagnóstico do Azure. [Consulte este artigo](../../azure-monitor/platform/diagnostics-extension-stream-event-hubs.md) para saber como configurar isso.

## <a name="next-steps"></a>Próximas etapas

* [Transmitir logs do Azure Active Directory com o Azure Monitor](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Saiba mais sobre os Logs de Diagnóstico do Azure](diagnostic-logs-overview.md)
* [Introdução aos Hubs de Evento](../../event-hubs/event-hubs-dotnet-standard-getstarted-send.md)

